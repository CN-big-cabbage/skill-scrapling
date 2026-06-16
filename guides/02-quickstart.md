# Scrapling 快速开始

## 基本用法

### 发送第一个请求

```python
from scrapling import Fetcher

# 创建 Fetcher 实例
fetcher = Fetcher()

# 获取页面
page = fetcher.fetch('https://example.com')

# 获取页面标题
title = page.css('title').text()
print(f'页面标题: {title}')
```

### 提取链接

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 提取所有链接
links = page.css('a')
for link in links:
    href = link.attrib.get('href', '')
    text = link.text()
    print(f'{text}: {href}')
```

### 提取文本内容

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 提取段落文本
paragraphs = page.css('p')
for p in paragraphs:
    print(p.text())
```

## 使用不同的 Fetcher

### HTTP 请求（默认）

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')
```

### 浏览器自动化（处理 JavaScript）

```python
from scrapling import DynamicFetcher

fetcher = DynamicFetcher()
page = fetcher.fetch('https://example.com')
```

### 隐身模式（绕过反爬虫）

```python
from scrapling import StealthyFetcher

fetcher = StealthyFetcher()
page = fetcher.fetch('https://example.com')
```

## 处理响应

### 检查状态码

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

print(f'状态码: {page.status}')
print(f'是否成功: {page.ok}')
```

### 获取响应头

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 获取所有响应头
headers = page.headers
print(f'Content-Type: {headers.get("Content-Type")}')
```

### 获取页面内容

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 获取 HTML 内容
html = page.body
print(html[:500])  # 打印前 500 个字符
```

## 使用 CSS 选择器

### 基本选择器

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 通过标签名
titles = page.css('h1')

# 通过类名
items = page.css('.item')

# 通过 ID
header = page.css('#header')

# 通过属性
links = page.css('a[href]')
```

### 组合选择器

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 后代选择器
items = page.css('div.item')

# 子元素选择器
items = page.css('div > .item')

# 相邻兄弟选择器
items = page.css('h1 + p')
```

## 提取属性

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com')

# 提取链接
links = page.css('a')
for link in links:
    href = link.attrib.get('href', '')
    title = link.attrib.get('title', '')
    print(f'链接: {href}, 标题: {title}')

# 提取图片
images = page.css('img')
for img in images:
    src = img.attrib.get('src', '')
    alt = img.attrib.get('alt', '')
    print(f'图片: {src}, 描述: {alt}')
```

## 错误处理

```python
from scrapling import Fetcher

fetcher = Fetcher()

try:
    page = fetcher.fetch('https://example.com')
    if page.ok:
        print('请求成功')
        print(f'状态码: {page.status}')
    else:
        print(f'请求失败: {page.status}')
except Exception as e:
    print(f'发生错误: {e}')
```

## 实际示例

### 抓取新闻标题

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://news.ycombinator.com')

# 提取新闻标题
titles = page.css('.titleline > a')
for i, title in enumerate(titles[:10], 1):
    print(f'{i}. {title.text()}')
```

### 抓取产品信息

```python
from scrapling import Fetcher

fetcher = Fetcher()
page = fetcher.fetch('https://example.com/products')

# 提取产品信息
products = page.css('.product')
for product in products:
    name = product.css('.name').text()
    price = product.css('.price').text()
    print(f'产品: {name}, 价格: {price}')
```

## 下一步

- 学习 [高级用法](03-advanced-usage.md) 了解更多功能
- 查看 [故障排查](../troubleshooting.md) 解决常见问题
- 访问 [官方文档](https://scrapling.readthedocs.io) 获取完整参考
