# vulcat配置文件

本节将介绍vulcat的配置文件config.yaml

（可以修改语言，支持中文 英文）

## 配置文件config.yaml

该文件位于**vulcat/config.yaml**，里面定义了vulcat的一些基本参数：**语言、ceye.io支持、默认Headers**等

![](<../.gitbook/assets/图片 (23).png>)

![](<../.gitbook/assets/图片 (32).png>)

### 1.1/ 语言

vulcat支持中文和英文，默认为英文，用户可以通过config.yaml进行修改

打开vulcat/config.yaml，将 language: **en-us** 修改为 language: **zh-cn**

修改完毕后，保存退出，再次运行vulcat，可以看到已经变为了中文

![](<../.gitbook/assets/图片 (15).png>)

![](<../.gitbook/assets/图片 (52).png>)

### 1.2/ ceye.io支持

ceye.io是一个DNSLOG平台，用于辅助无回显漏洞的验证，官网：[http://ceye.io/](http://ceye.io/)

vulcat默认使用的DNSLOG平台是[http://www.dnslog.cn/](http://www.dnslog.cn/)

如果你想使用ceye.io，则需要在官网进行注册，在你的个人主页获得专属的 域名和Token

![](<../.gitbook/assets/图片 (48).png>)

然后将你的 域名和Token 填写到config.yaml对应的地方，保存文件即可

![](<../.gitbook/assets/图片 (19).png>)

### 1.3/ 默认Headers

vulcat发送的所有HTTP请求，将默认带有以下Headers

![](<../.gitbook/assets/图片 (16).png>)

vulcat有一个--user-agent参数，可以使用自定义的User-Agent头

使用--user-agent指定，将会覆盖config.yaml的默认User-Agent头



