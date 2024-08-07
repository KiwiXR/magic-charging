# 将markdown中的latex math转为openreview能接受的math code

**注意，本实现暂不考虑Unicode中的punctuation-character**

+ TODOs
+ [x] `{}` 外的字符不用额外转义，进一步减少空间
+ [ ] `$$` 内的情况可能和 `$$$$` 内不太一样
+ [ ] `*` 可能也有问题

> Update 0805: File support tested.

保存下方代码为`openreview_latex.py`

交互式使用：
```shell
python openreview_latex.py
```

开启极限模式（实验）可大幅减少不必要转义字符数：
```shell
python openreview_latex.py -x
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
parser.add_argument('--extreme-mode', '-x', action='store_true', default=False, help="reduce to extremes the number of changes (experimental)")
args = parser.parse_args()

def is_punctuation_character(ch):
    ascii_punctuation_character = (
        '!', '"', '#', '$', '%', '&', "'", '(', ')', '*',
        '+', ',', '-', '.', '/', ':', ';', '<', '=', '>',
        '?', '@', '[', '\\', ']', '^', '_', '`', '{', '|',
        '}', '~'  # 32 in total
    )
    return ch in ascii_punctuation_character

def extreme_substitute(text):
    res = ""
    last_pos = text.rfind('_')
    # no '_' found
    if last_pos < 0:
        return text
    text, rest = text[:last_pos], text[last_pos:]
    text_len = len(text)
    for i in range(text_len):
        cur = text[i]
        if cur != '_':
            res += cur
            continue
        # cur == '_'
        pre = text[i-1] if i > 0 else ' '
        nxt = text[i+1] if i < text_len-1 else ' '
        if is_punctuation_character(pre) and nxt != ' ':
            res += '\\_'
        elif pre == ' ' and nxt != ' ':
            res += '\\_'
        else:
            res += '_'
    return res + rest

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

    # HANDLE '_'
    if args.extreme_mode:
        text = extreme_substitute(text)
    else:
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

## Info Bank

OpenReview相关信息
+ OpenReview采用 [MathJax](https://docs.mathjax.org/en/latest/index.html) 渲染latex公式 ( [ref](https://docs.openreview.net/reference/openreview-tex/openreview-tex-support) )
+ OpenReview支持的Markdown语法为 [CommonMark](https://commonmark.org/help/) ( [ref](https://docs.openreview.net/how-to-guides/submissions-comments-reviews-and-decisions/how-to-add-formatting-to-reviews-or-comments) )，其 [spec](https://spec.commonmark.org/0.29/)，一个在线试验 [地址](https://spec.commonmark.org/dingus/)

Thoughts
+ `_` 的一种处理办法为，迫使公式内不出现使其配对的 [left-flanking delimiter run](https://spec.commonmark.org/0.29/#left-flanking-delimiter-run) 和 [right-flanking delimiter run](https://spec.commonmark.org/0.29/#right-flanking-delimiter-run)
    > 2\. A single _ character can open emphasis iff it is part of a left-flanking delimiter run and either (a) not part of a right-flanking delimiter run or (b) part of a right-flanking delimiter run preceded by punctuation.
    > 
    > 4\. A single _ character can close emphasis iff it is part of a right-flanking delimiter run and either (a) not part of a left-flanking delimiter run or (b) part of a left-flanking delimiter run followed by punctuation.
    >
    > 9\. Emphasis begins with a delimiter that can open emphasis and ends with a delimiter that can close emphasis, and that uses the same character (_ or *) as the opening delimiter. The opening and closing delimiters must belong to separate delimiter runs. If one of the delimiters can both open and close emphasis, then the sum of the lengths of the delimiter runs containing the opening and closing delimiters must not be a multiple of 3 unless both lengths are multiples of 3.
+ left-flanking delimiter run
  + 后面不是空格
  + 以下二选一
    + 后面不是[punctuation-character](https://spec.commonmark.org/0.29/#punctuation-character)
    + 后面是[punctuation-character](https://spec.commonmark.org/0.29/#punctuation-character)，而前面是[punctuation-character](https://spec.commonmark.org/0.29/#punctuation-character)或空格
+ right-flanking delimiter run
  + 前面不是空格
  + 以下二选一
    + 前面不是[punctuation-character](https://spec.commonmark.org/0.29/#punctuation-character)
    + 前面是[punctuation-character](https://spec.commonmark.org/0.29/#punctuation-character)，而后面是[punctuation-character](https://spec.commonmark.org/0.29/#punctuation-character)或空格

我们考虑仅通过加空格来解决问题（可以的话不引入额外的转义），使所有 `_` （除了最后一个）都不满足left-flanking delimiter run的条件

首先，我们考虑 `_` 附近的字符，则有以下情况（`a` 代表常规字符，`!` 代表[punctuation-character](https://spec.commonmark.org/0.29/#punctuation-character), `S` 代表空格）：
```text
a_a  // 保持不变，CommonMark视其为连字符
a_!  // 保持不变，不满足left-flanking delimiter run定义
!_a  // 后面加空格或者前面加转义
!_!  // 后面加空格或者前面加转义
a_S  // 保持不变，后面有空格
!_S  // 保持不变，后面有空格
S_a  // 后面加空格或者前面加转义
S_!  // 后面加空格或者前面加转义
S_S  // 保持不变，后面有空格
```
其次，最后一个 '_' 可以不予考虑

一些现有的经验
> + https://kingsleyxie.cn/markdown-mathjax-conflicts-and-several-solutions/
> + https://github.com/orgs/community/discussions/65772
> + https://liam.page/2015/09/09/fix-conflict-between-mathjax-and-markdown/
