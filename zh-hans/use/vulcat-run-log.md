# vulcat基本使用

本节将介绍vulcat的基本使用方式

## 1/ 基本参数

### 1.1 -h/--help

```
python3 vulcat.py -h
python3 vulcat.py --help
```

<figure>此参数用于查看帮助信息<img src="../../../../../static/imgs/usage/usage_01-1.png" alt=""><figcaption></figcaption></figure>

### 1.2 --version

```
python3 vulcat.py --version
```

<figure>此参数用于查看版本信息<img src="../../../../../static/imgs/usage/usage_01-2.png" alt=""><figcaption></figcaption></figure>

### 1.3 --list

```
python3 vulcat.py --list
```

POC列表 从左到右分别是：

* 应用名称
* 漏洞编号（None表示暂无编号）
* 漏洞类型
* 是否支持vulcat的Shell交互模式（Y表示支持）
* 漏洞描述

<figure><img src="../../../../../static/imgs/usage/usage_01-3.png" alt=""><figcaption></figcaption></figure>

## 2/ 指定扫描站点

一共三个参数，下面将逐一介绍

<figure><img src="../../../../../static/imgs/usage/usage_02.png" alt=""><figcaption></figcaption></figure>

### 2.1 -u/--url

此参数用于指定单个站点 并进行扫描（很常见的参数）

```
python3 vulcat.py -u http://xxx.com
python3 vulcat.py --url http://xxx.com
```

我这里使用python 启动一个本地的HTTP服务器进行测试：

<figure><img src="../../../../../static/imgs/usage/usage_02-1-1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_02-1-2.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_02-1-3.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_02-1-4.png" alt=""><figcaption></figcaption></figure>


<figure>如果目标站点无法访问，则：<img src="../../../../../static/imgs/usage/usage_02-1-5.png" alt=""><figcaption></figcaption></figure>

### 2.2 -f/--file

此参数用于指定一个文件，vulcat会从这个文件中 批量读取URL并进行扫描，格式为每行一个URL

```
python3 vulcat.py -f url.txt
```

<figure><img src="../../../../../static/imgs/usage/usage_02-2-1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_02-2-2.png" alt=""><figcaption></figcaption></figure>

可以看到，url.txt里面有3个站点，vulcat会逐个去扫描

（什么？你问我为什么是一个一个扫，不能全部同时扫吗？---以后会考虑更新这个功能）

### 2.3 -r/--recursive

此参数可以递归 扫描URL的每层目录

