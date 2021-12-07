**面试问题**
### **ES6**
#### **Promise && Async/Await**
一个 Promise 对象值是未知的，状态是可变的，但是无论怎么变化，它的状态永远处于以下三种之间：

**pending**：初始状态，既不是成功，也不是失败。

**fulfilled**：意味着操作成功完成。

**rejected**：意味着操作失败。

|Promise 的状态会发生变化，成功时会从pending -> fulfilled，失败时会从pending -> rejected，但是此过程是不可逆的，也就是不能从另外两个状态变成pending。fulfilled/rejected这两个状态也被称为 settled 状态。|
| :- |
#### **Generator**
\1. 调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。

\2.  以后，每次调用遍历器对象的next方法，就会返回一个有着value和done两个属性的对象。

\3.  value属性表示当前的内部状态的值，是yield表达式后面那个表达式的值；done属性是一个布尔值，表示是否遍历结束。
#### **async**
\1. async其实就是对Generator的封装，只不过async可以自动执行next()。

\2. async必须等到里面所有的await执行完，async才开始return，返回的Promise状态才改变。除非遇到return和错误。

\3. async默认返回一个Promise，如果return不是一个Promise对象，就会被转为立即resolve的Promise，可以在then函数中获取返回值。

####
#### **从async/await面试题看宏观任务和微观任务**
##### **宏任务**
(macro)task（又称之为宏任务），可以理解是每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）。

浏览器为了能够使得JS内部(macro)task与DOM任务能够有序的执行，会在一个(macro)task执行结束后，在下一个(macro)task 执行开始前，对页面进行重新渲染，流程如下：

(macro)task->渲染->(macro)task->...

(macro)task主要包含：script(整体代码)、setTimeout、setInterval、I/O、UI交互事件、postMessage、MessageChannel、setImmediate(Node.js 环境)
##### **微任务**
microtask（又称为微任务），可以理解是在当前 task 执行结束后立即执行的任务。也就是说，在当前task任务后，下一个task之前，在渲染之前。

所以它的响应速度相比setTimeout（setTimeout是task）会更快，因为无需等渲染。也就是说，在某一个macrotask执行完后，就会将在它执行期间产生的所有microtask都执行完毕（在渲染前）。

microtask主要包含：Promise.then、MutaionObserver、process.nextTick(Node.js 环境)
##### **运行机制**
1、执行一个宏任务（栈中没有就从事件队列中获取）

2、执行过程中如果遇到微任务，就将它添加到微任务的任务队列中

3、宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）

4、当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染

5、渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

### **网络**
#### **跨域有了解过吗**
1、跨域问题的产生就是请求不同域名下的资源被拒绝访问，是浏览器的‘同源策略’ 就是一种安全机制

协议、域名、端口 三者只要有一项不同就不可以访问

2、如何解决跨域问题

1） 是服务端进行设置默认允许某些域名跨域访问

2） 从客户端入手想办法绕开同源安全策略

3）webpack-dev-server 原理和如何处理跨域

=》Webpack Proxy工作原理（本地跨域）

1> 配置在webpack的 devServer 属性中

proxy工作原理实质上是利用http-proxy-middleware 这个http代理中间件，实现请求转发给其他服务器。例如：本地主机A为http://localhost:3000，该主机浏览器发送一个请求，接口为/api，这个请求的数据（响应）在另外一台服务器Bhttp://10.231.133.22:80上，这时，就可以通过A主机设置webpack proxy，直接将请求发送给B主机

3、跨源资源共享（CORS）

1）简单请求

get、head、post

header中只有可信的字段

2）预检请求

需预检的请求”要求必须首先使用 OPTIONS   方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求

3）请求附带身份凭证 -> cookies

\* 如果发起请求时设置withCredentials 标志设置为 true，从而向服务器发送cookie

\* 但是如果服务器端的响应中未携带Access-Control-Allow-Credentials: true，浏览器将不会把响应内容返回给请求的发送者

\* 对于附带身份凭证的请求，服务器不得设置 Access-Control-Allow-Origin 的值为\*， 必须是某个具体的域名

### **优化**
做了哪些优化
#### **渲染优化**
渲染优化是前端优化中一个很重要的部分，一个好的首屏时间能给用户带来很好的体验，这里要说的一点是关于首屏时间的定义，不同的团队对首屏时间定义不一样，有的团队认为首屏时间就是白屏时间，是从页面加载到第一个画面出现的时间。但是当我们说到用户体验的时候，仅仅是这样还达不到效果，所以有的前端团队认为，首屏时间应该是从页面加载到用户可以进行正常的页面操作时间，那么我们就依照后者来进行说明

1）js css 加载顺序

- ` `问题 1：地址栏输入url 发生了什么

首先会进行 url 解析，根据 dns 系统进行 ip 查找

