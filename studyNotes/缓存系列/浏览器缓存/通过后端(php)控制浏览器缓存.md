##响应头Cache-Control控制缓存

**Cache-Control 一个用于定义缓存指令的通用头标。**

    max-age=[秒] — 执行缓存被认为是最新的最长时间。类似于过期时间，这个参数是基于请求时间的相对时间间隔，而不是绝对过期时间，[秒]是一个数字，单位是秒：从请求时间开始到过期时间之间的秒数。
    s-maxage=[秒] — 类似于max-age属性，除了他应用于共享（如：代理服务器）缓存
    public — 标记认证内容也可以被缓存，一般来说： 经过HTTP认证才能访问的内容，输出是自动不可以缓存的；
    private在服务器设置了private比如：Cache-Control:private, max-age=60的情况下，表示只有用户的浏览器可以缓存private响应，不允许任何中继Web代理对其进行缓存 – 例如，用户浏览器可以缓存包含用户私人信息的HTML网页，但是CDN不能缓存。
    no-cache — 强制每次请求直接发送给源服务器，而不经过本地缓存版本的校验。这对于需要确认认证应用很有用（可以和public结合使用），或者严格要求使用最新数据的应用（不惜牺牲使用缓存的所有好处）；
    no-store — 不做缓存
    must-revalidate — 告诉缓存必须遵循所有你给予副本的新鲜度。使用缓存的时候每次必须发送请求到服务器校验，比如通过If-Modified-Since或If-None-Match
    proxy-revalidate — 和 must-revalidate类似，除了他只对缓存代理服务器起作用，举例:Cache-Control: max-age=3600, must-revalidate
    
`
<?php
header("Content-type:text/html;charset=utf-8");

//用Cache-Control告诉浏览器有效期 5秒
header("Cache-Control:max-age=5");//等同于Cache-Control:public, max-age=5
$curr_time = date('Y-m-d H:i:s');
echo '服务器时间:'.$curr_time;
?>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Cache</title>
</head>
<body>
<br>
    Time:<?php  echo $curr_time; ?>
<br>
    <a href='cache_sample.php'>刷新时间</a>
</body>
</html>
`