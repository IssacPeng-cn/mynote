﻿1.解压以后找到bin目录

linux解压

```
tar -zxvf nacos-server-2.0.3.tar.gz
```

2.执行指令：

	2.1Linux/Unix/Mac
	
	启动命令(standalone代表着单机模式运行，非集群模式):

```
	sh startup.sh -m standalone
```

	2.2Windows
	
	启动命令(standalone代表着单机模式运行，非集群模式):

```
	startup.cmd -m standalone
```

	集群（其实Nacos本身默认启动就是集群模式）

```
	sh startup.sh
```

3.访问地址：

```
http://localhost:8848/nacos
```

默认的账号密码是：nacos/nacos

4.查看日志：

```
cat /home/msb/opt/nacos3/logs/start.out
tail -f /home/msb/opt/nacos3/logs/start.out
```