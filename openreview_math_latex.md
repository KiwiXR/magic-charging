# 将markdown中的latex math转为openreview能接受的math code

## Info Bank

OpenReview相关信息
+ OpenReview采用 [MathJax](https://docs.mathjax.org/en/latest/index.html) 渲染latex公式 ( [ref](https://docs.openreview.net/reference/openreview-tex/openreview-tex-support) )
+ OpenReview支持的Markdown语法为 [CommonMark](https://commonmark.org/help/) ( [ref](https://docs.openreview.net/how-to-guides/submissions-comments-reviews-and-decisions/how-to-add-formatting-to-reviews-or-comments) )，其 [spec](https://spec.commonmark.org/0.29/)，一个在线试验 [地址](https://spec.commonmark.org/dingus/)

一些现有的经验
> https://kingsleyxie.cn/markdown-mathjax-conflicts-and-several-solutions/
> https://github.com/orgs/community/discussions/65772
> https://liam.page/2015/09/09/fix-conflict-between-mathjax-and-markdown/

## Tool

+ TODOs
+ [ ] `{}` 外的字符不用额外转义，进一步减少空间
+ [ ] `$$` 内的情况可能和 `$$$$` 内不太一样，有空研究一下

> Update 0805: File support tested.

保存下方代码为`openreview_latex.py`

交互式使用：
```shell
python openreview_latex.py
```

从文件/字符串读取（untested）：分别使用`-f FILE`和`-s STRING`参数
```shell
python openreview_latex.py -f test.md
```

```python
import re
import argparse

parser = argparse.ArgumentParser(description="Convert Latex Math to Openreview Compatible, default mode is iterative string")
parser.add_argument('--file', '-f', type=str, default=None, help="file containing latex math code")
parser.add_argument('--string', '-s', type=str, default=None, help="a single string containing latex math code wrapped in quotes")
args = parser.parse_args()

def solve(text):
    """
    _ -> \_
    \_ -> \\_
    \, -> \\,
    \[non-alphabet] -> \\[non-alphabet]
    """
    text = re.sub(r"\\_", r"##ESCAPED##", text)  # \_ -> ##ESCAPED##
    # return text

    pattern = r"\\[^a-zA-Z]"
    text = re.sub(pattern, lambda match: "\\" + match.group(0), text) # \, -> \\,
    # return text

    text = re.sub(r"_", r"\_", text)  # _ -> \_
    # return text
    
    text = re.sub(r"##ESCAPED##", r"\\\\_", text)  # ##ESCAPED## -> \\_
    return text

def replace_in_math_mode(text):
    def replace_escapes(match):
        # Get the matched text (inside the math mode delimiters)
        content = match.group(2)
        
        content = solve(content)

        return f'{match.group(1)}{content}{match.group(1)}'
    
    # Replace content between $ and $ or $$ and $$ with the transformed content
    text = re.sub(r'(\${1,2})([^\$]+)\1', replace_escapes, text)
    
    return text

if args.file:
    with open(args.file, "r", encoding="utf-8") as f:
        math_output = []
        math_latex = f.readlines()
        for line in math_latex:
            # print(math_latex)
            math_output.append(replace_in_math_mode(line))
    with open(args.file+'.out', 'w', encoding='utf-8') as f:
        f.writelines(math_output)
elif args.string:
    print("[OUTPUTS]:\n")
    print(solve(args.string))
    print("\n")
else:
    while True:
        math_latex = input("Type in or paste latex math code: (Ctrl+C to exit)\n")
        print("[OUTPUTS]:\n")
        print(solve(math_latex))
        print()
```
