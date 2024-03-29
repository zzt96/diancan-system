## vue路由基础知识
#### route, routes, router
- route是一条路由，从A跳转至B是一条route
- routes是一组路由，复数，所以是很多个route的集合
- router是一个管理者，来管理整个路由（routes只是定义了一组路由，而router在请求过来的时候去进行处理（到routes找匹配请求的路由）
### 路由配置步骤
1. npm install vue-router --save
2. import路由模块，import组件模块
3. Vue.use(router)
4. 一个route的定义：在vue中，由两个部分组成：path和component，path就是自己定义的路径（在nuxt中会根据你设计的文件夹结构帮助你定义好路径）（在之后router-link的to属性中可以直接使用这个定义的路径找到页面）另一个是组件，就是路由要跳转到的目的地（可以是子组件哦）
5. route长啥样：
```
{ path: '/home', component: Home },
```
6. routes就是一组上面的route；这是一个数‘组’哦 
```
const routes =[  
{ path: '/home', component: Home },  
{ path: '/center', component: center },  
{ path: '/news', component: news },  
]
```
7. 创建路由对路由进行管理：使用new VueRoute方法，再把一个routes对象扔进去（所以外面有{}花括号）
```
const router = new VueRouter({
      routes // routes: routes 的简写
})
```
8. 最后，将这个router给注入到vue根实例中（就是最大的那个vue，天王老子）
```
const app = new Vue({
  router
}).$mount('#app')
```
9. 现在可以使用路由啦，在要用路由的地方写上router-link标签，里面的to="here is path"写上路径，然后在使用这个router-link标签的时候，router就回帮你找到与此路径匹配的component，（在routes里找啊）然后渲染到router-view（子组件）或者跳到另一个页面，比如：
```
<router-link :to="{'/homeLink'}" class="nav-link">主页</router-link>
```
关于路由设置的详细的代码到main.js(router的引用和注入根组件)以及routes.js(一组route配置的集合)里去看

### 路由小细节（redirect和tag）
#### tag
<router-link>默认的是嵌套成a标签，如果想要自定义其标签形式，则使用tag：  
如：tag="li"则将其设定为li标签  
tag="tr"则将其设定为tr标签  
#### redirect
routes中重定向，设定一个路由：{path:'*',redirect:'/'} *表示所有，如果没有匹配到上面其他的路由，则path会导向到这个路由上，redirct会将其重定向至/的位置  
> ps.router-link :to="homelink"即为使用动态属性，   
> 一般在data中赋予homelink值  

### 路由name属性
1. 给一个路由取个名字（menuLink）{path: '/menu', name: 'menuLink', component: Menu},  
2. 使用：<router-link :to="{name: 'menuLink'}">  
3. 注意to前面的冒号（属性绑定v-model）以及name属性是用花括号括起来的  
4. 跳转到 '/menu'这个路径  

### 二级三级路由
children :[]属性中写对应的路由  
<router-view></router-view>显示子组件  

### 路由守卫
#### 全局守卫 each
```
//to：进入哪个路由里去
//from： 从那个路由离开
//next：跳转函数
//这里的控制语句是指：当跳转路由在登陆或者注册之外的其他页面，则将其引导到登陆页面

router.beforeEach((to,from,next)=>{
  if(to.path== '/login' || to.path=='/register'){
    next();
  }else{
    alert('请先登录!');
    next('/login');
  }
})
```

#### 后置钩子
```
router.afterEach((to,from)=>{
alert('请先登录!');
}
```

#### 路由独享的守卫
```
//在一个route之中进行配置，当进入这个（beforeEnter）这个路由之前进行如下的操作（只对admin这个组件起作用）
{
    path: '/admin', name: 'adminLink', component: Admin,
    beforeEnter: (to, from, next) => {
    路由独享守卫
    alert('非登陆不能访问');
    next(false)//不会进行跳转
      if (to.path == '/login' || to.path == '/register') {
        next();
     } else {
        alert('请先登录!');
        next('/login');
      }
    }
  },
```
#### 组件内的守卫beforeRouteEnter/beforeRouteLeave
```
    //进入组件之前的守卫
    beforeRouteEnter: (to, from, next) => {
      // alert("hello " + this.name);//不能得到name（数据渲染之前）
    //解决办法：使用next回调函数，异步获取数据,当这个方法执行的时候，数据已经渲染完了，所以可以用vm获得姓名
      next(vm=>{
        alert("hello " + vm.name)
      })
    },

    //离开组件时候的守卫
    beforeRouteLeave : (to, from, next) => {
    if(confirm("确定离开嘛？")==true){
      next();
    }else{
      next(false);
    }
    }
```
注意这里的一个知识点，在守卫中使用this.name想要获得name的数据，却无法正常获取该数据(undefined)；原因是进入这个守卫的时候，数据还没有被渲染出来，所以想要获取这个数据，应该使用next的回调函数异步获取数据。
```
next(vm=>{
        alert("hello " + vm.name)
      })
```

### 复用router-view
1. 在路由中配置
 ```
{
    path: '/', name: 'homeLink', components: {
      default: Home,
      'orderingGuide': OrderingGuide,
      'Delivery': Delivery,
      'History': History,
    }
  },
```
注意这里的components表示这里引入的是多个组件，默认组件是Home
2. 在app.vue中使用
```
        <div class="col-sm-12 col-md-4">
          <router-view name="orderingGuide"></router-view>
        </div>
        <div class="col-sm-12 col-md-4">
          <router-view name="Delivery"></router-view>
        </div>
        <div class="col-sm-12 col-md-4">
          <router-view name="History"></router-view>
        </div>
```
这样每一个组件都有这三个子组件了

### 滚动行为
```
写在main.js的router中
//to：进入哪个路由里去
//from： 从那个路由离开
//savedPosition:通过浏览器的前进后退所保留的位置
scrollBehavior(to,from,savedPosition){
    // return{x:0,y:100} //100的位置
  // return{ selector:".btn"}//跳转后停留在第一个btn的位置
  if(savedPosition){
    return savedPosition
  } else{
    return{x:0,y:0}
  }
  }
```
