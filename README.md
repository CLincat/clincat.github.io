# vulcat简介

vulcat是一个用于扫描web端漏洞的工具，支持自定义POC、查看每个POC的请求日志、导出报告等功能。

vulcat的扫描过程为：WAF检测 --> 指纹识别 --> POC扫描 --> 输出报告

当vulcat发现问题时会输出漏洞信息、漏洞利用的Request数据包，使用者可以根据提示对漏洞进行手工验证、深入利用等。

vulcat中的部分漏洞还支持Shell交互模式，你可以在命令行持续输入自定义的Payload（例如whoami、id、ifconfig等命令），vulcat会用输入的Payload 替换默认的Payload，然后发送到目标主机上。
