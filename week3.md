>[说一下XSS和CSRF分别是什么?](#说一下xss和csrf分别是什么怎么防御xss又分了哪三类)  
>[对MVC、MVVM的理解](#对mvcmvvm的理解)   
>[说一下小程序的授权登录流程?](#说一下小程序的授权登录流程)  
>[React项目优化](#react项目优化)  
>[浏览器页面渲染流程](#浏览器页面渲染流程)  
>[算法题 - 两数相加](#两数相加)

## 说一下XSS和CSRF分别是什么？怎么防御？XSS又分了哪三类?

### XSS（跨域脚本攻击 攻击）
XSS攻击是指黑客往HTML文件中，或者DOM中注入恶意脚本，从而在用户浏览页面的时利用注入的恶意脚本对用户实施攻击的一种手段。

#### 注入恶意脚本之后可以做什么：
- 可以窃取用户 cookie。通过 document.cookie 获取信息，然后将数据发送给恶意服务器。恶意服务器拿到 cookie 之后，可以模拟用户的登录，进行转账等操作；
- 可以监听用户行为。 恶意 JavaScript 可以通过 addEventListener 接口监听用户的输入，将用户信息发送给恶意服务器；
- 操作 DOM，伪造假的登录窗口，让用户输入用户名、密码等；
- 在页面内生成浮窗广告，影响用户体验

#### 恶意注入脚本的三种类型：
1. 存储型 XSS 攻击  
存储型XSS流程：
- 首先黑客利用站点漏洞将一段恶意 JavaScript 代码提交到网站的数据库中；
- 然后用户向网站请求包含了恶意 JavaScript 脚本的页面；
- 当用户浏览该页面的时候，恶意脚本就会将用户的 Cookie 信息等数据上传到服务器。

2. 反射型 XSS 攻击  
用户将一段带有恶意代码的请求提交给 web 服务器，web 服务器收到请求后，又将恶意代码返回给浏览器，这就是反射型 XSS  
Web服务器不会存储反射型 XSS 攻击的恶意脚本，这是和存储型 XSS 攻击不同的地方。

3. 基于 DOM 的 XSS 攻击  
基于 DOM 的 XSS 攻击是在数据传输过程中，或者是用户使用过程中修改 Web 页面的数据

#### 如何阻止 XSS 攻击
各种类型的 XSS 攻击都有一个共同点，就是首先往浏览器注入恶意脚本，然后通过恶意脚本将用户信息发送给恶意服务器，所以要阻止 XSS 攻击，可以通过防止恶意脚本的注入和恶意消息的发送来实现

常用的阻止 XSS 攻击的策略：
1. 服务器对输入的脚本进行过滤或者是转码，过滤掉 `<script>`，或者将`<script>`  转成 `&lt;script&gt`;
2. 充分利用 CSP（内容安全策略）。CSP有入下功能：
    - 限制加载其他域下的资源文件，这样即使黑客插入了一个 script 文件，这个文件也无法被加载
    - 禁止向第三方域提交数据,这样用户数据也不会外泄
    - 禁止执行内联脚本和未授权的脚本
    - 还提供了上报机制，这样可以帮助我们尽快发现有哪些 XSS 攻击，以便尽快修复问题。
3. 使用 HttpOnly 属性来标记 Cookie，不能通过脚本读取。

CSP：
内容安全策略是一个额外的安全层，用于检测并削弱某些特定类型的攻击，包括跨站脚本和数据注入攻击等。
为了使CSP可用, 需要配置网络服务器返回  Content-Security-Policy  HTTP头部（X-Content-Security-Policy头部是旧版本的提法）

### CSRF（跨站请求伪造）
用户登录了一个网站 A，然后黑客引诱用户去打开黑客的网站 B，在黑客的网站中利用用户的登录状态发起跨站请求。

#### 如何防止 CSRF 攻击：
1. 充分利用 Cookie 的 SameSite 属性，如果是从第三方站点发起的请求，那么需要浏览器禁止发送某些关键 Cookie 数据到服务器
2. 服务端验证请求的来源站点，可以禁止来自第三方站点的请求。
3. 使用 CSRF Token 验证。

## 对MVC、MVVM的理解
MVC，MVP，MVVM是三种架构模式。MVC 是 MVP 和 MVVM 的基础

### MVC
MVC 将软件分为三个部分：视图、模型和控制器  
M：是 Model，指数据模型，用来存储数据  
V：是 View，指的是视图界面，主要是展示界面和响应用户交互事件  
C：是 Controller，控制器，处理业务逻辑  

MVC 的工作流程：  
View 响应用户事件，将用户指令传给 Controller，Controller 处理完业务逻辑之后，调用 Model 的接口对数据进行操作。Model 数据更新后，通知相关视图进行更新。

### MVP
MVP 是在 MVC 的基础上进行了优化，将 Controller 变成了 Presenter，同时改变了通信方式，Presenter 和 View，Presenter 和 Model 的通信都是双向的，View 和 Model 不发生联系，通过 Presenter 通信。

### MVVM
MVVM 在 MVP 的基础上又进行了改进，将 Presenter 改成了 ViewModel，采用了双向数据绑定，View 的改动自动反应到 ViewModel 上。  
M：是 Model，数据模型，在 vue 中对应 data  
V：是 View，视图，用来展示界面，响应用户事件，对应 vue 中的 `<template>`  
VM: 是 ViewModel，视图模型，对应 vue 实例，实现数据的双向绑定，Model 变化时会自动触发 View 的更新，View变化时，也会马上更新数据。


### MVVM 和 MVC 的区别：  
MVVM 是把 MVC 中的 Controller 改成了 ViewModel，MVVM 实现了 View 和 Model 的自动同步，当 Model 的属性改变时，不用手动操作 DOM 元素来更新 View，改变属性后对应的 View 会自动更新。


## 说一下小程序的授权登录流程？

小程序授权获取用户信息要绑定在一个 button 上，button的 open-type 设置为 getUserInfo，这样在button的 bindgetuserinfo 事件参数中就能获取到用户信息，和 encryptedData、iv 等信息。

登录的流程：
1. 客户端调用 wx.login() 获取微信登录凭证code
2. wx.login 之后，调动后端提供的登录接口，需要上传 code，encryptedData，iv 
3. 服务器调用微信服务器的 auth.code2Session接口，并上传 code，appId 和 appSecret，完成登录，换取到微信用户身份
4. 服务端会生成一个token，返回给客户端，之后每次请求都带上。


## React项目优化？
1. 在列表中使用 key
2. 页面销毁的时候清除自定义事件
3. class组件使用shouldComponentUpdate 和PureComponent，函数组件使用 React.memo()

class组件的优化：
1. 通过 shouldComponentUpdate(nextProps,nextState) 来优化：  
react 中，默认情况下，shouldComponentUpdate 默认返回 true，父组件更新，子组件也会更新，不管数据有没有变化。通过在 shouldComponentUpdate 中进行对比，如果数据相同就返回false，就不会重新渲染了
2. 通过PureComponent 来优化：  
    如果当前 class 组件继承自 PureComponen，就会自动在 shouldComponentUpdate 中实现了对state和props的浅比较，减少重复渲染

函数组建的优化：
1. 使用 React.memo() 来包裹组件：
   React.memo 与 PureComponent 相似，是用在函数组件中的，只检查props的变化，如果输入 props 相同则跳过组件渲染。如果用了useState 或 useContext 的组件变化了之后还是会更新。


## 浏览器页面渲染流程
浏览器获取到资源后，就要进行页面渲染。页面渲染分为以下几个阶段：
- DOM解析：将 html 解析成dom 树
- CSS 解析：然后解析CSS，生成 CSSOM 树
- render：结合 DOM 树和 CSSOM 树，构建render树
- layout：然后进行布局
- paint：最后绘制到页面上
- JavaScript编译执行，如果在执行 js 的过程中 修改了 dom 或者是 css 样式，就可能触发 重排 和 重绘  

重绘（repaint）：不改变元素在网页中的位置的样式时，会触发重绘，比如颜色的改变、visible的改变等。重绘不会重新布局  
重排（reflow）：如果改变了元素在网页中的位置就会触发重排。比如增加、删除、移动 DOM 节点。  
如何减少reflow：减少频繁改动，可以将多个结果的改动放到一个节点中一次添加到dom中

## 两数相加

给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。
请你将两个数相加，并以相同形式返回一个表示和的链表。
你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

链接：https://leetcode-cn.com/problems/add-two-numbers

示例：
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.

```
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var addTwoNumbers = function(l1, l2) {
    const initSum = l1.val + l2.val
    let result = new ListNode(initSum % 10)
    let temp = Math.floor(initSum / 10)
    let curL1 = l1.next
    let curL2 = l2.next
    let cur = result
    while(curL1 || curL2 || temp) {
        const n1 = curL1 ? curL1.val : 0
        const n2 = curL2 ? curL2.val : 0
        const sum = n1 + n2 + temp
        cur.next = new ListNode(sum % 10)
        temp = Math.floor(sum / 10)
        cur = cur.next
        curL1 = curL1 ? curL1.next : curL1
        curL2 = curL2 ? curL2.next : curL2
    }
    return result
};
```