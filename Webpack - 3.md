# SassLoader

[https://github.com/webpack-contrib/sass-loader](https://github.com/webpack-contrib/sass-loader)

[https://github.com/postcss/postcss-loader](https://github.com/postcss/postcss-loader)

[https://github.com/postcss/autoprefixer](https://github.com/postcss/autoprefixer)

```bash
$ npm i -D sass-loader node-sass
```

<br>

`webpack.config.js`

```js
module: {
    rules: [
      {
        test: /\.(sass|scss)$/,
        use: [
          'style-loader',
          'css-loader',
          'sass-loader'
        ]
      }
    ],
  }
```

<br>

`index.js`

```js
import '../scss/index.scss';
```

<br>

## postcss-loader

```bash
$ npm i -D postcss-loader postcss-cli autoprefixer
```

`Autoprefixing`：可以幫我們加上前綴字

<br>

在根目錄新增 `postcss.config.js`

[https://cythilya.github.io/2018/08/10/postcss/](https://cythilya.github.io/2018/08/10/postcss/)

```js
module.exports = {
  plugins: {
    autoprefixer: {
      // 加入各家瀏覽器的前綴詞
      overrideBrowserslist: [
        // 指定支援的瀏覽器版本
        'Chrome >= 52',
        'FireFox >= 44',
        'Safari >= 7',
        'Explorer >= 11',
        'last 2 Edge versions',
      ],
    },
  },
};
```

或者

`postcss.config.js`

```js
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```

將設定寫在 `package.json` ( [官方推薦](https://github.com/postcss/autoprefixer#environment-variables) )

```js
"browserslist": 
{
  "production": [
    ">0.2%",
    "not dead",
    "not op_mini all"
  ],
  "development": [
    "last 1 chrome version",
    "last 1 firefox version",
    "last 1 safari version",
    "last 1 ie version"
  ]
}
```

上面兩種方法擇一使用即可

<br>

`webpack.config.js`

```js
module: {
    rules: [
      {
        test: /\.s[ac]ss$/i,
        use: [
          'style-loader',
          'css-loader',
          'postcss-loader',
          'sass-loader'
        ]
      }
    ],
  }
```

<br>

# webpack-dev-server

> 可以啟動 server 環境，透過 localhost 開啟開發中的網頁

```bash
$ npm i -D webpack-dev-server
```

<br>

`webpack.config.js`

```js
devServer: {
  compress: true,
  port: 3000,
  stats: {
    assets: true,
    cached: false,
    chunkModules: false,
    chunkOrigins: false,
    chunks: false,
    colors: true,
    hash: false,
    modules: false,
    reasons: false,
    source: false,
    version: false,
    warnings: false
  },
},
```

<br>

`webpack-dev-server stats` 中英對照

```js
stats: {
  // 加入資源訊息
  assets: true,
  
  // 加入建構日期和建構時間
  builtAt: true,
  
  // 加入暫存（但未建構）模塊的信息
  cached: true,
  
  // 顯示暫存的資源（設置為 false 則僅顯示輸出的文件）
  cachedAssets: true, 

  // 加入 children 訊息
  children: true,
  
  // 加入 chunk 訊息（設置為 false 能允許較少的冗长輸出）
  chunks: true,   
  
  // 將建構模塊信息加入到 chunk 信息
  chunkModules: true, 

  // 等同 webpack --colors
  colors: true,  
  
  // 通過對應的 bundle 顯示入口起點
  entrypoints: false,

  // 加入錯誤訊息
  errors: true,    
  
  // 加入 compilation 的 hash 
  hash: true,       
  
  // 設置要顯示的模塊的最大數量
  maxModules: 15,    
  
  // 加入建構模塊訊息
  modules: true,
  
  // 顯示警告/錯誤的依賴和來源（ webpack 2.5.0 開始 ）
  moduleTrace: true,  
  
  // 當文件大小超过 performance.maxAssetSize 顯示性能提示
  performance: true,  
  
  // 顯示模塊的輸出
  providedExports: false,

  // 加入 public path 的訊息 
  publicPath: true,    
  
  // 加入模塊被引入的原因
  reasons: true,  
  
  // 加入模塊的源码
  source: false,    
  
  // 顯示哪个模塊導出被用到
  usedExports: false, 
  
  // 加入 webpack 版本信息 
  version: true,  
  
  // 加入警告 
  warnings: true,       	 
}
```

<br>

`package.json`

```js
"scripts": {
  "dev": "NODE_ENV=development webpack-dev-server --open"
},
```

<br>

```bash
$ npm run dev
```

就會幫我們直接開啟網頁，並且直接修改程式後網頁馬上就會改變，不會產生出 `dist` 資料夾。

雖然它沒有產生出檔案但是可以呈現在網頁上，是因為它都是在電腦記憶體上處理。

當我們需要上線的檔案時，在執行 `npm run deploy` 就可以囉。

<br>

# Babel

[https://babeljs.io/](https://babeljs.io/)

```bash
$ npm i -D babel-loader @babel/core @babel/preset-env
```

`@babel/core`：程式需要調用 Babel 的 API 進行編譯。

`@babel/preset-env`：可以使用最新版本的 JavaScript 然後去編譯，不用理會哪些語法需要轉換。

<br>

`webpack.config.js`

```js
module: {
    rules: [
      ...,
      {
        test: /\.(js)$/i,
        use: 'babel-loader'
      },
    ],
  }
```

<br>

根目錄新增 `.babelrc`

```js
{
  "presets": ["@babel/preset-env"]
}
```

<br>

# import and require

新增 `tool.js`

```js
export default function(a, b) {
  return a + b;
}
```

<br>

`index.js`

```js
import '../index.html';
import '../scss/index.scss';
import Tool from './tool.js';

let arr = [1, 2, 3];

arr.map(item => "a" + item);

console.log(Tool(1, 2));
```

```bash
npm run dev
```

<br>

## Node應用由模塊組成，採用 CommonJS 模塊規範

> 每個文件就是一個模塊，有自己的作用域。在一個文件裡面定義的變數、function、類別，都是私有的。

<br>

|輸出|引入|
|:---|:---|
|module.exports = 模塊&nbsp;&nbsp;&nbsp;|var 名稱 = require("模塊檔名")|
|export default 模塊;&nbsp;&nbsp;&nbsp;|import 名稱 from "模塊檔名"|

<br>

`require`：是在 ES6 出現前的模組化開發方式

`import`：是 ES6 出現後的模組化開發方式

