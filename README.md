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

ETAGS和过期规则：etag给每一个文件赋值一个唯一的ID, 资源不变ID不变，资源一旦变化ID变化。过期规则是指一个文件多长时间可以保持不变，以便浏览器不必从服务端下载这些资源。如果对于后台配置不是很熟悉(例如Tomcat, Nginx的配置)，可以关闭etags和显示设置过期规则。如果使用CDN，那么可以不用关心etag，如果是从自己的服务器获取资源，如果服务器做了load balence,那么可能客户端获取的资源id是不同的，就需要从服务器下载。以下是apache .htaccess文件(放在域根目录下)，关闭etag,显示设置过期时间

		<IfModule mod_headers.c>
		  Header unset Etag
		  FileETag none
		</IfModule>
		
		<IfModule mod_expires.c>
		  ExpiresActive On
		  ExpiresDefault "access plus 1 month"
		  ExpiresByType images/x-icon "access plus 1 year"
		  ExpiresByType images/gif "access plus 1 month"
		  ExpiresByType images/png "access plus 1 month"
		  ExpiresByType images/jpg "access plus 1 month"
		  ExpiresByType images/jpeg "access plus 1 month"
		  ExpiresByType text/css "access plus 1 month"
		  ExpiresByType application/javascript "access plus 1 year"
		</IfModule>

注意这里引用外部文件最好的情况是路径是相对的。把JavaScript, CSS, Image等静态资源的路径定义为相对路径，那么在不同的HTML文件中，访问这次资源时，因为路径相同，第一次加载后就被缓存，那么以后都是从缓存中读取。

另外一种缓存代码是使用CDN(Content Delivery Network),内容分发网络主要是将一些静态资源存储在第三方服务器上，这样可以根据访问者的地理位置以及网络情况，选择最优的静态资源服务器，将这些资源分发给访问者。同时也避免了，用户系统因为并发时重复加载这些资源，导致页面崩溃。

为了保证修改后可以得到反映，而不是使用缓存的文件。可以使用版本控制缓存、改变过期规则、或者取消缓存规则。

版本控制缓存: 请求url中加上版本

	<link rel="stylesheet" href="css/style.css?v=1.00" />
	<script src="js/app.js?v=1.00"></script>
	
改变过期时间: 设置.htaccess

    ExpiresByType text/html "access plus 1 day"
    

取消缓存规则: 在html页面头中取消缓存

	<meta http-equiv="cache-control" content="no-cache" /> 


- 压缩代码

使用工具压缩JavaScript, CSS代码

