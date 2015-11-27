# js workshop #4

## 概要

### 日時

2015/11/xx 19:30〜20:30

### 目的

ES6の基本文法の続きを学ぶ。

### 想定参加者

* Node.jsを触ったことがある
  * ない場合は[js workshop #1](https://github.com/do7be/js_workshop_1)をやってみてください
* NPM, gulpを触ったことがある
  * ない場合は[js workshop #2](https://github.com/do7be/js_workshop_2)をやってみてください
* ES6を何も知らない
  * ない場合は[js workshop #3](https://github.com/do7be/js_workshop_3)をやってみてください


### 内容

* git clone
* 開発環境準備(NPM, BABEL, gulp)
* フロントエンドでES6を使ってみる
* ES6の基本文法の続きを試してみる
  * Default Parameter
  * Rest Parameter, Spread Operator
* ES5→ES6にリファクタリングしてみよう


### 持ち物

ノートPC

* Mac推奨
* WinでもいいがVM構築必須
* Git環境


## workshop前の基本知識

* ES6の基本文法(前編)に関しては[js workshop #3](https://github.com/do7be/js_workshop_3)を参照


## workshop本題

### 準備

ワークショップ用リポジトリをクローンする

```bash
$ cd ${適当なディレクトリ}
$ git clone https://github.com/do7be/js_workshop_4.git
$ cd js_workshop_4
$ npm install -dev
$ npm install -g babel-cli
```

### フロントエンドでもES6を使ってみる(browserify, babelify)

gulpfile.jsにて同じようにトランスパイルしても、importなどはrequireに変換されるため、フロントエンド(ブラウザ側)からは利用することが出来ない。

なのでトランスパイルしたJSファイルをさらにブラウザで使えるように変換する必要がある。

browserifyというパッケージを用いる。これを用いてrequireが含まれるコードをコンパイルすると同じファイルに混ぜて擬似的なrequireを実現する。babelifyに関しては、babelでbrowserify対応するためのパッケージである。

```bash
$ npm install --save-dev babel-core babelify browserify vinyl-source-stream babel-preset-es2015
```

下記のgulpfile.jsを作成する。

```javascript
// パッケージ宣言
var gulp       = require('gulp'),
    browserify = require('browserify'),
    babelify   = require('babelify'),
    source     = require('vinyl-source-stream');

// フロントエンド用トランスパイルのタスク宣言
gulp.task('browserify', function() {
  browserify('front/es6/index.js', { debug: true })
    .transform(babelify, {presets: ['es2015']})
    .bundle()
    .on("error", function (err) { console.log("Error : " + err.message); })
    .pipe(source('bundle.js')) // この場合だと結合されてbundle.jsが出力される
    .pipe(gulp.dest('front/'))
});

// ファイル監視
gulp.task('watch', function() {
  gulp.watch('front/es6/*.js', ['browserify'])
});

// default task
gulp.task('default', ['browserify', 'watch']);
```

front/es6/export.jsをimportして、front/index.htmlの`#message`にしまむーのセリフを表示するプログラムを作成しよう。

```bash
$ gulp watch

別のターミナルを開く

$ vi front/es6/index.js
```

ヒント

* `#message`の中身にテキストを入れる場合は`document.getElementById('message').textContent=hoge`
* `front/es6/index.js`から作成された`front/bundle.js`がHTMLから読み込まれる
* importのやり方については https://github.com/do7be/js_workshop_3#module-export-import を参照。

### ES6の基本文法の続きを試してみる

いちいちgulpfileを書いてトランスパイルするのが面倒なので、今回からbabel-nodeを使いたいと思います。

また、Babel 6.0.0からbabelというパッケージが削除され、babel-cliとbabel-coreにわかれましたのでそれぞれ対応します。

```
$ npm uninstall -g babel
$ npm install -g babel-cli
$ npm intall --save-dev babel-core
```

#### Default Parameter

ES5までは引数に値が渡されなかった場合の処理を以下のように書いていた。

```javascript
var hoge = function(num) {
  var num = typeof num !== 'undefined' ?  num : 1;

  return num * num;
}

console.log(hoge(2)); // 4
console.log(hoge());  // 1
```

ES6からは以下のように書ける。

```javascript
var hoge = function(num = 1) {
  return num * num;
}

console.log(hoge(2)); // 4
console.log(hoge());  // 1
```

それでは、ES6でDefault Parameterを用いて2つの引数の和を計算するプログラムを作成してください。ただし、引数の初期値は0とします。



#### Rest Parameter, Spread Operator

ES5までは引数の個数が決まっていない場合（可変長引数）を実現するためにargumentsを使っていた。

```javascript
var hoge = function() {
  var sum = 0;
  for (var i = 0; i < arguments.length; i++) {
    sum += arguments[i];
  }
  return sum;
}

console.log(hoge(1, 2, 3)); // 6
```

ES6からは以下のように書ける。

```javascript
let hoge = function(...arg) {
  let sum = arg.reduce((sum, x) => sum + x);
  return sum;
}

console.log(hoge(1, 2, 3)); // 6

// または

let numbers = [1, 2, 3];
console.log(hoge(...numbers)); // 6
```

上記の`function(...arg)`のような記述をRest Parameter、`hoge(...numbers)`のような記述をSpread Operatorと呼びます。


それでは、ES6で可変長引数の平均を計算するプログラムを作成してください。（Rest Parameter、Spread Operatorを使ってください）


### ES5→ES6にリファクタリングしてみよう

では、今まで習得したES6(ES2015)の知識を用いて以下のES5のプログラムをES6にリファクタリングしましょう。Class化しても構いません。

```javascript
var Logger = {
  count: 5,
  sleepAndLog: function(logMessage, msec) {
    var msec = typeof msec !== 'undefined' ?  msec : 0;

    var self = this;

    for (var i = 1; i <= this.count; i++) {
      setTimeout(function() {
        console.log(self.count + '回出力します：' + logMessage)
      } , msec);
    }
  },
}

Logger.sleepAndLog('ろぎんぐなう！', 1000);

Logger.count = 3;
Logger.sleepAndLog('ろぐ');
```

ES2015に関してより深く知りたい場合は以下のワークショッパーをやるといいかもです。

https://github.com/yosuke-furukawa/tower-of-babel

おわり。