` `根据 ip 就可以找到服务器，然后浏览器和服务器会进行 TCP 三次握手建立连接，如果此时是 https 的话，还会建立 TLS 连接以及协商加密算法，这里就会出现另一个需要注意的问题"https 和 http 的区别"（下文会讲到）

`    `连接建立之后浏览器开始发送请求获取文件，此时这里还会出现一种情况就是缓存，建立连接后是走缓存还是直接重新获取，需要看后台设置，所以这里会有一个关注的问题"浏览器缓存机制"，缓存我们等会在讲，现在我们就当没有缓存，直接去获取文件

` `首先获取 html 文件，构建 DOM 树，这个过程是边下载边解析，并不是等 html 文件全部下载完了，再去解析 html，这样比较浪费时间，而是下载一点解析一点

` `好了解析到 html 头部时候，又会出现一种问题，css,js 放到哪里了？不同的位置会造成渲染的不同，此时就会出现另一个需要关注的问题"css,js 位置应该放哪里?为什么"，我们先按照正确的位置来说明(css 放头部,js 放尾部)

` `解析到了 html 头部发现有 css 文件，此时下载 css 文件，css 文件也是一边下载一边解析的，构建的是 CSSOM 树，当 DOM 树和 CSSOM 树全部构建完之后，浏览器会把 DOM 树和 CSSOM 树构建成渲染树。

` `样式计算, 上面最后一句"DOM 树和 CSSOM 树会一起构建成渲染树"说的有点笼统，其实还有更细一点的操作，但是一般回答到上面应该就可以了，我们现在接上面说一下构造渲染树的时候还做了哪些事情。第一个就是样式计算，DOM树 和 CSSOM树有了之后，浏览器开始样式计算，主要是为 DOM 树上的节点找到对应的样式

` `构建布局树，样式计算完之后就开始构建布局树。主要是为 DOM 树上的节点找到页面上对应位置以及一些"display:none"元素的隐藏。

` `构建分层树，布局树完成后浏览器还需要建立分层树，主要是为了满足滚动条，z-index，position 这些复杂的分层操作

` `将分层树图块化，利用光栅找到视图窗口下的对应的位图。主要是因为一个页面可能有几屏那么长，一下渲染出来比较浪费，所以浏览器会找到视图窗口对应的图块，将这部分的图块进行渲染

` `最终渲染进程将整个页面渲染出来，在渲染的过程中会还出现重排和重绘，这也是比较爱问的问题"重排重绘为什么会影响渲染，如何避免?"



- ` `问题 2：js css 顺序对前端优化影响

渲染树的构成必须要 DOM 树和 CSSOM 树的，所以尽快的构建 CSSOM 树是一个重要的优化手段，如果 css 文件放在尾部，那么整个过程就是一个串行的过程先解析了 dom，再去解析 css。所以 css 我们一般都是放在头部，这样 DOM 树和 CSSOM 树的构建是同步进行的。

再来看 js，因为 js 的运行会阻止 DOM 树的渲染的，所以一旦我们的 js 放在了头部，而且也没有异步加载这些操作的话，js 一旦一直在运行，DOM 树就一直构建不出来，那么页面就会一直出现白屏界面，所以一般我们会把 js 文件放在尾部。当然放到尾部也不是就没有问题了，只是问题相对较小，放到尾部的 js 文件如果过大，运行时间长，代码加载时，就会有大量耗时的操作造成页面不可点击，这就是另一个问题，但这肯定比白屏要好，白屏是什么页面都没有，这种是页面有了只是操作不流畅。

js 脚本放在尾部还有一个原因，有时候 js 代码会有操作 dom 节点的情况，如果放在头部执行，DOM树还没有构建，拿不到 DOM 节点但是你又去使用就会出现报错情况，错误没处理好的话页面会直接崩掉


- ` `问题 3：重排重绘为什么会影响渲染，如何避免?
  - 重绘

重绘指的是不影响界面布局的操作，比如更改颜色，那么根据上面的渲染讲解我们知道，重绘之后我们只需要在重复进行一下样式计算，就可以直接渲染了，对浏览器渲染的影响相对较小

- 重排

重排指的是影响界面布局的操作，比如改变宽高，隐藏节点等。对于重排就不是一个重新计算样式那么简单了，因为改变了布局，根据上面的渲染流程来看涉及到的阶段有样式计算，布局树重新生成，分层树重新生成，所以重排对浏览器的渲染影响是比较高的

- 避免方法

`    `\* js 尽量减少对样式的操作，能用 css 完成的就用 css

`    `\* 如果必须要用 js 操作样式，能合并尽量合并不要分多次操作resize 事件 最好加上防抖，能尽量少触发就少触发

