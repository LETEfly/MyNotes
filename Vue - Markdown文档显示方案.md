# Markdown文档显示方案

## 起因

在个人网站上展示个人信息以及平时学习和工作时的笔记内容。所以需要一个能够将markdown内容转化到页面上展示（就类似于github或者各大博客网站）。

## 参考教程

以下教程是用于vue3的，但我用的是vue2，不过看着瞒详细的，所以就参照这个来吧。

[https://blog.csdn.net/yml9528/article/details/118058894]: Vue3展示Markdown内容

## 安装

```bash
# 使用 npm
npm i @kangc/v-md-editor@next -S

# 使用 yarn
yarn add @kangc/v-md-editor@next

```

## 引入

```js
import '@kangc/v-md-editor/lib/style/base-editor.css';
//主题
import vuepressTheme from '@kangc/v-md-editor/lib/theme/vuepress.js';
import '@kangc/v-md-editor/lib/theme/style/vuepress.css';
//表情包功能依赖样式
import '@kangc/v-md-editor/lib/plugins/emoji/emoji.css';
//代码复制功能依赖样式
import '@kangc/v-md-editor/lib/plugins/copy-code/copy-code.css';

import VMdPreview from '@kangc/v-md-editor/lib/preview';
//表情包功能
import createEmojiPlugin from '@kangc/v-md-editor/lib/plugins/emoji/index';
//代码行序号功能
import createLineNumberPlugin from '@kangc/v-md-editor/lib/plugins/line-number/index';
//代码复制功能
import createCopyCodePlugin from '@kangc/v-md-editor/lib/plugins/copy-code/index';
//代码高亮功能
import Prism from 'prismjs';

VMdPreview.use(vuepressTheme, {Prism,});
VMdPreview.use(createEmojiPlugin())
VMdPreview.use(createLineNumberPlugin());
VMdPreview.use(createCopyCodePlugin());

Vue.use(VMdPreview);
```

## 使用

```vue
<template>
  <div class="component-previewMarkdown">
    <!--大纲内容-->  
    <div v-for="anchor in titles"
         :style="{ padding: `10px 0 10px ${anchor.indent * 20}px` }"
         @click="handleAnchorClick(anchor)">
      <a style="cursor: pointer">{{ anchor.title }}</a>
    </div>
    <v-md-preview :text="markdownPreviewText" height="100%"  ref="preview" @change="handleValueChange"></v-md-preview>
  </div>
</template>

<script>
// 这是以字符串的方式引入本地文件内容
// import previewDemo from '!!raw-loader!@/note/ECMAScript.md'
import axios from "axios";

export default {
  name: "previewMarkdown",
  components: {

  },
  props: {},
  data() {
    return {
      // previewDemo,
      markdownPreviewText:'',
      titles: [],
    }
  },
  created() {
    axios.get('/static/note/Vue之Markdown文档显示.md').then(res=>{
      console.log(res);
      this.markdownPreviewText = res.data
    })
  },
  mounted() {
  },
  filters: {},
  watch: {
  },
  computed: {},
  methods: {
    handleValueChange(text,html){
      setTimeout(()=>{
        this.initList()
      },500)
    },
    /**
    * 获取大纲列表
    */
    initList(){
      if(!this.$refs.preview) return;
      console.log(this.$refs.preview);
      const anchors = this.$refs.preview.$el.querySelectorAll('h1,h2,h3,h4,h5,h6');
      const titles = Array.from(anchors).filter((title) => !!title.innerText.trim());
      console.log(titles);
      if (!titles.length) {
        this.titles = [];
        return;
      }

      const hTags = Array.from(new Set(titles.map((title) => title.tagName))).sort();

      this.titles = titles.map((el) => ({
        title: el.innerText,
        lineIndex: el.getAttribute('data-v-md-line'),
        indent: hTags.indexOf(el.tagName),
      }));
      console.log(this.titles);
    },
    /**
    * 点击大纲定位到内容所在地方
    */
    handleAnchorClick(anchor) {
      const { preview } = this.$refs;
      const { lineIndex } = anchor;

      const heading = preview.$el.querySelector(`[data-v-md-line="${lineIndex}"]`);

      if (heading) {
        preview.scrollToTarget({
          target: heading,
          //scrollContainer不一定是window，根据你当前的容器而定
          scrollContainer: window,
          top: 60,
        });
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.component-previewMarkdown {

}
</style>
```

## 代码高亮支持语言扩展

请通过[babel-plugin-prismjs (opens new window)](https://github.com/mAAdhaTTah/babel-plugin-prismjs)插件按需引入对应的语言包。

安装 `babel-plugin-prismjs` 插件

```bash
# yarn
yarn add babel-plugin-prismjs --dev

# npm
npm install babel-plugin-prismjs
```

按需引入（推荐），目前vue语法不支持

```js
// babel.config.js
module.exports = {
  plugins: [
    [
      'prismjs',
      {
        languages: ['js','json','bash']
      },
    ],
  ],
};
```

引入所有语言包（不推荐）

```js
// babel.config.js
const components = require('prismjs/components');
const allLanguages = Object.keys(components.languages).filter((item) => item !== 'meta');

module.exports = {
  plugins: [
    [
      'prismjs',
      {
        languages: allLanguages,
      },
    ],
  ],
};
```

