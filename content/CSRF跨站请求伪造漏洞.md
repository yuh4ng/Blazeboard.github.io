+++
title = "CSRF跨站请求伪造漏洞"
date = 2021-06-22

[taxonomies]
categories = ["安全"]
+++

# csrf的定义
攻击者在**一定的攻击条件**下，**利用被攻击者的身份**向服务器发起请求，
<!-- more -->
服务器可以正常解析并**返回正确结果**<br />**![截屏2021-06-22 下午11.31.41.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624375922566-5bac7b83-1e73-4757-a59d-5fc863c610c1.png#align=left&display=inline&height=768&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-22%20%E4%B8%8B%E5%8D%8811.31.41.png&originHeight=768&originWidth=1668&size=677535&status=done&style=none&width=1668)**
# csrf发生的条件

- 被攻击者在【网站A】处于登陆状态
- 必须要在【网站A】同浏览器中打开黑客的链接
- 后台身份验证不严格（例如除cookie外无其他验证）
# csrf能做什么

- 发邮件
- 发消息
- 财产操作比如转账、或者购买商品
- 修改密码
- 删除文章
# csrf防御

- 验证HTTP Referer字段

在HTTP头中有个字段是Refere，记录了HTTP请求的来源地址，在正常的页面请求中，如果用户是点击按钮等正常点击跳转都会将Referer发送给服务器，程序只需拿到值之后进行校验，如果没有或者是其他网站的话就可能是csrf攻击。

- 请求参数添加token验证

csrf之所以能够成功，是因为攻击者可以伪造用户的请求，请求中所有的用户验证信息都在cookie中。我们要找一个不可伪造的参数来校验请求是否伪造，可以在请求中加入随机的token，每次的请求都随机，这样服务端验证token即可。

- 在HTTP头中添加自定义属性

该方法也是使用token验证，和第二种方法不同的是，并不是将token放到HTTP参数中，而是放到HTTP自定义属性中，这样一次性给所有的HTTP请求都添加了token，解决了第二种方法挨个添加的不便，同时token不会记录到地址栏中，减少了token泄漏的风险。

- 验证码

验证码和token验证一样，都具有随机性，可以很好的解决CSRF的问题，但很多时候为了用户体验，网站不能给所有的操作都加上验证码，所以验证码只能作为一种辅助手段。
# DVWA中的CSRF
## LOW
![截屏2021-06-23 上午12.58.35.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624381175607-df3d7d1d-5688-4a8b-a511-2a2090368042.png#align=left&display=inline&height=1746&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-23%20%E4%B8%8A%E5%8D%8812.58.35.png&originHeight=1746&originWidth=2784&size=404646&status=done&style=none&width=2784)<br />输入用户名和密码<br />admin<br />password<br />成功进入<br />![截屏2021-06-23 上午12.59.53.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624381205289-eaf8c6ee-efd7-474a-8151-dc290b059820.png#align=left&display=inline&height=1746&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-23%20%E4%B8%8A%E5%8D%8812.59.53.png&originHeight=1746&originWidth=2784&size=534575&status=done&style=none&width=2784)<br />这里可以修改密码，我们把密码修改成lalala<br />![截屏2021-06-23 上午1.01.07.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624381299690-d9b1ba57-7894-41ee-b0a9-7c3f6e227c81.png#align=left&display=inline&height=1746&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-23%20%E4%B8%8A%E5%8D%881.01.07.png&originHeight=1746&originWidth=2784&size=556136&status=done&style=none&width=2784)<br />注意上图中的URL，我们发现修改的新密码通过URL传递到服务器<br />由此构造钓鱼网页<br />写下如下代码
```html
<html>
	<head>
		<meta charset="UTF-8">
		<title>CSRF</title>
		<script>
		function abc(){
			var im = new Image();
			im.src = "http://127.0.0.1:8081/vulnerabilities/csrf/?password_new=123456&password_conf=123456&Change=Change#";
		}
		</script>
	</head>
		<body>
			<img src="timg.png" width="100%" height="100%" onclick="abc()">
		</body>
</html>
```
找到一张诱导人点击的图片<br />![timg.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624381475919-903d004a-e340-41a6-a44d-dfbbe7e91fa0.png#align=left&display=inline&height=227&margin=%5Bobject%20Object%5D&name=timg.png&originHeight=1326&originWidth=2646&size=4462229&status=done&style=none&width=452)<br />制成网页后，在网站上发布这个网页<br />当受害人在登陆DVWA后，在并没有关掉DVWA的情况下，又用打开DVWA的浏览器打开了我们制作的网页，则受害人的DVWA的密码将会被改成我们的密码，如下<br />假使受害人点开了我们的钓鱼网页<br />![截屏2021-06-23 上午1.10.25.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624381876786-567f93e2-baa8-4bd6-9367-4f195c7a6cb2.png#align=left&display=inline&height=1746&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-23%20%E4%B8%8A%E5%8D%881.10.25.png&originHeight=1746&originWidth=2784&size=6284211&status=done&style=none&width=2784)<br />且经不住诱惑，点击了网页中的任何区域<br />到这里CSRF攻击就完成了<br />之后我们再登陆DVWA，输入密码lalala，显示登陆失败<br />![截屏2021-06-23 上午1.11.39.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624381949457-5d20916e-3388-4b40-8ac0-88c57d4faa65.png#align=left&display=inline&height=102&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-23%20%E4%B8%8A%E5%8D%881.11.39.png&originHeight=154&originWidth=290&size=5630&status=done&style=none&width=193)<br />只有输入我们自己的密码123456才能登陆成功

后台源码
```php

<?php 

if( isset( $_GET[ 'Change' ] ) ) { 
    // Get input 
    $pass_new  = $_GET[ 'password_new' ]; 
    $pass_conf = $_GET[ 'password_conf' ]; 

    // Do the passwords match? 
    if( $pass_new == $pass_conf ) { 
        // They do! 
        $pass_new = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_new ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : "")); 
        $pass_new = md5( $pass_new ); 

        // Update the database 
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';"; 
        $result = mysqli_query($GLOBALS["___mysqli_ston"],  $insert ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

        // Feedback for the user 
        echo "<pre>Password Changed.</pre>"; 
    } 
    else { 
        // Issue with passwords matching 
        echo "<pre>Passwords did not match.</pre>"; 
    } 

    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res); 
} 

?> 
```
## MEDIUM（referer检测绕过）
这里再用LOW中的方法便会失败<br />burp抓包<br />当我们正常发包时，可以发现密码被成功改变<br />![截屏2021-06-23 上午1.27.26.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624382862089-da1626d7-e058-4126-b6ac-1e8d6f76f193.png#align=left&display=inline&height=1762&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-23%20%E4%B8%8A%E5%8D%881.27.26.png&originHeight=1762&originWidth=2696&size=750354&status=done&style=none&width=2696)<br />而我们更改一下发送包中的Referer，便会发现密码更改失败<br />![截屏2021-06-23 上午1.26.18.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624382796236-c94c8b22-64ce-41fc-aa8f-2812193a4c9d.png#align=left&display=inline&height=1762&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-23%20%E4%B8%8A%E5%8D%881.26.18.png&originHeight=1762&originWidth=2696&size=751608&status=done&style=none&width=2696)<br />由此发现服务器端用了referer校验

这里查看一下后台代码
```php
<?php

if( isset( $_GET[ 'Change' ] ) ) {
    // Checks to see where the request came from
    if( stripos( $_SERVER[ 'HTTP_REFERER' ] ,$_SERVER[ 'SERVER_NAME' ]) !== false ) {
        // Get input
        $pass_new  = $_GET[ 'password_new' ];
        $pass_conf = $_GET[ 'password_conf' ];

        // Do the passwords match?
        if( $pass_new == $pass_conf ) {
            // They do!
            $pass_new = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_new ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));
            $pass_new = md5( $pass_new );

            // Update the database
            $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";
            $result = mysqli_query($GLOBALS["___mysqli_ston"],  $insert ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

            // Feedback for the user
            echo "<pre>Password Changed.</pre>";
        }
        else {
            // Issue with passwords matching
            echo "<pre>Passwords did not match.</pre>";
        }
    }
    else {
        // Didn't come from a trusted source
        echo "<pre>That request didn't look correct.</pre>";
    }

    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);
}

?>

```
可以看到，后台源码只判断referer中是否存在含有DVWA网站地址的字符串<br />这里只需要将我们的钓鱼网页的名称改为目标网站的地址即可绕过<br />这里burp怎么也抓不到包，借用margin视频中的截图<br />![截屏2021-06-23 上午1.47.30.png](https://cdn.nlark.com/yuque/0/2021/png/2424924/1624384064585-272baa24-1ffd-4fa8-ba76-cf79071327f6.png#align=left&display=inline&height=886&margin=%5Bobject%20Object%5D&name=%E6%88%AA%E5%B1%8F2021-06-23%20%E4%B8%8A%E5%8D%881.47.30.png&originHeight=886&originWidth=1372&size=766843&status=done&style=none&width=1372)<br />请看包中的referer值
# igaming靶场
这个cms我安装失败了……<br />不过利用方法跟上面那个相同，只不过GET请求，变成了POST请求
# ESPCMS靶场
我累了，不想安装这个了……<br />利用方法与上面igaming相同
