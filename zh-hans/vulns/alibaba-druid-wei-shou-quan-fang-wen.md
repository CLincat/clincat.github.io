# 💛 Alibaba Druid-未授权访问

### 1）漏洞信息

|漏洞名称	|受影响组件	|漏洞类型	|漏洞编号	|
|--	|--	|--	|--	|
|阿里巴巴Druid未授权访问	|[Alibaba Druid](https://github.com/alibaba/druid)	|未授权访问	|暂无	|

|漏洞简介	|
|--	|
|<p>    Druid是阿里巴巴数据库事业部出品，为监控而生的数据库连接池。</p><p>    Druid提供监控功能，监控SQL的执行时间、Web URI的请求、Session等。</p><p>    当开发者配置不当时就可能造成未授权访问漏洞。</p>	|

|参考链接	|
|--	|
|<ol><li><a href="https://www.cxybb.com/article/qq_46119575/128542168">https://www.cxybb.com/article/qq_46119575/128542168</a></li><li><a href="https://blog.csdn.net/hawinlolo/article/details/125481204">https://blog.csdn.net/hawinlolo/article/details/125481204</a></li></ol>	|



### 2）vulcat验证过程

```
python3 vulcat.py -u <URL> -a alidruid
```

<figure><img src="https://raw.githubusercontent.com/CLincat/blog-imgs/main/vulcat-docs/vulns/alibaba/druid_unauth_01.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://raw.githubusercontent.com/CLincat/blog-imgs/main/vulcat-docs/vulns/alibaba/druid_unauth_01.png" alt=""><figcaption></figcaption></figure>

