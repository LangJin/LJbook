# Vue环境搭建
公司的前端小姐姐真的是太菜了，我作为测试我都看不下去了，所以打算学一学Vue，然后把她开了算了。
## Node.js的安装
下载地址：https://npm.taobao.org/mirrors/node/
>说明：Node.js 是一个基于Chrome JavaScript 运行时建立的一个平台。
>Node.js主要包含了npm\webpack\vue-cli三个核心工具。
>npm是Node.js的包管理工具。
>webpack是打包工具。
>vue-cli是生成Vue项目模板的工具。

Node.js的安装非常的简单，直接默认下一步即可。
安装完成后，在cmd输入 node -v 和npm -v检查是否安装成功
在nodejs文件夹下面分别创建两个文件夹node_global和node_cache，用来存放npm的仓库和缓存。
并且把node_global添加到环境变量中。
```cmd
npm config set prefix "D:\program files\nodejs\node_global"  # 把npm仓库设置到node_global
npm config set cache "D:\program files\nodejs\node_cache"  # 把npm缓存设置到node_cache
npm config set registry=http://registry.npm.taobao.org   # 把npm镜像设置到淘宝
```

## Vue的安装
安装vue：npm install vue -g
安装vue-router：npm install vue-router -g
安装vue脚手架：npm install vue-cli -g

## Vue的运行
进入项目路径，npm run dev