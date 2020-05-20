```python
import win32con
import win32clipboard

def getClipboardText():
    win32clipboard.OpenClipboard()
    text = win32clipboard.GetClipboardData(win32con.CF_UNICODETEXT)
    win32clipboard.CloseClipboard()
    return text

def setClipboardText(s):
    win32clipboard.OpenClipboard()
    win32clipboard.EmptyClipboard()
    win32clipboard.SetClipboardData(win32con.CF_UNICODETEXT, s)
    win32clipboard.CloseClipboard()
```