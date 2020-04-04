```python
import time

def test_(func):
    def wrapper():
        start = time.time()
        func()
        end = time.time()
        print("Time used:%ss" % str(end-start))
    return wrapper
    
@test_
def test():
    print("test")
    time.sleep(1)  # 停止1秒，结果更明显

test()
"""
test
Time used:1.0002307891845703s
"""
# 接近1秒
```

> 装饰器