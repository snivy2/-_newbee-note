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

  





