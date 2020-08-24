# 美团server
## 起node服务 解决跨域
1. express生成器
```
- 全局安装
     npm install express-generator - g
- 生成项目
    express 项目名
```
2. 修改入口文件app.js
```js
var proxyRouter = require('./routes/proxy')

// 支持跨域
app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
})
// router
app.use('/api', proxyRouter) // /3000端口/api的请求
```
3. 编写`proxy.js` route
```
- 安装request@2.87.0 插件
```
```js
var express = require('express');
var router = express.Router()
var request = require('request') // request插件

/* GET users listing. */
router.post('/', function(req, res, next) {
  var url = decodeURIComponent(req.body.url); // 前端获取的url
  var data = req.body.params; // 参数

  var options = {
    uri: url,
    method: 'POST',
    headers: {
      'User-Agent': `Mozilla/5.0 (iPhone; CPU iPhone OS 10_3_1 like Mac OS X)
                         AppleWebKit/603.1.30 (KHTML, like Gecko) Version/10.0 Mobile/14E304 Safari/602.1`,
      'Content-Type': 'application/x-www-form-urlencoded',
      'Host': 'i.waimai.meituan.com',
      'Referer': 'http://i.waimai.meituan.com',
      'Cookie': 'terminal=i;xxxxxx4'
    },
    form: data
  };

  request(options, function(error , response, body){
      console.log('body:',body)

      //返回请求结果
      try {
        res.json(JSON.parse(body)); 
      }catch(e){
        res.json({});
      }
  });
});

module.exports = router;

```
4. 默认监听3000端口
**/bin/www.js**