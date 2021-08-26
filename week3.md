>[说一下XSS和CSRF分别是什么?](#说一下XSS和CSRF分别是什么)  
>[对MVC、MVVM的理解](#对MVC、MVVM的理解)   
>[说一下小程序的授权登录流程?](#说一下小程序的授权登录流程)  
>[React项目优化做了什么](#react项目优化做了什么)  
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

#### 如果阻止 XSS 攻击
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

## 说一下小程序的授权登录流程？

## React项目优化做了什么？

## 浏览器页面渲染流程

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