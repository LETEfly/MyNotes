> 在进行Vue开发的时候，需要配置项目对应服务端的ip地址，但如果需要在多个服务端间进行切换，通常的做法是:手动修改`vue.config.js`配置文件中的服务端ip并保存，然后重启前端服务。本文将介绍如何在使用npm命令的时自定义服务端地址以及通过选择列表的方式进行选择地址。

# 一、自定义服务端地址

## 1.1 解析获取命令行中输入的服务端ip

创建一个`server-ip.js`文件，用于解析命令行中输入的参数，目前主要是为了获取命令行中输入的服务端ip,并将获取得到的ip暴露出去。

```javascript
/**
* server-ip.js
**/
const configArgv = JSON.parse(process.env.npm_config_argv)

const original = configArgv.original.slice(1);

const ip = original[1] ? original[1].replace(/-/g,''):''

module.exports = ip;
```
## 1.2 更改配置端口的文件

如果cli版本为2.0就在`config/index.js`文件中引用`server-ip.js`即可

```js
// see http://vuejs-templates.github.io/webpack for documentation.
var path = require('path')
//const baseConfig = require('./baseConfig')// 这里存放的是所有的ip但只能有一个被引用，其它的注释了
//const serverIP = require('./customSeverIP.js')
//const PROXY_URL = serverIP || baseConfig.baseUrl
module.exports = {
  dev: {
    env: require('./dev.env'),
    host: 'localhost',
    port: 8080,
    autoOpenBrowser: true,
    autoOpenPage: '/login',
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {
      '/api': {
        target: PROXY_URL,
        pathRewrite: {
          '^/api': '/'
        }
      }
    },
      ...
  }
}
```

而如果cli版本为3.0就在`vue.config.js`文件中引用

```javascript
/**
* vue.config.js
**/

const BASE_URL = process.env.NODE_ENV === 'development' ? '/' : './';
//引用server-ip.js文件获取命令行中输入的ip地址
const serverIP = require('./server-ip.js')
//不破坏原项目的启动方式，如果命令行中没有相关参数，则还是以原来的启动方式进行启动
const PROXY_URL = serverIP || 'http://192.168.1.225'

module.exports = {
    publicPath: BASE_URL,
    lintOnSave:false,
    productionSourceMap:false,
    devServer:{
        host:'0.0.0.0',
        port:8000,
        proxy:{
            '/api/upms':{
                target:PROXY_URL + ':6000/api/upms',
                changeOrigin:true,
                pathRewrite:{
                    '^/api/upms':''
                }
            },
            '/api':{
                target:PROXY_URL + ':3000/api',
                changeOrigin:true,
                pathRewrite:{
                    '^/api':''
                }
            }
        }
    }
}
```

## 1.3 使用

使用就非常方便了，如果项目中`package.json`中添加了一个脚本`"dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",`或是`"serve": "vue-cli-service serve"`，那么现在启动直接在命令行中输入`npm run dev -http://192.168.1.113`或`npm run serve -http://192.168.1.113`,即可启动前端服务并将相应后端ip地址设置为`http://192.168.1.113`。

![20200622165738.png](https://gitee.com/GitWuJun/MyPicBed/raw/master/images/20200622165738.png)

# 二、选择列表脚本

如果在开发的时候经常会来回在几个后端服务中进行切换，那么我们还可以使用[inquirer](https://www.npmjs.com/package/inquirer)写一个可以与命令行交互的服务端ip选择列表。该操作需要以以上配置为前提。

## 2.1 创建脚本文件

创建一个`select-ip.js`文件，其中引入的三个引用需要安装相应的依赖包

```javascript
const inquirer = require('inquirer');
const chalk = require('chalk');
const ls = require('child_process');
const ipList = [
  {
    owner: '187线上',
    ip: 'http://192.168.1.187:8002/api'
  },
  {
    owner: '吴凤州',
    ip: 'http://192.168.1.218:8002/api'
  },
  {
    owner: '吴燕云',
    ip: 'http://192.168.1.211:8002/api'
  },
  {
    owner: '赖明浩',
    ip: 'http://192.168.1.202:8002/api'
  },
  {
    owner:'客户现场',
    ip:'http://11.101.160.22:8002'
  }
];

let ip = null

const promptList = [
  {
    type: 'list',
    message: '选择要连接的服务器ip和端口号:',
    name: 'ips',
    choices: ipList.map(item => {
      return `${item.owner}(${item.ip})`;
    }),
  }
];
inquirer.prompt(promptList).then(answers => {
  let regex = /\((.+?)\)/g;
  ip = regex.exec(answers.ips)[1];
  console.log(`${chalk.green('选择的服务器ip和端口号是:')}${chalk.red(ip)}`);
  //在windows下npm的执行名不同,win32得用‘npm.cmd’
  ls.spawn(process.platform === 'win32' ? 'npm.cmd' : 'npm', ['run', 'dev' `-${ip}`], {
    stdio: 'inherit',
  } );
});
```

## 2.2 配置package.json

在`package.json`配置`"dev:ip": "node ./config/customeSelectIP.js"`或者`"serve:ip": "node ./config/customeSelectIP.js"`，其中dev或serve后的ip可以换成其它名称，如selectip，这不是固定写法。

## 2.3 使用

当我们在命令行中输入`npm run server:ip`时效果大致如下，我们可以移动选择我们要连接的后端服务器地址：

![20200622170659.png](https://gitee.com/GitWuJun/MyPicBed/raw/master/images/20200622170659.png)

