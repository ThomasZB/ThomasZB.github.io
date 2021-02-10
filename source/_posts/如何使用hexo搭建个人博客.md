---
title: 如何使用hexo搭建个人博客
date: 2020-05-15 20:06:42
tags:
---

# 准备工作

我这里使用github+hexo的组合搭建，所以要安装git和hexo，git安装我后面再建个博客写，这里我先介绍hexo的安装
## 下载[nodejs](https://nodejs.org/en/)

一般选择LTS（长期支持版）。安装完后有两个组件，一个是nodejs组件（hexo需要nodejs支持），一个是npm软件管理包。下载下来安装一直点next差不多就可以了。  
下载完后用`node -v`和`npm -v`查看是否安装成功。
## 安装cnpm（换成淘宝源）

由于npm服务器在国外，下载速度慢，所以换成cnpm。使用命令`npm install -g cnpm --registry=https://registry.npm.taobao.org`安装。
## 利用cnpm安装hexo博客

命令`cnpm install -g hexo-cli`，`-g`的意思是全局安装，就可以在命令行使用这个命令（相当于添加环境变量）。安装后使用`hexo -v`查看是否安装成功。

---

# 正式搭建hexo博客

## 创建博客的空间
创建一个存放博客文件的空间，以后博客的管理等等都在该空间
## hexo博客常用命令
* 初始化一个博客：`hexo init`
* 创建一篇博客，创建的博客默认保存在`source/_posts/`，该命令在博客目录执行：`hexo n "xxx"`
* 清理：`hexo clean`
* 生成：`hexo g`
* 启动：`hexo s`

## 将hexo博客同步到github云端
1. 在github上创建博客的仓库，名称为`username.github.io`
2. 安装git部署插件：`cnpm install --save hexo-deployer-git`
3.  配置`_config.yml`文件，在最后的Deployment配置如下  
<div align=left><img src ="https://raw.githubusercontent.com/ThomasZB/picture/master/deployment_set.png"/></div>
4. 部署到云端，现在默认下会使用https上传，输入github账户和密码就行了：`hexo d`

# 主题配置

## 基础主题使用

hexo的主题配置分三步走

* 选择主题并下载

* 在_config.yml文件中更换主题
* 在主题的config文件中修改个人信息

1. 选择主题

   方法：在github上面搜索hexo-thmes

2. 修改_config.yml文件

   ![image-20210210205648241](https://raw.githubusercontent.com/ThomasZB/picture/master/image-20210210205648241.png)

3. 配置该主题

   修改主题文件下的_config.yml文件

## 进阶主题配置

进阶的主题配置参考了[这篇文章](https://blog.csdn.net/nightmare_dimple/article/details/86661502)

1. 点击烟花

   在`/themes/next/source/js/src`下添加如下代码

   ```js
   "use strict";function updateCoords(e){pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),n=[-1,1][anime.random(0,1)]*a;return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){var a={};return a.x=e,a.y=t,a.color="#F00",a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){ctx.globalAlpha=a.alpha,ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}}function animateParticules(e,t){for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},y:function(e){return e.endPos.y},radius:0.1,duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule}).add({targets:a,radius:anime.random(80,160),lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule,offset:0})}function debounce(e,t){var a;return function(){var n=this,i=arguments;clearTimeout(a),a=setTimeout(function(){e.apply(n,i)},t)}}var canvasEl=document.querySelector(".fireworks");if(canvasEl){var ctx=canvasEl.getContext("2d"),numberOfParticules=30,pointerX=0,pointerY=0,tap="mousedown",colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],setCanvasSize=debounce(function(){canvasEl.width=2*window.innerWidth,canvasEl.height=2*window.innerHeight,canvasEl.style.width=window.innerWidth+"px",canvasEl.style.height=window.innerHeight+"px",canvasEl.getContext("2d").scale(2,2)},500),render=anime({duration:1/0,update:function(){ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),updateCoords(e),animateParticules(pointerX,pointerY))},!1),setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)}"use strict";function updateCoords(e){pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),n=[-1,1][anime.random(0,1)]*a;return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){var a={};return a.x=e,a.y=t,a.color="#F00",a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){ctx.globalAlpha=a.alpha,ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}}function animateParticules(e,t){for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},y:function(e){return e.endPos.y},radius:0.1,duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule}).add({targets:a,radius:anime.random(80,160),lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule,offset:0})}function debounce(e,t){var a;return function(){var n=this,i=arguments;clearTimeout(a),a=setTimeout(function(){e.apply(n,i)},t)}}var canvasEl=document.querySelector(".fireworks");if(canvasEl){var ctx=canvasEl.getContext("2d"),numberOfParticules=30,pointerX=0,pointerY=0,tap="mousedown",colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],setCanvasSize=debounce(function(){canvasEl.width=2*window.innerWidth,canvasEl.height=2*window.innerHeight,canvasEl.style.width=window.innerWidth+"px",canvasEl.style.height=window.innerHeight+"px",canvasEl.getContext("2d").scale(2,2)},500),render=anime({duration:1/0,update:function(){ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),updateCoords(e),animateParticules(pointerX,pointerY))},!1),setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)};
   
   ```

   1. 在`\themes\next\layout\_layout.swig`末尾添加

      ```swig
      {% if theme.fireworks %}
         <canvas class="fireworks" style="position: fixed;left: 0;top: 0;z-index: 1; pointer-events: none;" ></canvas> 
         <script type="text/javascript" src="//cdn.bootcss.com/animejs/2.2.0/anime.min.js"></script> 
         <script type="text/javascript" src="/js/src/fireworks.js"></script>
      {% endif %}
      
      ```

   2. 打开主题配置文件，在里面最后写下：

      ```yml
      # Fireworks
      fireworks: true
      ```

2. 动态背景

3. 添加宠物