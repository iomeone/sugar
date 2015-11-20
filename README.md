# sugar
A JavaScript framework for building modular and component frontend projects

一个构建模块化、组件化前端项目的JavaScript框架


# 1、简单介绍
* 目录结构：

	`examples` 基本使用示例

	`jquery` jquery-2.1.4，sugar的依赖库之一（以后可以考虑去除该依赖）

	`vue` vue-1.0.4，视图层mvvm库，sugar的依赖库之二

	`sugar.js` sugar主文件，内部自动加载依赖

	`sugar.sea.js` 为seajs加载器包装的模块文件

	`util.js` 辅助功能函数库，sugar的依赖库之三

* 依赖的两个库jquery和vue在sugar的作用：

	vue只用到了它的数据绑定；jquery只用了ajax和视图的DOM操作。两者都是为了方便模块布局而引入的工具库，去掉这两个依赖也是可以的。整个sugar的模块树构建是基于模块继承`Root.extend`和模块定义类`sugar.Module`，而不是vue中的Component。

	这些天，关于sugar.js大家主要吐槽点集中在vue上了，的确，为了数据绑定就依赖了vue，就像为了吃薯条买了个全家桶一样。接下来难道要自己写个mvvm渲染引擎？唉，这轮子不太好造……


# 2、使用
以seajs作为模块加载器为例，创建一个简单的页面（模块）：

页面入口文件：`index.js`

```javascript
var sugar = require('sugar');
var $ = sugar.jquery;

// 异步创建page.js模块到body中：
sugar.core.createAsync('page', 'modules/page.base', {
	'target': $('body')
});
```

页面模块文件：`modules/page.js`

```javascript
var sugar = require('sugar');

/*
 * 定义Page模块，继承于sugar.Container（约定了视图模块基础功能和API的模块类）
 * 之后Page相当于获得了一个独立的视图，布局展现、数据和逻辑行为都可以通过API来完成
 */
var Page = sugar.Container.extend({
	init: function(config) {
		config = sugar.cover(config, {
			'class'   : 'mainPage',
			'cbRender': 'afterRender',
			// 模板文件地址
			'template': 'page.html',
			// vue数据配置
			'vModle'  : {
				'navs'       : [], // 导航数据
				'footContent': '<h2>Welcome to use sugar.js</h2>' // 页脚内容
			}
		});
		this.Super('init', [config]);
	},
	afterRender: function() {
		var navs = [{'text': '导航A'}, {'text': '导航B'}, {'text': '导航C'}];
		// 将数据渲染到MVVM：
		this.vm.set('navs', navs);

		// 创建body子模块（组件）
		this.createTplModules({
			// 子模块（组件）body的模块配置参数
			'body': {
				'html': '这是body模块的内容，在body模块内可以继续创建子模块。'
			}
		});
	}
});
exports.base = Page;
```

页面模板文件：`page.html`

```html
<!-- 头部导航 -->
<div class="header">
	<ul>
		<li v-for="item in navs">{{item.text}}</li>
	</ul>
</div>
<!-- 中间的body模块/组件 -->
<div class="body" m-name="body" m-module="modules/body.base"></div>
<!-- 页脚 -->
<div class="footer" v-html="footContent"></div>
```

page的子模块（组件）body：`modules/body.js`

```javascript
var sugar = require('sugar');

var Body = sugar.Container.extend({
	init: function(config) {
		config = sugar.cover(config, {
			'class': 'bodyComponent',
			'html' : '',
			'css'  : null
		});
		this.Super('init', arguments);
	}
});
exports.base = Body;
```
最终效果：

<img src="http://7xodrz.com1.z0.glb.clouddn.com/tbc_sugar_example" width="500">


# 3、更多demo
以下是用requirejs作为模块加载器创建的4个小例子：
* <a href="http://www.tangbc.com/blog/sugar/examples/demo1" target="_blank">demo1：创建一个简单的模块化页面</a>
* <a href="http://www.tangbc.com/blog/sugar/examples/demo2" target="_blank">demo2：异步创建一个模块</a>
* <a href="http://www.tangbc.com/blog/sugar/examples/demo3" target="_blank">demo3：利用模板和MVVM构建模块布局</a>
* <a href="http://www.tangbc.com/blog/sugar/examples/demo4" target="_blank">demo4：模块的复用与继承</a>

另外，我的博客项目<a href="https://github.com/tangbc/blog" target="_blank">blog</a>也是基于sugar来构建的，有兴趣可以去参考下


# 4、API
详见：<a href="https://github.com/tangbc/sugar/blob/master/API.md" target="_blank">API.md</a>


# 5、改进&建议
由于水平有限，sugar必然存在很多不足之处，欢迎各种意见、提bug和issues！


# 6、At last
最后推荐一首适合边写代码边听的英文歌：<a href="http://music.163.com/#/song?id=29019227" target="_blank">Suagr - Maroon 5</a>