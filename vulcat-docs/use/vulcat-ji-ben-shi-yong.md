# vulcat基本使用

本节将介绍vulcat的基本使用方式

## 1/ 基本参数

### 1.1 -h/--help

此参数用于查看帮助信息

```
python3 vulcat.py -h
python3 vulcat.py --help
```

![](<../.gitbook/assets/图片 (4).png>)

### 1.2 --version

此参数用于查看版本信息

```
python3 vulcat.py --version
```

![](<../.gitbook/assets/图片 (28).png>)

### 1.3 --list

此参数用于查看vulcat漏洞列表

列表 从左到右分别是：

* 应用名称
* 漏洞编号（None表示暂无编号）
* 漏洞类型
* 是否支持vulcat的Shell交互模式（Y表示支持）
* 漏洞描述

![](<../.gitbook/assets/图片 (43).png>)

## 2/ 指定扫描站点

一共三个参数，下面将逐一介绍

![](<../.gitbook/assets/图片 (12).png>)

### 2.1 -u/--url

此参数用于指定单个站点 并进行扫描（很常见的参数）

```
python3 vulcat.py -u http://xxx.com
python3 vulcat.py --url http://xxx.com
```

我这里使用python 启动一个本地的HTTP服务器进行测试：

![](<../.gitbook/assets/图片 (14).png>)

![](<../.gitbook/assets/图片 (34).png>)

<figure><img src="../.gitbook/assets/图片 (44).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/图片 (40).png" alt=""><figcaption></figcaption></figure>

如果目标站点无法访问，则：

1个请求：验证目标站点是否存活

10秒：vulcat**默认超时时间**为10秒，你可以通过--timeout延长 超时时间

![](<../.gitbook/assets/图片 (39).png>)

### 2.2 -f/--file

此参数用于指定一个文件，vulcat会从这个文件中 批量读取URL并进行扫描，格式为每行一个URL

```
python3 vulcat.py -f url.txt
```

![](<../.gitbook/assets/图片 (25).png>)

![](<../.gitbook/assets/图片 (45).png>)

可以看到，url.txt里面有3个站点，vulcat会逐个去扫描

（什么？你问我为什么是一个一个扫，不能全部同时扫吗？---以后会考虑更新这个功能）

### 2.3 -r/--recursive

此参数可以递归 扫描URL的每层目录

