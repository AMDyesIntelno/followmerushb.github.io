# Misaka's Notes

>我的个人云笔记
>
>主要用于记录计算机科学相关的知识
>
>如果有写错的地方,请务必点醒我这只小菜鸡`(*>﹏<*)′

## 配置

使用了[docsify](https://docsify.js.org/#/zh-cn/)进行搭建

在`index.html`中进行如下修改

### 添加css

>仅对Firefox有效

```html
<style>
*{
  scrollbar-width: thin;
}
</style>
```

在`<head></head>`中添加`<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@latest/dist/katex.min.css"/>`

### 添加script

``` html
<script src="https://cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/docsify/lib/plugins/zoom-image.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/docsify-pagination/dist/docsify-pagination.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/docsify-copy-code/dist/docsify-copy-code.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/docsify-tabs/dist/docsify-tabs.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/docsify/lib/plugins/search.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/docsify/lib/plugins/external-script.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-bash.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-go.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-c.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-cpp.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-python.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-json.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-powershell.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-bash.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/docsify-katex@latest/dist/docsify-katex.js"></script>
<script src="https://cdn.jsdelivr.net/npm/prismjs/components/prism-nasm.min.js"></script>
```