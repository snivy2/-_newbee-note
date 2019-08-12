# -_newbee-note
记录工作中遇到的一些小技巧和坑
###### month1

- 图片不确定大小的时候，可以用这个属性

  ```css
  object-fit: cover||contain;
  ```

  

- 在别人的基础上改位置用translate最佳（暂时这么觉得）(现在已经不这么觉得了）

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

- 所用媒体查询

  ```css
   /*ipad横屏*/
      @media screen        
              and  (min-width : 768px)
              and  (max-width : 1024px)
              and  (orientation : landscape){
          #producttab1004{
          }
      }
      /*ipad竖屏*/
      @media screen
              and (min-width : 768px)
              and (max-width : 1024px)
              and (orientation : portrait){
      }
      /*手机竖屏*/
      @media screen 
              and ( min-width: 374px ) 
              and ( max-width: 736px ) 
              and (orientation:portrait ){
             .tab li{
              float:none;
                  }
  }
      /*手机横屏*/
      @media screen 
              and ( min-width:374px) 
              and ( max-width: 736px)
              and (orientation:landscape) {
      }
  ```

- jqr事件中使用this要用$（）包起来再变为jqr对象才能继续使用jqr的方法

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




