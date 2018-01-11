# ionic部分
### 生命周期
ionic有自身的生命周期，不可与angular混用;
### 路由
ionic有自身的路由机制，不可用angular路由;
### ionic颜色定制
 * 坑：ionic的部分组件是有color属性，更改其组件颜色只需更改其color值，开发时需要新的颜色，直接改style太粗暴。
 * 解决：theme目录下variables.scss可在$colors处定制颜色。
### 页面组件ion-content
 * 坑：页面默认有下拉阻尼效果，我们不想要页面可以被下拉
 * 解决：给ion-content标签加no-bounce属性
### 下拉刷新组件ion-refresher
 * 坑：下拉时准备动画消失
 * 解决：给ion-refresher组件加上ionPull事件。
 * 坑：当使用横向滚动组件时，触发refresher组件。
 * 解决：给横向滚动组件加摁下和离开事件，改变一个布尔值的值，把这个值与ion-refresher enabled属性绑定。
 * ```html 
   <ion-refresher [enabled] ='refresherB && showLoading$ | async' pullMin='60' pullMax='300' (ionPull)='doRefress()'
    (ionRefresh)="doRefresh($event)"></ion-refresher>   

### navCtrl.push
 * 坑：navCtrl.push到一个新页面后需要底部tabs消失
 * 解决：
 * ```js 
    //在页面构造前的钩子上加上
   let elements = document.querySelectorAll(".tabbar");
   if (elements != null) {
        Object.keys(elements).map((key) => {
            elements[key].style.display = 'none';
        });
    }
    //在页面销毁的钩子上加上 
    let elements = document.querySelectorAll(".tabbar");
    if (elements != null) {
      Object.keys(elements).map((key) => {
        elements[key].style.display = 'flex';
      });
    }
### ion-tabs组件
* 坑：ion-tab无法通过常规方式定制图标
* 解决：通过sass指令遍历要替换的图标名称，实行替换   

# angular部分
### 无关联组件间通讯
* 坑：无关联组件如何更优雅的通讯
* 解决：注册一个全局的服务，创建一个可观察的对象，然后在需要的组件订阅它。
* ```Ts
    public eventBus:Subject<string> = new Subject<string>();
###  Animations 动画模块
* 坑：Safari默认不支持angular的所用Animations
* 解决：npm i web-animations-js 之后在polyfills文件引入
* 坑：从服务端取到数据后给定义的变量赋值，所有相关dom都触发动画效果
* 解决：取到数据后，不要做赋值操作，在原变量上进行增删操作。
### ngRx与脏检查
* 坑：当页面逻辑复杂时，生命周期无法满足对状态的管理。
* 解决：使用ngRx来优雅的进行状态管理，因为ngRx对状态的管理基于流，angualr的生命周期基于脏检查，两种不同的逻辑，所以应在项目开始时就决定是否使用。
* 坑：使用ngRx后组件状态混乱
* 解决：使用ngRx应关闭angualr脏检查机制
* ```ts
  @Component({
    changeDetection:ChangeDetectionStrategy.OnPush
  });
* 坑：关闭angular脏检查后，需要使用脏检查检查更新组件时
* 解决：引入angualr的ChangeDetectorRef组件在组件构造函数声明后调用其detectChanges()方法触发刷新。
### HttpClientModule
* 坑：jsonp
* 解决：angular4.3之后推出的HttpClient对jsonp非常不友好，除了发请求功能全无，无法设置查询参数（可以用拼字符串解决），和后台协商使用get或post；
* 坑：查询参数 HttpParams 的set方法
* 解决：每当调用 set() 方法，将会返回包含新值的 HttpParams 对象，因此如果使用下面的方式，将不能正确的设置参数。
* ```ts
  //无法正确设置，这是angualr4.3之前http模块的写法
  const params = new HttpParams();
  params.set('orderBy', '"$key"');
  params.set('limitToFirst', "1");
  //代替方案
  const params = new HttpParams();
  params = params.append('orderBy', "$key");
  params = params.append('limitToFirst', "1");
* 坑：post请求查询参数设置  
* 解决：post请求依然可以使用HttpParams对象作为设置查询参数方式，为了不出问题最好params.toString();
