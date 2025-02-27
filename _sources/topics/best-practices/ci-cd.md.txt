# CI/CD 实践

本文将介绍如何使用 GitHub Actions 实现持续集成和持续部署（CI/CD）。

## GitHub Actions 基础

GitHub Actions 是 GitHub 提供的自动化工作流工具，可以自动化软件开发的各个环节。

### 基本概念

- **Workflow**：工作流程，由一个或多个 jobs 组成
- **Job**：作业，由多个 steps 组成
- **Step**：步骤，具体的执行任务
- **Action**：可重用的工作单元

## 文档自动化部署

### 配置 ReadTheDocs

创建 `.readthedocs.yaml`：

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

### 文档构建工作流

创建 `.github/workflows/docs.yml`：

```yaml
name: Documentation

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  docs:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.11'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r docs/requirements.txt
    
    - name: Build documentation
      run: |
        cd docs
        make html
    
    - name: Deploy to GitHub Pages
      if: github.ref == 'refs/heads/main'
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./docs/_build/html
```

## 代码质量检查

### 配置 pre-commit

创建 `.pre-commit-config.yaml`：

```yaml
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
    -   id: trailing-whitespace
    -   id: end-of-file-fixer
    -   id: check-yaml
    -   id: check-added-large-files

-   repo: https://github.com/psf/black
    rev: 23.3.0
    hooks:
    -   id: black

-   repo: https://github.com/pycqa/flake8
    rev: 6.0.0
    hooks:
    -   id: flake8
```

### 代码检查工作流

创建 `.github/workflows/code-quality.yml`：

```yaml
name: Code Quality

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.11'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install flake8 black pytest
    
    - name: Run black
      run: black . --check
    
    - name: Run flake8
      run: flake8 .
    
    - name: Run tests
      run: pytest
```

## 自动发布

### 版本发布工作流

创建 `.github/workflows/release.yml`：

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - name: Create Release
      id: create_release
      uses: actions/create-release@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        tag_name: ${{ github.ref }}
        release_name: Release ${{ github.ref }}
        draft: false
        prerelease: false
```

## 最佳实践

1. **分支保护**：配置分支保护规则，要求通过 CI 检查才能合并
2. **自动化测试**：包含单元测试、集成测试和文档测试
3. **代码审查**：使用 Pull Request 进行代码审查
4. **版本控制**：使用语义化版本号
5. **环境隔离**：使用环境变量和 secrets 管理敏感信息

## 工作流示例

### 完整的 CI/CD 流程

1. 开发者提交代码到特性分支
2. 触发代码质量检查和测试
3. 创建 Pull Request
4. 自动运行 CI 检查
5. 代码审查
6. 合并到主分支
7. 自动部署文档
8. 发布新版本

## 安全最佳实践

1. 使用 `secrets` 存储敏感信息
2. 限制 workflow 权限
3. 定期更新依赖
4. 使用固定版本的 actions
5. 审查第三方 actions

## 下一步

- 配置更多自动化工作流
- 添加性能测试
- 实现自动化部署到生产环境
- 配置监控和告警系统
