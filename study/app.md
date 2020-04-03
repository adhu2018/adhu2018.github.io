```python
# coding:utf-8
from kivy.app import App
from kivy.uix.button import Button

class MyDream(App):
    def build(self):
        return Button(text='I have a dream!')

MyDream().run()
```

> 一个简单的app示例