（和**dirsearch**的-r/--recursive是相同的作用，项目地址：[https://github.com/maurosoria/dirsearch](https://github.com/maurosoria/dirsearch)）

默认情况下，vulcat**只会检测zxc/目录下有无漏洞**，而不会去检测 根目录、qwe/以及asd/目录，这可能会导致你错过某个漏洞：

<figure><img src="../../../../../static/imgs/usage/usage_02-3-1.png" alt=""><figcaption></figcaption></figure>

如果手动指定每个路径 又太麻烦了，这时候你可以使用-r/--recursive参数

```
python3 vulcat.py -u http://192.168.80.1/qwe/asd/zxc/ -r
```

添加-r参数之后，vulcat将会从 站点根目录开始检测，一直到最后一个目录：

<figure><img src="../../../../../static/imgs/usage/usage_02-3-2.png" alt=""><figcaption></figcaption></figure>

建议每次扫描时都添加-r参数，这样可以扩大检测范围，提高漏洞发现几率

（后续可能会为vulcat添加爬虫功能，自动爬取站点上的所有目录 并进行检测，敬请期待）

## 3/ 扫描速率

有以下三个参数
* -t/--thread
* --delay
* --timeout

### 3.1 -t/--thread（增加/减少 并发量）

此参数用于设置线程数，默认为3（同一时间内，最少有3个POC在运行）

你可以通过 -t或--thread 来调高并发POC数量

```
python3 vulcat.py -u http://192.168.80.1/ -t 10
python3 vulcat.py -u http://192.168.80.1/ --thread 10
```

<figure><img src="../../../../../static/imgs/usage/usage_03-1-2.png" alt=""><figcaption></figcaption></figure>

### 3.2 --delay（加快/放慢 扫描速度）

此参数用于设置**请求间隔时间**，默认为1秒
* 每个HTTP请求发送后
* and 每次并发之间
* 将会暂停1秒
* （防止请求过快，导致IP被封禁、）

如果你是单纯的想vulcat**扫描快一点**，可以将间隔时间**调低**

```
# 调整请求间隔时间为0.3秒

python3 vulcat.py -u http://192.168.80.1/ --delay 0.3
```

<figure><img src="../../../../../static/imgs/usage/usage_03-2-2.png" alt=""><figcaption></figcaption></figure>

### 3.3 --timeout（网络延迟高的时候，将该参数调高）

此参数用于设置**超时时间**，默认为10秒

发送的每一个HTTP请求，如果超过10秒 目标站点都没有响应，则请求超时，终止当前请求

对于**网速不好**的用户来说，应该将timeout调高
* 防止vulcat误认为目标站点不存活，从而跳过检查
* 防止POC超时，没有成功检测到漏洞

```
python3 vulcat.py -u http://192.168.80.1/ --timeout 30
```

<figure><img src="../../../../../static/imgs/usage/usage_03-3-1.png" alt=""><figcaption></figcaption></figure>

## 4/ 自定义Request Header

### 4.1 --user-agent

此参数用于自定义User-Agent头，会覆盖config.yaml里的默认User-Agent

```
# 指定User-Agent头为abcdefg, 指定vulcat的HTTP代理地址为192.168.80.1:8080
python3 vulcat.py -u http://192.168.80.1/ --user-agent "abcdefg" --http-proxy 192.168.80.1:8080
```

<figure><img src="../../../../../static/imgs/usage/usage_03-4-1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_03-4-2.png" alt=""><figcaption></figcaption></figure>

可以看到参数已经生效

<figure><img src="../../../../../static/imgs/usage/usage_03-4-3.png" alt=""><figcaption></figcaption></figure>

### 4.2 --cookie

此参数用于指定Cookie

```
python3 vulcat.py -u http://192.168.80.1/ --cookie "sessid=xxxxxx; user=admin" --http-proxy 192.168.80.1:8080
```

<figure><img src="../../../../../static/imgs/usage/usage_03-5-1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_03-5-2.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_03-5-3.png" alt=""><figcaption></figcaption></figure>

### 4.3 --auth

此参数用于指定Authorization

```
python3 vulcat.py -u http://192.168.80.1/ --auth "Basic YWRtaW46YWRtaW4=" --http-proxy 192.168.80.1:8080
```

<figure><img src="../../../../../static/imgs/usage/usage_03-6-1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_03-6-2.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_03-6-3.png" alt=""><figcaption></figcaption></figure>

## 5/ 指纹识别与WAF检测

<figure><img src="../../../../../static/imgs/usage/usage_04.png" alt=""><figcaption></figcaption></figure>

### 5.1 --no-waf

添加此参数后，将跳过所有WAF检测

```
python3 vulcat.py -u http://192.168.80.1/ --no-waf
```

<figure><img src="../../../../../static/imgs/usage/usage_04-1-1.png" alt=""><figcaption></figcaption></figure>

添加--no-waf参数之后，已经没有了WAF检测的提示（跳过了WAF检测）

<figure><img src="../../../../../static/imgs/usage/usage_04-1-2.png" alt=""><figcaption></figcaption></figure>

### 5.2 --no-poc

添加此参数后，将跳过所有POC检测

```
python3 vulcat.py -u http://192.168.80.1/ --no-poc
```

<figure><img src="../../../../../static/imgs/usage/usage_04-2-1.png" alt=""><figcaption></figcaption></figure>

如图

<figure><img src="../../../../../static/imgs/usage/usage_04-2-2.png" alt=""><figcaption></figcaption></figure>

### 5.3 --batch

添加此参数后，当遇到需要手动输入的情况时，vulcat将自动使用默认参数值

例如WAF检测，如果检测到了WAF，则会跳出提示并要求用户输入

* 如果输入y或yes，则会继续进行 指纹识别和POC扫描等操作
* 如果输入n或no，则会跳过当前站点的扫描

<figure><img src="../../../../../static/imgs/usage/usage_04-3-1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../../static/imgs/usage/usage_04-3-2.png" alt=""><figcaption></figcaption></figure>

从图中可以看到，No的N是大写的，而yes的y是小写的

* 说明默认选项为No
* （有WAF的话 当然不扫了，vulcat暂时没有绕WAF的功能）
* （什么？你说你偏要扫？--- 我alniW!83k&&%\*$Qkklcaklnsd）

```
python3 vulcat.py -u http://192.168.80.1/ --batch
```

当指定--batch参数时，vulcat会自动选择no，不需要用户手动输入

<figure><img src="../../../../../static/imgs/usage/usage_04-3-2.png" alt=""><figcaption></figcaption></figure>





