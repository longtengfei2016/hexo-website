---
title: 用node实现一个简单的滑块拖动验证（极验验证）
date: 2018-01-02 18:22:50
tags: node
comments: true
---
## 一.准备

1.到极验验证注册体验免费版 http://www.geetest.com/ 拿到 id 和 key
2.Node4+

<!-- more -->
## 二.安装环境

1.服务端相关依赖包 
安装所需依赖
express, body-parser, express-session,supervisor；前三个一般node_modules里都有，最好看看有没有安装
``` javascript
npm install supervisor --save 
```
2.服务端安装极验SDK
``` javascript
npm install gt3-sdk 
```
3.客户端下载gt.js库 http://static.geetest.com/static/tools/gt.js 

## 三.服务端代码

这里以登录验证为例
新启一个sever.js服务
``` javascript
var http = require('http');
var express = require('express');
var path = require('path');
var app = express();
var apiRouter = require('./router/router');
var publicPath = path.join(__dirname, 'public');
app.use(express.static(publicPath));
app.use(apiRouter);
app.get('/', function(req, res){
    res.sendFile(publicPath + '/index.html')
})
http.Server(app).listen(8888, function(){
    console.log('running')
})
```
在/router/router.js下实现/login路由，部分代码如下：
``` javascript
var router = require('express').Router();
var Geetest = require('gt3-sdk');
var captcha = new Geetest({
    geetest_id: 'id',       //上文所述id,key
    geetest_key: 'key'
});
router.use(function(req, res, next){
    const m = new Date();
    const dateString = `${m.getFullYear()}/${m.getMonth() +
        1}/${m.getDate()}  ${m.getHours()}:${m.getMinutes()}:${m.getSeconds()}`;
    console.log('Time: ', dateString);
    next();
});
router.post('/login',function(req, res){
    captcha.register(null, function (err, data) {    // 传入null默认不管传入什么参数验证都通过，可根据需要传入用户名密码
        if (err) {
            console.error(err);
            return;
        }
        if (!data.success) {
            res.send(data);
        } else {
            res.json({
                result: 'success',
                data: data
            })
        }
    });
});
module.exports = router;
```
## 四.前端代码

前端调用/login的接口，拿到验证数据，然后对geetest初始化，部分代码如下，这样我们就完成了一个简单的滑块拖动验证
``` javascript
var geetest = $('#geetest');
var initData = {
    gt: '',
    challenge: '',
    offline: 0,
    new_captcha: false
};
function login(name, password){
    $apis.post('/login', {name: name, password: password}, function(r){     //$apis为Ajax的封装函数
        if(r.result==='success'){
            initData.gt = r.data.gt;
            initData.challenge = r.data.challenge;
            initData.offline = !r.data.success;
            initData.new_captcha = r.data.new_captcha;
            init(initData);
        }
    })    
}
function init(data){
    initGeetest(data, function (captchaObj) {
        console.log('init success')
        captchaObj.appendTo(geetest);
    })
}
```
在server.js 目录下运行node server.js就OK了，点击就可以验证
![](geetest.png)

是不是非常的简单？？

github完整代码： https://github.com/longtengfei2016/my-demo/tree/master/node/geetest