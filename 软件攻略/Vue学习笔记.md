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
- 安装vue：npm install vue -g
- 安装vue-router：npm install vue-router -g
- 安装vue脚手架：npm install vue-cli -g

## Vue的运行
- 初始化项目 vue init webpack Vue-Project
- 进入项目路径，npm run dev

## 踩坑
使用vue生成项目模板的时候，会报这个错误。
1、不要升级自带的任意组件的版本。
2、node.js不要安装在C盘。
3、安装版本请务必和官网提供的当前稳定版本保持一致！

# Vue学习
## 项目结构
![](/source/img/Vue学习笔记/2020-07-14-01-19-18.png)
- assets 静态资源
- components 子组件
- icons  图标
- router 路由
- store 状态
- styles  样式
- utils 公共方法
- views 视图
- App.vue 主页面组件
- main.js 程序入口
- index.html 首页

## 关键字说明
### Vue实例关键字
```js
<script>
    Vue.component('liitem',{
        props:['content','index'],
        template:'<li @click="listdelete">{{content}}</li>',
        methods:{
            listdelete:function(){
                this.$emit('delete',this.index)
            }
        }
    })

    new Vue({
        el:"#root",
        data:{
            getvalue:"",
            xlist:[]
        },
        methods:{
            showlist:function(){
                this.xlist.push(this.getvalue);
                this.getvalue = "";
            },
            listdelete:function(index){
                this.xlist.splice(index,1)
            }
        }
    })
</script>
```
- name：方法名
- el：挂载点
- data：数据，可以理解成定义内部变量
- methods：方法，可以理解成python的类中的成员方法
- template：模板（组件）
- props：参数接收器，可以往组件中传参。理解成python的__init__()方法。
### Vue组件关键字
```html
<div id="root">
    请输入：<input v-model="getvalue" />
    <button @click="showlist">点击</button>
    <ul>
        <liitem v-for="(item,index) of xlist" :key="index" :content="item" :index="index" @delete="listdelete"></liitem>
    </ul>
    
</div>
```
- v-for="(item,index) of xlist" ：从xlist这个数组中循环值和下标。
- :name="张三" ：定义一个变量叫name，并复制张三，如果张三是个变量，则显示变量值。
- @call="delete"：调用delete这个方法，并给方法取名为call。
### Vue的样式
```css
<style scoped>
h1, h2 {
  font-weight: normal;
}
</style>
```
- scoped：声明这个样式只对当前组件生效
### Vue组件的导入
```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
```
- import 组件名 from 路径/组件名
### Vue重要常用包
- vue-router  路由管理
https://www.cnblogs.com/92xcd/p/9933457.html
- axios  操作接口的
https://www.cnblogs.com/art-poet/p/12502113.html
- vue-cookies  操作cookies的
https://www.jianshu.com/p/60c13168cc8f
- element-ui
https://element.faas.ele.me/#/zh-CN
- 自定义js包
https://blog.csdn.net/qq_29483485/article/details/86605215


### 项目结构
index.html是项目静态文件所有的页面的挂载点。
APP.vue挂载到index.html中。
其他的组件逐级挂载，最终都会统一的挂载到APP.vue中。
在router.index.js中设置路由跳转。

### 坑
路由切换url 中有一个"#"号，它是哪来的呢？这是因为 vue-router 默认使用 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL。#号在浏览器的 URL 中是一个锚点，在当前页改变"#"号的参数，页面会跳转到锚点所在的位置，通过 JavaScript 我们可以获取到"#"号后的参数： location.hash // 获取URL hash location.hash = "#list" //改变URL hash copy 当 URL 改变时，页面不会重新加载。 有这个"#"号，虽然不影响功能，但看起来有点奇怪，处女座一定不能忍。要消除这个"#"号，需要做一下配置，修改 router/index.js： export default new Router({ mode: 'history', routes: routes })


#### 参考资料
vue中使用element-ui进行表单验证
https://blog.csdn.net/sunhuaqiang1/article/details/85235441
第一个 ElementUI 页面 (登录页)
https://www.jianshu.com/p/96143f0917aa
vue中export和export default的使用
https://blog.51cto.com/11871779/2348288
