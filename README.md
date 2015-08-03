##How to make JavaScript faster

###压缩以及缓存JavaScript代码

- 缓存代码

一般糟糕的做法是将JavaScript直接嵌入在HTML Document中，例如以下代码:

		<script type="text/javascript">
		//...
		</script>
		
这样每次请求该页面都会下载例如10K的HTML页面，浏览器会从上到下顺序加载css,js并解析执行。应该讲这部分代码作为一个外部引用JavaScript

		<script src="..."></script>
		
这样浏览器第一次加载完成后，会缓存在浏览器端，当再次请求包含<script src="..."></script>页面时，HTTP请求头返回304, 浏览器会从客户端本地读取缓存，并执行。最优的情况是客户端缓存，服务端也缓存这个脚本。

注意这里引用外部文件最好的情况是路径是相对的。把JavaScript, CSS, Image等静态资源的路径定义为相对路径，那么在不同的HTML文件中，访问这次资源时，因为路径相同，第一次加载后就被缓存，那么以后都是从缓存中读取。

另外一种缓存代码是使用CDN(Content Delivery Network),内容分发网络主要是将一些静态资源存储在第三方服务器上，这样可以根据访问者的地理位置以及网络情况，选择最优的静态资源服务器，将这些资源分发给访问者。同时也避免了，用户系统因为并发时重复加载这些资源，导致页面崩溃。

- 压缩代码
