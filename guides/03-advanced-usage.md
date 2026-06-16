# Scrapling 高级用法

## 异步处理

### 使用 AsyncFetcher

```python
import asyncio
from scrapling import AsyncFetcher

async def fetch_page(url):
    fetcher = AsyncFetcher()
    page = await fetcher.fetch(url)
    return page

async def main():
    urls = [
        'https://example.com',
        'https://example.org',
        'https://example.net'
    ]
    
    tasks = [fetch_page(url) for url in urls]
    pages = await asyncio.gather(*tasks)
    
    for page in pages:
        print(f'标题: {page.css("title").text()}')

# 运行异步代码
asyncio.run(main())
```

### 批量处理

```python
import asyncio
from scrapling import AsyncFetcher

async def process_urls(urls):
    fetcher = AsyncFetcher()
    results = []
    
    for url in urls:
        try:
            page = await fetcher.fetch(url)
            results.append({
                'url': url,
                'title': page.css('title').text(),
                'status': page.status
            })
        except Exception as e:
            results.append({
                'url': url,
                'error': str(e)
            })
    
    return results

# 使用
urls = ['https://example.com', 'https://example.org']
results = asyncio.run(process_urls(urls))
```

## 代理配置

### 基本代理

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch(
    'https://example.com',
    proxies={
        'http': 'http://proxy:port',
        'https': 'http://proxy:port'
    }
)
```

### 认证代理

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch(
    'https://example.com',
    proxies={
        'http': 'http://user:pass@proxy:port',
        'https': 'http://user:pass@proxy:port'
    }
)
```

### 代理轮换

```python
import random
from scrapling import Fetcher

proxies = [
    'http://proxy1:port',
    'http://proxy2:port',
    'http://proxy3:port'
]

fetcher = Fetcher()
for url in urls:
    proxy = random.choice(proxies)
    page = fetcher.fetch(url, proxies={'http': proxy, 'https': proxy})
```

## 自定义请求头

```python
from scrapling import Fetcher

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    'Accept-Language': 'en-US,en;q=0.5',
    'Accept-Encoding': 'gzip, deflate',
    'Connection': 'keep-alive',
}

fetcher = Fetcher()
page = fetcher.fetch('https://example.com', headers=headers)
```

## 处理 Cookies

### 自动 Cookies

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 获取 cookies
cookies = page.cookies
for cookie in cookies:
    print(f'{cookie.name}: {cookie.value}')
```

### 自定义 Cookies

```python
from scrapling import Fetcher

cookies = {
    'session_id': 'abc123',
    'user_pref': 'dark_mode'
}

fetcher = Fetcher()
page = fetcher.fetch('https://example.com', cookies=cookies)
```

## 表单提交

### GET 请求

```python
from scrapling import Fetcher

params = {
    'q': 'search term',
    'page': '1'
}

fetcher = Fetcher()
page = fetcher.fetch('https://example.com/search', params=params)
```

### POST 请求

```python
from scrapling import Fetcher

data = {
    'username': 'user',
    'password': 'pass'
}

fetcher = Fetcher()
page = fetcher.fetch('https://example.com/login', method='POST', data=data)
```

### JSON 请求

```python
from scrapling import Fetcher

json_data = {
    'name': 'John',
    'email': 'john@example.com'
}

fetcher = Fetcher()
page = fetcher.fetch(
    'https://example.com/api',
    method='POST',
    json=json_data
)
```

## 超时控制

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com', timeout=30)  # 30秒超时
```

## 重试机制

```python
from scrapling import Fetcher

fetcher = Fetcher()

for attempt in range(3):
    try:
        page = fetcher.fetch('https://example.com')
        if page.ok:
            break
    except Exception as e:
        if attempt == 2:
            raise
        print(f'重试 {attempt + 1}/3: {e}')
```

## 爬虫（Spider）

### 基本爬虫

```python
from scrapling import Fetcher, Spider

class MySpider(Spider):
    def parse(self, response):
        # 解析页面
        title = response.css('title').text()
        print(f'标题: {title}')
        
        # 提取链接
        links = response.css('a::attr(href)').getall()
        for link in links:
            yield response.follow(link, self.parse)

# 使用
fetcher = Fetcher()
spider = MySpider()
spider.start(['https://example.com'], fetcher)
```

### 递归爬虫

```python
from scrapling import Fetcher, Spider

class RecursiveSpider(Spider):
    def __init__(self, max_depth=3):
        self.max_depth = max_depth
    
    def parse(self, response, depth=0):
        if depth >= self.max_depth:
            return
        
        # 处理当前页面
        title = response.css('title').text()
        print(f'深度 {depth}: {title}')
        
        # 递归爬取链接
        links = response.css('a::attr(href)').getall()
        for link in links:
            yield response.follow(
                link,
                self.parse,
                meta={'depth': depth + 1}
            )
```

## 性能优化

### 连接池

```python
from scrapling import Fetcher

# 复用连接
fetcher = Fetcher()

for url in urls:
    page = fetcher.fetch(url)
    # 处理页面
```

### 内存管理

```python
from scrapling import Fetcher

fetcher = Fetcher()

for url in urls:
    page = fetcher.fetch(url)
    # 处理页面数据
    data = extract_data(page)
    save_data(data)
    
    # 及时释放内存
    del page
```

### 并发控制

```python
import asyncio
from scrapling import AsyncFetcher

async def limited_fetch(urls, max_concurrent=5):
    fetcher = AsyncFetcher()
    semaphore = asyncio.Semaphore(max_concurrent)
    
    async def fetch_with_limit(url):
        async with semaphore:
            return await fetcher.fetch(url)
    
    tasks = [fetch_with_limit(url) for url in urls]
    return await asyncio.gather(*tasks)
```

## 数据提取技巧

### XPath 选择器

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 使用 XPath
titles = page.xpath('//h1/text()')
links = page.xpath('//a/@href')
```

### 正则表达式

```python
import re
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 提取邮箱
emails = re.findall(r'[\w.+-]+@[\w-]+\.[\w.]+', page.body)

# 提取电话
phones = re.findall(r'\d{3}-\d{3}-\d{4}', page.body)
```

### JSON 提取

```python
import json
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com/api/data')

# 解析 JSON
data = json.loads(page.body)
print(data)
```

## 错误处理最佳实践

```python
from scrapling import Fetcher
from scrapling.exceptions import (
    FetchError,
    TimeoutError,
    ConnectionError
)

fetcher = Fetcher()

try:
    page = fetcher.fetch('https://example.com')
    if page.ok:
        # 处理成功响应
        process_page(page)
    else:
        # 处理错误响应
        handle_error(page.status)
except TimeoutError:
    print('请求超时')
except ConnectionError:
    print('连接失败')
except FetchError as e:
    print(f'抓取错误: {e}')
except Exception as e:
    print(f'未知错误: {e}')
```

## 下一步

- 查看 [故障排查](../troubleshooting.md) 解决常见问题
- 访问 [官方文档](https://scrapling.readthedocs.io) 获取完整参考
- 参与 [GitHub 讨论](https://github.com/D4Vinci/Scrapling/discussions)
