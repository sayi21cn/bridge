# bridge
前后台彻底分离的中间交互部分的核心文件<br/>
如果没有java基础的童鞋直接把项目下载下来之后,<br/>
只需要查看WebRoot中的view文件夹就行了


/*
 * bridge桥梁的意思,bridge.js主要是为了架起前后端开发的桥梁.<br/>
 * bridge.js类库旨在解决以下问题,这也是bridge.js目的及意义所在.
 * 1.静态页面的发送post请求,会出现500错误,一个项目不可能全是get请求.
 * 2.前后台彻底分离后,静态资源的访问有两种
 * 		2.1相对路径:需要用../来寻找目标资源,开发难度大
 * 		2.2绝对路径.每次需要手动添加根目录名称,根目录只要有变化修改的难度就会很大.
 * 3.对于一些后台项目,会有超时管理.对于页面的零散的ajax请求怎么做到统一管理.
 * 	对超时时返回的数据及时作出响应,比跳转到登录页
 * 4.前后台并行开发,提高工作效率,和产品质量.
 * 5.前端开发不不依附于后端的开发工具,比如MyEclipse,Visual Studio.前端也有很多优秀的开发工具
 * 6.分工协作,前后端分离是必然的趋势,我们不能停留在几十年前的开发模式里,一人独揽全栈,
 * 	  环境变化这么快,不进步,就是退步.
 * 7.前端各种技术日益成熟,比如代码压缩,模块化开发.前后台不分离,再好的技术对我们来说只是名词.
 * 8.彻底分离时,会造成前端请求时出现跨域的尴尬境地,前端人员对于服务器环境的生疏,寸步难行.
 * 9.每个ajax请求都有可能出错,同样的报错代码,总不能在每个ajax代码里都写一遍或重新调用一遍
 * =========================================================================
 * bridge.js是在jQuery的基础上做的二次封装.
 * 1.ajax封装介绍(和jQuery的调用一样):
 * 	1.0.所有的请求被分为三种,因为请求方式不一样,路径格式也不一样.
 * 		跨域+远程==>彻底分离时,发出的请求.
 * 		只远程==>项目整合的时候,没有了跨域问题.
 * 		只本地==>请求前端的本地资源
 * 	1.1.一切post的请求都会被转换成远程请求.
 * 	1.2.get请求即可以访问本地资源,也可以发送远程请求
 * 	1.3.bridge.js不支持ajax的链式写法,因为ajax返回的是promise对象,
 * 		.done(),.fail()无法被被重写封装.(可惜!可惜!)
 *  1.4.若有参数cross:true,就发送远程(+跨域)请求,未定义参数cross或cross:false发送本地请求
 * 	1.4.
 * 		1.4.1.既可以远程也可以本地请求的方法.
 * 			bg.ajax(opt)
 * 			bg.load(url,param,callback).
 * 			bg.get(url,param,callback,type).
 * 			bg.getJSON(url,param,callback).
 * 		
 * 		1.4.2.因为无法添加参数cross:true,只能请求本地资源的方法
 * 			bg.getScript(url,callback).
 * 		
 * 		1.4.3.本地无法发送post请求,只能发送远程请求的方法
 * 			bg.post().
 *
 * bg.inti配置介绍:
 * 		root:"",//根目录,前后端的项目名称(根目录)最好相同,整合的时候比较容易
 *		view:"",//视图.前端的所有编码都放在一个目录下,这个目录就是视图
 *		cross:true,//跨域.开发时这里是true.整合后,改为false.有跨域+远程==>远程
 *  	hostName:"http://172.20.12.243:8080",//主机.跨域时的主机名称
 *		checkSession:false,//是否检测session失效的问题,有些网站不需要检测,但是大部分登录系统都要判断session
 *		noSession:function(data){//sessiong失效时执行的函数
 *		console.log("session失效,跳转页面");
 *		}
 *		
 *	后端配置:新建一个过滤器,
 *	设置:
 *	response.setHeader("Access-Control-Allow-Origin", "http://127.0.0.1:8020");
 *	或:
 *	response.setHeader("Access-Control-Allow-Origin", "*");
 *  前后端整合完毕后,屏蔽这个设置,就不存在跨域以及跨域带来的安全问题了.
 * 
 */
