# Vendor.js 與 Entry.js

小專案沒差，但是專案過大的時候會導致 Entry.js 會超肥。

在來是每次打包都需要對 node_modules 去做打包，效率差。

<br>

可以透過 webpack 內建的 `optimization` 來達到拆分

[https://webpack.docschina.org/configuration/optimization/](https://webpack.docschina.org/configuration/optimization/)

```js
// webpack.config.js

optimization: {
  splitChunks: {
    cacheGroups: {
      vendor: {
        test: /node_modules/,
        name: 'vendor',
        chunks: 'initial',
        enforce: true
      }
    }
  }
}
```

<br>

將 `index.js` 調整一下

```js
import '../scss/index.scss';
import '@babel/polyfill';
import $ from 'jquery';

let arr = [1, 2, 3];

arr.map(item => "a" + item);
```

<br>

還要在調整 `webpack.config.js`

註解 `ProvidePlugin`

`chunks` 加上 `vendor`

```js
plugins: [
  new CopyWebpackPlugin([
    { from: 'assets', to: 'assets' }
  ]),
  // new webpack.ProvidePlugin({
  //   $: 'jquery',
  //   jQuery: 'jquery',
  //   'window.jQuery': 'jquery'
  // }),
  new HtmlWebpackPlugin({
    title: 'Webpack前端自動化開發',
    filename: 'index.html',
    template: 'template/template.html',
    Keywords: 'Webpack前端自動化開發、前端、工程師、線上教學、教學範例',
    chunks: [ 'vendor', 'index' ],
  }),
  new HtmlWebpackPlugin({
    title: '關於我',
    filename: 'about.html',
    template: 'template/template.html',
    Keywords: '關於我的簡介',
    chunks: [ 'vendor', 'about' ],
  }),
]
```

<br>

## 總結

所以用這個方法把 `node_modules` 打包成另一個 `vendor.js`

用戶再次造訪網頁時就不用再載入 `vendor.js`

只有第一次造訪時才會需要多點時間

才會現在主流習慣把 plugins 拆一支 JS

自己寫的拆一支 JS

<br>

# image-webpack-loader

[https://www.npmjs.com/package/image-webpack-loader](https://www.npmjs.com/package/image-webpack-loader)

```bash
$ npm i -D image-webpack-loader
```

<br>

`webpack.config.js`

```js
rules: [{
  test: /\.(gif|png|jpe?g|svg)$/i,
  use: [
    'file-loader',
    {
      loader: 'image-webpack-loader',
      options: {
        mozjpeg: {
          progressive: true,
          quality: 65
        },
        // optipng.enabled: false will disable optipng
        optipng: {
          enabled: false,
        },
        pngquant: {
          quality: [0.65, 0.90],
          speed: 4
        },
        gifsicle: {
          interlaced: false,
        }
      }
    },
  ],
}]
```

<br>

`index.js`

```js
import '../images/image2.jpg';
```

或者

`index.scss`

```scss
#app {
  width: 500px;
  height: 500px;
  background: url('../images/image2.jpg') center;
  background-size: cover;
}
```

在執行 `npm scripts` 後，路徑都會幫我們改變，非常方便唷～

<br>

而跳出的 WARNING 是告訴我們檔案太大了

    WARNING in asset size limit: The following asset(s) exceed the recommended size limit (244 KiB).
    This can impact web performance.
    Assets: 
      e4a81a532e6562d576b0c7a03dbef41b.jpg (1.13 MiB)

    WARNING in webpack performance recommendations: 
    You can limit the size of your bundles by using import() or require.ensure to lazy load some parts of your application.
    For more info visit https://webpack.js.org/guides/code-splitting/

可以參考 [stack overflow](https://stackoverflow.com/questions/49348365/webpack-4-size-exceeds-the-recommended-limit-244-kib) 在 `webpack.config.js` 配置 `performance`

```js
performance: {
  hints: 'warning',
  maxAssetSize: 1536000
}
```

`hints`

有三個值 `false` | `"warning"` | `"error"`

<br>

`1536000`：就是設為 `1.5M` 檔案大小

超過的話就會跳出 WARNING 來提示

<br>

如果也要產生出來資料夾 dist 裡面有資料夾 images 可以去調整 `webpack.config.js` 中的 `file-loader`

```js
use: [
  {
    loader: 'file-loader',
    options: {
      name: '[path][name].[ext]'
    }
  }
]
```

<br>

# ?[hash:8] or ?[hash:16]

```js
// webpack.config.js

output: {
  path: path.resolve(__dirname, 'dist'),
  filename: 'js/[name].js?[hash:8]'
},
```

因為瀏覽器在記快取時是記網址，如果後面加上 `?1234567` 亂碼的話會視為不同網址，就不會有快取的問題！

<br>

另外一種

```js
// webpack.config.js

output: {
  path: path.resolve(__dirname, 'dist'),
  filename: 'js/[name].[hash:8].js'
},
```

這樣子上 Git 時，可以有嚴謹的版本控管！

<br>

```bash
$ npm run deploy
```

打包出來的檔案就會有參雜亂數

這樣子可以上 Git 後用亂數來比對有沒有改過程式碼

<br>

# 讓手機可以連到開發中網站測試 RWD

筆電和手機都要使用同一個 WIFI

`package.json`

```js
"scripts": {
  "dev": "NODE_ENV=development webpack-dev-server --open --host 填寫 IP 位置"
}
```

這樣子就可以用 `IP:port` 手機也可以開啟網頁了！！！

<br>

# webpack-dev-server proxy

[https://webpack.docschina.org/configuration/dev-server/#devserver-proxy](https://webpack.docschina.org/configuration/dev-server/#devserver-proxy)

<br>

先來到 [京站威秀](https://www.vscinemas.com.tw/vsweb/theater/detail.aspx?id=2) 看它頁面的 API

開啟開發者工具 > `Network` > `XHR` > 重新整理

就可以看到 API

<br>

`index.js`

```js
import '../scss/index.scss';
import '@babel/polyfill';
import axios from 'axios';

axios.get('https://www.vscinemas.com.tw/VsWeb/api/GetLstDicCinema').then(res => {
  console.log(res);
})
```

<br>

```bash
$ npm run dev
```

會發現 Console 跑出錯誤

    Access to XMLHttpRequest at 'https://www.vscinemas.com.tw/VsWeb/api/GetLstDicCinema' from origin 'http://192.168.0.101:3000' has been blocked by CORS policy: The 'Access-Control-Allow-Origin' header contains multiple values 'http://www.vscinemas.com.tw/vsweb/, https://www.vscinemas.com.tw/vsweb/', but only one is allowed.

<br>

我們要在 `webpack.config.js` 設定 `proxy`

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
 proxy: {
    '/api': {
      target: 'https://www.vscinemas.com.tw/VsWeb',
      changeOrigin: true
    }
  }
},
```

<br>

`index.js` 要修改一下 API 路徑

```js
axios.get('api/GetLstDicCinema').then(res => {
  console.log(res);
})
```

<br>

這樣子就可以撈回 API 資料囉～
