# Scrapling 安装指南

## 系统要求

- Python 3.8 或更高版本
- pip 包管理器
- 操作系统：Linux、macOS 或 Windows

## 安装方式

### 基础安装（仅解析器）

```bash
pip install scrapling
```

### 推荐安装（包含 HTTP 和浏览器自动化）

```bash
pip install "scrapling[fetchers]"
scrapling install
```

### 完整安装（包含 CLI 工具）

```bash
pip install "scrapling[shell]"
```

### AI 集成安装（包含 MCP 服务器）

```bash
pip install "scrapling[ai]"
```

## 虚拟环境安装（推荐）

### 创建虚拟环境

```bash
# 创建虚拟环境
python -m venv scrapling-env

# 激活虚拟环境
# Linux/macOS
source scrapling-env/bin/activate

# Windows
scrapling-env\Scripts\activate
```

### 在虚拟环境中安装

```bash
pip install "scrapling[fetchers]"
scrapling install
```

## 系统依赖安装

### Ubuntu/Debian

```bash
sudo apt-get update
sudo apt-get install -y python3-dev build-essential
```

### CentOS/RHEL

```bash
sudo yum groupinstall -y "Development Tools"
sudo yum install -y python3-devel
```

### macOS

```bash
xcode-select --install
```

## 验证安装

```bash
# 检查 Scrapling 版本
python -c "import scrapling; print(scrapling.__version__)"

# 测试基本功能
python -c "
from scrapling import Fetcher
fetcher = Fetcher()
page = fetcher.fetch('https://example.com')
print(f'Title: {page.css(\"title\").text()}')
"
```

## 升级 Scrapling

```bash
pip install --upgrade scrapling
pip install --upgrade "scrapling[fetchers]"
scrapling install
```

## 卸载 Scrapling

```bash
pip uninstall scrapling
```

## 常见安装问题

### 权限问题

```bash
# 使用 --user 安装
pip install --user scrapling

# 或使用虚拟环境（推荐）
python -m venv venv
source venv/bin/activate
pip install scrapling
```

### 依赖冲突

```bash
# 创建新的虚拟环境
python -m venv fresh-env
source fresh-env/bin/activate
pip install scrapling
```

### 网络问题

```bash
# 使用国内镜像源
pip install scrapling -i https://pypi.tuna.tsinghua.edu.cn/simple

# 或使用阿里云镜像
pip install scrapling -i https://mirrors.aliyun.com/pypi/simple/
```

## 下一步

安装完成后，请参阅 [快速开始指南](02-quickstart.md) 学习基本用法。
