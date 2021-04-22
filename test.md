---
layout: blog
banana: true
category: 安全测试
title: SQLMap 常用命令
date: 2017-07-30 10:06:42
background-image: 
tags:
- SQLMap
- 信息安全
---

## SQLMap 常用命令

#### 1.指定目标 `-u`

    使用参数 `-u` 或 `–url` 指定一个 URL 作为目标，该参数后跟一个表示 URL 的字符串，还可以指定端口，如：

```
sqlmap.py -u "192.168.x.x/index.php?id=1"
sqlmap.py -url "192.168.x.x/index.php?id=1"
```

#### 2.输出等级 `-v`

    该命令，输出输入的信息，共有七个等级，默认是 1，也可以在命令后指定 `-v 3` 就是等级 3 的输出。

```
0：只显示 Python 的 tracebacks 信息、错误信息 [ERROR] 和关键信息 [CRITICAL]；
1：同时显示普通信息 [INFO] 和警告信息 [WARNING]；
2：同时显示调试信息 [DEBUG]；
3：同时显示注入使用的攻击荷载；
4：同时显示 HTTP 请求；
5：同时显示 HTTP 响应头；
6：同时显示 HTTP 响应体。
```

#### 3. 列库 `--dbs`

在检测出了存在注入后，就要进行对数据库的列举，命令是 `--dbs`

#### 4.列表 `--tables`

在列出了 Web 程序的库后，要根据这个库来列出表，命令是 `-D ‘库名’ --tables`

#### 5. 列出表中字段 `columns`

得到了库和表之后，可以根据这个库表来列出表里的字段进行数据的读取，命令是 `-D ‘数据库名字’ -T ‘表的名字’ --columns`

#### 6. level && risk

刚开始在前面说了，level 算是一共注入测试等级，一共是五级，级别越高，检测的内容也就越多，检测级别大于等于 2 时会检测 cookie 是否有注入，检测级别大于等于 3 时会检测 User-Agent 和 Referer 是否有注入。比较推荐使用 3 等级进行测试。

#### 7. os

OS 是检测数据库版本信息的一个命令，但是 SQLMap 默认会自动去检测。

#### 3. *指定注入点

SQLMap 可以区分一个 URL 里面的参数来进行注入点测试，但在遇到了一些做了伪静态的网页就无法自动识别了。

`/admin/1/` 类似于这种，SQLMap 便无法进行注入测试，但它实际上可能是这样的： `/admin.php?id=1`

它只是把参数隐藏在了 URL 中，对于这种网页，直接在参数后加上一个星号即可。

```
sqlmap.py -u "www.baidu.com/admin/1*"
```

星号之后，众生平等。

#### 4. POST 数据

> 使用参数：`-dota`

在 URL 后跟上你要提交的 POST 数据，SQLMap 也会去自动测试。

```
sqlmap.py -u "http://baidu.com/admin.php" --data="id=1&name=admin"
```

#### 5. cookie

> 使用参数：`-cookie`

这种方式多半来进行 cookie 的注入检测，设置比较繁琐，也可以像我之前所说的直接设置测试等级为 3，会自动测试是否存在 cookie 注入。

```
sqlmap.py -u "http://baidu.com/admin.php" --cookie "customerId=591edabaab5b52292042df8a"
```

#### 6. 从文件载入 HTTP 请求测试

> 使用参数：`-r`

可以可以理解抓个包下来，获取 HTTP 请求然后保存在文件里，然后让 SQLMap 自己去解析请求来进行注入测试。

可以从 burp suite 上抓包下来配合 SQLMap 进行联合使用。

#### 7. Google 搜索进行测试

> 使用参数：`-g`

SQLMap 会自动爬取谷歌上的搜索结果来进行注入，对带有 GET 参数的 URL 进行挨个测试，前提是你能够科学上网。

#### 8. 超时延迟

> 使用参数：`-timeout`

默认的超时时间是 30 秒，可以使用命令 `-timeout 31` 来进行修改，比如修改为 31 秒超时。