`    `\* 加载对 dom 操作尽量少，能用 createDocumentFragment 的地方尽量用

`    `\* 加载图片的时候，提前写好宽高

- ` `问题 4：浏览器缓存机制

1）缓存方式

一种是强制缓存，一种是协商缓存

- 强制缓存

强制缓存就是文件直接从缓存中获取，不需要发送请求

- 协商缓存

协商缓存意思是文件已经被缓存了，但是否从缓存中读取是需要和服务器进行协商，具体如何协商要看请求头/响应头的字段设置，下面会说到。需要注意的是协商缓存还是发了请求的

2）缓存实现

**强缓存**

HTPP1.1 引入了一个新的响应头 cache-control 它的可选值如下

- ` `max-age: 缓存过期时间，是一个相对时间
- public: 表示客户端和代理服务器都会缓存
- ` `private: 表示只在客户端缓存
- ` `no-cache: 协商缓存标识符，表示文件会被缓存但是需要和服务器协商
- ` `no-store: 表示文件不会被缓存

HTTP1.1 利用的就是 max-age:600 来强制缓存，因为是相对时间，所以不会出现 Expires 问题

**协商缓存**

协商缓存是利用 Last-Modified/if-Modified-Since,Etag/if-None-Match 这两对请求、响应头。

- ` `Last-Modified/if-Modified-Since（文件改动时间）
- ` `Etag/If-None-Match (记录文件hash，是否文件有改动)
- 浏览器第一次发送请求获取文件缓存下来，服务器响应头返回一个 if-Modified-Since，记录被改动的时间
- 浏览器第二次发送请求的时候会带上一个 Last-Modified 请求头，时间就是 if-Modified-Since 返回的值。然后服务器拿到这个字段和自己内部设置的时间进行对比，时间相同表示没有修改，就直接返回 304 从缓存里面获取文件

3）缓存在哪里

缓存的来源有两个地方 from dist cache,from memeory cache

- ` `form memory cache

这个是缓存在内存里面，优点是快速，但是具有时效性，当关闭 tab 时候缓存就会失效。

- ` `from dist cache

这个是缓存在磁盘里面，虽然慢但是还是比请求快，优点是缓存可以一直被保留，即使关闭 tab 页，也会一直存在

#### **请求优化**
- ` `减少请求数量
- 将小图片打包成base64
- 利用雪碧图融合多个小图片
- 利用缓存上面已经说到过
- ` `减少请求时间
  - 将js，css，html等文件能压缩的尽量压缩，减少文件大小，加快下载速度
  - 用webpack打包根据路由进行懒加载，不要初始就加载全部，那样文件会很大
  - 建立内部CDN能更快速的获取文件
#### **白屏优化**
白屏的定义

- ` `首次绘制（First Paint，FP）算起到首次内容绘制（First Contentful Paint，FCP）这段时间算白屏

白屏时间 = firstPaint - performance.timing.navigationStart

白屏时间内发生了什么：

- ` `回车按下,浏览器解析网址,进行 DNS 查询,查询返回 IP,通过 IP 发出 HTTP(S) 请求
- ` `服务器返回HTML,浏览器开始解析 HTML,此时触发请求 js 和 css 资源
- ` `js 被加载,开始执行 js,调用各种函数创建 DOM 并渲染到根节点,直到第一个可见元素产生

#### **打包优化**

#### **VUE优化**
##### **代码层面**
**1、利用v-if和v-show减少初始化渲染和切换渲染的性能开销**

**2、computed、watch、methods区分使用场景**

- computed：

一个数据受多个数据影响的。

该数据要经过性能开销比较大的计算，如它需要遍历一个巨大的数组并做大量的计算才能得到，这时就可以利用computed的缓存特性，只有它计算时依赖的数据发现变化时才会重新计算，否则直接返回缓存值。

- watch：

一个数据影响多个数据的。

当数据变化时，需要执行异步或开销较大的操作时。如果数据变化时请求一个接口。

- methods：

希望数据是实时更新，不需要缓存。

**3、提前处理好数据解决v-if和v-for必须同级的问题**

- ` `因为当Vue处理指令时，v-for比v-if具有更高的优先级，意味着v-if 将分别重复运行于每个v-for循环中。
- ` `永远不要把 v-if 和 v-for 同时用在同一个元素上。一般我们在两种常见的情况下会倾向于这样做：
- ` `可以在computed中提前把要v-for的数据中v-if的数据项给过滤处理了。

**问：v-for比v-if具有更高的优先级**

总结来说是编译有三个过程，parse->optimize->codegen。在codegen过程中，会先解析AST树中的与v-for相关的属性，再解析与v-if相关的属性

**4、给v-for循环项加上key提高diff计算速度**

- ` `为什么加key会提高diff计算速度。

