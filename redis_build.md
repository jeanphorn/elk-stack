## 概况

机器：10.xxx.xxx.170
端口：9988
密码：pass-pass        
目录：/home/lijianfang/local/redis-3.0.5    #目录权限已设为可读

## redis配置运行

配置redis.conf,主要有以下几点,根据需要配置：

```
save 900 1
save 300 10
save 60 10000

port 9988                                       # 确保端口没有被占用
bind 0.0.0.0
requirepass pass-pass    #可不配置，
```
运行redis server

>
> nohup ./bin/redis-server redis.conf &
> 

## 防护墙配置
如果机器设置了防火墙，则需要进行防火墙的配置。

查看机器防火墙配置

> Chain INPUT (policy ACCEPT)
> target     prot opt source               destination         
> ACCEPT     tcp  --  anywhere             anywhere            tcp dpt:ftp 
> ACCEPT     tcp  --  anywhere             anywhere            tcp dpt:webcache 
> ACCEPT     tcp  --  anywhere             anywhere            tcp dpt:http 
> ACCEPT     all  --  anywhere             anywhere            state RELATED,ESTABLISHED 
> ACCEPT     icmp --  anywhere             anywhere            
> ACCEPT     all  --  anywhere             anywhere            
> ACCEPT     tcp  --  anywhere             anywhere            state NEW tcp dpt:ssh 
> REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited 
> 
> Chain FORWARD (policy ACCEPT)
> target     prot opt source               destination         
> REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited 
> 
> Chain OUTPUT (policy ACCEPT)
> target     prot opt source               destination         
> 

为redis server端口9988配置防火墙过滤

> sudo iptables -I INPUT 3 -p tcp --dport 9988 -j ACCEPT     # -I 指定此规则加到第三条上
> sudo iptables -A OUTPUT -p tcp --dport 9988 -j ACCEPT

-----------------------------------
**注意:** iptables执行规则是从上往下执行，匹配到了规则则执行，否则继续往下匹配。
所以，如果有ACCEPT的规则与REJECT的规则有重复的地方，ACCEPT的规则要放到前面复制不能通过

----------------------------------

## 连通性测试

> telnet 10.xxx.xxx.170 9988 
