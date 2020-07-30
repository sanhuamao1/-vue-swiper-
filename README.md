做个轮播碰到了不少坑，记录一下。
这里主要是在html的基础上用vue实现轮播功能

## CDN
首先在lib文件夹上存放会用到的脚本，并在index文件中导入。由于图片数据是通过接口获得的，所以我这里使用了axios插件来帮助我获取数据
```html
<head>
    //Vue相关脚本
    <script src="./lib/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    
     //Swiper相关脚本
    <link rel="stylesheet" href="https://unpkg.com/swiper/swiper-bundle.css">  
    <link rel="stylesheet" href="https://unpkg.com/swiper/swiper-bundle.min.css">    
    <script src="https://unpkg.com/swiper/swiper-bundle.js"> </script>  
    <script src="https://unpkg.com/swiper/swiper-bundle.min.js"> </script>
    <script src="https://unpkg.com/swiper/swiper-bundle.min.js"> </script>
</head>
```


## 创建vue实例
```html
    <div id="app" class="main">
    </div>
```
```js
 new Vue({
    el:'#app',
    data:{
        //存放图片数据
       picList:[],
    },
 })
```


## 功能
这里是单纯地实现轮播功能，于是我根据swiper的官方文档只用了一部分,这里面的`.swiper-slide`存放的是你想轮播的内容。由于我使用的是动态获取数据，于是我修改了一下
```html
<div class="swiper-container">
    <div class="swiper-wrapper">
        <div class="swiper-slide">Slide 1</div>
        <div class="swiper-slide">Slide 2</div>
        <div class="swiper-slide">Slide 3</div>
    </div>
</div>

<!-- 修改后 -->
<div class="swiper-container">
    <div class="swiper-wrapper">
        <div class="swiper-slide" v-for="(item,i) in picList" :key="i">
            <img :src="item.url">
        </div>
    </div>
</div>
```

## 接口获取数据的方法
```js
methods:{
    getPicList(){
        //如果你需要传参，可以在url后面加个对象`{}`，具体可以看axios官方文档
        axios.post('url')
         .then( res=> {
            //这里我过滤了url不为空的对象，使之组合成一个新的数组对象。你可以根据你自己返回的数据进行修改
            this.picList=res.data.Data.filter(item=>{
            return item.url!=="" })     
          }).catch(function (error) {
              console.log('error');
             });
           },
        }
```


## 初始化Swiper
```js
//这是官方文档的写法
var mySwiper = new Swiper ('.swiper-container', {
  direction: 'vertical', // 垂直切换选项
  loop: true, // 循环模式选项
 }) 
 
 //本实例我选择用函数包裹卸载methods中，加定时器是解决loop失效的问题。其他的配置属性建议去swiper的API文档进行查看
initSwiper(){
  setTimeout(()=>{
  var mySwiper = new Swiper ('.swiper-container', {
  loop: true,
  direction:"horizontal",
  speed:500,
  autoplay :{
       //防止拖拽后无法自动滑动
       disableOnInteraction:false,
       delay:4000
  },
  //防止拖拽后无法自动滑动
  observer:true,
  observeParents:true,
  observeSlideChildren:true,
  })  
  },300)

}
```

## 在生命钩子上调用接口函数和初始化函数
```js
 mounted() {
    this.getPicList()
    this.initSwiper()
},
```

好啦！祝你一切顺利！