经过旧头新头、旧尾新尾、旧头新尾、旧尾新头四次交叉比对后，都没有匹配到值得比对的节点，这时如果新节点有key的话。可以通过map直接获得值得对比的旧节点的下标，如果没有key的话，就要通过循环旧节点数组用sameVnode方法判断新节点和该旧节点是否值得比较，值得就返回该旧节点的下标。显然通过map比通过循环数组的计算速度来的快。

- ` `什么是diff计算

对于渲染watcher触发时会执行vm.\_update(vm.\_render(), hydrating)，在vm.\_undata方法中会调用vm.\_\_patch\_\_，而vm.\_\_patch\_\_指向patch方法，diff计算是指在调用patch方法开始，用sameVnode方法判断节点是否值得比较，若不值得直接新节点替换旧节点后结束。值得对比进入patchVnode方法，分别处理一下几种情况，若新旧节点都有文本节点，新节点下的文本节点直接替换旧节点下的文本节点，如果新节点有子节点，旧节点没有子节点，那么直接把新节点查到旧节点的父级中，如果新节点没有子节点，旧节点有子节点，那么旧节点的父级下的子节点都删了。如果新旧节点都有子节点，进入updateChildren方法，通过旧头新头、旧尾新尾、旧头新尾、旧尾新头四次交叉比对，如果值得对比再进入patchVnode方法，如果都不值得对比，有key用map获得值得对比的旧节点，没有key通过循环旧节点获得值得对比的旧节点。当新节点都对比完，旧节点还没对比完，将还没对比完的旧节点删掉。当旧节点都对比完，新节点还没对比完，将新节点添加到最后一个对比过的新节点后面，完成diff计算

**在采取diff算法比较新旧节点的时候，比较只会在同层级进行, 不会跨层级比较。**

- 每次对比的逻辑大概如下所示

1、在patch方法内，用sameVnode判断新旧节点是否值得比较。

2、如果不值得比较，直接在旧节点的父级中添加新节点，然后删除旧节点，退出对比。

3、如果值得比较，调用patchVnode方法。

4、如果新旧节点是否完全相等，如果是，退出对比。

5、如果不是，找到对应的真实DOM，记为el。

6、如果新旧节点都有文本节点并且不相等，那么将el的文本节点设置为新节点的文本节点，退出对比。

7、如果新节点有子节点，旧节点没有子节点，则将新节点的子节点生成真实DOM后添加到el中，退出对比。

8、如果新节点没有子节点，旧节点有子节点，则删除el的子节点，退出对比。

9、如果新节点和旧节点都有子节点，则开始对比它们的子节点，用的是updateChildren方法。

10、将旧节点的子节点记为oldCh是个数组,其头部用oldCh[oldStartIdx]获取记为oldStartVnode，oldStartIdx初始为0。其尾部用oldCh[oldEndIdx]获取记为oldEndVnode，oldEndIdx初始为oldCh.length - 1。

11、将旧节点的子节点记为newCh是个数组,其头部用newCh[newStartIdx]获取记为newStartVnode，newStartIdx初始为0。其尾部用newCh[newEndIdx]获取记为newEndVnode，newEndIdx初始为newCh.length - 1。

12、将旧子节点的头部和新子节点的头部，简称旧头和新头用sameVnode判断是否值得比较。

13、如果值得比较，调用patchVnode方法，重新执行第3步。同时用oldCh[++oldStartIdx]重新获取旧子节点头部，用newCh[++newStartIdx]重新获取新子节点头部。

14、如果不值得比较，将旧子节点的尾部和新子节点的尾部，简称旧尾和新尾用sameVnode判断是否值得比较。

15、如果值得比较，调用patchVnode方法，重新执行第3步。同时用oldCh[--oldEndIdx]重新获取旧子节点尾部，重新用newCh[--newEndIdx]获取新子节点尾部。

16、如果不值得比较，将旧子节点的头部和新子节点的尾部，简称旧头和新尾用sameVnode判断是否值得比较。

17、如果值得比较，调用patchVnode方法，重新执行第3步。同时将旧子节点的头部oldStartVnode对应的真实DOM移动到旧子节点的尾部oldEndVnode对应的真实DOM后面。同时用oldCh[++oldStartIdx]重新获取旧子节点头部，用newCh[--newEndIdx]重新获取新子节点尾部。

18、如果不值得比较，将旧子节点的尾部和新子节点的头部，简称旧尾和新头用sameVnode判断是否值得比较。

19、如果值得比较，调用patchVnode方法，重新执行第3步。同时将旧子节点的尾部oldEndVnode对应的真实DOM移动到旧子节点的头部oldStartVnode对应的真实DOM后面。同时用oldCh[--oldEndIdx]重新获取旧子节点尾部，用newCh[++newStartIdx]重新获取新子节点头部。

