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
  * Promise
  * Default Parameter
  * Spread Parameter
* ES5→ES6にリファクタリングしてみよう


### 持ち物

ノートPC

* Mac推奨
* WinでもいいがVM構築必須
* Git環境


## workshop前の基本知識



## workshop本題

### 準備

ワークショップ用リポジトリをクローンする

```bash
$ cd ${適当なディレクトリ}
$ git clone https://github.com/do7be/js_workshop_4.git
$ cd js_workshop_4
```

### フロントエンドでもES6を使ってみる(browserify, babelify)

gulpfile.jsにて同じようにトランスパイルしても、importなどはrequireに変換されるため、フロントエンド(ブラウザ側)からは利用することが出来ない。

なのでトランスパイルしたJSファイルをさらにブラウザで使えるように変換する必要がある。

browserifyというパッケージを用いる。

```bash
$ npm install --save-dev babelify browserify vinyl-source-stream
```

gulpfile.jsの要所要所に下記を追記する。

```javascript
// パッケージ宣言
var browserify = require('browserify'),
    babelify   = require('babelify'),
    source     = require('vinyl-source-stream');

// フロントエンド用トランスパイルのタスク宣言
gulp.task('browserify', function() {
  browserify('front/es6/*.js', { debug: true })
    .transform(babelify)
    .bundle()
    .on("error", function (err) { console.log("Error : " + err.message); })
    .pipe(source('bundle.js')) // この場合だと結合されてbundle.jsが出力される
    .pipe(gulp.dest('front/'))
});

// ファイル監視に追加
gulp.watch('front/es6/*.js', ['browserify'])
```

設定を変えたので、一度gulpを終了する。

es6/export.jsをimportして、front/index.htmlの`#message`にしまむーの名前を表示させてみよう。

```bash
$ gulp watch

別のターミナルを開く

$ vi front/es6/index.js
```

ヒント

* `#message`の中身にテキストを入れる場合は`document.getElementById('message').textContent=hoge`
* `front/es6/index.js`から作成された`front/es6/bundle.js`がHTMLから読み込まれる
