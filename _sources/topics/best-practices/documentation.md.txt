# 文档自动化

本文将介绍如何使用 Sphinx 和 ReadTheDocs 构建自动化的文档系统。

## Sphinx 配置

### 安装依赖

```bash
pip install -r docs/requirements.txt
```

主要依赖包括：
- `sphinx`: 文档生成工具
- `sphinx-rtd-theme`: ReadTheDocs 主题
- `myst-parser`: Markdown 支持
- `sphinxcontrib-mermaid`: 图表支持

### 配置文件说明

`conf.py` 是 Sphinx 的主要配置文件：

```python
# 项目信息
project = 'Project Name'
copyright = '2024, Author'
author = 'Author'

# 扩展
extensions = [
    'sphinx.ext.duration',
    'sphinx.ext.doctest',
    'sphinx.ext.autodoc',
    'sphinx.ext.autosummary',
    'sphinx.ext.intersphinx',
    'myst_parser',
    'sphinxcontrib.mermaid',
]

# 主题设置
html_theme = 'sphinx_rtd_theme'
```

## ReadTheDocs 配置

### .readthedocs.yaml

在项目根目录创建 `.readthedocs.yaml`：

```yaml
version: 2

build:
  os: ubuntu-22.04
  tools:
    python: "3.11"

sphinx:
  configuration: docs/conf.py

python:
  install:
    - requirements: docs/requirements.txt
```

### 集成步骤

1. 在 [ReadTheDocs](https://readthedocs.org/) 注册账号
2. 导入 GitHub 仓库
3. 配置构建设置
4. 触发首次构建

## 文档编写指南

### 目录结构

```
docs/
├── _static/          # 静态文件
├── _templates/       # 模板文件
├── topics/          # 主题文档
├── conf.py          # Sphinx 配置
├── index.md         # 首页
└── requirements.txt # 依赖文件
```

### Markdown 语法扩展

MyST 解析器支持多种扩展语法：

```markdown
# 警告框
:::warning
这是一个警告信息
:::

# 代码块
```python
def hello():
    print("Hello, World!")
```

# 数学公式
$$
E = mc^2
$$

# Mermaid 图表
```{mermaid}
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```
```

## 自动化部署

### GitHub Actions

创建 `.github/workflows/docs.yml`：

```yaml
name: docs

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: '3.11'
      - name: Install dependencies
        run: |
          pip install -r docs/requirements.txt
      - name: Build docs
        run: |
          cd docs
          make html
```

## 最佳实践

1. **文档即代码**：将文档视为代码的一部分
2. **持续更新**：随代码变化及时更新文档
3. **版本控制**：使用 Git 管理文档版本
4. **自动构建**：配置自动化工作流
5. **及时反馈**：通过 CI/CD 确保文档质量

## 下一步

- [CI/CD 实践](ci-cd.md)：了解如何配置完整的 CI/CD 流程
