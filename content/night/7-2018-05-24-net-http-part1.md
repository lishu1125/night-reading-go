---
title: 第 7 期 2018-05-24 线下活动 - Go 标准包阅读
date: 2018-05-24T11:49:10+08:00
---
>参与人数: 10 人

*Go 标准包阅读*

Go 版本：go 1.10.1

### net/http

- server.go

### 问题

1. Next Protocol Negotiation = NPN
2. Expect 100 Continue support

>见参考资料

3. header提到了：Expect和host
4. 判断了 header里面的HOST，但是后面又删除，为什么？

server.go#L980

```go	
delete(req.Header, "Host")
```

5. 判断是否支持 HTTP2 （isH2Upgrade）

```go
// isH2Upgrade reports whether r represents the http2 "client preface"
// magic string.
func (r *Request) isH2Upgrade() bool {
	return r.Method == "PRI" && len(r.Header) == 0 && r.URL.Path == "*" && r.Proto == "HTTP/2.0"
}
```

```go
调用：ProtoAtLeast(1, 1)
...
// ProtoAtLeast reports whether the HTTP protocol used
// in the request is at least major.minor.
func (r *Request) ProtoAtLeast(major, minor int) bool {
	return r.ProtoMajor > major ||
		r.ProtoMajor == major && r.ProtoMinor >= minor
}
```

## 会议讨论

20:07:45	 From 永京 李 : ok
20:07:46	 From 斯 艾 : 可以
20:07:50	 From joe sean : ok
20:07:51	 From 洪范 郝 : 可以
20:07:54	 From 力宁 关 : 可以
20:08:01	 From 洪范 郝 : 可以
20:08:05	 From 力宁 关 : ok
20:08:09	 From joe sean : 有杂音
20:08:46	 From 斯 艾 : 有声音
20:08:51	 From joe sean : 有的
20:08:54	 From Jayden Yang : 有
20:08:59	 From 永京 李 : you
20:09:17	 From Jayden Yang : 回声
20:09:17	 From 洪范 郝 : 噪音太大
20:10:28	 From caigaoxing : 2343
20:12:43	 From 洪范 郝 : 屏幕卡了?
20:12:49	 From joe sean : 1
20:12:53	 From Jayden Yang : 1
20:13:57	 From 迪 麦 : 屏幕是卡主了
20:14:26	 From joe sean : 能看到鼠标动，看不到屏幕动
20:16:18	 From caigaoxing : ok
20:17:25	 From caigaoxing : 是不是不动了
20:17:45	 From 和宽 熊 : 动了
20:17:47	 From joe sean : activeConn是连接池吧
20:19:03	 From joe sean : 又卡了
20:19:43	 From 斯 艾 : 屏幕不动了
20:20:38	 From kas li : 不行哦， 卡屏，只有鼠标能显示
20:20:43	 From caigaoxing : 1
20:20:44	 From caigaoxing : 1
20:20:44	 From caigaoxing : 1
20:20:44	 From caigaoxing : 1
20:20:44	 From caigaoxing : 1
20:20:45	 From caigaoxing : 1
20:20:45	 From caigaoxing : 1
20:20:45	 From caigaoxing : 1
20:20:45	 From caigaoxing : 1
20:20:46	 From caigaoxing : 1
20:20:46	 From caigaoxing : 1
20:20:46	 From caigaoxing : 1
20:20:46	 From caigaoxing : 1
20:20:46	 From caigaoxing : 1
20:20:46	 From caigaoxing : 1
20:20:47	 From caigaoxing : 1
20:20:48	 From caigaoxing : 1
20:20:51	 From caigaoxing : 卡了
20:20:55	 From caigaoxing : 卡了
20:20:58	 From joe sean : 1
20:21:04	 From 建雷 张 : hi
20:21:07	 From kas li : 卡屏，只有鼠标能显示
20:21:08	 From caigaoxing : 不动了屏幕
20:21:09	 From 叶 思杰 : 只能看到屏幕
20:21:14	 From 叶 思杰 : 只能看到鼠标
20:21:19	 From joe sean : ok
20:21:28	 From brile ho : ok
20:21:31	 From 叶 思杰 : ok
20:22:18	 From kas li : 多余的视频关了，减少带宽使用
20:25:52	 From caigaoxing : ok
20:25:56	 From joe sean : 可以
20:26:01	 From 夜 暗 : 先整体，再细节？
20:26:02	 From 龙 周 : ok
20:26:04	 From kas li : 跳进去有思路，也是不错的
20:26:31	 From yi zhang : 听到声音了
20:29:20	 From 洪范 郝 : setupHTTP2_Serve 这命名不规范呀
20:30:09	 From DW : 跟踪 listener 主要是做什么的？
20:30:59	 From ksir : 用goland不是很爽？
20:31:09	 From yi zhang : 用goland啊
20:31:12	 From Jayden Yang : goland呢
20:31:19	 From Jayden Yang : vscode呢
20:31:27	 From DW : sublime 跳转也很好用
20:32:05	 From 建雷 张 : emacs 
20:32:53	 From mai yang : 现在直播正常了吧。
20:33:07	 From DW : 临时错误
20:36:18	 From DW : 我还以为是动然规划算法 。。。
20:36:43	 From 永京 李 : backoff 算法设置重试间隔
20:37:40	 From DW : 协程数量有没有限制的？
20:37:54	 From 洪范 郝 : 可以
20:39:08	 From Jayden Yang : 直接深入吧
20:39:12	 From yi zhang : srv.trackListener(l, true)
	defer srv.trackListener(l, false)
20:39:21	 From yi zhang : 这两个函数是在干嘛啊？
20:39:46	 From DW : 每连接一个连接，就 go 一个，会不会出现资源耗尽？
20:42:38	 From yi zhang : srv.trackListener(l, true)
	defer srv.trackListener(l, false)
