# -_newbee-note
记录工作中遇到的一些小技巧和坑
###### month1

- 图片不确定大小的时候，可以用这个属性

  ```css
  object-fit: cover||contain;
  ```

  

- 子元素数目不定且横向一排，父盒子自动撑开，浮动搞定不了，用的以下代码实现

  ```css
  .father{
      display:inline-block;
      white-space:nowrap;
  }
  .child{
      width:200px;
      height:200px;
      display:inline-block;
      margin:0px 10px;
  }
  ```


- 在某个图片相关的需求时，之前人运用了jqr的懒加载插件，所以我们在获取img这个dom的时候，拿到的全是懒加载的那张gif的dom，所以我们这里需要运用js原生里面的img.onload事件，先监听img的src的变化，然后再判断‘phoenix-lazyload’这个属性为空，说明图片已经懒加载结束，具体代码如下（这里用的jqr获取元素，要先转成原生dom)：

  ```javascript
  $("img")[0].onload = function () {
  		    		
  		   	  if($("img").attr('phoenix-lazyload')!=''){
  		   		   //加载完可以操作dom	   		  
  		   	   console.log('开始操作dom')   
  		   	   console.log($("img").css('width'))
  		   	   }
  ```

- 有意思的css属性    filter: grayscale(1);

- ！！！！！重坑！！！！在写vue的路由时，发现一级路由可以跳，一级跳二级就不行，从路由到各种地方都一一排查始终没有找到原因所在，最后写了个新页面，发现可以使用，经过排查发现在钩子函数中写了beforerouteupdate这个钩子函数，推测估计是这个把路由跳转拦截掉了，删了钩子函数后跳转正常！

- ！！！！！重坑！！！！vue在从后台请求到对象数组后，要给对象加新的键值对需要用vue自带的$set方法，并且最好是写在请求的回调里面，不能直接用对象的obj【‘key’】方法，会加不上新的键值对！！！！！

  ```javascript
  axios.get(url).then((res)=>{
    this.Arr=res.data;
    liveopen()
  })
  liveopen(){
     this.Arr.forEach(item => {
     this.$set(item,'open',false);
    })
   },
  ```
- 在v-for中使用Clipboard进行对当前item的复制，
1. div :class="'usablecardbody videobox'+ item.videoId"
2. var clipboard = new Clipboard('.videobox'+val+' .pushflowadd .el-button')  
3. :data-clipboard-target="'.videobox'+ item.videoId+' .pushflowpwd .el-input__inner'" @click="copyPwd(item.videoId)
 主要是要通过固定的标识把当前的元素标记起来，然后用他的api的时候拼接相应的字符串即可

- 在vue中生成二维码时，可以使用专属vue的使用vue-qriously生成二维码，更加快捷方便，来替代qrcord
具体使用见https://www.jianshu.com/p/01edb4c06823

- egg解密加密aes,用https://www.npmjs.com/package/aes-ecb 三行代码搞定
- 字符串的trim方法，可以去掉 字符串里 一些乱码，比较实用，trim()方法实际上trim掉了字符串两端Unicode编码小于等于32（\u0020）的所有字符。所以一些空格，退格都可以删掉

- 使用vue的history模式时   会出现打包到后台时  页面刷新404的问题，解决方法是在egg里面配置一个中间件,用来执行路由

  ```javascript

// app/middleware/history_fallback.js
module.exports = () => {
  return async function historyFallback(ctx, next) {
    await next();
    if (ctx.status === 404 && !ctx.body) {
      if (ctx.acceptJSON) {
        ctx.body = { error: 'Not Found' };
      } else {
        ctx.render('index')
      }
    }
  };
};
 // config/config.default.js
module.exports = {
  middleware: [ 'historyFallback' ],
};
  ```
- 在打包vue时因为js文件太大，把第三方资源都采用外部引入的方式，但是在抽离elementui时，最后报错了，然后改了行代码，玄学般的生效了
    ```javascript
  externals:{
    'vue':'Vue',
    'vue-router':'VueRouter',
    'vuex':'Vuex',
    'axios':'axios',
    'element-ui':'ELEMENT'
  },
  ```

- 使用js,css引入使用element-ui时,出现了老生常谈的element-ui图标显示错误,这次由于没走框架,不能通过配置来解决,于是先看请求,发现element-ui.css默认走的是根目录下的css/fonts这个文件夹下的图标文件,网上下载相关文件 放到fonts下的时候,发现图标还是 显示不了,但是请求已经正常,后来发现是版本问题,下的css文件是2.12的而下的字体文件是最新的2.13,两者不兼容,于是将字体也降级以后完美解决~
ps:element-ui关于图标的坑还是蛮多的,自己就遇到过这么多次,不过现在遇到图标丢失的问题也不会慌了,基本上都是路径问题

- vue-cli打包相关:
1.关于url的hashHistory与browserHistory,hashHistory是默认的路由方式,会在url中显示#拼接路由,好处是不需要后端进行特殊的处理,就可以进行路由的前端处理,但是不符合用户的使用习惯和审美.browserHistory则是一般的路由形式,url中不会出现特殊字符,但是需要后端进行配合,因为后端在解析路由的时候,例如/user,服务器上的静态资源并没有这个文件夹,于是就需要做个映射,让路由交给前端去处理(在node中的处理上文也有讲到如何处理,在其他服务器上如Apache,Nginx等,则需要相应的进行配置).这两个路由模式可以通过src/router/index.js 里的mode修改即可.
2.关于打包后静态资源路径的问题.大部分情况下我们的资源都是发布在根目录的,所以采用默认的打包配置即可,但是如果不是发布在根目录,则需要进行相关的配置https://cli.vuejs.org/config/#publicpath 详情见官方文档.主要是通过修改vue.config里的publicPath来达成相应的效果,默认是绝对路径'/',可以修改为'./'
