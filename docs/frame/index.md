## MVC、MVVM

View 和 Model：

- View 很简单，就是用户看到的视图
- Model 同样很简单，一般就是本地数据和数据库中的数据

### MVC

传统的 MVC 架构通常是使用控制器更新模型，视图从模型中获取数据去渲染。当用户有输入时，会通过控制器去更新模型，并且通知视图进行更新。

但是 MVC 有一个巨大的缺陷就是控制器承担的责任太大了，随着项目愈加复杂，控制器中的代码会越来越臃肿，导致出现不利于维护的情况。

### MVVM

引入了 ViewModel 的概念。ViewModel 只关心数据和业务的处理，不关心 View 如何处理数据，在这种情况下，View 和 Model 都可以独立出来，任何一方改变了也不一定需要改变另一方，并且可以将一些可复用的逻辑放在一个 ViewModel 中，让多个 View 复用这个 ViewModel。

以 Vue 框架来举例：

- ViewModel 就是组件的实例。View 就是模板，Model 的话在引入 Vuex 的情况下是完全可以和组件分离的。
- 除了以上三个部分，其实在 MVVM 中还引入了一个隐式的 Binder 层，实现了 View 和 ViewModel 的绑定。
- 这个隐式的 Binder 层就是 Vue 通过解析模板中的插值和指令从而实现 View 与 ViewModel 的绑定。

对于 MVVM 来说，其实最重要的并不是通过双向绑定或者其他的方式将 View 与 ViewModel 绑定起来，而是 `通过 ViewModel 将视图中的状态和用户的行为分离出一个抽象`，这才是 MVVM 的精髓

## 虚拟 DOM

就是一个普通的 JavaScript 对象，包含了 tag(selector)、props(data)、children 三个属性

- 实现：

  1. 用 JavaScript 对象模拟真实 DOM 树，对真实 DOM 进行抽象；
  2. diff 算法 — 比较两棵虚拟 DOM 树的差异；
  3. pach 算法 — 将两个虚拟 DOM 对象的差异应用到真正的 DOM 树

- 优势：
  1. 保证性能下限: 虚拟 DOM 可以经过 diff 找出最小差异,然后批量进行 patch,这种操作虽然比不上手动优化,但是比起粗暴的 DOM 操作性能要好很多,因此虚拟 DOM 可以保证性能下限
  2. 无需手动操作 DOM: 虚拟 DOM 的 diff 和 patch 都是在一次更新中自动进行的,我们无需手动操作 DOM,极大提高开发效率
  3. 跨平台: 虚拟 DOM 本质上是 JavaScript 对象,而 DOM 与平台强相关,相比之下虚拟 DOM 可以进行更方便地跨平台操作,例如服务器渲染、移动端开发等等
  4. 实现组件的高度抽象化

## 前端路由

本质就是监听 URL 的变化，然后匹配路由规则，显示相应的页面，并且无须刷新

- hash 模式

  - 当 # 后面的哈希值发生变化时，不会向服务器请求数据，可以通过 hashchange 事件来监听到 URL 的变化，从而进行跳转页面
  - URL 中 hash 值只是客户端的一种状态，也就是说当向服务器端发出请求时，hash 部分不会被发送；
  - hash 值的改变，都会在浏览器的访问历史中增加一个记录。因此我们能通过浏览器的回退、前进按钮控制hash 的切换；
  - 可以通过 a 标签，并设置 href 属性，当用户点击这个标签后，URL 的 hash 值会发生改变；或者使用  JavaScript 来对 location.hash 进行赋值，改变 URL 的 hash 值；
  - 我们可以使用 hashchange 事件来监听 hash 值的变化，从而对页面进行跳转（渲染）

- history 模式

  - 利用 HTML5 中 history 提供的 pushState、replaceState 这两个 API。它们提供了操作浏览器历史栈的方法。pushState、replaceState 能够在不加载页面的情况下改变浏览器的 URL
  - 通过 pushState 和 replaceState 虽然能改变 URL，但是不会主动触发浏览器 reload 和 popstate 事件。
  - pushState、replaceState 的区别是 history.pushState()是新增历史记录条目； history.replaceState()是修改（替换）当前历史记录条目 window.onpopstate 事件监听的是浏览器前进、后退事件

- 对比
  1. Hash 模式只可以更改 # 后面的内容，History 模式可以通过 API 设置任意的同源 URL
  2. History 模式可以通过 API 添加任意类型的数据到历史记录中，Hash 模式只能更改哈希值，也就是字符串
  3. Hash 模式无需后端配置，并且兼容性好。History 模式在用户手动输入地址或者刷新页面的时候会发起 URL 请求，后端需要配置 index.html 页面用于匹配不到静态资源的时候

## koa、express 区别

- Handler 处理方式

  - Express 使用普通的回调函数，一种线性的逻辑，在同一个线程上完成所有的 HTTP 请求
  - Koa2 这个现在是 Koa 的默认版本，与 Koa1 最大的区别是使用 ES7 的 Async/Await 替换了原来的 Generator + co 的模式，也无需引入第三方库，底层原生支持，Async/Await 现在也称为 JS 异步的终极解决方案

- 中间件

  - Express 中间件实现是基于 Callback 回调函数同步的，它不会去等待异步（Promise）完成
  - Koa 的中间件机制中使用 Async/Await（背后全是 Promise）通过 next 来驱动以同步的方式来管理异步代码，它则可以等待异步操作（洋葱模型）

- 响应机制
  - 在 Express 中我们直接操作的是 res 对象，res.send() 之后就立即响应了，无法在上层中间件做操作
  - Koa 中数据的响应是通过 ctx.body 进行设置，注意这里仅是设置并没有立即响应，而是在所有的中间件结束之后做了响应
