## iGem Wiki webpack plugin
iGem wiki官方将JavaScript文件的放置位置设置在了如`https://2021.igem.org/wiki/index.php?title=Template:BNUZ-China/scripts/vue&action=raw&ctype=text/javascript`的URL中。这样，当我们使用如`webpack`、`vue-cli`等构建工具时，就很难通过设置`publicPath`选项来完成正确打包。
> `publicPath`一般只能在文件前添加字符串

本插件基于`html-webpack-plugin`，进行了少量修改，使之可以满足需求。

## Install
```
npm install igem-wiki-webpack-plugin --save-dev
```
> 本插件自带了`html-webpack-plugin`的全部功能，不需要另外安装`html-webpack-plugin`


## Usage
在`vue.config.js`下`configureWebpack.plugins`数组中添加插件：
```javascript
configureWebpack: {
  entry: {
    index: './src/index.js'
  },
  plugins: [
    new igemWikiWebpackPlugin({
      template: `public/index.html`,
      filename: `index.html`,
      templateParameters: {
        BASE_URL: './'
      },
      chunks: [name],
      tagDefinitionPreprocessor: preprocessor
    })
  ]
}
```
这里的选项（`igemWikiWebpackPlugin`的构造参数）相比于`html-webpack-plugin`多了一项`tagDefinitionPreprocessor`。该参数接受一个带一个参数的函数，传入`tagDefinition`参数，返回预处理后的`tagDefinition`。下给一例：
```javascript
var preprocessor = function (tagDefinition) {
  if (tagDefinition.tagName === 'script') {
    let result = tagDefinition;
    result.attributes.src = 'https://aaa.com/' + result.attributes.src + '&aa=bb';
    return result;
  }
  return tagDefinition
}
```
> 有关`tagDefinition`的相关文档请参阅`html-webpack-plugin`文档或使用调试器查看。

上述处理后的结果如下：  
原始的javascript文件路径为`src/index.js`，生成的`<scirpt>`标签的`src`属性为`https://aaa/js/index.js&aa=bb`。
