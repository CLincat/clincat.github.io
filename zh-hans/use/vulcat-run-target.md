# 指定目标站点

## -u/--url

此参数用于指定单个站点 并进行扫描（很常见的参数）

```
python3 vulcat.py -u http://xxx.com
python3 vulcat.py --url http://xxx.com
```

我这里使用python 启动一个本地的HTTP服务器进行测试：

<figure><img src="../../static/imgs/usage/usage_02-1-1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../static/imgs/usage/usage_02-1-2.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../static/imgs/usage/usage_02-1-3.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../static/imgs/usage/usage_02-1-4.png" alt=""><figcaption></figcaption></figure>


<figure>如果目标站点无法访问，则：<img src="../../static/imgs/usage/usage_02-1-5.png" alt=""><figcaption></figcaption></figure>

## -f/--file

此参数用于指定一个文件，vulcat会从这个文件中 批量读取URL并进行扫描，格式为每行一个URL

```
python3 vulcat.py -f url.txt
```

<figure><img src="../../static/imgs/usage/usage_02-2-1.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../static/imgs/usage/usage_02-2-2.png" alt=""><figcaption></figcaption></figure>

可以看到，url.txt里面有3个站点，vulcat会逐个去扫描

（什么？你问我为什么是一个一个扫，不能全部同时扫吗？---以后会考虑更新这个功能）

## -r/--recursive

此参数可以递归 扫描URL的每层目录

（和**dirsearch**的-r/--recursive是相同的作用，项目地址：[https://github.com/maurosoria/dirsearch](https://github.com/maurosoria/dirsearch)）

默认情况下，vulcat**只会检测zxc/目录下有无漏洞**，而不会去检测 根目录、qwe/以及asd/目录，这可能会导致你错过某个漏洞：

<figure><img src="../../static/imgs/usage/usage_02-3-1.png" alt=""><figcaption></figcaption></figure>

如果手动指定每个路径 又太麻烦了，这时候你可以使用-r/--recursive参数

```
python3 vulcat.py -u http://192.168.80.1/qwe/asd/zxc/ -r
```

添加-r参数之后，vulcat将会从 站点根目录开始检测，一直到最后一个目录：

<figure><img src="../../static/imgs/usage/usage_02-3-2.png" alt=""><figcaption></figcaption></figure>

建议每次扫描时都添加-r参数，这样可以扩大检测范围，提高漏洞发现几率

（后续可能会为vulcat添加爬虫功能，自动爬取站点上的所有目录 并进行检测，敬请期待）
