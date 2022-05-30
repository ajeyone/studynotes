## import
```py
import smtplib
from email.mime.text import MIMEText
from email.header import Header
```

## 配置服务器、发送方、接收方
```python
mail_host="smtp.163.com"
mail_user="ajeyone@163.com"
mail_pass="xxxxx"
sender = 'ajeyone@163.com'
receivers = [sender]
```

## 设置HTML内容和主题

```python
version_string = '1.2.1'
mail_msg = """
<p>TextHere 测试版已发布</p>
<p>版本号：""" + version_string + """</p>
<p>使用 Safari 打开：<a href="https://www.pgyer.com/1rRc">下载链接</a></p>
<p>iPhone 扫描二维码直接安装</p>
<p><img src="https://www.pgyer.com/app/qrcode/1rRc" /></p>
<p></p>
<p>release note:</p>
<p>"""+release_note+"""</p>
"""
message = MIMEText(mail_msg, 'html', 'utf-8')
subject = 'TextHere %s ad-hoc 测试版发布了！' % version_string
message['Subject'] = Header(subject, 'utf-8')
```

## 发送
```python
try:
    smtpObj = smtplib.SMTP() 
    smtpObj.connect(mail_host, 25)
    // 以上两句也可以写成 smtpObj = smtplib.SMTP(mail_host, 25) 
    smtpObj.starttls() // 根据服务器的配置，这里可以启用加密
    smtpObj.login(mail_user,mail_pass)  
    smtpObj.sendmail(sender, receivers, message.as_string())
    print("邮件发送成功")
except smtplib.SMTPException:
    print("Error: 无法发送邮件")
```

## 常见问题

### SMTP AUTH extension not supported by server

以下内容转自 https://blog.csdn.net/zyh960/article/details/118156944

```python
import smtplib
s = smtplib.SMTP('邮箱的地址',25)#25是端口号
```

这里有一点是需要注意的是，就是如果是采用ssl协议的，那么我们这里就要进行微调。如果是使用587/467这些端口那么就要使用SSL协议
```python
s = smtplib.SMTP_SSL('邮箱的地址', 587)
s.ehlo()
```

如果输出内容是
```
(250, 'relay02.testdomain.com\nPIPELINING\nSIZE 34603008\nETRN\nSTARTTLS\nENHANCEDSTATUSCODES\n8BITMIME\nDSN')
```

那么说明端口是没问题的
我们再去测试tls协议

```python
s.starttls()
```

如果输出内容是
```
(220, '2.0.0 Ready to start TLS')
```

说明是可以使用TLS协议的，既然这样，我们再去使用 `login()` 登入

```python
s.login('你的邮箱','密码')
```

如果报错的内容是

```python
smtplib.SMTPException: SMTP AUTH extension not supported by server.
```

说明是login这一块出了问题，我们可以直接不要login这个方法，直接就是发送邮箱试试：

```python
s.sendmail('你的邮箱',['收件人邮箱'],'test')
```


然后就很奇妙的成功了。
