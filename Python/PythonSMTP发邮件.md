## import
```py
import smtplib
from email.mime.text import MIMEText
from email.header import Header
```

## 配置服务器、发送方、接收方
```python
mail_host="smtp.v1.cn"
mail_user="wangjunjie@v1.cn"
mail_pass="xxxxx"
sender = 'wangjunjie@v1.cn'
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
    smtpObj.login(mail_user,mail_pass)  
    smtpObj.sendmail(sender, receivers, message.as_string())
    print("邮件发送成功")
except smtplib.SMTPException:
    print("Error: 无法发送邮件")
```