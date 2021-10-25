## 更新历史

<a href="https://github.com/qq20004604/wti-form">源码地址</a>

<p>
  <a href="https://www.npmjs.org/package/wti-form">
    <img src="https://img.shields.io/npm/v/wti-form.svg">
  </a>
  <a href="https://npmcharts.com/compare/wti-form?minimal=true">
    <img src="http://img.shields.io/npm/dm/wti-form.svg">
  </a>
</p>


![Github stars](https://img.shields.io/github/stars/qq20004604/wti-form.svg?label=Stars&color=success)


### 更新历史

> 1.0.12

1. 让全部收起/展开按钮，可以根据实际展开收起逻辑，显示【全部收起】【全部展开】了；
2. 区块在收起的时候，不会再有 15px 的空白留出来；

> 1.0.11

1. 修复数字类型输入框，输入中文后，焦点上移的问题；

> 1.0.10

1. 再次优化输入搜索下拉框，可以在配置里添加自定义回填函数，格式是 ``querySearchAsync(queryString, cb, formItem)``

> 1.0.9

1. 修复子表单被默认隐藏\显示的时候，会出现的子表单数量错误；
2. 输入搜索下拉框，支持使用自定义函数回填了；

> 1.0.8

1. 添加表单元素默认隐藏功能；
2. 添加子表单元素的默认隐藏、禁用功能；

> 1.0.7

1. 修复了使用 axios 请求时，当拿取返回值时，可能因为数据格式导致的出错；

> 1.0.6

1. 添加表格，文档待补充；

> 1.0.5

1. 优化 props 的引入，可以支持全局配置字典请求和每个组件单独配置字典请求了；
2. 让组件内的 axios，使用公共 axios 配置（即无需另配 baseURL），注意，配置语法同 props；
3. 修复了子表单在纯文本模式下，依然有删除按钮的 bug；

> 1.0.4

修复了 axios 在请求字典值时，会因为数据格式报错的问题

> 1.0.3

1. 优化了多级联动的纯文本模式下内容显示错误的问题；
2. 修复了子表单全局禁用失效的问题

> 1.0.2

解决了之前 npm 包配置错误的问题，目前可以通过 npm 安装后直接使用了。

> 1.0.1

优化功能，添加 多级联动下拉框、标准数字输入框；更新文档

> 1.0.0

初版发布，已可用。
