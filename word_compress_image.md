# 问道妈耶，那我问你

```python
import sys
import zipfile
import shutil
import os
from PIL import Image
import xml.etree.ElementTree as ET
from cairosvg import svg2png

delete_unused = False  # 是否删除未使用的图片
docx_name = '6.5 最终科技报告'
zip_file = zipfile.ZipFile(docx_name+'.docx', 'r')
output_dir = os.path.normpath(f"./{docx_name}")
# 如果 output_dir 存在，先删除
if os.path.exists(output_dir):
    shutil.rmtree(output_dir)
    print(f"已删除存在的目录: {output_dir}")
zip_file.extractall(output_dir)
print(f"解压缩完成，文件已保存到 {output_dir} 文件夹中。")
zip_file.close()

def check_docx_accessibility(docx_path):
    """
    检查最终生成的 docx 文件是否被占用
    """
    if os.path.exists(docx_path):
        try:
            with open(docx_path, 'w'):  # 尝试以追加模式打开文件
                pass
        except IOError:
            print(f"文件被占用，无法写入: {docx_path}")
            sys.exit(1)  # 退出程序

# 重新打包为新的 docx 文件
new_docx_name = docx_name + '(1).docx'

# 检查 docx 文件是否被占用
check_docx_accessibility(new_docx_name)

def compress_image(file_path, target_size=64, step=10, quality=85):
    """
    使用 JPEG 压缩 PNG 文件，处理透明背景
    """
    try:
        original_size = os.path.getsize(file_path) / 1024  # 压缩前文件大小 (KB)
        im = Image.open(file_path)

        # 如果是 PNG 且有透明背景，填充为白色背景
        if im.mode in ('RGBA', 'LA'):  # 检查是否有透明通道
            background = Image.new('RGB', im.size, (255, 255, 255))  # 创建白色背景
            background.paste(im, mask=im.split()[3])  # 使用透明通道作为掩码
            im = background
        elif im.mode != 'RGB':  # 如果不是 RGB 模式，转换为 RGB
            im = im.convert('RGB')

        # 使用 JPEG 格式保存临时文件
        temp_path = file_path.rsplit('.', 1)[0] + '_compressed.jpeg'
        while original_size >= target_size:
            try:
                # 保存为 JPEG 格式
                im.save(temp_path, format='JPEG', quality=quality)
                if not os.path.exists(temp_path):
                    raise FileNotFoundError(f"临时文件未生成: {temp_path}")
                quality -= step
                if quality <= 0:
                    break
                original_size = os.path.getsize(temp_path) / 1024
            except Exception as e:
                print(f"生成临时文件时出错: {file_path}, 错误: {e}")
                return
        # 替换原文件内容
        if os.path.exists(temp_path):
            os.replace(temp_path, file_path)
            compressed_size = os.path.getsize(file_path) / 1024  # 压缩后文件大小 (KB)
            print(f"图片压缩完成: {file_path}")
            print(f"压缩前大小: {original_size:.2f} KB, 压缩后大小: {compressed_size:.2f} KB")
        else:
            print(f"临时文件未找到，无法替换原文件: {file_path}")
    except Exception as e:
        print(f"压缩图片时出错: {file_path}, 错误: {e}")

def convert_to_jpeg_or_png(file_path, target_format='jpeg', quality=85):
    """
    将 SVG 或 EMF 文件转换为 JPEG 或 PNG 格式，确保透明背景正确处理
    """
    try:
        if file_path.endswith('.svg'):
            # 转换 SVG 为 PNG
            png_path = file_path.replace('.svg', '.png')
            svg2png(url=file_path, write_to=png_path)
            print(f"SVG 转换为 PNG: {png_path}")

            # 处理透明背景，将其转换为白色背景
            with Image.open(png_path) as im:
                if im.mode in ('RGBA', 'LA'):  # 如果有透明通道
                    background = Image.new('RGB', im.size, (255, 255, 255))  # 创建白色背景
                    background.paste(im, mask=im.split()[3])  # 使用透明通道作为掩码
                    im = background
                jpeg_path = png_path.replace('.png', '.jpeg')
                im.save(jpeg_path, format='JPEG', quality=quality)
                os.remove(png_path)  # 删除中间的 PNG 文件
                print(f"PNG 转换为 JPEG: {jpeg_path}")
                return jpeg_path

        elif file_path.endswith('.emf'):
            # 转换 EMF 为 JPEG
            jpeg_path = file_path.replace('.emf', '.jpeg')
            with Image.open(file_path) as im:
                if im.mode != 'RGB':
                    im = im.convert('RGB')
                im.save(jpeg_path, format='JPEG', quality=quality)
            print(f"EMF 转换为 JPEG: {jpeg_path}")
            return jpeg_path

    except Exception as e:
        print(f"文件转换失败: {file_path}, 错误: {e}")
        return None

# 更新 document.xml.rels 文件
def update_rels_file(rels_path, old_file, new_file):
    """
    更新 document.xml.rels 文件中的图片引用
    """
    if os.path.exists(rels_path):
        tree = ET.parse(rels_path)
        root = tree.getroot()
        namespaces = {'r': 'http://schemas.openxmlformats.org/package/2006/relationships'}
        for rel in root.findall('.//r:Relationship', namespaces):
            target = rel.attrib.get('Target')
            if target and old_file in target:
                rel.attrib['Target'] = target.replace(old_file, new_file)
                print(f"更新 rels 文件: {old_file} -> {new_file}")
        tree.write(rels_path, encoding='utf-8', xml_declaration=True)

# 更新 document.xml 文件
def update_document_xml(document_path, old_file, new_file):
    """
    更新 document.xml 文件中的图片引用
    """
    if os.path.exists(document_path):
        tree = ET.parse(document_path)
        root = tree.getroot()
        namespaces = {
            'a': 'http://schemas.openxmlformats.org/drawingml/2006/main',
            'r': 'http://schemas.openxmlformats.org/officeDocument/2006/relationships'
        }
        for blip in root.findall('.//a:blip', namespaces):
            embed = blip.attrib.get('{http://schemas.openxmlformats.org/officeDocument/2006/relationships}embed')
            if embed and old_file in embed:
                blip.attrib['{http://schemas.openxmlformats.org/officeDocument/2006/relationships}embed'] = embed.replace(old_file, new_file)
                print(f"更新 document.xml 文件: {old_file} -> {new_file}")
        tree.write(document_path, encoding='utf-8', xml_declaration=True)

compress_suffix = ['jpg', 'jpeg', 'png', 'tmp']
valid_suffix = ['jpg', 'jpeg', 'png']

rels_path = os.path.join(output_dir, 'word', '_rels', 'document.xml.rels')
image_references = {}
if os.path.exists(rels_path):
    # 解析 document.xml.rels 文件
    rels_tree = ET.parse(rels_path)
    rels_root = rels_tree.getroot()
    rels_namespaces = {'r': 'http://schemas.openxmlformats.org/package/2006/relationships'}
    
    # 遍历 Relationship 节点，找到图片引用
    for rel in rels_root.findall('.//r:Relationship', rels_namespaces):
        rId = rel.attrib.get('Id')  # 获取 rId
        target = rel.attrib.get('Target')  # 获取图片路径
        if target and 'media/' in target:  # 确保是图片文件
            image_references[rId] = os.path.basename(target)

print(f"找到 rId 与图片文件的映射: {image_references}")

# 主逻辑
media_dir = os.path.join(output_dir, 'word', 'media')
rels_path = os.path.join(output_dir, 'word', '_rels', 'document.xml.rels')
document_path = os.path.join(output_dir, 'word', 'document.xml')
if os.path.exists(media_dir):
    all_images = os.listdir(media_dir)
    for file_name in all_images:
        file_ext = file_name.split('.')[-1].lower()
        file_path = os.path.join(media_dir, file_name)

        # 判断图片是否被引用
        if file_name not in image_references.values():
            if delete_unused:
                print(f"未使用的图片: {file_name}，已删除")
                os.remove(file_path)
            continue
        # 如果是 tmp 文件，尝试识别其实际格式
        if file_ext == 'tmp':
            try:
                with Image.open(file_path) as im:
                    file_ext = im.format.lower()  # 获取实际格式
            except Exception as e:
                print(f"无法识别文件格式: {file_name}, 错误: {e}")
                continue

        if file_ext in ['svg', 'emf']:
            # 转换文件为 JPEG 或 PNG
            new_file_path = convert_to_jpeg_or_png(file_path)
            # 现在后缀应该是 jpeg
            compress_image(new_file_path, target_size=64, step=10, quality=85)
            if new_file_path:
                new_file_name = os.path.basename(new_file_path)
                # 更新 rels 文件
                update_rels_file(rels_path, file_name, new_file_name)
                # 更新 document.xml 文件
                update_document_xml(document_path, file_name, new_file_name)
                # 删除原始文件
                os.remove(file_path)
            continue
        if file_ext in ["png"]:
            compress_image(file_path, target_size=64, step=10, quality=85)
        elif file_ext in ["jpg", "jpeg"]:
            compress_image(file_path, target_size=64, step=10, quality=90)
        else:
            print(f"不支持的文件格式: {file_name} 后缀 {file_ext}，已跳过")
            continue

# 重新打包为新的 docx 文件
zip_file = zipfile.ZipFile(new_docx_name, 'w')
for path, dirs, filenames in os.walk(output_dir):
    fpath = os.path.relpath(path, output_dir)
    for filename in filenames:
        zip_file.write(os.path.join(path, filename), os.path.join(fpath, filename))
zip_file.close()
print(f"所有图片已转换为 JPEG，新文件已保存为 {new_docx_name}")
```
