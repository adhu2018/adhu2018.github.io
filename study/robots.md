[目录](./) 

```python
# -*- coding: utf-8 -*-
import re
from requests_html import HTMLSession


def _robots(r):
    """
    任何一条Disallow记录为空，说明该网站的所有部分都允许被访问，在"/robots.txt"文件中，至少要有一条Disallow记录。
    如果"/robots.txt"是一个空文件，则对于所有的搜索引擎robot，该网站都是开放的。
    一个网站的所有URL默认是Allow的，所以Allow通常与Disallow搭配使用，实现允许访问一部分网页同时禁止访问其它所有URL的功能。
    需要特别注意的是Disallow与Allow行的顺序是有意义的，robot会根据第一个匹配成功的Allow或Disallow行确定是否访问某个URL。
    robots支持使用通配符"*"和"$"来模糊匹配url：  # 其他的不符合正则语法，需要转义
        "$" 匹配行结束符。
        "*" 匹配0或多个任意字符
    """
    if r.status_code == 200:
        _list1 = re.split(r"\n", r.text)
        list1 = []
        reg = re.compile(r"^User-agent:\s*(.*)", re.I)
        # reg1 = re.compile(r"^(Disallow):\s*(.*)", re.I)
        # reg2 = re.compile(r"^(Allow):\s*(.*)", re.I)
        reg12 = re.compile(r"^((Dis)?allow):\s*(.*)", re.I)
        reg3 = re.compile(r"^Crawl-delay:\s*(.*)", re.I)
        regz = re.compile(r"([\{\}\[\]\\\^\.\?\+])")  # {}[]\^.?+ 记起了再补充
        reg_ = re.compile(r"(\*)")
        reg__ = re.compile(r"(\$$)")
        for i in _list1:
            if i == "":  # 空行
                pass
            elif reg.search(i):
                list1.append([])  # 爬虫
                list1[-1].append(reg.search(i)[1].upper())  # 爬虫名称，大写 [][0]
                list1[-1].append([])  # (Disallow or Allow):value [][1] 需要特别注意的是Disallow与Allow行的顺序是有意义的
                list1[-1].append([])  # Crawl-delay [][2] 时间间隔
            elif reg12.search(i):
                # [][1][0] ['Disallow', '/baidu']
                # [][1][0][0] 'Disallow'
                if reg__.search(i):  # $标识
                    list1[-1][1].append([reg12.search(i)[1], ".*?"+reg_.sub(r".*", regz.sub(r"\\\1", reg12.search(i)[3]))])
                else:
                    list1[-1][1].append([reg12.search(i)[1], ".*?"+reg_.sub(r".*", regz.sub(r"\\\1", reg12.search(i)[3]))+".*"])
            elif reg3.search(i):
                list1[-1][2].append(reg3.search(i)[1])
        return list1
    else:
        return False

def robots_(_url):
    session = HTMLSession()
    url_com = re.search(r"https?(://[^/]*)", _url)[1]
    url1 = "http%s/robots.txt" % str(url_com)
    url2 = "https%s/robots.txt" % str(url_com)
    try:
        r1 = session.get(url1)
    except:  # 尽量验证签名，（使用fiddler等）证书验证有问题时不验证签名。会有一个Warning，这样你就知道当前是没有验证签名的。也可以直接不验证签名，然后把Warning去掉，但不推荐。
        r1 = session.get(url1, verify=False)
    list1 = _robots(r1)
    if list1:
        return list1
    else:
        try:
            r2 = session.get(url2)
        except:
            r2 = session.get(url2, verify=False)
        list2 = _robots(r2)
        if list2:
            return list2
        else:
            return False

# allow([type, ]_url)
def allow(*_str):
    _url = ""
    type = "*"
    regcom = re.compile(r"https?(://[^/]*)", re.I)
    while _url == "":
        for i in _str:
            if regcom.search(i):
                _url = i
            else:
                type = i.upper()
    _list = robots_(_url)
    b = True  # 默认允许爬取
    if _list:
        for i in _list:
            # 爬虫名称或种类
            if i[0] == type or i[0] == "*":
                i = i[1]
                for j in i:  # j ['Disallow', '.*?/s\\?.*']
                    reg = re.compile(j[1], re.I)
                    if j[0].upper() == "DISALLOW" and reg.match(_url):
                        b = False
    return b

if __name__ == '__main__':
    pass
```

urllib有个robotparser也是解析robots的，但是用着不顺手（emmm，好吧，其实是不想记用法。。）