20、如果不值得比较，如果旧子节点有key，可以用createKeyToOldIdx方法获得以旧子节点的key为健，其下标为值的map结构,记为oldKeyToIdx。

21、如果新子节点的头部newStartVnode有key属性，直接通过oldKeyToIdx[newStartVnode.key]获取对应的下标idxInOld。

22、如果新子节点的头部newStartVnode没有key属性，要用过findIdxInOld方法，找到值得对比的旧子节点对应的下标idxInOld。

23、经过查找。如果idxInOld不存在。则调用createElm方法直接生成newStartVnode对应的真实DOM插入oldStartVnode对应真实DOM前面。

24、如果idxInOld存在，则把用通过oldCh[idxInOld]获取到Vnode记为vnodeToMove和newStartVnode用sameVnode判断是否值得比较。

25、如果值得比较，调用patchVnode方法，重新执行第3步。同时执行oldCh[idxInOld] = undefined，免得被重复比较。同时将vnodeToMove对应的真实DOM移动到旧子节点的头部oldStartVnode对应的真实DOM前面。

26、如果不值得比较，则调用createElm方法直接生成newStartVnode对应的真实DOM插入oldStartVnode对应真实DOM前面。

27、用newCh[++newStartIdx]重新获取新子节点头部

28、如果满足oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx继续执行步骤9。

29、如果oldStartIdx > oldEndIdx，说明所有旧子节点已经都比对完了，还剩下未比对的新子节点都调用createElm方法生成对应的真实DOM，插到newCh[newEndIdx + 1]对应的真实DOM后面。

30、如果newStartIdx > newEndIdx，说明所有新子节点都比较完，那么还剩下的旧子节点都删除掉。

**5、利用v-once处理只会渲染一次的元素或组件**

**6、利用Object.freeze()冻结不需要响应式变化的数据**

**7、提前过滤掉非必须数据，优化data选项中的数据结构**

**8、避免在v-for循环中读取data中数组类型的数据**

**9、防抖和节流**

- ` `防抖：触发事件后规定时间内事件只会执行一次。简单来说就是防止手抖，短时间操作了好多次。
- ` `节流：事件在规定时间内只执行一次。

**10、利用import()异步引入组件实现按需引入**

**11、组件库的按需引入**

babelrc.js

{

`  `"presets": [["es2015", { "modules": false }]],

`  `"plugins": [

`    `[

`      `"component",

`      `{

`        `"libraryName": "element-ui",

`        `"styleLibraryName": "theme-chalk"

`      `}

`    `]

`  `]

}

##### **webpack层面**
- 对图片进行压缩
- 使用 CommonsChunkPlugin 插件提取公共代码
- 提取组件的 CSS
- 优化 SourceMap
- 构建结果输出分析，利用 webpack-bundle-analyzer 可视化分析工具

### **CSS**
1、如何编写sass函数

@function function-name($args) { 

`    `@return value-to-be-returned;

}

$total: 8; 

$col: 3

@function column-width() { 

`  `@return percentage($col/$total); 

}

2、如何编写less-mixin

.mixin(@color; @padding:2) {

`  `color-2: @color;

`  `padding-2: @padding;

}


### **打包**
#### **webpack**

#### **打包优化**
<https://juejin.cn/post/6857856269488193549#heading-17>

### **思维逻辑：**
1、如果让你做一个消息队列，说下你的思路

1）收消息，消费消息

2）在一定时间内收消息，然后批量处理

### **VUE3**
1. Vue 3.0 性能提升主要是通过哪几方面体现的

1）响应式系统提升

vue2在初始化的时候，对data中的每个属性使用definepropery调用getter和setter使之变为响应式对象。如果属性值为对象，还会递归调用defineproperty使之变为响应式对象。

vue3使用proxy对象重写响应式。proxy的性能本来比defineproperty好，proxy可以拦截属性的访问、赋值、删除等操作，不需要初始化的时候遍历所有属性，另外有多层属性嵌套的话，只有访问某个属性的时候，才会递归处理下一级的属性。

优势：

可以监听动态新增的属性；

可以监听删除的属性 ；

可以监听数组的索引和 length 属性；

2）编译优化

优化编译和重写虚拟dom，让首次渲染和更新dom性能有更大的提升 vue2 通过标记静态根节点,优化 diff 算法 vue3 标记和提升所有静态根节点,diff 的时候只比较动态节点内容

Fragments, 模板里面不用创建唯一根节点,可以直接放同级标签和文本内容


3）源码体积的优化

vue3移除了一些不常用的api，例如：inline-template、filter等 使用tree-shaking

2、Composition Api 与 Vue 2.x使用的Options Api 有什么区别？

Options Api

包含一个描述组件选项（data、methods、props等）的对象 options；

