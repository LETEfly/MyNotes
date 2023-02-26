# ExtJS

## 什么是ExtJs?

ExtJS可以用来开发RIA也即富客户端的AJAX应用，是一个用javascript写的，主要用于创建前端用户界面，是一个与后台技术无关的前端Ajax框架。因此，可以把ExtJS用在.Net、Java、Php等各种开发语言开发的应用中。
ExtJS的前身来自于YUI，经过不断发展与改进，现在已经成为最完整与成熟的一套构建RIA Web应用的JavaScript基础库。利用ExtJS构建的RIA Web应用具有与桌面程序一样的标准用户界面与操作方式，并且能够横跨不同的浏览器平台。ExtJS已经成为开发具有完满用户体验的Web应用完美选择。
ExtJs最开始基于YUI技术，其UI组件模型和开发理念脱胎、成型于Yahoo组件库YUI和Java平台上Swing两者，并为开发者屏蔽了大量跨浏览器方面的处理。相对来说，EXT要比开发者直接针对DOM、W3C对象模型开发UI组件轻松。

## ExtJs的特点

- 纯Html/CSS+JS技术,重新定义表示层的耦合；

  基于纯Html/CSS+JS技术，提供丰富的跨浏览器UI组件，灵活采用JSON/XML数据源开发，使得服务端表示层的负荷真正减轻，从而达到客户端的MVC应用；

- 集成多种JS底层库， 满足开发者不同需求；

  Ext初期仅是对YUI的对话框扩展，后来逐渐有了自己的特色，深受网友的喜爱。发展至今，Ext除YUI外还支持Jquery Prototype等的JS库，让大家自由地选择；

- 多浏览器支持、支持多平台下的主流浏览器。

## ExtJs的优缺点

### ExtJs的优点

- UI组件丰富，外观漂亮。

  Ext JS库有着丰富且漂亮的UI组件，大大缩短了我们的开发周期，而且组件拥有漂亮的布局，经过简单的调用与配置就可以实现不错的界面布局。ExtJS提供的各种组件可以用更加标准的方式展示数据降低了开发难度。

- 浏览器兼容性好。

  使用ExtJS对浏览器没有任何要求。可以说是一种绿色的富客户端实现方式，ExtJs基本可以运行于现在主流的浏览器。

- 有很多动画效果做得很不错，提高了用户的感知度。

- 和后台代码无关。

  不管后台用什么语言开发的都不会受影响，不管你是用C#也好 JAVA也好 还是PHP都和它没关系。

- 将Web程序向桌面系统转化。

  ExtJS最大的优势在于它将Web应用程序的操作方式向传统桌面应用程序的操作方式进行转化甚至消除了这种差异，从根本上提高了用户的使用体验，这是ExtJS应用前景广阔的主要原因。

- 相对丰富的文档和示例。

  毫无疑问，刚刚接触到ExtJS的人多数都是被它附带的例子和开发文档吸引过去的，它的文档做的确实不错。

### ExtJs的缺点

- 体积较大，速度稍慢。

  由于使用了大量的UI组件，所以体积较大，导致页面加载速度比较慢。　

- 收费,好像不免费。

  因为它太优秀了，所以从Ext JS 2.0以后的版本都是收费的。也许这一点不能算是它的缺点，但这确实阻碍了它的推广与应用。

- 没有合适的开发利器。

  毫无疑问，一个好的开发工具可以大大的提高编码的速度，但是对于ExtJS，始终没有一个完美的开发工具，可以推荐的有Aptana Studio， Spket IDE，和Spket 提供的提示文件，但是都是各有优缺点，都不完美，只能一边看SDK一边写代码。

- 没有界面设计工具。

  虽然有人提供了一个在线的界面设计工具，但是和Visual Studio提供的ASP.Net设计工具来说，真的可以说是天壤之别。因此，只能一边预览，一边写代码。

- 文档不全。

  虽然ExtJS提供的文档很丰富，但是还是跟不上源代码的更新速度，所以，经常要通过看源代码，调试才能真正解决问题。

- 不能编译。

  这一点可以说是JavaScript的缺点（如果能编译，就不叫JavaScript了），在实际的开发中，经常会敲错一些代码，比如大小写错误等，不能通过编译得到反馈，只能在运行时排错，导致开发的效率比较低下。

## ExtJs注意事项

- 尽量使用ExtJS的方言。

  ExtJS提供了很多有用的方法，解决客户端JavaScript常见的开发任务，常见的有查询HTMLDom，创建HTML元素，为HTML元素注册事件响应函数等，这些大可以全部使用ExtJS提供的方法，使自己代码构建与ExtJS之上，举几个例子：

> 查询ID为container的DIV下所有的checkbox，可以使用：Ext.fly(‘container’).select(‘input[type=checkbox]’);
> 在ID为container的DIV内创建一个按钮，可以使用：Ext.fly(‘container’).createChild({ tag: ‘input’, type: ‘button’});
> 为ID为container的DIV的click事件注册处理函数，使用：Ext.fly(‘container’).on(‘click’, handlerFn, scope);

- 自定义事件比较好。

  ExtJS的自定义事件很好用，可以实现一对多的通知，而且任何自定义事件都可以中途停止，只要有一个处理函数返回false。

- Store合并为一个文件。

  用ExtJS显示数据，自然就需要用到Ext.data.Store及其派生出来的类，可以考虑所有的Store合并到一个文件，这样对重用有很大的帮助。

- 脚本文件管理。

  尽可能的每个模块做成一个类，一个类一个文件，类似与Java或C# 的文件处理方法，每个文件注明其作用，依赖的文件等，如果太多的话可以考虑写一个配置文件，通过读配置文件来输出脚本到客户端。

- 调试和部署注意。

  调试和部署分别加载Debug和Release版本的脚本 ExtJS附带的例子中没有使用完整Debug版本的例子，所以很多人找不到完整的Debug版本的引用顺序，通过对Source文件夹下的ext.jsb文件进行分析，就可以得到正确的加载顺序，如下：

  ```
  Debug
  /ext-path/source/core/ext.js
  /ext-path/source/adapter/ext-base.js
  /ext-path/ext-all-debug.js
  Release
  /ext-path/adapter/ext/ext-base.js
  /ext-path/ext-all.js
  ```

- 对Script进行压缩。

  对项目中有大量的JavaScript的话，对其进行压缩是很有必要的，这里我推荐的是ExtJS的论坛提供的JS Builder，可以通过配置文件来对Script和CSS进行压缩，据说ExtJS就是用这个工具进行压缩的，不过有一个缺点，就是不支持UTF-8编码。