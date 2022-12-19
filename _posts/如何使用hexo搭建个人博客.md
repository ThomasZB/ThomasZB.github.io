---

title: 如何使用hexo搭建个人博客
date: 2020-05-15 20:06:42
tags:
categories: 程序猿基础技能

---

<p align="center">
    这就是这个博客的来源
</p>

<!--more-->

# 准备工作

我这里使用github+hexo的组合搭建，所以要安装git和hexo，git安装我后面再建个博客写，这里我先介绍hexo的安装

官方教程：[文档 | Hexo](https://hexo.io/zh-cn/docs/)

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
* 创建一篇博客：`hexo n "xxx"`，创建的博客默认保存在`source/_posts/`
* 清理：`hexo clean`
* 生成：`hexo g`
* 启动：`hexo s`
* 删除博客：删除博客，然后清理、生成、启动

## 将hexo博客同步到github云端
1. 在github上创建博客的仓库，名称为`username.github.io`
2. 安装git部署插件：`cnpm install --save hexo-deployer-git`
3. 配置`_config.yml`文件，在最后的Deployment配置如下  
   * 这里要注意，如如果使用ssh方式提交（不想输入密码）需要修改git仓库的地址为ssh方式
<div align=left><img src ="https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193442.png"/></div>

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

   ![](https://pic-1302177449.cos.ap-chongqing.myqcloud.com//blog_pic/20210308193554.png)

3. 配置该主题

   修改主题文件下的_config.yml文件

## 进阶主题配置

进阶的主题配置参考了[这篇文章](https://blog.csdn.net/nightmare_dimple/article/details/86661502)

1. 点击效果

   ​	1. 在主题的`_config.yml`下添加动态配置

   ```yaml
   cursor_effect:
     enabled: true
     type: love  # fireworks：礼花 | explosion：爆炸 | love：浮出爱心 | text：浮出文字
   ```

   2. 在`\themes\next\layout\_layout.swig`末尾添加，用于动态配置点击效果

   ```swig
   {% if theme.cursor_effect %}
     {% if theme.cursor_effect.type == "fireworks" %}
       <script src="/js/cursor/fireworks.js"></script>
     {% elseif theme.cursor_effect.type == "explosion" %}
       <canvas class="fireworks" style="position: fixed;left: 0;top: 0;z-index: 1; pointer-events: none;" ></canvas>
       <script src="//cdn.bootcss.com/animejs/2.2.0/anime.min.js"></script>
       <script src="/js/cursor/explosion.min.js"></script>
     {% elseif theme.cursor_effect.type == "love" %}
       <script src="/js/cursor/love.min.js"></script>
     {% elseif theme.cursor_effect.type == "text" %}
       <script src="/js/cursor/text.js"></script>
     {% endif %}
   {% endif %}
   ```

   3. 创建以下4个文件，并复制到`\themes\next\source\js\coursor`下

      * fireworks.js

      ```js
      class Circle {
        constructor({ origin, speed, color, angle, context }) {
          this.origin = origin
          this.position = { ...this.origin }
          this.color = color
          this.speed = speed
          this.angle = angle
          this.context = context
          this.renderCount = 0
        }
      
        draw() {
          this.context.fillStyle = this.color
          this.context.beginPath()
          this.context.arc(this.position.x, this.position.y, 2, 0, Math.PI * 2)
          this.context.fill()
        }
      
        move() {
          this.position.x = (Math.sin(this.angle) * this.speed) + this.position.x
          this.position.y = (Math.cos(this.angle) * this.speed) + this.position.y + (this.renderCount * 0.3)
          this.renderCount++
        }
      }
      
      class Boom {
        constructor ({ origin, context, circleCount = 16, area }) {
          this.origin = origin
          this.context = context
          this.circleCount = circleCount
          this.area = area
          this.stop = false
          this.circles = []
        }
      
        randomArray(range) {
          const length = range.length
          const randomIndex = Math.floor(length * Math.random())
          return range[randomIndex]
        }
      
        randomColor() {
          const range = ['8', '9', 'A', 'B', 'C', 'D', 'E', 'F']
          return '#' + this.randomArray(range) + this.randomArray(range) + this.randomArray(range) + this.randomArray(range) + this.randomArray(range) + this.randomArray(range)
        }
      
        randomRange(start, end) {
          return (end - start) * Math.random() + start
        }
      
        init() {
          for(let i = 0; i < this.circleCount; i++) {
            const circle = new Circle({
              context: this.context,
              origin: this.origin,
              color: this.randomColor(),
              angle: this.randomRange(Math.PI - 1, Math.PI + 1),
              speed: this.randomRange(1, 6)
            })
            this.circles.push(circle)
          }
        }
      
        move() {
          this.circles.forEach((circle, index) => {
            if (circle.position.x > this.area.width || circle.position.y > this.area.height) {
              return this.circles.splice(index, 1)
            }
            circle.move()
          })
          if (this.circles.length == 0) {
            this.stop = true
          }
        }
      
        draw() {
          this.circles.forEach(circle => circle.draw())
        }
      }
      
      class CursorSpecialEffects {
        constructor() {
          this.computerCanvas = document.createElement('canvas')
          this.renderCanvas = document.createElement('canvas')
      
          this.computerContext = this.computerCanvas.getContext('2d')
          this.renderContext = this.renderCanvas.getContext('2d')
      
          this.globalWidth = window.innerWidth
          this.globalHeight = window.innerHeight
      
          this.booms = []
          this.running = false
        }
      
        handleMouseDown(e) {
          const boom = new Boom({
            origin: { x: e.clientX, y: e.clientY },
            context: this.computerContext,
            area: {
              width: this.globalWidth,
              height: this.globalHeight
            }
          })
          boom.init()
          this.booms.push(boom)
          this.running || this.run()
        }
      
        handlePageHide() {
          this.booms = []
          this.running = false
        }
      
        init() {
          const style = this.renderCanvas.style
          style.position = 'fixed'
          style.top = style.left = 0
          style.zIndex = '999999999999999999999999999999999999999999'
          style.pointerEvents = 'none'
      
          style.width = this.renderCanvas.width = this.computerCanvas.width = this.globalWidth
          style.height = this.renderCanvas.height = this.computerCanvas.height = this.globalHeight
      
          document.body.append(this.renderCanvas)
      
          window.addEventListener('mousedown', this.handleMouseDown.bind(this))
          window.addEventListener('pagehide', this.handlePageHide.bind(this))
        }
      
        run() {
          this.running = true
          if (this.booms.length == 0) {
            return this.running = false
          }
      
          requestAnimationFrame(this.run.bind(this))
      
          this.computerContext.clearRect(0, 0, this.globalWidth, this.globalHeight)
          this.renderContext.clearRect(0, 0, this.globalWidth, this.globalHeight)
      
          this.booms.forEach((boom, index) => {
            if (boom.stop) {
              return this.booms.splice(index, 1)
            }
            boom.move()
            boom.draw()
          })
          this.renderContext.drawImage(this.computerCanvas, 0, 0, this.globalWidth, this.globalHeight)
        }
      }
      
      const cursorSpecialEffects = new CursorSpecialEffects()
      cursorSpecialEffects.init()
      ```

      * explosion.min.js

      ```js
      "use strict";function updateCoords(e){pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),n=[-1,1][anime.random(0,1)]*a;return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){var a={};return a.x=e,a.y=t,a.color="#F00",a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){ctx.globalAlpha=a.alpha,ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}}function animateParticules(e,t){for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},y:function(e){return e.endPos.y},radius:0.1,duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule}).add({targets:a,radius:anime.random(80,160),lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule,offset:0})}function debounce(e,t){var a;return function(){var n=this,i=arguments;clearTimeout(a),a=setTimeout(function(){e.apply(n,i)},t)}}var canvasEl=document.querySelector(".fireworks");if(canvasEl){var ctx=canvasEl.getContext("2d"),numberOfParticules=30,pointerX=0,pointerY=0,tap="mousedown",colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],setCanvasSize=debounce(function(){canvasEl.width=2*window.innerWidth,canvasEl.height=2*window.innerHeight,canvasEl.style.width=window.innerWidth+"px",canvasEl.style.height=window.innerHeight+"px",canvasEl.getContext("2d").scale(2,2)},500),render=anime({duration:1/0,update:function(){ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),updateCoords(e),animateParticules(pointerX,pointerY))},!1),setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)}"use strict";function updateCoords(e){pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),n=[-1,1][anime.random(0,1)]*a;return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){var a={};return a.x=e,a.y=t,a.color="#F00",a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){ctx.globalAlpha=a.alpha,ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}}function animateParticules(e,t){for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},y:function(e){return e.endPos.y},radius:0.1,duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule}).add({targets:a,radius:anime.random(80,160),lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule,offset:0})}function debounce(e,t){var a;return function(){var n=this,i=arguments;clearTimeout(a),a=setTimeout(function(){e.apply(n,i)},t)}}var canvasEl=document.querySelector(".fireworks");if(canvasEl){var ctx=canvasEl.getContext("2d"),numberOfParticules=30,pointerX=0,pointerY=0,tap="mousedown",colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],setCanvasSize=debounce(function(){canvasEl.width=2*window.innerWidth,canvasEl.height=2*window.innerHeight,canvasEl.style.width=window.innerWidth+"px",canvasEl.style.height=window.innerHeight+"px",canvasEl.getContext("2d").scale(2,2)},500),render=anime({duration:1/0,update:function(){ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),updateCoords(e),animateParticules(pointerX,pointerY))},!1),setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)};
      ```

      * love.min.js

      ```js
      !function(e,t,a){function n(){c(".heart{width: 10px;height: 10px;position: fixed;background: #f00;transform: rotate(45deg);-webkit-transform: rotate(45deg);-moz-transform: rotate(45deg);}.heart:after,.heart:before{content: '';width: inherit;height: inherit;background: inherit;border-radius: 50%;-webkit-border-radius: 50%;-moz-border-radius: 50%;position: fixed;}.heart:after{top: -5px;}.heart:before{left: -5px;}"),o(),r()}function r(){for(var e=0;e<d.length;e++)d[e].alpha<=0?(t.body.removeChild(d[e].el),d.splice(e,1)):(d[e].y--,d[e].scale+=.004,d[e].alpha-=.013,d[e].el.style.cssText="left:"+d[e].x+"px;top:"+d[e].y+"px;opacity:"+d[e].alpha+";transform:scale("+d[e].scale+","+d[e].scale+") rotate(45deg);background:"+d[e].color+";z-index:99999");requestAnimationFrame(r)}function o(){var t="function"==typeof e.onclick&&e.onclick;e.οnclick=function(e){t&&t(),i(e)}}function i(e){var a=t.createElement("div");a.className="heart",d.push({el:a,x:e.clientX-5,y:e.clientY-5,scale:1,alpha:1,color:s()}),t.body.appendChild(a)}function c(e){var a=t.createElement("style");a.type="text/css";try{a.appendChild(t.createTextNode(e))}catch(t){a.styleSheet.cssText=e}t.getElementsByTagName("head")[0].appendChild(a)}function s(){return"rgb("+~~(255*Math.random())+","+~~(255*Math.random())+","+~~(255*Math.random())+")"}var d=[];e.requestAnimationFrame=function(){return e.requestAnimationFrame||e.webkitRequestAnimationFrame||e.mozRequestAnimationFrame||e.oRequestAnimationFrame||e.msRequestAnimationFrame||function(e){setTimeout(e,1e3/60)}}(),n()}(window,document);
      ```

      * text.js

      ```js
      var a_idx = 0;
      jQuery(document).ready(function($) {
        $("body").click(function(e) {
          var a = new Array("富强", "民主", "文明", "和谐", "自由", "平等", "公正" ,"法治", "爱国", "敬业", "诚信", "友善");
          var $i = $("<span/>").text(a[a_idx]);
          var x = e.pageX,
            y = e.pageY;
          $i.css({
            "z-index": 99999,
            "top": y - 28,
            "left": x - a[a_idx].length * 8,
            "position": "absolute",
            "color": "#ff7a45"
          });
          $("body").append($i);
          $i.animate({
            "top": y - 180,
            "opacity": 0
          }, 1500, function() {
            $i.remove();
          });
          a_idx = (a_idx + 1) % a.length;
        });
      });
      ```

2. 新建页面

   * 添加自己的分类页面`hexo new page categories`

   * 修改categories里面的index.md文档，改成如下

     ```html
     ---
     title: 分类
     date: 2021-03-03 22:39:24
     type: categories
     ---
     ```

   * 给自己的文章添加categories属性，并且clean后重新生成

3. 动态背景

4. 添加宠物

   * 安装插件

     > npm install -save hexo-helper-live2d

   * 选择一个宠物

     * 宠物查看网址（https://huaji8.top/post/live2d-plugin-2.0/）

   * 下载相应的插件

     > npm install live2d-widget-model-xxxx // xxx代表宠物的名称

   * 站点配置文件下加入

     ```yaml
     live2d:
       enable: true
       scriptFrom: local
       pluginRootPath: live2dw/
       pluginJsPath: lib/
       pluginModelPath: assets/
       tagMode: false
       model:
         use: live2d-widget-model-wanko  #选择哪种模型
       display: #放置位置和大小
         position: right
         width: 150  # 大小
         height: 300  # 离地面的高度
       mobile:
         show: false #是否在手机端显示
     
     ```


## 遇到的问题

1. 公式：不同的主题参考不同的改法，不过一般都需要更换一些更好的渲染器：[Hexo中的数学公式 - 简书 (jianshu.com)](https://www.jianshu.com/p/9024d6b23c3a)


# 多设备博客同步问题

之前一直有两个设备，使用时问题好像不大，不过现在我只有一个设备，而且工作经常在ubuntu下，有时想要记录个东西就比较麻烦。目前我的解决方案是：

1. 将`source`文件夹中的博客同步到github
2. 在不同的环境下各自生成一个博客的空间

这里记录一下博客的初始化

1. 正常安装node，按照主题volants的需求（node和npm的版本一定要满足）：

   ```
   Hexo: 5.4 ~ 6.x
   hexo-cli: 4.3 ~ latest
   node.js: 16.x LTS ~ latest LTS
   npm: 8.x ~ latest LTS
   ```

2. 使用`hexo init`初始化博客空间，后面的命令都在该空间执行

3. 检查空间下的插件和版本`hexo -v`，如果`hexo`版本不满足要求则需要升级

   ```shell
   # 更新 package.json 中的 hexo 及个插件版本
   npm install -g npm-check           # 检查之前安装的插件，都有哪些是可以升级的 
   npm install -g npm-upgrade         # 升级系统中的插件
   npm-check
   npm-upgrade
   # 更新 hexo 及所有插件
   cnpm update
   # 确认 hexo 已经更新
   hexo -v
   ```

4. 由于使用到公式，需要换一下更好用的渲染器

   ```shell
   npm un hexo-renderer-marked
   npm i hexo-renderer-pandoc
   ```

   * 这里由于pandoc的兼容性不好，ubuntu下主要通过`github`安装2.2.3.2版本：https://github.com/jgm/pandoc/releases?page=6

5. 除此之外，还需要安装一些其他插件：

   ```shell
   npm i hexo-theme-volantis 	# 主题
   npm i -S hexo-generator-json-content  # 评论系统
   npm install --save hexo-deployer-git  # 用于deploy到github
   ```

6. 将`_config.yml`覆盖，博客复制到source目录下

7. 修改配置文件：`node_modules/HEXO-THEME-VOLANTIS/source/css/_first/base_first.style`

   ​	修改自己喜欢的透明度

   ```css
   .translucent
     @supports (backdrop-filter: blur(20px))
       background: alpha($color-card, .6) !important
   ```

8. `hexo g`生成`hexo s`查看是否成功