API开发复杂组件，同一个功能逻辑的代码被拆分到不同选项 ；

使用mixin重用公用代码，也有问题：命名冲突，数据来源不清晰；

composition Api

vue3 新增的一组 api，它是基于函数的 api，可以更灵活的组织组件的逻辑。

解决options api在大型项目中，options api不好拆分和重用的问题。

3、Proxy 相对于 Object.defineProperty

proxy的性能本来比defineproperty好，proxy可以拦截属性的访问、赋值、删除等操作，不需要初始化的时候遍历所有属性，另外有多层属性嵌套的话，只有访问某个属性的时候，才会递归处理下一级的属性。

可以\* 监听数组变化

可以劫持整个对象

操作时不是对原对象操作,是 new Proxy 返回的一个新对象

可以劫持的操作有 13 种

### **WebkSocket**
服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。

1）与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443 ，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器

2）建立在TCP协议基础之上，和http协议同属于应用层数据格式比较轻量，性能开销小，通信高效。 

3）可以发送文本，也可以发送二进制数据。 

4）没有同源限制，客户端可以与任意服务器通信协议标识符是ws（如果加密，则为wss）

#### **如何建立一个websocket，流程**
function startWS() {

`    `ws = new WebSocket('ws://127.0.0.1:8001');

`    `ws.onopen = function (msg) {

`        `console.log('WebSocket opened!');

`    `};

`    `ws.onmessage = function (message) {

`    `};

`    `ws.onerror = function (error) {

`        `console.log('Error: ' + error.name + error.number);

`    `};

`    `ws.onclose = function () {

`        `console.log('WebSocket closed!');

`    `};

}

function sendMessage() {

`    `console.log('Sending a message...');

`    `var text = document.getElementById('text');

`    `ws.send(text.value);

}
#### **websocket状态码**
状态码 名称 描述

0–999 - 保留段, 未使用。

1000 CLOSE\_NORMAL 正常关闭; 无论为何目的而创建, 该链接都已成功完成任务。

1001 CLOSE\_GOING\_AWAY 终端离开, 可能因为服务端错误, 也可能因为浏览器正从打开连接的页面跳转离开。

1002 CLOSE\_PROTOCOL\_ERROR 由于协议错误而中断连接。

1003 CLOSE\_UNSUPPORTED 由于接收到不允许的数据类型而断开连接 (如仅接收文本数据的终端接收到了二进制数据)。

1004 - 保留。 其意义可能会在未来定义。

1005 CLOSE\_NO\_STATUS 保留。 表示没有收到预期的状态码。

1006 CLOSE\_ABNORMAL 保留。 用于期望收到状态码时连接非正常关闭 (也就是说, 没有发送关闭帧)。

1007 Unsupported Data 由于收到了格式不符的数据而断开连接 (如文本消息中包含了非 UTF-8 数据)。

1008 Policy Violation 由于收到不符合约定的数据而断开连接。 这是一个通用状态码, 用于不适合使用 1003 和 1009 状态码的场景。

1009 CLOSE\_TOO\_LARGE 由于收到过大的数据帧而断开连接。

1010 Missing Extension 客户端期望服务器商定一个或多个拓展, 但服务器没有处理, 因此客户端断开连接。

1011 Internal Error 客户端由于遇到没有预料的情况阻止其完成请求, 因此服务端断开连接。

1012 Service Restart 服务器由于重启而断开连接。 [Ref]

1013 Try Again Later 服务器由于临时原因断开连接, 如服务器过载因此断开一部分客户端连接。 [Ref]

1014 - 由 WebSocket 标准保留以便未来使用。

1015 TLS Handshake 保留。 表示连接由于无法完成 TLS 握手而关闭 (例如无法验证服务器证书)。

1016–1999 - 由 WebSocket 标准保留以便未来使用。

2000–2999 - 由 WebSocket 拓展保留使用。

3000–3999 - 可以由库或框架使用。 不应由应用使用。 可以在 IANA 注册, 先到先得。

4000–4999 - 可以由应用使用。
#### **websoket端口**
Websocket默认使用请求协议为:ws://,默认端口:80。对TLS加密请求协议为:wss://，端口:443。
#### **握手过程：**
1、浏览器、服务器建立TCP连接，三次握手。这是通信的基础，传输控制层，若失败后续都不执行。

2、TCP连接成功后，浏览器通过HTTP协议向服务器传送WebSocket支持的版本号等信息。（开始前的HTTP握手）

3、服务器收到客户端的握手请求后，同样采用HTTP协议回馈数据。

4、当收到了连接成功的消息后，通过TCP通道进行传输通信。
#### **webSocket与HTTP的关系**
**相同点：**

都是一样基于TCP的，都是可靠性传输协议。

都是应用层协议。

