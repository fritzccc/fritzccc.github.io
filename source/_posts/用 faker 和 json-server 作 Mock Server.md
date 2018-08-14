---
title: 用 Faker 和 json-server 作 Mock Server
date: 2018-08-14 22:06:54
categories: 
- Frontend
tags:
- mock
- json-server
- faker
- axios
---

这俩模块可以在段时间内搭建一个Restful API，用来让前段代码跑axios等http请求做测试。



<!--more-->




### Faker.js

----

[Github](https://github.com/marak/Faker.js/)、[Demo](https://rawgit.com/Marak/faker.js/master/examples/browser/index.html)

可以mock的数据类型种类之全，让人感叹，用来做黑盒测试再适合不过。
```bash
npm i -D faker
```
简单的引入

```javascript
var faker = require('faker');

var randomName = faker.name.findName(); // Rowan Nikolaus
var randomEmail = faker.internet.email(); // Kassandra.Haley@erich.biz
var randomCard = faker.helpers.createCard(); // random contact card containing many properties
```

不仅如此，还可以本地化生成有地域性的数据：

```JavaScript
// sets locale to zh_CN
faker.setLocale("zh_CN");
// or
faker.locale = "zh_CN";
// or in Node
var faker = require('faker/locale/zh_CN');
```

然后就可以愉快的在接下来要介绍的json-server中使用了。



### json-server

----

[Github](https://github.com/typicode/json-server)

> Get a full fake REST API with **zero coding** in **less than 30 seconds** (seriously) 

这介绍也是666……

具体Doc看GitHub地址也够用了，这里记录下自己常用的使用方式

首先安装模块

```bash
npm i -D json-server
```

然后在自己的目录下 (以 `test/api/` 下为例) 创建 `db.js` 、 `route.js` 、

 `server.js` 3个文件。顾名思义， `db.js` 用语提供或伪造数据；而 `route.js` 则是记录了我们自定义的路由信息；最后 `server.js` 是启动`json-server` 的配置文件啦。

> db.js

使用刚才的Faker.js大量生成数据，注意，每次启动 `json-server` 的时候，数据都是不同的哦：

```javascript
const faker = require('faker/locale/zh_CN');
const moment = require('moment')
function genComment(){
  let comment=[];
  //生成随机0-10条评论
  for (let i = 0; i < faker.random.number(10); i++) {
    comment.push({
      comment_id:moment(faker.date.past()).format('YYYYMMDDhhmmss'),
      user_id:faker.random.uuid(),
      user_nm:faker.name.findName(),
      comment:faker.lorem.words()
    });
  }
  return comment;
}

module.exports = {
  comment：genComment()
}
```

> route.js

比如说我的项目实际请求地址的 base_url 是 `https://aabbcc.com/` 而获取评论则是 `api/ver1.0.0/comment` 那么就需要我们自定义路由信息

```javascript
var route={
  'api/ver1.0.0/comment':'/comment',
}
module.exports = route
```

即可，是不是很简单？

> server.js

先上基本配置

```javascript
const jsonServer = require('json-server');
const dbfile = require('./db');
const route = require('./route')
const server = jsonServer.create();
const router = jsonServer.router(dbfile);
const middlewares = jsonServer.defaults();

server.use(middlewares)
server.use(jsonServer.bodyParser)

//使用我们自定义的路由
server.use(jsonServer.rewriter(route));
server.use(router);

//设置端口
server.listen(3003, function () {
  console.log('JSON Server is running');
})
```

值得一提的是，`json-server` 是基于Rest API的，也就意味着 `POST` 请求是被用来更新或者提交数据的，和我们以往的不同，当然我们也可以自定义改写其默认的 `POST` 为 `GET`：

```javascript
server.use(function (req, res, next) {
  if (req.method === 'POST') {
    //将请求信息打印出来，便于调试
    console.log('body=>', req.body);
    req.method = 'GET';
    req.query = req.body;
  }
  next();
})
```

如此一来，所有的 `POST` 方法都将被以 `GET` 的方式执行，如果你不愿意，当然也可以指定某一个请求使用 `GET` :

```javascript
server.post('/ver1.0.0/file', function (req, res,next) {
  req.method = 'GET';
  req.query = req.body;
  next();
  //也可以直接返回数据
  //res.jsonp({username: 'file', token: '6666'})
})
```

大功告成！

最后别忘记在 `package.json` 里添加脚本：

```json
"mock":node test/api/server.js
```

 以后就可以直接 `npm run mock` 启动啦！