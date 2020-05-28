# WePY HTML

## 简介

「WePY HTML」是基于「WePY」小程序框架所开发的富文本内容组件。与小程序原生的「rich-text」组件相比，本组件能实现更多交互效果；与「wxParse」相比，本组件的解析性能更高、灵活性更强。

（注意：本组件目前仅支持基于「WePY」开发的微信小程序项目）

## 安装

在小程序项目目录下安装本组件：

``` bash
npm install wepy-html --save
```

此外，还要安装一个构建插件：

``` bash
npm install wepy-plugin-replace --save-dev
```

## 调用

首先要在「wepy.config.js」中增加构建步骤（开发和生产都要加）：

``` javascript
{
	plugins: {
		replace: {
			filter: /\.wxml$/,
			config: {
				find: /<\!-- wepyhtml-repeat start -->([\W\w]+?)<\!-- wepyhtml-repeat end -->/,
				replace(match, tpl) {
					let result = '';
					let prefix = '';

					tpl = tpl.replace(/\{\{\s*(\$.*?\$)thisIsMe\s*\}\}/, (match, p) => {
						prefix = p;
						return '';
					});

					for (let i = 0; i <= 20; i++) {
						result += '\n' + tpl
							.replace('wepyhtml-0', 'wepyhtml-' + i)
							.replace(/<\!-- next template -->/g, () => {
								return i === 20 ?
									'' :
									`<template is="wepyhtml-${ i + 1 }" wx:if="{{ item.children }}" data="{{ ${ prefix }content: item.children, ${ prefix }imgInsteadOfVideo: ${ prefix }imgInsteadOfVideo }}"></template>`;
							});
					}
					return result;
				}
			}
		}
	}
}
```

然后就可以在页面（page.wpy）中调用组件了，例如：

``` html
<template lang="wxml">
	<wepy-html />
</template>

<style>
/* 可以覆盖默认样式 */
.wepyhtml-tag-p {
	margin: 1em 0;
}
</style>

<script>
import WepyHTML from 'wepy-html';

export default class Page extends wepy.component {
	components: {
		'wepy-html': WepyHTML
	},

	onLoad() {
		// 调用组件接口，传入HTML内容和相关参数
		this.$invoke('wepy-html', 'updateContent', htmlCode, {
			// 是否使用图片代替视频（点击时全屏播放视频），
			// 主要用于防止视频组件遮挡其他元素
			imgInsteadOfVideo: true,

			// 可以在此函数中处理链接点击
			onHyperlinkTap(e) {
				// e.href 为链接地址
			},

			// 可以在此函数中修改属性
			onNodeCreate(nodeName, attrs) {
				// nodeName 为标签名
				// attrs 为属性集合
			}
		});
	}
}
</script>
```
## 腾迅视频支持
支持解析如下格式的代码为<txv-video>标签 需要小程序增加官方腾讯视频插件
```html
<video class="txv-video" data-vid="n0326tsvoqf" data-playerid="n0326tsvoqf"></video>
```