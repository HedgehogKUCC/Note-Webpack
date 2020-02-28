# babel/polyfill

[https://babeljs.io/docs/en/babel-polyfill](https://babeljs.io/docs/en/babel-polyfill)

`babel/polyfill`

有些語法ie不支援，所以要靠 @babel/polyfill 來解決ie的問題。

直接在 entry 進入的 index.js 加上即可

```js
import '@babel/polyfill';
```

<br>

```bash
npm i -S @babel/polyfill
```

> Because this is a polyfill ( which will run before your source code ), we need it to be a `dependency`, not a `devDependency`

<br>

## Axios

[https://github.com/axios/axios](https://github.com/axios/axios)

```bash
npm i -S axios
```

因為 axios 回傳的是 Promise 物件，而 IE 並不支援所以才會需要用到 `babel/polyfill`。

<br>

# CopyWebpackPlugin

> 專門拿來搬移不會經過 loader 的 plugin

```bash
$ npm i -D copy-webpack-plugin
```

<br>

先載入 copy-webpack-plugin 模組

```js
// webpack.config.js

const CopyWebpackPlugin = require('copy-webpack-plugin');
```

<br>

加入plugins使用

```js
// webpack.config.js

plugins: [
  new CopyWebpackPlugin([
    { from: 'assets', to: 'assets' }
  ])
]
```

<br>

在資料夾 `src` 下新增資料夾 `assets`

<br>

## 如果搬的是字型檔案的話記得要讓 webpack 去判斷他的副檔名

```js
// webpack.config.js

{
    test: /\.(woff|woff2|ttf|eot)$/,
    loader: 'file-loader',
    options: {
       name: '[path][name].[ext]?[hash:8]'
    }
}
```

```css
@font-face {
    font-family: "MyName";
    src: url("~assets/字型.ttf");
}
```

<br>

# ProvidePlugin

原本就在 webpack 裡面的一個功能，所以我們先抓取它的模組。

```js
// webpack.config.js

const webpack = require('webpack');
```

<br>

透過 ProvidePlugin 這樣的方式可以讓每支 js 裡面不用去 import 第三方套件就可以在全域取得

```js
// webpack.config.js

plugins: [
  new webpack.ProvidePlugin({
    $: 'jquery',
    jQuery: 'jquery',
    'window.jQuery': 'jquery'
  })
]
```

<br>

我們安裝 jQuery 來測試看看

```bash
$ npm i -S jquery
```

<br>

`index.js`

```js
console.log($);
console.log(jQuery);
console.log(window.jQuery);
```

<br>

```bash
$ npm run dev
```

打開開發者工具 Console 查看

<br>

## 非必要盡量不要使用 ProvidePlugin 這個做法!!!

失去了我們透過模組化 js 的好處了

1. 你會無法得知哪個 js 組件引入了那些 lib

2. 當組件出現 bug 時我們無法得知是第三方套件問題還是自己問題

<br>

# Html-Webpack-Plugin  Template

> 可以透過模版的方式讓 webpack 幫我們產生 html 檔案，但是首先我們要準備一個模版。


```bash
$ npm i -D html-webpack-plugin
```

<br>

`webpack.config.js`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

plugins: [
  new HtmlWebpackPlugin({
    title: 'Webpack前端自動化開發',
    filename: 'index.html',
    template: 'template/template.html',
    Keywords: 'Webpack前端自動化開發、前端、工程師、線上教學、教學範例',
    chunks: [ 'index' ],
  }),  
]
```

> chunks 就是對每個不同的 HTML 注入不同的 JS

對應到 entry 裡面的 KEY 值

<br>

```html
<!-- 透過這樣的方式在 html 裡面去接收 webpack 帶過來的參數 -->
<%= htmlWebpackPlugin.options.[参數] %>
```

<br>

`template.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <div id="app">
      <%= htmlWebpackPlugin.options.Keywords %>
    </div>
  </body>
</html>
```

<br>

`index.js` 刪除 `import '../index.html';` 

```js
// webpack.config.js
// 註解掉這段

{
  test: /\.html$/i,
  use: [{
    loader: 'file-loader',
    options: {
      name: '[path][name].[ext]'
    }
  }]
},
```

這樣 `npm run deploy` 才不會產生出 template 資料

`npm run dev` 也會正常跑出網頁

> 會自動注入 JS 的檔案進去所以不用自己手動引入 JS

<br>

如果要在產生 `about.html`

在 `src/js` 新增 `about.js`

然後 `webpack.config.js` 加入以下程式碼

```js
entry: {
  index: './js/index.js',
  about: './js/about.js'
},

plugins: [
    new HtmlWebpackPlugin({
      title: 'Webpack前端自動化開發',
      filename: 'index.html',
      template: 'template/template.html',
      Keywords: 'Webpack前端自動化開發、前端、工程師、線上教學、教學範例',
      chunks: [ 'index' ],
    }),
    new HtmlWebpackPlugin({
      title: '關於我',
      filename: 'about.html',
      template: 'template/template.html',
      Keywords: '關於我的簡介',
      chunks: [ 'about' ],
    }),
  ]
```