**不同点:**

WebSocket是双向通信协议，模拟Socket协议，可以双向发送或接受信息。

HTTP是单向的。

WebSocket是需要握手进行建立连接的。

**联系：**

WebSocket在建立握手时，数据是通过HTTP传输的。但是建立之后，在真正传输时候是不需要HTTP协议的。
#### **Websocket握手**
- Websocket握手请求报文：

GET /chat HTTP/1.1

Host: server.example.com

Upgrade: websocket

Connection: Upgrade

Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==

Sec-WebSocket-Protocol: chat, superchat

Sec-WebSocket-Version: 13

Origin: http://example.com

复制代码

1、101 响应码 表示要转换协议。

2、Connection: Upgrade 表示升级新协议请求。

3、Upgrade: websocket 表示升级为 WebSocket 协议。

4、Sec-WebSocket-Accept 是经过服务器确认，并且加密过后的 Sec-WebSocket-Key。用来证明客户端和服务器之间能进行通信了。

5、Sec-WebSocket-Protocol 表示最终使用的协议。

### **Electron**
#### **electron 的主进程，渲染进程之间区别和他们通信手段**
通过 ipc（进程间通信）模块允许您在主进程和渲染进程之间发送和接收同步和异步消息.

这个模块有一个版本可用于这两个进程: ipcMain 和 ipcRenderer.
##### **渲染进程**
由于 Electron 使用 Chromium 来展示页面，所以 Chromium 的多进程结构也被充分利用。每个 Electron 的页面都在运行着自己的进程，这样的进程我们称之为渲染进程。

也就是说每创建一个 web 页面都会创建一个渲染进程。每个 web 页面都运行在它自己的渲染进程中。每个渲染进程是独立的，它只关心它所运行的页面

const ipc = require('electron').ipcRenderer

const asyncMsgBtn = document.getElementById('async-msg')

asyncMsgBtn.addEventListener('click', function () {

`  `ipc.send('asynchronous-message', 'ping')

})

ipc.on('asynchronous-reply', function (event, arg) {

`  `const message = `异步消息回复: ${arg}`

`  `document.getElementById('async-reply').innerHTML = message

})
##### **主进程**
在 Electron 中，启动项目时运行的 main.js 脚本就是我们说的主进程。在主进程运行的脚本可以以创建 web 页面的形式展示 GUI。

const ipc = require('electron').ipcMain

ipc.on('asynchronous-message', function (event, arg) {

`  `event.sender.send('asynchronous-reply', 'pong')

})
#### **主进程、渲染进程区别**
主进程使用 BrowserWindow 实例创建网页。每个 BrowserWindow 实例都在自己的渲染进程中运行。当一个 BrowserWindow 实例被销毁后，相应的渲染进程也会被终止

#### **sqlite使用**
const sqlite3 = require('sqlite3').verbose();

//创建内存数据库

let db = new sqlite3.Database(':memory:', (err) => {

`    `if (err) {

`        `return console.error(err.message);

`    `}

`    `console.log('已经成功连接SQLite数据库');

});


### **React**
#### **必须知道的规则**
1、state 只能在 constructor 中初始化

2、props 是不可变对象

3、componentDidMount 对在一个组件的 Lifecycle 中只会被调用一次

4、需要给 useEffect 一个依赖列表 —— 起码是一个空数组.

#### **定义组件**

#### **谈一下React 的Fiber架构和Hooks**

#### **什么是状态提升**

#### **React 性能优化**

#### **React 重用Hock**
##### **.useRef vs .useState**
当可以在你在函数式组件中想按你永远直觉预期获取到状态最新值的时, 就用 .useRef.

![](Aspose.Words.5ce428a6-aee7-4edd-adcf-ec7c422e07f7.001.png)
##### **依赖列表(Deps)**
我们需要给 useEffect 一个依赖列表 —— 起码是一个空数组.

**deps 什么时候为 []？**

可以将 componentDidMount 中的数据请求逻辑放在 React.useEffect(callback, [])，生命周期内只会调用一次

**如果 deps 不为空会如何？**

依赖值变化，做处理
##### **.useRef**
.useRef 是提供了一个保存值的容器, 并允许你能严格按顺序读取它.

.useState 不同, 更新 sthRef.current 不会引起 Functional Comopnent 的 re-render.

const sthRef = useRef(null)

sthRef.current = 1;

setTimout(() => {

`    `sthRef.current = 3;

}, 3000);

setTimeout(() => {

`    `sthRef.current = 9;

}, 9000);
##### **利用useTimeout**
import { useEffect, useRef } from 'react'

