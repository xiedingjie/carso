##1.响应头Cache-Control控制缓存

**Cache-Control 一个用于定义缓存指令的通用头标。**

一般使用方式


`
//用Cache-Control告诉浏览器有效期 5秒
header("Cache-Control:max-age=5");//等同于Cache-Control:public, max-age=5`


后端告诉浏览器，5秒之内，再次访问页面将从缓存中取数据，无需将请求发送给服务器。

**注意，此情况仅适用于，再次访问页面，浏览器的后退。当刷新页面，时仍会重新请求服务器。**


##2.应头Expires控制缓存

一般使用方式

`
//设置10秒的有效期,时间格式是GMT
//时间是当前服务器时间 + 10秒
$expires = gmdate('D, d M Y H:i:s', time() + 10) . ' GMT'; 
header("Expires:$expires");
`
后端获取当前服务器时间，再加上缓存时间。返回给浏览器。在这个缓存时间范围内，将不再有请求发到服务器。


**注意，当服务器时间和浏览器时间有误差时，缓存将不再准确。所以建议使用Cache-Control的方式缓存，因为此方式时间控制在浏览器，与服务器无关。**


##3通过HTTP 304: Not Modified 控制缓存

一般使用方式

` 
 //响应头Last-Modified
 $lastmodified = filemtime('./cache.php');
 $lastmodifiedGMT = gmdate('D, d M Y H:i:s',$lastmodified). ' GMT';
 header("Last-Modified:$lastmodifiedGMT");
 
 //响应头ETag
 $etag = md5_file('./cache.php');
 header("ETag:$etag");
 
 
 
 
 if (@$_SERVER['HTTP_IF_MODIFIED_SINCE'] == $lastmodifiedGMT ||
     @trim($_SERVER['HTTP_IF_NONE_MATCH']) == $etag) {
         header("HTTP/1.1 304 Not Modified");
         exit;
     }
`

以上方式，在浏览器第一次请求服务器时。取当前文件的修改时间。
把这个时间作为一个标识"Last-Modified",返回给浏览器。同时取这个文件的另外标识"Etag"（可以是文本的md5，文件的某一行，或者其他自定义能区分是否缓存的标识）
"Etag"标识也一起返回给浏览器。
当浏览器第二次请求的时候，会带上两个标识。
通过后端取到这两个标识，再判断是否使用缓存，如果使用缓存返回 `header("HTTP/1.1 304 Not Modified");`，告诉浏览器，文件没有修改过，请使用现有缓存。

**注意，这种方式的缓存和上面1、2两种的方式有点不同。第3种缓存方式，仍会把请求发送给服务器，只是在服务器的开始阶段，判断内容是否有修改，没有修改就使用缓存。而1、2种缓存方式，直接是在浏览器，不会请求到服务器。**