（和**dirsearch**的-r/--recursive是相同的作用，项目地址：[https://github.com/maurosoria/dirsearch](https://github.com/maurosoria/dirsearch)）

默认情况下，vulcat**只会检测zxc/目录下有无漏洞**，而不会去检测qwe/以及asd/目录，这可能会导致你错过某个漏洞：

<img src="../.gitbook/assets/图片 (5).png" alt="" data-size="original">

如果手动指定每个路径 又太麻烦了，这时候你可以使用-r/--recursive参数

```
python3 vulcat.py -u http://192.168.80.1/qwe/asd/zxc/ -r
```

添加-r参数之后，vulcat将会从 站点根目录开始检测，一直到最后一个目录：

![](<../.gitbook/assets/图片 (20).png>)

建议每次扫描时都添加-r参数，这样可以扩大检测范围，提高漏洞发现几率

（后续可能会为vulcat添加爬虫功能，自动爬取站点上的所有目录 并进行检测，敬请期待）

## 3/ 功能参数

vulcat提供了一些功能参数，下面逐一介绍

![](<../.gitbook/assets/图片 (9).png>)

### 3.1 -t/--thread

此参数用于设置vulcat的**线程数**，默认为2，-t指定的数值越大 同时间运行的POC也越多

例如默认线程数为3，则**同一时间会有 3+个 POC运行**（最少3个）

![](<../.gitbook/assets/图片 (24).png>)

指定线程数为10，则同一时间会有 10+个 POC运行（最少10个）

```
python3 vulcat.py -u http://192.168.80.1/ -t 10
```

可以看到进度条的数值 也发生了变化，由上图的34变为11：

![](<../.gitbook/assets/图片 (41).png>)

上面看起来不太明显，可以添加--log参数，查看详细日志

```
# 线程数为3, 日志等级为2
python3 vulcat.py -u http://192.168.80.1/ -t 3 --log 2

# 线程数为10, 日志等级为2
python3 vulcat.py -u http://192.168.80.1/ -t 10 --log 2
```

线程3和线程10作对比，从日志的时间中可以看出，每秒发送的HTTP数据包数量明显增加

![](<../.gitbook/assets/图片 (21).png>)

![](<../.gitbook/assets/图片 (1).png>)

### 3.2 --delay

此参数用于设置vulcat**每次请求的间隔时间**，默认为1秒，--delay指定的数值越小 则vulcat扫描耗时越短**（主要还是看网速）**

* \--delay默认为1秒，-t/--thread指定为2线程，则：
* 每运行2个POC，暂停1秒
* 在单个POC中，每发送一个HTTP请求，暂停1秒

![](<../.gitbook/assets/图片 (30).png>)

```
# 线程数为1, 请求间隔时间为0.3秒 (默认为1秒)
python3 vulcat.py -u http://192.168.80.1/ --delay 0.3
```

![](<../.gitbook/assets/图片 (18).png>)

### 3.3 --timeout

此参数用于设置**超时时间**，默认为10秒

每次发送的HTTP请求，如果超过10秒 目标站点都没有响应，则请求超时，终止当前请求

对于**网速不好**的用户来说，应该将timeout调高，防止vulcat误认为目标站点不存活，从而跳过检查

```
python3 vulcat.py -u http://192.168.80.1/ --timeout 30
```

![](<../.gitbook/assets/图片 (36).png>)

### 3.4 --user-agent

此参数用于自定义User-Agent头，会覆盖config.yaml里的默认User-Agent

```
# 指定User-Agent头为abcdefg, 指定vulcat的HTTP代理地址为192.168.80.1:8080
python3 vulcat.py -u http://192.168.80.1/ --user-agent "abcdefg" --http-proxy 192.168.80.1:8080
```

![](<../.gitbook/assets/图片 (42).png>)

![](<../.gitbook/assets/图片 (11).png>)

可以看到参数已经生效

![](<../.gitbook/assets/图片 (46).png>)

### 3.5 --cookie

此参数用于指定Cookie

```
python3 vulcat.py -u http://192.168.80.1/ --cookie "sessid=xxxxxx; user=admin" --http-proxy 192.168.80.1:8080
```

![](<../.gitbook/assets/图片 (42).png>)

![](<../.gitbook/assets/图片 (35).png>)

![](<../.gitbook/assets/图片 (27).png>)

### 3.6 --auth

此参数用于指定Authorization

```
python3 vulcat.py -u http://192.168.80.1/ --auth "Basic YWRtaW46YWRtaW4=" --http-proxy 192.168.80.1:8080
```

![](<../.gitbook/assets/图片 (42).png>)

![](<../.gitbook/assets/图片 (8).png>)

![](<../.gitbook/assets/图片 (3).png>)

## 4/ 通用工作参数

![](<../.gitbook/assets/图片 (6).png>)

### 4.1 --no-waf

添加此参数后，将跳过所有WAF检测

```
python3 vulcat.py -u http://192.168.80.1/ --no-waf
```

![](<../.gitbook/assets/图片 (50).png>)

添加--no-waf参数之后，已经没有了WAF检测的提示（跳过了WAF检测）

![](<../.gitbook/assets/图片 (47).png>)

### 4.2 --no-poc

添加此参数后，将跳过所有POC检测

```
python3 vulcat.py -u http://192.168.80.1/ --no-poc
```

![](<../.gitbook/assets/图片 (53).png>)

如图

![](<../.gitbook/assets/图片 (17).png>)

### 4.3 --batch

添加此参数后，当遇到需要手动输入的情况时，vulcat将自动使用默认参数值

例如WAF检测，如果检测到了WAF，则会跳出提示并要求用户输入

* 如果输入y或yes，则会继续进行 指纹识别和POC扫描等操作
* 如果输入n或no，则会跳过当前站点的扫描

![](<../.gitbook/assets/图片 (33).png>)

![](<../.gitbook/assets/图片 (13).png>)

从图中可以看到，No的N是大写的，而yes的y是小写的

* 说明默认选项为No
* （有WAF的话 当然不扫了，vulcat暂时没有绕WAF的功能）
* （什么？你说你偏要扫？--- 我alniW!83k&&%\*$Qkklcaklnsd）

```
python3 vulcat.py -u http://192.168.80.1/ --batch
```

当指定--batch参数时，vulcat会自动选择no，不需要用户手动输入

![](<../.gitbook/assets/图片 (22).png>)