function useTimeout(callback: () => void, delay: number | null) {

`  `const savedCallback = useRef(callback)

`  `// Remember the latest callback if it changes.

`  `useEffect(() => {

`    `savedCallback.current = callback

`  `}, [callback])

`  `// Set up the timeout.

`  `useEffect(() => {

`    `// Don't schedule if no delay is specified.

`    `if (delay === null) {

`      `return

`    `}

`    `const id = setTimeout(() => savedCallback.current(), delay)

`    `return () => clearTimeout(id)

`  `}, [delay])

}

export default useTimeout

\*

import React, { useState } from 'react'

import useTimeout from './useTimeout'

export default function Component() {

`  `const [visible, setVisible] = useState(true)

`  `const hide = () => setVisible(false)

`  `useTimeout(hide, 5000)

`  `return (

`    `<div>

`      `<p>

`        `{visible

`          `? "I'm visible for 5000ms"

`          `: 'You can no longer see this content'}

`      `</p>

`    `</div>

`  `)

}

#### **React Hooks 中 useEffect "闭包陷阱"**
**出现原因：**

react用了链表这种数据结构来存储 FunctionComponent 里面的 hooks

type Hook = {

`  `memoizedState: any,

`  `baseState: any,

`  `baseUpdate: Update<any, any> | null,

`  `queue: UpdateQueue<any, any> | null,

`  `next: Hook | null,

};

// 回调陷阱

function App(){

`    `const [count, setCount] = useState(1);

`    `useEffect(()=>{

`        `setInterval(()=>{

`            `console.log(count)

`        `}, 1000)

`    `}, [])

`    `function click(){ setCount(2) }

}

// 组件第一次渲染的时候 App() 时的 count， count的值为1，因为在定时器的回调函数里面被引用了，形成了闭包一直被保存
##### **为什么使用useRef能够每次拿到新鲜的值**
在组件每一次渲染的过程中。 比如 ref = useRef() 所返回的都是同一个对象，每次组件更新所生成的ref指向的都是同一片内存空间， 那么当然能够每次都拿到最新鲜的值


#### **React Hooks 里的 useCallback 更新幽灵**
##### **useCallback 的用法**
const memoizedCallback = useCallback(

`  `() => {

`    `doSomething(a, b);

`  `},

`  `[a, b],

);
##### **基本工作原理**

|<p>1、在一个 FC 中, 包装一个函数, 这个函数会被"记住", 如果没有别的原因, 这个函数在 FC 多次被调用的过程中是不变的 (也就是 react hook 的状态)</p><p>2、如果 useCallback(cb, deps) 的 deps 列表中有任意一项发生了变化 (对于 primitive type 而言, 是值的变化; 对于 object 而言, 是引用的变化/内存地址的变化), 则 memoizedCallback 会被更新, 并且在 FC 的下一次渲染中使用新的副本.</p>|
| :- |
**幽灵场景**

只有当 FC 进行重新渲染的时候, useCallback(cb, [sortBy, orderBy])所标记的有状态的函数副本才会被更新

+++++++++ first time render ++++++++

sortBy := useState()

orderBy := useState()

loadData = useCallback(cb, [sortBy, orderBy]) // now it's snapshot\_v1

handlePageChange = useCallback(cb, [loadData]) 

...(asynchronous -- "when table head of f1 colum clicked": 

------- setSortBy(xxx) ---> update sortBy on next tick

------- setOrderBy(xxx) ---> update orderBy on next tick

------- loadData() --- still snapshot\_v1, hasn't been updated!

)

...( trigger re-render )

+++++++++ second time render ++++++++

sortBy := useState() <--- updated

orderBy := useState() <--- updated

loadData = useCallback(cb, [sortBy, orderBy]) // now it's snapshot\_v2

handlePageChange = useCallback(cb, [loadData])

...

\*

显而易见, 当第一次 f1 列的表头被点击的时候, 尽管我们已经调用 setSortBy, setOrderBy, 但紧接着立刻被调用的 loadData版本依然是之前的 snapshot\_v1!

因此, 和我们的预期相比, 实际上你会感觉每一次"点击 f1 列的表头"对应的更新数据动作总是会"慢一拍", 因为 loadData的值总是比你认为的版本要旧. 这个旧版本值像一个幽灵 , 总是跟在你的最新操作中.

\*

**改进**

于这种"幽灵"情况, 不要在 setSortBy, setOrderBy 之后, 立刻调用对它们的值有依赖的 loadData. 我们可以转为另外两种方式:

1、当 handlePageChange 被触发时, 将 sortBy 和 orderBy 直接作为参数传递给 loadData.

1. (不推荐)使用 useEffect 观察 sortBy 和 orderBy 的变化, 当两个值变化时, 再调用 loadData.

方式 2 的问题是: 你可能无法保证按序调用的 setSortBy, setOrderBy 所引起的状态变更总是发生在同一批 React State Update 中

## **前端监控系统**
### **Sentry**


