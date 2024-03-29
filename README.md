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

- 第一次在业务中使用递归
第一次在业务中使用递归就一下子用了三个，hhh，主要是根据 后台返回的数据,来先渲染成一个递归的下拉选择框,然后在编辑时,由于list中的数据,只包含最子级的id,又需要递归出他的父级id从而才可以正确显示在下拉中

  ```javascript
    // 递归处理权限下拉数据结构
    getAuthorityOption(list) {
      list.forEach(items => {
        items["key"] = items.id;
        items.children.forEach(item => {
          items.list.push(item);
        });
        if (items.children.length > 0) {
          this.getAuthorityOption(items.children);
        }
      });
    }
    
       //递归查找权限父级
      function buildParentList(arr) {
        arr.forEach(g => {
          if (g.group != undefined) {
            let pid = g["group"];
            let oid = g["key"];
            if (pid != oid) {
              parentList[oid] = pid;
            }
          } else if (g.parentId != undefined && g.parentId != 0) {
            let pid = g["parentId"];
            let oid = g["key"];
            if (pid != oid) {
              parentList[oid] = pid;
            }
          }
          if (g.list != undefined) buildParentList(g["list"]);
        });
      }
      let that = this
      //递归插入父级到权限数组里
      function findParent(idx) {
        if (parentList[idx] != undefined) {
          let pid = parentList[idx];
          that.temp.authority.unshift(pid)
          findParent(pid);
        }
      }
      let parentList = {};
      buildParentList(this.authorityOptions);
      findParent(row.authority); 
  ```

- 在局域网测试时遇到的host与cookies相关的问题。后端采用了cookie的域校验，所以域名不能才能默认的localhost的问题。解决方法是先在本地host文件，配置相关的子父域名的host，再修改vueconfig里面的配置项，“disablehostcheck:true”忽略host校验就可以了。注意，这里之前的cookie存不上的原因其实不是没存上，而是在当前的域名里存的其他域名cookie并不会在浏览器里的相关地方看到。之后直接输入配的域名就可以访问了
- element ui 日期选择器 只让选择固定天数，在使用选择器的时候，遇到只让选择跨度为x天的需求，通过使用掘金大佬的代码后发现 他是x天内的都可以选，稍微改造了一下，完成如下：
他自带的return return出去是不让选的部分，所以这地方逻辑要好好思考

- element ui tree组件 搜索框清空时关闭所有折叠，看了源码以及参考网上的大神之后：
    ```javascript
      if (!value) {
        //全部折叠
        this.$refs.tree2.store._getAllNodes().map((n) => (n.expanded = false));//获取所有节点
        this.$refs.tree2.root.childNodes.forEach((ele) => {//发现没法收缩根节点后 看了源码 新增了这一行
          ele.expanded = false;
        });
      }
```

- 生产环境请求踩坑：开发环境下请求正常发送，生产环境下，请求连network里都看不到，所以排除了一系列和请求地址有关的问题，最后发现是mock.js在线上请求的时候，把部分数据拦截掉了。注释掉相关代码即可。
    ```javascript
  import { mockXHR } from '../mock'
  if (process.env.NODE_ENV === 'production') {
  mockXHR()}
```






- 组里的人遇到一个比较难的业务层面的代码实现，看了下确实坑很多，帮他搞定了 记录一下：
业务：elementui 可展开表格下嵌套一个element可多选表格，要求可以展开收起后能记录住选择的哪些
实现和坑：首先先决定主逻辑，主逻辑很简单，就是用一个变量记录住 这行多选的内容，并push，然后回显的时候拿到这个变量用于回显；
坑1：其实不算坑，但是初用element的新人可能不知道，就是自带的方法传值想要默认值和自己的值的时候，可以写成  方法名（$event,item）的方式，可以同时拿到两个并处理；
坑2：expand收起的时候，会让里面的内容不渲染，从而多选过的行会全部被清空：解决：也很简单，我选择用一个变量记录收起还是展开，展开的时候 才会调用toggle方法。这个主要是要定位到问题所在，定位到是因为收起dom重新渲染 才会触发 表格的change方法后，解决就只是时间问题了。
坑3：可多选表格的toggle方法需要获取到ref 而expand展开的第一时间，这个ref并没有挂载上去，解决：我选择用简单粗暴的定时器来解决，实战中没试nexttick是否可以。

主要是对新人算是难的业务，要对element的文档 和逻辑性有一些要求，但是在主逻辑确定后，后面基本上就是抽丝剥茧的 去如何利用官方文档和解决一些常见的坑了。
