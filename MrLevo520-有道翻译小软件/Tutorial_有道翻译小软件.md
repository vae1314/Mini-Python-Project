***From [用python做个翻译小软件吧~](http://blog.csdn.net/mrlevo520/article/details/51674188)***

- Python 2.7.13
- IDE Pycharm 5.0.3
- macOS 10.12.1 



------

## 前言

>  ​	花了一点时间，半抄半写半修改的写了第一个能用的python小程序，作用是在IDE端模拟有道词典的访问，效果如下图所示，不足之处在于，当输入的中英文字符串超过一定数量，会抛出中间代码，新手并不知道怎么处理，望知道的不吝赐教



------

## 初阶：交互界面

> 首先在jupyter或者pycharm中进行交互的操作，核心语句是使用raw_input捕获系统输入

### 效果图

![效果图](http://img.blog.csdn.net/20160614193811639)



### 代码

```python
# -*- coding: utf-8 -*-

import urllib2
import urllib  # python2.7才需要两个urllib
import json
while True:
    content = raw_input("请输入需要翻译的内容：")  # 系统捕获输入，就是命令框会弹出提示，需要你进行手动输入
    if content == 'q':  # 输入q退出while循环
        break
    
    url = "http://fanyi.youdao.com/translate?smartresult=dict&smartresult=rule&smartresult=ugc&sessionFrom=null"
    data = {}  # 构造data，里面构造参数传入
    data['type'] = 'AUTO'
    data['i']=content
    data['doctype'] = 'json' 
    data['xmlVersion'] = '1.8'
    data['keyfrom'] = 'fanyi.web'
    data['ue'] = 'UTF-8'
    data['action'] = 'FY_BY_ENTER'
    data['typoResult'] = 'true'
    
    data = urllib.urlencode(data).encode('utf-8')  # 将构造的data编码
    req = urllib2.Request(url)  # 向浏览器发出请求
    response = urllib2.urlopen(req, data)   # 带参请求，返回执行结果
    html = response.read().decode('utf-8')
    # print(html)  # 可以取消print的注释，查看其中效果，这边获取的结果是进行解析

    target = json.loads(html)   # 以json形式载入获取到的html字符串
    
    print "翻译的内容是："+target['translateResult'][0][0]['tgt'].encode('utf-8')
    

# 请输入需要翻译的内容：test
# 翻译的内容是：测试
# 请输入需要翻译的内容：测试
# 翻译的内容是：test
# 请输入需要翻译的内容：q
```

### 



**注意：**这里的data字典中的数据根据实际网页中数据为准，可能会不一样，具体操作，点击审查元素。或见小甲鱼54讲。



------



## 进阶：做成gui

> 离实用还差那么两步，第一步是先做成GUI

可以参考以前我写的: [Python基于Tkinter的二输入规则器(乞丐版)](http://blog.csdn.net/mrlevo520/article/details/51812096)



### 界面效果



![这里写图片描述](http://img.blog.csdn.net/20170723211730352?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJMZXZvNTIw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



### 代码

```Python
# -*- coding: utf-8 -*-
'''
Author:哈士奇说喵
UDate: 2016.7.21
'''
from Tkinter import *
import difflib
import urllib2
import urllib  # python2.7才需要两个urllib
import json
  

# ----------------------主框架部分----------------------

root = Tk()
root.title('翻译GUI&beta1')
root.geometry()
Label_root=Label(root)

#-----------------------定义规则------------------------

def translate(content):
    
    url = "http://fanyi.youdao.com/translate?smartresult=dict&smartresult=rule&smartresult=ugc&sessionFrom=null"
    data = {}  # 构造data，里面构造参数传入
    data['type'] = 'AUTO'
    data['i']=content
    data['doctype'] = 'json' 
    data['xmlVersion'] = '1.8'
    data['keyfrom'] = 'fanyi.web'
    data['ue'] = 'UTF-8'
    data['action'] = 'FY_BY_ENTER'
    data['typoResult'] = 'true'
    
    data = urllib.urlencode(data).encode('utf-8')  # 将构造的data编码
    req = urllib2.Request(url)  # 向浏览器发出请求
    response = urllib2.urlopen(req, data)   # 带参请求，返回执行结果
    html = response.read().decode('utf-8')
    # print(html)  # 可以取消print的注释，查看其中效果，这边获取的结果是进行解析

    target = json.loads(html)   # 以json形式载入获取到的html字符串
    
    #print u"翻译的内容是："+target['translateResult'][0][0]['tgt']
    return target['translateResult'][0][0]['tgt'].encode('utf-8')
  


#还可以继续增加规则函数，只要是两输入的参数都可以
#----------------------触发函数-----------------------

def Answ():# 规则函数

    Ans.insert(END,"翻译 %s: "%var_first.get().encode('utf-8') + translate(var_first.get().encode('utf-8')))
    
def Clea():#清空函数
    input_num_first.delete(0,END)#这里entry的delect用0
    Ans.delete(0,END)#text中的用0.0


#----------------------输入选择框架--------------------
frame_input = Frame(root)
Label_input=Label(frame_input, text='请输入需要翻译的内容', font=('',15))
var_first = StringVar()
input_num_first = Entry(frame_input, textvariable=var_first)


#---------------------计算结果框架---------------------
frame_output = Frame(root)
Label_output=Label(frame_output, font=('',15))
Ans = Listbox(frame_output, height=5,width=30) #text也可以，Listbox好处在于换行


#-----------------------Button-----------------------

calc = Button(frame_output,text='翻译', command=Answ)
cle = Button(frame_output,text='清空', command=Clea)



Label_root.pack(side=TOP)
frame_input.pack(side=TOP)
Label_input.pack(side=LEFT)

input_num_first.pack(side=LEFT)

frame_output.pack(side=TOP)
Label_output.pack(side=LEFT)
calc.pack(side=LEFT)
cle.pack(side=LEFT)
Ans.pack(side=LEFT)

#-------------------root.mainloop()------------------

root.mainloop()
```

---
##高阶：发布应用
> 如何在小伙伴面前装B才是我学习的动力，哈哈哈
> 教程更新如下：[将自己的python程序打包成.exe/.app(秀同学一脸呐)](http://blog.csdn.net/mrlevo520/article/details/51840217)

------

## Pay Attention

- python3的用户注意url包的使用和python2是有区别的，请根据实际需求自行百度



- Python如果操作频率太快或者网页限制机器人对此的访问，则需要修改head了，修改代码后.当然每个电脑的user都不一样，具体去审查元素查看。

```
req = urllib2.Request(url) # 生成对象
# 添加如下一行代码；
req.add_header('User-Agent','Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.101 Safari/537.36')，这样就可以伪装成人类啦
```



-  当然也可以添加延时模块, 即可限定访问时间。

```
import time  #添加延时模块
time.sleep(1)#休息1秒钟再进行操作
```



- python3的同学需要Tkinter改成小写，还有就是注意编码部分的转化，具体建议可见[Python基于Tkinter的二输入规则器(乞丐版)](http://blog.csdn.net/mrlevo520/article/details/51812096)中的参考建议

- mac的同学可能遇到tkinter无法输入中文问题，可能是由tkinter版本过低导致，解决方案参考：[MAC 系统中，Tkinter 无法用 中文输入法 输入中文](http://www.jianshu.com/p/f9e30bdc5806)


---
## 更新


 -  2016.6.14  初次撰写
 -  2017.7.22  重新排版，增加tkinter等
