
# NodeJS定时爬取微博热搜榜单，热点信息不要错过

### 前情提要
    不知道从啥时候开始，点开微博热搜变成我的下意识操作。虽然里面很多娱乐热搜的内容都不太感兴趣。但是还是忍不住瞅上两眼。所以乎。
    一日，晴，闲于家中，百无聊赖，故爬之。（嘿嘿，其实是想学点东西）
    
### 项目前期准备
**工欲善其事，必先利其器**
#### cherrio
* 提到node爬虫，这个cherrio第三方包不得不用到。和jquery的语法基本上是如出一辙，操作和选择页面的DOM节点十分简单，咱们来简单的举几个例子，剩下的需要大家自己去看看文档。
```javascript
    //示例来自官网。
    //html
    <ul id="fruits">
    <li class="apple">Apple</li>
    <li class="orange">Orange</li>
    <li class="pear">Pear</li>
    </ul>
    //加载html
    const $ = cheerio.load(html);
    //常用的举例
    $('.apple', '#fruits').text()
    //=> Apple
    $('ul .pear').attr('class')
    //=> pear
    $('#fruits').find('li').length
    //=> 3
    var fruits = [];
    $('li').each(function(i, elem) {
    fruits[i] = $(this).text();
    });
    fruits.join(', ');
    //=> Apple, Orange, Pear
```

#### 定时器
* 由于咱们要定时爬取微博热搜，所以需要一个定时器，咱们这里用到一个十分好用的第三方包，我们也是简单的看下项目中需要用到的语法。
```javascript
const schedule=require('node-schedule');
const ruler=new schedule.RecurrenceRule();
ruler.hour=[0,new schedule.Range(0,23)];//每小时执行一次。
schedule.scheduleJob(ruler,function(times){
   //定时执行的操作
})
//ruler.minute=[0,new schedule.Range(0,59)];每分钟执行一次
//ruler.second=[0,new schedule.Range(0,59)];每秒执行一次
```
### 爬取微博热搜
#### 分析页面结构
* 打开微博热搜的界面，分析页面的DOM结构，我们需要爬取第一页热搜的名称和a标签里面的链接地址。在tbody>tr>第二个td。找到自己需要爬取的DOM节点的位置之后，便可以开始写代码。
![](https://user-gold-cdn.xitu.io/2019/10/22/16def291685c1f6c?w=1540&h=734&f=png&s=242367)
```javascript
const https=require("https")
const cheerio=require("cheerio")
const fs=require("fs")

module.exports=function weibo(){
//爬取微博热搜
const url="https://s.weibo.com/top/summary?cate=realtimehot"
const address="https://s.weibo.com"
https.get(url,(res)=>{
    var htmlData=''
    res.on('data',(chunk)=>{
        htmlData+=chunk;
        let shuju=filtersContent(htmlData);
        fs.writeFileSync('01.txt',shuju.join('/n'),function(err){
            if(err){
                console.log('写入文件失败')
            }else{
                console.log('成功写入')
            }
        })
    })
}).on("error",function(){
    console.log('爬取错误')
})

function filtersContent(htmlData){
    var $=cheerio.load(htmlData)
    var content=$("tbody")
    var a=[];
    content.find("tr").each(function(item,b){
        var item=$(this).children().eq(1).children();
        var name=item.text();
        var srcs=item.attr('href');
        var src=address+srcs;
        var data=name+'-'+src;
        console.log(src,data);
        a.push(data);        
    })

```

![](https://user-gold-cdn.xitu.io/2019/10/22/16def3bca0f946b9?w=1430&h=215&f=png&s=66039)
* 将热搜数据写入文件做持久化，最后封装成一个函数，放入定时器中定时爬取热搜并写入文件。
### 搭建服务器
* 搭建服务器，这里我们使用KOA搭建服务器,最后使用ejs将文件中存储的热搜数据渲染到页面上.
```javascript
const koa=require('koa');
const Router=require('koa-router');
const ejs=require('koa-ejs');
const path=require('path');

let server=new koa();
let router=new Router();

server.listen(3000);
console.log(`服务器启动在3000端口`);
ejs(server,{
    root: path.resolve(__dirname, 'template'),
    layout: false,
    viewExt: 'ejs',
    cache: false,
    debug: false
})
//路由
router.use('/',require('./routers/www'));
server.use(router.routes());
```
* 前端界面：用bootstrap简单的弄了界面。
### 项目完成
简单展示：

![](https://user-gold-cdn.xitu.io/2019/10/22/16def3ecf527e1de?w=1576&h=731&f=png&s=97572)
最后的最后，希望各位看客大大们赏俺几个赞和关注，在此谢过。