# Scrapling 故障排查

## 安装问题

### pip install 失败

**症状**: `pip install scrapling` 报错

**解决**:
```bash
# 升级 pip
pip install --upgrade pip

# 使用 Python 3.8+
python --version

# 如果使用虚拟环境
python -m venv venv
source venv/bin/activate  # Linux/macOS
# 或
venv\Scripts\activate  # Windows

pip install scrapling
```

### 依赖安装失败

**症状**: `pip install "scrapling[fetchers]"` 报错

**解决**:
```bash
# 安装系统依赖（Ubuntu/Debian）
sudo apt-get update
sudo apt-get install -y python3-dev build-essential

# 安装系统依赖（macOS）
xcode-select --install

# 重新安装
pip install "scrapling[fetchers]"
scrapling install
```

## 使用问题

### 403/429 被阻止

**症状**: 请求返回 403 Forbidden 或 429 Too Many Requests

**解决**:
```python
# 使用 StealthyFetcher 绕过反爬虫
from scrapling import StealthyFetcher

fetcher = StealthyFetcher()
page = fetcher.fetch("https://example.com")
```

### Cloudflare 保护

**症状**: 遇到 Cloudflare 验证页面

**解决**:
```python
# 使用 StealthyFetcher 或 cloudscraper
from scrapling import StealthyFetcher

fetcher = StealthyFetcher()
page = fetcher.fetch("https://example.com")
```

### JavaScript 渲染问题

**症状**: 页面内容为空或不完整

**解决**:
```python
# 使用 DynamicFetcher 处理 JavaScript 渲染的页面
from scrapling import DynamicFetcher

fetcher = DynamicFetcher()
page = fetcher.fetch("https://example.com")
```

### 网站结构变化

**症状**: 选择器失效，无法提取数据

**解决**:
```python
# 使用 adaptive=True 自动适应网站变化
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch("https://example.com", adaptive=True)
```

## 网络问题

### 代理配置

**症状**: 需要通过代理访问网络

**解决**:
```python
# 配置代理
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch(
    "https://example.com",
    proxies={
        "http": "http://proxy:port",
        "https": "http://proxy:port"
    }
)
```

### 超时问题

**症状**: 请求超时

**解决**:
```python
# 增加超时时间
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch("https://example.com", timeout=30)
```

## 高级问题

### Captcha 验证

**症状**: 遇到 Captcha 验证

**说明**: Scrapling 无法绕过 Captcha，建议：
1. 使用官方 API（如果可用）
2. 跳过该页面
3. 使用第三方 Captcha 解决服务

### 需要认证

**症状**: 页面需要登录才能访问

**说明**: Scrapling 不建议绕过认证，建议：
1. 使用官方 API
2. 使用浏览器自动化工具（如 Playwright）手动登录
3. 使用 API 逆向工程方法

## 性能问题

### 内存占用过高

**症状**: 处理大量页面时内存占用过高

**解决**:
```python
# 使用流式处理
from scrapling import Fetcher

fetcher = Fetcher()
for url in urls:
    page = fetcher.fetch(url)
    # 处理页面
    # 及时释放资源
    del page
```

### 速度慢

**症状**: 抓取速度慢

**解决**:
```python
# 使用异步处理
import asyncio
from scrapling import AsyncFetcher

async def fetch_all(urls):
    fetcher = AsyncFetcher()
    tasks = [fetcher.fetch(url) for url in urls]
    return await asyncio.gather(*tasks)

# 批量处理
pages = asyncio.run(fetch_all(urls))
```

## 获取帮助

如果以上方法都无法解决问题：

1. 查看 [官方文档](https://scrapling.readthedocs.io)
2. 搜索 [GitHub Issues](https://github.com/D4Vinci/Scrapling/issues)
3. 提交新的 Issue，包含：
   - 错误信息
   - 代码示例
   - Python 版本
   - 操作系统信息
