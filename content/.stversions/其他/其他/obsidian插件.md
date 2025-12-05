# Latex Suite

增添配置：

```
{trigger: "^", replacement: "^{$0}$1", options: "mA"},
{trigger: "@n", replacement: "\\nabla", options: "mA"},
{trigger: "@p", replacement: "\\partial", options: "mA"},
{trigger: "frac", replacement: "\\frac{$1}{$2}$3", options: "mA"},
{trigger: "()", replacement: "\\left($0\\right", options: "mA"},
{trigger: "||", replacement: "\\left|$0\\right|", options: "mA"},
{trigger: "@{", replacement: "\\\{$0\\\}", options: "mA"},
{trigger: "bb", replacement: "\\mathbb{$0}$1", options: "mA"},
{trigger: "cal", replacement: "\\mathcal{$0}$1", options: "mA"},
{trigger: "@ra", replacement: "\\rightarrow ", options: "mA"},
{trigger: "@RA", replacement: "\\Rightarrow ", options: "mA"},
{trigger: "vert", replacement: "\\lVert $0\\lVert", options: "mA"},
```

修改配置：

```
{trigger: "align", replacement: "\\begin{aligned}\n$0\n\\end{aligned}", options: "mA"},
{trigger: "cdot", replacement: "\\cdot ", options: "mA"},
```

# wakatime

