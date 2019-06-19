# 电影项目App
技术栈使用： Hbuilder(测试、打包)+mui(前端框架，提高开发效率)+h5+runtime(提供调用设备原生功能的能力)+Vue

## 下载地址
1. 下载地址: https://service.dcloud.net.cn/build/download/df4afd10-927d-11e9-8111-4bafadf5a5e6 （注意该地址为临时下载地址，只能下载5次）
ios 由于设备限制，没有下载测试，效果不清楚；

2. Android下载地址: https://service.dcloud.net.cn/build/download/df5e87e0-927d-11e9-8557-537a1bcfe6d4 （注意该地址为临时下载地址，只能下载5次）
测试基本功能实现

## 技术点总结：
1. 响应式布局设置：
```
document.documentElement.style.fontSize = document.documentElement.clientWidth / 3.75 + 'px'
window.onresize = function  () {
	document.documentElement.style.fontSize = document.documentElement.clientWidth / 3.75 + 'px'
}
```

2. 创建`webview`窗口例子
```
(function ($, doc) {
  $.init();
  $.plusReady(function () { //页面预加载
    let self = plus.webview.currentWebview();//获取当前窗口的WebviewObject对象
    createFooterWebview(self);
    createMainWebview(self);
    createHeaderWebview(self);
  })
  //封装创建Webview窗口
  function createHeaderWebview(current) {
    let header_webview = plus.webview.create('header.html', 'header.html', { //创建新的Webview窗口
      height: '50px',
      zindex: 10
    })
    current.append(header_webview);//在Webview窗口中添加子窗口
    return header_webview;
  }
})(mui, window)
```

3. 点击导航栏跳转不同的页面
```
handleChangeActive(page_id) {
  let index_webview = plus.webview.getLaunchWebview();//获取应用首页WebviewObject窗口对象
  let main_webview = plus.webview.getWebviewById('main.html');//查找id为main.html的WebviewObject窗口
  mui.fire(main_webview, 'webview:switch', {page_id});//触发自定义事件
}
```
监听自定义事件
```
window.addEventListener('webview:switch', (e)=>{
  let id = e.detail.page_id;
  let film_id = e.detail.film_id;
  if(active_webview_id === id) return false;//点击同一个页面时，跳出函数
  active_webview_id=id;
  showWebviewById(id, film_id);
})
```

4. 上拉加载,若同一个页面有两个及以上的上拉加载则需要拆分到不同的页面
```
initRefresh() { //配置拉动刷新的操作
  mui.init({
    pullRefresh: {
      container: '#app',
      up: {
        contentrefresh: '正在加载...',
        callback: this.pullUpHandler,
        contentnomore:'到底了。。。'
      }
    }
  })
}
```
```
pullUpHandler() { //上拉加载的处理函数
  this.page++;
  //根据新页数获取数据之后还原刷新样式
  this.getMoviesLists((hasMore) => {
    mui('#app').pullRefresh().endPullupToRefresh(!hasMore);
  })
}
```

5. 登陆页面设置保存用户信息
```
this.user_info = JSON.parse(localStorage.user_info || {})
```
移除用户信息
```
localStorage.removeItem('user_info');
```

6. 登陆获取用户信息
```
localStorage.user_info = JSON.stringify(auth.userInfo);
```

7. Vue中兄弟组件传值
* 新建一个`Vue`实例:`let app = new Vue();`
* 发布事件`app.$emit('subDistrictId', this.districtId);`
* 订阅事件: 写在mounted生命周期内
```
app.$on('subDistrictId', (subDistrictId) => {
  this.subDistrictId = subDistrictId;
  this.getCinemaList();
});
```
8. 实现二级联动，利用`filter`过滤和对应处理得到符合条件的数组
```
getSubDistrict() {
  this.subDistrict = this.district.filter((item) => {
    return item.id === this.districtId
  })[0].subItems;
}
```

9. 引入h5页面
* 利用`Vue.mixin( mixin )`的全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。插件作者可以使用混入，向组件注入自定义的行为。
```
var global_mixin = {
	methods: {
		toDetail (id) {
			//显示详情
			let main_webview = plus.webview.getWebviewById('main.html')
			mui.fire(main_webview, 'webview:switch', { page_id: 'detail.html', film_id: id })				
		}
	}
}
```
标签绑定事件`@tap="toDetail(movie.id)`其中`mui`提供了`tap`事件替换了`html5`的`click`事件,解决了300ms延时的问题。


## 效果图
<img src="https://github.com/Melody523/film-app/blob/master/images/%E6%95%88%E6%9E%9C%E5%9B%BE/index_1.jpg" width="200"><img src="https://github.com/Melody523/film-app/blob/master/images/%E6%95%88%E6%9E%9C%E5%9B%BE/index_2.jpg" width="200"><img src="https://github.com/Melody523/film-app/blob/master/images/%E6%95%88%E6%9E%9C%E5%9B%BE/cinema_1.jpg" width="200"><img src="https://github.com/Melody523/film-app/blob/master/images/%E6%95%88%E6%9E%9C%E5%9B%BE/cinema_2.jpg" width="200"><img src="https://github.com/Melody523/film-app/blob/master/images/%E6%95%88%E6%9E%9C%E5%9B%BE/cinema_3.jpg" width="200"><img src="https://github.com/Melody523/film-app/blob/master/images/%E6%95%88%E6%9E%9C%E5%9B%BE/cinema_4.jpg" width="200">