这两个函数是在干嘛啊？
20:43:29	 From yi zhang : 嗯 好
20:43:37	 From yi zhang : 谢谢
20:44:14	 From 洪范 郝 : 代码为啥不一样呢
20:44:41	 From 大 猫 : @DW 我感觉创建上百万个协程没事，而且一个连接处理完了，资源就会回收
20:47:02	 From mai yang : go1.10
20:47:05	 From DW : 也就是说还没有一个安全机制进行控制是吧 @大猫
20:49:54	 From 大 猫 : 有没有机制我也不知道，如果系统实在没法继续处理的话，应该表现的会卡，然后超时
20:50:45	 From DW : 单机应该支持不了那么多的
20:51:12	 From jinleileiking : 提问的人说的话听不清楚
20:51:19	 From DW : 这里的 context 主要作用是什么？
20:52:18	 From yi zhang : 控制其他goroutine的 取消、停止 等一些操作吧
20:53:05	 From DW : 嗯，对应该是与其他 goroutine 交互的
20:54:08	 From yi zhang : 哈哈 真的应该早点用用goland，没法跟踪函数，看interface的实现，简直就没法看代码啊
20:54:44	 From jinleileiking : vim-go 跳转无压力
20:56:54	 From DW : vs 无压力
20:58:51	 From yi zhang : infinite
20:58:58	 From yi zhang : 时间吧
20:59:01	 From jinleileiking : { cr.remain = maxInt64 }
20:59:03	 From yi zhang : 无穷
21:01:12	 From yi zhang : 950
21:01:17	 From yi zhang : :950
21:04:22	 From DW : 主次版本
21:04:29	 From Jayden Yang : && 优先级高么
21:04:46	 From cym : major.minor
21:04:47	 From DW : || 最高
21:08:36	 From yi zhang : // ValidHostHeader reports whether h is a valid host header.
func ValidHostHeader(h string) bool {
	// The latest spec is actually this:
	//
	// http://tools.ietf.org/html/rfc7230#section-5.4
	//     Host = uri-host [ ":" port ]
	//
	// Where uri-host is:
	//     http://tools.ietf.org/html/rfc3986#section-3.2.2
	//
	// But we're going to be much more lenient for now and just
	// search for any byte that's not a valid byte in any of those
	// expressions.
	for i := 0; i < len(h); i++ {
		if !validHostByte[h[i]] {
			return false
		}
	}
	return true
}
21:09:43	 From 建雷 张 : godef: no declaration found for httplex.ValidHostHeader
21:09:57	 From Jayden Yang : httplex.go
21:10:29	 From jinleileiking : -   var validHostByte = [256]bool{
|       '0': true, '1': true, '2': true, '3': true, '4': true, '5': true, '6': true, '7': true,
|       '8': true, '9': true,
|
|       'a': true, 'b': true, 'c': true, 'd': true, 'e': true, 'f': true, 'g': true, 'h': true,
21:10:55	 From Jayden Yang : /usr/local/go/src/vendor/golang_org/x/net/lex/httplex/httplex.go
21:12:06	 From jinleileiking : [256]bool 下标是 ‘0’
21:13:41	 From 协 崔 : 	if deleteHostHeader {
		delete(req.Header, "Host")
	}卧槽，我看到的是这样的
21:14:40	 From joe sean : 1
21:15:17	 From mai yang : go1.10.1
21:15:20	 From Jayden Yang : deleteHostHeader 这个又是啥
21:15:22	 From mai yang : 我们基于这个版本的。
21:16:03	 From Jayden Yang : // whether Close should stop early
21:19:15	 From 协 崔 : 有个 叫chunked的编码
21:28:40	 From 马嘉 :  再来把
21:29:25	 From 建雷 张 : ...
21:29:28	 From mai yang to Henry Lee (Privately) : 刚刚网络问题中断了。现在继续。
21:29:38	 From mai yang : 刚刚网络问题中断了。现在继续。
21:29:45	 From 大 猫 : 屏幕没共享
21:30:54	 From 马嘉 : ok
21:31:01	 From 建雷 张 : ok
21:32:19	 From Jayden Yang : 你们只能开一个
21:32:24	 From Jayden Yang : 现场只开一个
21:32:34	 From Jayden Yang : 音中音了
21:38:52	 From DW : 什么样的客户端会使用 100-continue 协议呢？
21:39:21	 From Core Code : 这个是询问服务端是否支持大的包吧
21:39:48	 From DW : 浏览器是不是自动实现了这种协议
21:52:15	 From 洪范 郝 : // Buffered returns the number of bytes that can be read from the current buffer.
func (b *Reader) Buffered() int { return b.w - b.r }
21:57:59	 From Jayden Yang : closeNotifyCh is the channel returned by CloseNotify.
21:59:06	 From Jayden Yang : 发送关闭信号给所有的goroutune
21:59:41	 From Jayden Yang : // closeNotifyCh是由CloseNotify返回的频道。
// TODO（bradfitz）：目前（对于Go 1.8）总是这样
//非零。 让这个懒洋洋地再创造一次，因为它曾经是？
22:12:04	 From Jayden Yang : 看不到屏幕
22:12:37	 From jinleileiking : 看不到屏幕

## 延伸阅读

1. https://github.com/golang/go/issues/22128
2. https://tools.ietf.org/html/draft-ietf-httpbis-p2-semantics-26#section-6.2.1
3. https://www.cnblogs.com/tekkaman/archive/2013/04/03/2997781.html
4. https://benramsey.com/blog/2008/04/http-status-100-continue/
5. http://www.ituring.com.cn/article/130844

## 观看视频

{{< youtube id="H3oXjpiOReQ" >}}