有三种方式：

1. lxml.etree 这个是一个很强大的做爬虫用的HTML解析库，当然也能解析XML，对XPath的支持完整。
> 1. 安装 lxml 库
step
> 2. from lxml import etree  # etree全称：ElementTree 元素树
> 3. selector = etree.HTML(网页源代码)
> 4. selector.xpath(一段神奇的符号)
```python
# 获取Xcode项目plist中的版本号
selector.xpath('//dict/key[.=\'CFBundleVersion\']/following-sibling::string')[0].text
```
2. xml.etree.ElementTree 轻量级DOM，支持少量XPath
3. xml.sax 占用内存少，使用起来复杂
4. xml.dom.minidom 整个xml都解析成DOM保存在内存中，占用内存大