# 初探 Webpack

建立資料夾 `webpack demo`

依照[官網](https://webpack.js.org/) `provide custom webpack.config.js`

新增 `webpack.config.js`

```js
// webpack.config.js

const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    // 這行先註解
    // path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
};
```

<br>

依照 `entry: './src/index.js'` 

新增資料夾 `src` 在新增 `index.js`

```js
// index.js

console.log('Hello');
```

<br>

開啟終端機

輸入 `npm init`

輸入 `npm install webpack webpack-cli --save-dev`

<br>

`node_modules`

就是我們透過 npm 下載下來的套件跟工具都會放在這個資料夾裡面，剛剛我們下載下來的 webpack

還有跟 webpack 有關係的套件都會在這資料夾裡面。

<br>

`package.json`

關於這整包專案所有的資訊，包含我們安裝的套件版本，專案版本， npm 指令都可以在這個 

json 檔案裡面找到，之後要搬移專案重新安裝套件也需要靠這個 json 檔案。

<br>

`package-lock.json`

是 npm5 版本新增的，是專門紀錄 **package.json** 裡面更細節的內容，例如安裝套件的詳細版本，

或是確認你的 dependency 是被哪個函式庫所要求...等等。

<br>

將這片段覆蓋在 `package.json` 

```js
"scripts": {
  "start": "webpack"
}
```

<br>

終端機 執行 `npm run start`

就會看到產生出來的 `dist/bundle.js`

最後會看到我們在 `index.js` 寫的 `console.log('Hello');`

```js
// bundle.js

!function(e){var t={};function n(r){if(t[r])return t[r].exports;var o=t[r]={i:r,l:!1,exports:{}};return e[r].call(o.exports,o,o.exports,n),o.l=!0,o.exports}n.m=e,n.c=t,n.d=function(e,t,r){n.o(e,t)||Object.defineProperty(e,t,{enumerable:!0,get:r})},n.r=function(e){"undefined"!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},n.t=function(e,t){if(1&t&&(e=n(e)),8&t)return e;if(4&t&&"object"==typeof e&&e&&e.__esModule)return e;var r=Object.create(null);if(n.r(r),Object.defineProperty(r,"default",{enumerable:!0,value:e}),2&t&&"string"!=typeof e)for(var o in e)n.d(r,o,function(t){return e[t]}.bind(null,o));return r},n.n=function(e){var t=e&&e.__esModule?function(){return e.default}:function(){return e};return n.d(t,"a",t),t},n.o=function(e,t){return Object.prototype.hasOwnProperty.call(e,t)},n.p="",n(n.s=0)}([function(e,t){console.log("Hello")}]);
```

<br>

# Path 檔案路徑設定

資料夾 `src` => 待編譯

資料夾 `dist` => 已編譯

<br>

`path.resolve()`

可以將**相對路徑**或**路徑片段**解析成**絕對路徑**

<br>

`__dirname`

在 nodejs 裡面代表的一個特殊變數，指的是當前執行文件所在目錄的完整目錄位置。

<br>

使用 `console.log` 查看 `path.resolve(__dirname, 'dist')`

`webpack.config.js`

```js
const path = require('path');

console.log('=>', path.resolve(__dirname, 'dist'));

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
};
```

<br>

執行 `npm run start`

就可以看到完整絕對路徑

```bash
=> /Users/kucc/Desktop/六角學院/webpack demo/dist
```

<br>

> 為什麼要使用這方法將相對路徑轉成絕對路徑呢？

使用這樣的方法可以確保在不同系統下路徑是一致的而不會出錯

<br>

將 `webpack.config.js` 的 `entry: './src/index.js'` 修改為以下

```js
entry: path.resolve(__dirname, 'src'),
```

確保在進入點在不同系統下路徑是一致的！

<br>

# npm scripts 的使用

當每寫一次都要存檔重新執行一次會非常麻煩！！！

所以可以在 `package.json` 加上 `--watch`

這樣執行緒就不會斷掉，當我改寫程式時會自動在重新執行編譯。

```js
"scripts": {
  "start": "webpack --watch"
},
```

<br>

在前面執行 `npm run start` 後，都會跑出以下 WARNING。

這段是在說 webpack 預設是 `production` 狀態

當專案愈來愈大時就會影響到執行效率

```bash
WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/configuration/mode/
```

<br>

所以我們要去設定 `mode`

當在執行這 `npm scripts` 的時候是什麼的狀態

這樣有設定 `mode` 後 WARNING 就會不見了

```js
// package.json

"scripts": {
  "start": "webpack --mode development",
  "deploy": "webpack --mode production"
},
```

可以在去執行 `npm run start` 和 `npm run deploy`

去看看 `dist/bundle.js` 裡面的變化！！！

<br>

# 環境變數 NODE_ENV 的概念與使用

如果是 WINDOWS 環境要安裝 `cross-env`

```bash
$ npm install cross-env --save-dev
```

<br>

WINDOWS 的 `package.json` 設定

```js
"scripts": {
  "start": "cross-env NODE_ENV=development webpack",
  "deploy": "cross-env NODE_ENV=production webpack"
},
```

<br>

而 MAC 和 LINUX 就不需要安裝 `cross-env`

`package.json` 設定

```js
"scripts": {
  "start": "NODE_ENV=development webpack",
  "deploy": "NODE_ENV=production webpack"
},
```

<br>

## 讀取 NODE_ENV

使用 `process.env.NODE_ENV` 來讀取

這樣子就可以從 `package.json` 帶入 `NODE_ENV` 的值

```js
// webpack.config.js

const path = require('path');

module.exports = {
  mode: process.env.NODE_ENV,
  entry: path.resolve(__dirname, 'src'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
};
```

<br>

### 總結

MAC 不用 cross-env

WINDOWS 無法讀取 NODE_ENV 這個環境變數才需要 cross-env

<br>

# 永不停止的 WATCH

> package.json

```js
"scripts": {
  "watch": "NODE_ENV=development webpack --watch",
  "start": "NODE_ENV=development webpack",
  "deploy": "NODE_ENV=production webpack"
},
```

```bash
$ npm run watch
```
<br>

改寫完程式碼**儲存**就會自動重新編譯

要停止的話就按 `control + C`
