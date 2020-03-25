# Polymer Starter Kit の gulpfile.js を読み解く

```
この記事は 2015-06-16 JST 付で Qiita に投稿したものです。
```

## Polymer Starter Kit is 何

`Polymer Starter Kit` は Android I/O と揶揄される Google I/O 2015 で発表された、数少ない Web 領域の発表である `Polymer 1.0` を簡単に試すことが出来る `boilerplate` であり、`scaffold` の要素もあるので `Yeoman` のような存在でもあり、フロントエンドのモダンなワークフローを知る良い教材です。

> Build tools & boilerplate for creating production-quality web apps using Polymer
https://developers.google.com/web/tools/polymer-starter-kit/

Google は 2014年に `Polymer Starter Kit` の前身とも言える `Web Starter Kit` をリリースしていますが、こちらはマルチデバイス対応のサイト、いわゆるレスポンシブウェブデザインのサイトを効率的に作るためのツールに留まっていて、`Polymer Starter Kit` はその発展形と捉えることも出来ます。

> Boilerplate & Tooling for Multi-Device Development
https://developers.google.com/web/tools/starter-kit/


## Polymer Starter Kit に含まれるもの

- [Polymer](http://polymer-project.org/)、[Paperエレメント](https://elements.polymer-project.org/browse?package=paper-elements)、[Ironエレメント](https://elements.polymer-project.org/browse?package=iron-elements)、[Neonエレメント](https://elements.polymer-project.org/browse?package=neon-elements)
- マテリアルデザインレイアウト
- [Page.js](https://visionmedia.github.io/page.js/) でルーティングする SPA
- [Web Component Tester](https://github.com/Polymer/web-component-tester) を使ったユニットテスト
- [Platinum Service Worker elements](https://elements.polymer-project.org/browse?package=platinum-elements) を使ったオフラインファーストの準備
 - Service Worker を簡単に使うことが出来る
- [Vulcanize](https://github.com/Polymer/vulcanize)を含むエンドツーエンドのビルドツール


## gulpfile.js を読み解く

前置きが長くなりましたが、本題に入ります。

前述の通り、`Polymer Starter Kit` の前身として `Web Starter Kit` があり、同じように `gulpfile.js` がタスクランナーとして用いられています。

`Web Starter Kit` から変わっていない設定もいくつかありますが、`Polymer Starter Kit` ならではの設定もありますので、ひとつひとつ読み解いていきます。

ちなみに `Web Starter Kit` の `gulpfile.js` は zchee さんが Qiita に投稿されています。
[Google Web Starter Kitのgulpfileを翻訳してみた](http://qiita.com/zchee/items/5cc8bc318f6407e47a4c)


### use scrict

```
'use strict';
```

strict モードの宣言です。

エラーではないが、しかし怪しい、曖昧な記述がエラーになります。
潜在的や誤りの早期発見、非 strict モードよりも高速な実行というメリットがあります。


### Include Gulp & Tools We'll Use

```
// Include Gulp & Tools We'll Use
var gulp = require('gulp');
var $ = require('gulp-load-plugins')();
var del = require('del');
var runSequence = require('run-sequence');
var browserSync = require('browser-sync');
var reload = browserSync.reload;
var merge = require('merge-stream');
var path = require('path');
var fs = require('fs');
var glob = require('glob');
```

使用する package　の変数宣言です。

`gulp-load-plugins` を使うことで `gulp-*` という命名規則に沿っている package を個別に宣言する必要がなくなります。

```package.json
{
  "private": true,
  "devDependencies": {
    "browser-sync": "^2.7.7",
    "del": "^1.1.1",
    "glob": "^5.0.6",
    "gulp": "^3.8.5",
    "gulp-autoprefixer": "^2.1.0",
    "gulp-cache": "^0.2.8",
    "gulp-changed": "^1.0.0",
    "gulp-cssmin": "^0.1.7",
    "gulp-flatten": "0.0.4",
    "gulp-if": "^1.2.1",
    "gulp-imagemin": "^2.2.1",
    "gulp-jshint": "^1.6.3",
    "gulp-load-plugins": "^0.10.0",
    "gulp-minify-html": "^1.0.2",
    "gulp-rename": "^1.2.0",
    "gulp-replace": "^0.5.3",
    "gulp-size": "^1.0.0",
    "gulp-uglify": "^1.2.0",
    "gulp-uncss": "^1.0.1",
    "gulp-useref": "^1.1.2",
    "gulp-vulcanize": "^6.0.0",
    "jshint-stylish": "^2.0.0",
    "merge-stream": "^0.1.7",
    "opn": "^1.0.0",
    "require-dir": "^0.3.0",
    "run-sequence": "^1.0.2",
    "vulcanize": ">= 1.4.2",
    "web-component-tester": "^3.1.3"
  },
  "engines": {
    "node": ">=0.10.0"
  }
}
```

変数宣言しているものを含め、`Polymer Starter Kit` で使用している package は `package.json` で定義しています。

`package.json` の `devDependencies` に記載されている package は全部で28個と結構な個数を呼んでいます。

```
var AUTOPREFIXER_BROWSERS = [
  'ie >= 10',
  'ie_mob >= 10',
  'ff >= 30',
  'chrome >= 34',
  'safari >= 7',
  'opera >= 23',
  'ios >= 7',
  'android >= 4.4',
  'bb >= 10'
];
```

PostCSS である Autoprefixer 用の変数宣言です。
`Web Starter Kit` と同じ設定です。

作成するウェブサイトの要件に合わせて変更すると良いでしょう。

#### Pleeease

完全に私感且つ厳密には同じ用途ではありませんが、CSS だけで複数のツールを併用しなければならない昨今の状況を鑑みると、`Pleeease` がもう少し流行っても良いのではないかと思っています。

> Pleeease
All the annoying CSS stuff we don't want to do in 1 tool!
http://pleeease.io/

とはいえ、プリフィックスを付与する一点に絞っている `Autoprefixer` の方が、こういう場面では組み合わせやすいのだろうなーとも思います。

```
var styleTask = function (stylesPath, srcs) {
  return gulp.src(srcs.map(function(src) {
      return path.join('app', stylesPath, src);
    }))
    .pipe($.changed(stylesPath, {extension: '.css'}))
    .pipe($.autoprefixer(AUTOPREFIXER_BROWSERS))
    .pipe(gulp.dest('.tmp/' + stylesPath))
    .pipe($.if('*.css', $.cssmin()))
    .pipe(gulp.dest('dist/' + stylesPath))
    .pipe($.size({title: stylesPath}));
};
```

`styleTask` という変数宣言です。

`stylesPath` で定義されるディレクトリ以下の `CSS` ファイルを対象として、下記の処理を行ないます。

1. autoprefixer でプリフィックスを付与
2. `.tmp/' + stylesPath` にプリフィックスが付与された `CSS` ファイルを出力
3. `.css` ファイルであれば、CSS を縮小化する `cssmin` を実行
4. `dist/' + stylesPath` に縮小化された `CSS` ファイルを出力
5. ファイルサイズを表示


### Compile and Automatically Prefix Stylesheets

```
// Compile and Automatically Prefix Stylesheets
gulp.task('styles', function () {
  return styleTask('styles', ['**/*.css']);
});

gulp.task('elements', function () {
  return styleTask('elements', ['**/*.css']);
});
```

`styles` タスクと `elements` タスクを定義し、それぞれ前述の `styleTask` を `.css` ファイルに対して実行します。


### Lint JavaScript

```
// Lint JavaScript
gulp.task('jshint', function () {
  return gulp.src([
      'app/scripts/**/*.js',
      'app/elements/**/*.js',
      'app/elements/**/*.html'
    ])
    .pipe(reload({stream: true, once: true}))
    .pipe($.jshint.extract()) // Extract JS from .html files
    .pipe($.jshint())
    .pipe($.jshint.reporter('jshint-stylish'))
    .pipe($.if(!browserSync.active, $.jshint.reporter('fail')));
});
```

JavaScript の構文を解析するタスクです。

`browserSync` がアクティブな場合に限り、`app/scripts` 以下、`app/elements` 以下の `.js` ファイルと `app/elements` 以下の `.html` ファイルを対象として、`JSHint` による構文解析を行ない、`jshint-stylish` で CLI に最適化したレポートを表示させます。


### Optimize Images

```
// Optimize Images
gulp.task('images', function () {
  return gulp.src('app/images/**/*')
    .pipe($.cache($.imagemin({
      progressive: true,
      interlaced: true
    })))
    .pipe(gulp.dest('dist/images'))
    .pipe($.size({title: 'images'}));
});
```

画像を圧縮するタスクです。

`Web Starter Kit` と同じ設定です。

`app/images` 以下の画像ファイルに対して、`imagemin` で画像圧縮を行ない、`dist/images` 以下に出力し、コンソールにファイルサイズを表示します。


### Copy All Files At The Root Level (app)

```
// Copy All Files At The Root Level (app)
gulp.task('copy', function () {
  var app = gulp.src([
    'app/*',
    '!app/test',
    '!app/precache.json'
  ], {
    dot: true
  }).pipe(gulp.dest('dist'));

  var bower = gulp.src([
    'bower_components/**/*'
  ]).pipe(gulp.dest('dist/bower_components'));

  var elements = gulp.src(['app/elements/**/*.html'])
    .pipe(gulp.dest('dist/elements'));

  var swBootstrap = gulp.src(['bower_components/platinum-sw/bootstrap/*.js'])
    .pipe(gulp.dest('dist/elements/bootstrap'));

  var swToolbox = gulp.src(['bower_components/sw-toolbox/*.js'])
    .pipe(gulp.dest('dist/sw-toolbox'));

  var vulcanized = gulp.src(['app/elements/elements.html'])
    .pipe($.rename('elements.vulcanized.html'))
    .pipe(gulp.dest('dist/elements'));

  return merge(app, bower, elements, vulcanized, swBootstrap, swToolbox)
    .pipe($.size({title: 'copy'}));
});
```

コピータスクです。

下記の内容を実行します。

- `app/test` 以下と `app/precache.json` を除く、`app` 以下のすべてのファイルを `dist` にコピー
- `bower_components` 以下のファイルを `dist/bower_components` にコピー
- `app/elements` 以下の `.html` ファイルを `dist/elements` にコピー
- `bower_components/platinum-sw/bootstrap` 以下の `.js` ファイルを `dist/elements/bootstrap` にコピー
- `bower_components/sw-toolbox` 以下の `.js` ファイルを `dist/sw-toolbox` にコピー
- `app/elements/elements.html` を `elements.vulcanized.html` にリネームして `dist/elements` にコピー
- 上記のコピーを実行した後のファイルサイズをコンソールに表示します。

`Web Starter Kit` にも似たような設定がありますが、より細かい要件が設定されています。


### Copy Web Fonts To Dist

```
// Copy Web Fonts To Dist
gulp.task('fonts', function () {
  return gulp.src(['app/fonts/**'])
    .pipe(gulp.dest('dist/fonts'))
    .pipe($.size({title: 'fonts'}));
});
```

Web フォントをコピーするタスクです。

`Web Starter Kit` と同じ設定です。

`app/fonts` 以下のファイルを `dist/fonts` 以下にコピーし、コンソールにファイルサイズを表示します。


### Scan Your HTML For Assets & Optimize Them

```
// Scan Your HTML For Assets & Optimize Them
gulp.task('html', function () {
  var assets = $.useref.assets({searchPath: ['.tmp', 'app', 'dist']});

  return gulp.src(['app/**/*.html', '!app/{elements,test}/**/*.html'])
    // Replace path for vulcanized assets
    .pipe($.if('*.html', $.replace('elements/elements.html', 'elements/elements.vulcanized.html')))
    .pipe(assets)
    // Concatenate And Minify JavaScript
    .pipe($.if('*.js', $.uglify({preserveComments: 'some'})))
    // Concatenate And Minify Styles
    // In case you are still using useref build blocks
    .pipe($.if('*.css', $.cssmin()))
    .pipe(assets.restore())
    .pipe($.useref())
    // Minify Any HTML
    .pipe($.if('*.html', $.minifyHtml({
      quotes: true,
      empty: true,
      spare: true
    })))
    // Output Files
    .pipe(gulp.dest('dist'))
    .pipe($.size({title: 'html'}));
});
```

`app` 以下の `.html` 、`app/elements` と `app/test` 以下には無い `.html` を対象として、下記の最適化を実行します。

1. `.html` ファイルであれば、`elements/elements.html` を `elements/elements.vulcanized.html` に置き換える
2. `.js` ファイルであれば `uglify` で複数の `.js` ファイルを 1つのファイルに結合して圧縮
3. `.css` ファイルであれば `cssmin` でファイルサイズを縮小
4. `.html` ファイルであれば `minifyHtml` でファイルサイズを縮小
5. `dist` ディレクトリに上記のファイルを出力
6. ファイルサイズを表示


### Vulcanize imports

```
// Vulcanize imports
gulp.task('vulcanize', function () {
  var DEST_DIR = 'dist/elements';

  return gulp.src('dist/elements/elements.vulcanized.html')
    .pipe($.vulcanize({
      dest: DEST_DIR,
      strip: true,
      inlineCss: true,
      inlineScripts: true
    }))
    .pipe(gulp.dest(DEST_DIR))
    .pipe($.size({title: 'vulcanize'}));
});
```

Polymer は便利で素敵なモジュールを import すればするほど、リクエスト数が増えてしまいます。

このリクエスト数の増加を解消するツールが vulcanize です。

> Vulcanize
Reduce an HTML file and its dependent HTML Imports into one file
https://github.com/Polymer/vulcanize

Vulcanize を使うことで、複数のモジュールを結合し、リクエスト数を大幅に減らすことが出来ます。

ここではその vulcanize タスクを定義しています。

`dist/elements/elements.vulcanized.html` から import している UI モジュールを結合して、`dist/elements` にファイルを吐き出し、ファイルサイズを表示します。


### Generate a list of files that should be precached when serving from 'dist'.

```
// Generate a list of files that should be precached when serving from 'dist'.
// The list will be consumed by the <platinum-sw-cache> element.
gulp.task('precache', function (callback) {
  var dir = 'dist';

  glob('{elements,scripts,styles}/**/*.*', {cwd: dir}, function(error, files) {
    if (error) {
      callback(error);
    } else {
      files.push('index.html', './', 'bower_components/webcomponentsjs/webcomponents-lite.min.js');
      var filePath = path.join(dir, 'precache.json');
      fs.writeFile(filePath, JSON.stringify(files), callback);
    }
  });
});
```

`elements` 、`scripts` 、`styles` 以下のファイルを対象として、`Service Worker` でオフライン対応を実現するコードを生成する `precache` タスクです。


### Clean Output Directory

```
// Clean Output Directory
gulp.task('clean', del.bind(null, ['.tmp', 'dist']));
```

`.tmp` と `dist` ディレクトリを削除します。

`Web Starter Kit` と同じ設定です。


### Watch Files For Changes & Reload

```
// Watch Files For Changes & Reload
gulp.task('serve', ['styles', 'elements', 'images'], function () {
  browserSync({
    notify: false,
    snippetOptions: {
      rule: {
        match: '<span id="browser-sync-binding"></span>',
        fn: function (snippet) {
          return snippet;
        }
      }
    },
    // Run as an https by uncommenting 'https: true'
    // Note: this uses an unsigned certificate which on first access
    //       will present a certificate warning in the browser.
    // https: true,
    server: {
      baseDir: ['.tmp', 'app'],
      routes: {
        '/bower_components': 'bower_components'
      }
    }
  });

  gulp.watch(['app/**/*.html'], reload);
  gulp.watch(['app/styles/**/*.css'], ['styles', reload]);
  gulp.watch(['app/elements/**/*.css'], ['elements', reload]);
  gulp.watch(['app/{scripts,elements}/**/*.js'], ['jshint']);
  gulp.watch(['app/images/**/*'], reload);
});
```

`browserSync` で `.tmp` と `app` ディレクトリをルートとしたローカルサーバを起動し、`styles` タスク、`elements` タスク、`images` タスクを並列で実行します。

また、ファイルの変更を監視し、指定のファイルに変更があれば以下の内容を実行します。

- `app` 以下の `.html` ファイルに変更があればブラウザをリロード
- `app/styles` 以下の `.css` ファイルに変更があれば `styles` タスクを実行してブラウザをリロード
- `app/elements` 以下の `.css` ファイルに変更があれば `elements ` タスクを実行してブラウザをリロード
- `app/scripts` 、`app/elements` 以下の `.js` ファイルに変更があれば `jshint ` タスクを実行
- `app/images` 以下のファイルに変更があればブラウザをリロード

`Web Starter Kit` にも近しい設定がありますが、より細かい要件が設定されています。


### Build and serve the output from the dist build

```
// Build and serve the output from the dist build
gulp.task('serve:dist', ['default'], function () {
  browserSync({
    notify: false,
    snippetOptions: {
      rule: {
        match: '<span id="browser-sync-binding"></span>',
        fn: function (snippet) {
          return snippet;
        }
      }
    },
    // Run as an https by uncommenting 'https: true'
    // Note: this uses an unsigned certificate which on first access
    //       will present a certificate warning in the browser.
    // https: true,
    server: 'dist'
  });
});
```

本番環境用の `dist` ディレクトリをルートとして `browserSync` でローカルサーバを起動します。

`snippetOptions` が指定されている以外は `Web Starter Kit` と同じ設定です。


### Build Production Files, the Default Task

```
// Build Production Files, the Default Task
gulp.task('default', ['clean'], function (cb) {
  runSequence(
    ['copy', 'styles'],
    'elements',
    ['jshint', 'images', 'fonts', 'html'],
    'vulcanize', 'precache',
    cb);
});
```

`gulp` のデフォルトタスクです。

`gulp` はその特性上、タスクが並列実行され、実行順序を担保出来ないので、`runSequence` を使い、下記の順序でタスクを処理させます。

> runSequence
Runs a sequence of gulp tasks in the specified order. This function is designed to solve the situation where you have defined run-order, but choose not to or cannot use dependencies.
https://github.com/OverZealous/run-sequence

1. `clean` タスクを実行
2. `runSequence` で `copy`、`styles` タスクのみを実行
3. `elements` タスクを実行
4. `runSequence` で `jshint`、`images`、`fonts`、`html` タスクのみを実行
5. `vulcanize`、`precache` タスクを実行

なお、`run-sequence` は `orchestrator` が `non-dependent ordered tasks` を実装するまでの場つなぎであると明言されています。

> Please Note
This is intended to be a temporary solution until orchestrator is updated to support non-dependent ordered tasks.
https://github.com/OverZealous/run-sequence

`Polymer Starter Kit` ならではの `elements` タスク、`vulcanize` タスク、`precache` タスクが設定されていることが `Web Starter Kit` との違いです。


### Load tasks for web-component-tester

```
// Load tasks for web-component-tester
// Adds tasks for `gulp test:local` and `gulp test:remote`
try { require('web-component-tester').gulp.init(gulp); } catch (err) {}
```

`gulp test:local` と `gulp test:remote` タスクを定義し、`web-component-tester` を使ったユニットテストを実行するタスクです。

> web-component-tester
`web-component-tester` makes testing your web components a breeze!
https://github.com/Polymer/web-component-tester

`gulp test:local` を叩くと `wct:local` が呼ばれ、標準設定では `Selenium` で `chrome 41`、`chrome 43`、`firefox 38`、`safari 8.0.5` のブラウザが立ち上がり、`app/test` 以下を対象としたテストが実行されます。

下記のエラーが表示される場合は、指定の URI から `selenium-safari-driver` をダウンロード、インストールしてください。

> Test run ended in failure: Until Selenium's SafariDriver supports Safari 6.2+, 7.1+, & 8.0+, you must
manually install it. Follow the steps at:
https://code.google.com/p/selenium/issues/detail?id=7933#c23

…と記載されていますが、自分の環境では `selenium-safari-driver` のインストールがうまくいきません……。


### Load custom tasks from the `tasks` directory

```
// Load custom tasks from the `tasks` directory
try { require('require-dir')('tasks'); } catch (err) {}
```

`require-dir` を使って `tasks` ディレクトリに定義するタスクを読み込みます。

> require-dir
Node helper to require() directories. The directory's files are examined, and each one that can be require()'d is require()'d and returned as part of a hash from that file's basename to its exported contents.
https://www.npmjs.com/package/require-dir

`Web Starter Kit` と同じ設定です。

`gulpfile.js` には手を入れず、独自のタスクは別ファイルで追加します。

## 以下、雑感

- `Web Starter Kit` も良く出来ているが、`Polymer Starter Kit` はより「2015年6月現在のトレンド」に即した作りになっているように感じる。
- とはいえ `Web Starter Kit` 方向性が異なるので、`Sass` や `Compass` 、`CoffeeScript` や `JSX` をコンパイルするタスクは用意されていない。
- `Google Page Speed Insights` タスクも用意されていないし、`Web Starter Kit` と `Polymer Starter Kit` をいい感じに組み合わせるのが良いかもしれない。
- なんにせよここまで簡単に `Polymer` でレスポンシブデザイン且つマテリアルデザインなオフライン対応された SPA をサクッと作れるのは感動。


## resource

- [WebComponents.org](http://webcomponents.org/)
- [Google Web Starter Kitのgulpfileを翻訳してみた](http://qiita.com/zchee/items/5cc8bc318f6407e47a4c)
- [Strict モード](https://developer.mozilla.org/ja/docs/Web/JavaScript/Strict_mode)
- [vulcanizeでPolymer Web Componentsの初期ロードを速くする](http://www.tejitak.com/blog/?p=848)
- [Gulp でタスクを並列/直列処理する](http://qiita.com/naoiwata/items/4c82140a5fb5d7bdb3f8)
- [Polymer エレメントの開発ツールと CI](http://motemen.hatenablog.com/entry/2015/06/polymer-tools-and-ci)
- [SeleniumでSafariを動かす](http://qiita.com/oh_rusty_nail/items/488d6a0fa1684c809819)
- [gulpfile.coffeeをtask毎に分割する](http://qiita.com/KazIgu/items/25fc8406f2534a8dcb2a)
