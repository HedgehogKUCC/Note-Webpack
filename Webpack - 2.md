# Filename 檔案名稱修正

今天在寫網站時會有多隻 JS 檔案

我們在 `src` 新增 `index.js` 和 `about.js`

```js
// index.js

console.log('index');
```

```js
// about.js

console.log('about');
```

<br>

記得一個頁面 ( index.html、about.html ) 就是會對應到 

`entry` 裡面所設定的 JS ( index.js、about.js )

<br>

`webpack.config.js` 設定

```js
const path = require('path');

module.exports = {
  mode: process.env.NODE_ENV,
  entry: {
    index: './src/index.js',
    about: './src/about.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  }
};
```

記得將 `filename: 'bundle.js'` 改為 `[name].js`

這樣才會去抓到 `entry` 裡面的 KEY 值，產生出相對應的**檔案名稱**。

產生出來的資料夾 `dist` 裡面就會有 `index.js` 和 `about.js`

> 非常重要！！！ 在 entry 裡面設定的 KEY 值一定要和檔案名稱一致

<br>

加上 `context` 在 `webpack.config.js`

可以幫助 `entry` 在指定的時候是在 `src`

```js
// webpack.config.js

const path = require('path');

module.exports = {
  mode: process.env.NODE_ENV,
  context: path.resolve(__dirname, 'src'),
  entry: {
    index: './index.js',
    about: './about.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  }
};
```

<br>

## 總結

`[name]`

會依照 entry 的 KEY 值來更改 output 檔案名稱

<br>

利用 Object 的方式可以有多個 entry

一個 HTML 頁面只會對應到一個 entry 的 JS

```js
entry: {
  index: './index.js',
  about: './about.js',
  address: './address.js',
  home: './home.js'
}
```

<br>

# CSS Loader

[https://github.com/webpack-contrib/css-loader](https://github.com/webpack-contrib/css-loader)

> Loader 是為了可以去讀取解析除了 JS 以外的檔案

```bash
$ npm install css-loader style-loader --save-dev
```

<br>

在根目錄新增 `index.html`

`src` 新增 `index.js` 和 `index.css`

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
  </head>
  <body>
    <h1>NAME</h1>
    <script src="./dist/index.js"></script>
  </body>
</html>
```

```css
/* index.css */

h1 {
  color: red;
}
```

```js
// index.js

import './index.css';
```

<br>

依照官網在 `webpack.config.js` 設定

```js
// 記得將 entry 裡面的 about 設定刪除

module.exports = {
  ...,
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
};
```

<br>

## Loader 的順序都是由後面執行到前面的

所以會先使用 `css-loader` 後再執行 `style-loader`

```js
[
  'style-loader',
  'css-loader'
]
```

<br>

`style-loader`

會將 CSS 放入 JS 中去執行，就不會產生單獨的 `.css` 檔案。

<br>

# 獨立拆分 CSS 檔

[https://github.com/webpack-contrib/extract-text-webpack-plugin](https://github.com/webpack-contrib/extract-text-webpack-plugin)

在 releases 裡面的 `v4.0.0-beta.0` 可以找到適用在 webpack 4

Plugins 就是拿來解決 loader 做不到的事情

```bash
$ npm i -D extract-text-webpack-plugin@next
```

<br>

`webpack.config.js` 新增

```js
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const extractCSS = new ExtractTextPlugin('css/[name].css');

module.exports = {
  ...,
  plugins: [
    extractCSS
  ]
};
```

<br>

|獨立 CSS|沒有獨立 CSS|
|:---:|:---:|
|減少 style 標籤 ( 舊版本 IE 有限制 )|減少額外的 HTTP 請求|
|瀏覽器運行時優先讀取 CSS| JS 和 CSS 同時載入完成|
|CSS 單獨暫存|減少多餘的 CSS 檔案|
|CSS 單獨載入並行|組件化更佳乾淨的專案架構|

<br>

# FileLoader 搬移檔案

[https://github.com/webpack-contrib/file-loader](https://github.com/webpack-contrib/file-loader)

```bash
npm i -D file-loader
```

<br>

`webpack.config.js`

```js
module: {
    rules: [
      {
        test: /\.html$/i,
        use: [{
          loader: 'file-loader',
          options: {
            name: '[path][name].[ext]'
          }
        }]
      }
    ],
  },
```

`[path]`：路徑

`[name]`：檔名

`[ext]`：副檔名

<br>

`index.js`

```js
// 在 index.js 裡面 import，就可以搬檔案囉～

import './index.html';
```
