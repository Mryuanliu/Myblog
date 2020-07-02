# Node.js:使用crypto加密模块来加密后台管理员的密码

#### 背景
* 哈哈，其实俺也不知道开头为啥要用背景这两字，可能是有逼格把。因为要完成一个后台管理功能，但是实际上考虑到管理员就只有那几个人，好吧，应该就一个人。没必要(懒得)去创建一张表。所以就突发奇想，为啥我不直接存在一个json文件里呢？但是考虑到安全问题，所以就想把加密之后的密码存在json文件里。由于自己使用的node。所以自然而然就想到了crypto这个加密模块。还是第一次使用他，所以决定记录一下。

优点：
**防内也防外，就算被攻击，也拿不到管理员的明文密码...**
#### crypto模块
    
```javascript
     nodejs提供了众多的加密解密的封装器。
     这个由于自己水平不够，害怕讲不清楚，所以就暂时拿来主义吧。
     是有点坑哈，各位大佬抬一手...劳烦大家看看官方文档

```
#### 项目中的使用
第一步，肯定是要引入咱们的加密模块crypto，然后选择加密方式。
```javascript
//crypto.js
const crypto=require('crypto');
module.export={
    md5(buffer){
        let data=crypto.createHash('md5');
        data.update(buffer);
        return obj.digest('hex');
    }
}

```
网上很多网站都可以进行md5的加密。这个时候你可以随便输入一串字符（稍微还是要记下），然后进行md5加密。最后保存32位的密文，将他们存入json文件。

```json
//adminLogin.json
[
    {"username":"haha","password":"1057e654ff00001fb5ed8d1ad4c3f46b"},
    {"username":"hehe","password":"01ddae4032e17a1c338baac9c4322b30"},
    {"username":"gugu","password":"c143c1a80a4ae60298b655595581efa4"}
]
```
创建服务
```javascript
const koa=require('koa');
const Router=require('koa-router');
const body=require('koa-better-body');
const path=require('path');
let server=new koa();
let router=new Router();
server.listen(8080);
server.use(body());
console.log(`服务器启动在8080端口`);
//路由
router.use('/admin',require('./routers/admin'));
server.use(router.routes());
```

将该模块导入到管理员登陆下的index.js
```javascript
const Router=require('koa-router');
const fs=require('await-fs');
const path=require('path');
const common=require('./crypto');
 router.post('/login',async ctx=>{
        let {username,password}=ctx.request.fields;
        。
        let admins=JSON.parse((await fs.readFile(
            path.resolve(__dirname,'./adminLogin.json')
        )).toString());
        function findAdmin(username){
            let a=null;
            admins.forEach(admin=>{
                if(admin.username==username)
                a=admin;
            })
            return a;
        }
        let admin=findAdmin(username);
  if(!admin){
    ctx.body='管理员用户名错误';  
  
  }else if(admin.password!=common.md5(password)){
    ctx.body='管理员密码错误';  
  }else{
    ctx.body="登陆成功"；
  }
});
```
前端页面就是一个超级简略的表单，就不贴出来了。

嗯，最后的话应该也差不多了，就这样了。
万岁千山总是情，给小弟点赞行不行？
如果真的不太行，留言鼓励也还行！再次谢过各位看客大老爷

![](https://user-gold-cdn.xitu.io/2019/10/21/16dee9f405f8669e?w=500&h=313&f=jpeg&s=20467)