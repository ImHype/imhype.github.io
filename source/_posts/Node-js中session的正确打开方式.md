title: session在Node.js中实现
date: 2016-01-13 14:49:02
tags: node.js
---

一提到session的机制，往往前端的小伙伴会觉得神鬼莫测，这是什么啊，竟然能保持登录信息，还能做一些其他较短信息的收录，好神奇！！！
今天我就带大家揭开session神秘的面纱，其实session并不神秘
<!--more-->
### session是什么？
```javascript
var session = {};
var myId = new Date().getTime()+Math.round(Math.random()*1000);
var myData = {
	"用户名":"君羽",
	"密码":"junyuxu"
};
session.add = function(name,json){
	session[name] = json;
}
session.add(myId,myData);
console.log(session[myId]);
```
输出什么？是否会打印出我的信息？
这就是session所做的事情！

我用一条保证唯一不重复的ID来代表用户，myId会存在服务端内存（或硬盘）中，冰倩会采取一定措施来使其过期

### 怎么在Node.js中实现session？
#### 生成sessionId
首先要有一个sessionId，用于具体的区分用户之间到底谁是谁

```javascript
var sessions = {};
var key = "sessionId";
var EXPIRES = 20*60*1000;

```
该ID在用户第一次访问时建立，并在第一次访问时存入服务端内存和到客户端的cookie内部

#### 获取用户cookie
需要有一个获取用户cookie的中间件，代码如下
```javascript
ar parseCookie = function(cookie){
	var cookies = {};
	if(!cookie){
		return cookies
	}else{
		var list = cookie.split(";");
		for (var i = 0; i < list.length; i++) {
			var pair = list[i].split("=");
			cookies[pair[0].trim()]=pair[1];
		};
	}
	return cookies;
}
var cookMid = function (req,res){
	req.cookies = parseCookie(req.headers.cookie);
}
```
在服务器代码内调用cookMid之后可以直观地使用req.cookies读取用户cookie，此处写入对客户端cookie无用
```javascript
http.createServer(function(req,res){
	cookMid(req,res);
	console.log(req.cookies);
}).listen(80);
```
#### 初始化session
然而这只是个概念，我们还没涉及到写入session的部分，此处我封装了一个函数用于初始化session
```javascript
var generate = function(){
	var session = {};
	session.id=(new Date()).getTime()+Math.random();
	session.cookie = {
		expire:(new Date().getTime())+EXPIRES
	};
	sessions[session.id] = session;
	return session;
}
```
有了这个函数，就能进行服务端内存中session的存入了，继续封装在中间件内
```javascript
var sessionMid = function(req,res){
	// 读取cookie
	var sessionId = req.cookies[key];
	
	// 客户端是否存在该cookie值，
	if(!sessionId){
		// 不存在的话初始化内存中的session
		req.session = generate();
	}else{
		var session = sessions[sessionId];
		// 存在cookie的话，但不存在内存中的session （可能是由于你重启了服务器，数据被清空了）
		if(!session)
		{
			req.session = generate();		
			return;
		}
		// 如果你的session没有过期，则重新写入expire属性
		if(session.cookie.expire > (new Date()).getTime()){
			session.cookie.expire = (new Date()).getTime()+EXPIRES;
			req.session = session;
		}else{
			// 如果你的sesssion过期了，重新初始化
			delete session[id];
			req.session = generate();
		}
	}
}
```

#### cookie的格式化
到此处为止，你已经可以修改服务器内存中的session了，接下来要做的是与客户端session的一个同步。
怎么玩呢，你需要封装一个写入cookie的中间件,并把你的sessinId写进去
由于cookie要遵从一定的格式，故做如下代码，用户cookie的一个格式化：

```javascript
var serialize = function(name,val,opt){
	var pairs = [name+'='+(val)];
	opt = opt||{};
	if(opt.maxAge) pairs.push('Max-Age='+opt.maxAge);
	if(opt.domain) pairs.push('Domain='+opt.domin);
	if(opt.path) pairs.push('Path='+opt.path);
	if(opt.expires) pairs.push('Expires='+opt.expires);
	if(opt.httpOnly) pairs.push('HttpOnly');
	if(opt.secure) pairs.push(' ');
	return pairs.join(';');
}
```
有了这个函数就能轻易的制造符合规范的cookie了，如
```javascript
var test = serialize("name","xujunyu",{
	domain:"127.0.0.1",
	path:"/"
});
console.log(test);
// name=xujunyu;Domain=127.0.0.1;Path=/
```

#### 修改res.writeHead
然后，是否会有个问题？
现在生成cookie格式字符串的函数也有，可是，用在哪呢
有两种方式，
* 再封装一个设置头函数，然后必须在writeHead之前调用
* 重写res.writeHead，在响应建立头之前，把cookie 用 res.setHeader(cookieString)的方式写入
显然我更喜欢第二种，无形之中封装好的，第一种实在不方便。此处还是以一个中间件的形式

```javascript
function Wheader(req,res){
	var writeHead = res.writeHead;
	res.writeHead = function(){
		var cookies =res.getHeader("Set-Cookie");
		var session = serialize(key, req.session.id);
		cookies = Array.isArray(cookies)?cookies.concat(session):[cookies,session];
		res.setHeader('Set-Cookie',cookies);
		return writeHead.apply(this,arguments);
	}
}
```
#### 使用
到此代码已经可以跑了,createServer内部的代码应该是这样的
```javascript
http.createServer(function(req,res){
	cookMid(req,res);
	sessionMid(req,res);
	Wheader(req,res);
	if(req.url=="/"){
		if(!req.session.isVisit){
			req.session.isVisit=1;
			res.end("first");
		}else{
			req.session.isVisit=req.session.isVisit+1;
			res.writeHead(200);
			res.end("again");
		}	
	}
}).listen(80);
```

我用了三个中间件，在实行具体代码之前，依次载入，并在req.session和req.cookies里面绑定你的个人信息
### 总结
这一节讲了点啥呢，讲了一个人如何对应到你在星巴克的信息
你需要一个vip号，vip号存在你的vip卡里面里面，掏出会员卡你就知道你的会员号
星巴克里存着你的个人信息，但是这么多同学，怎样进行一一对应了？
就是靠vip号。
你是怎样在星巴克享受会员待遇呢

* 首先，如果还你不是会员，你得先办会员吧，办理一张代表你会员身份的会员卡(建立cookie)
* 之后每次你到星巴克消费的时候，你先掏出会员卡，并且成功让星巴克通过你的会员号对应到你的用户信息上(通过cookie找到你在服务器上对应的信息)
* 然后你要消费，星巴克通过你的会员号，找到的用户信息上，增加消费记录，这是和你的会员卡没关系的(之后所有的修改都是在服务器上进行操作，与你的cookie没有半毛钱关系)

当然关于超时的地方，这个例子上没有很好的展现。这个例子的作用是理解会员号这个思路
<small>注：具体的代码在我的github仓库里的一个小轮子内部，点击此处查看</small>