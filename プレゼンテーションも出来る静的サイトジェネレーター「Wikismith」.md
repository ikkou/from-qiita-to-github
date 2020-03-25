# プレゼンテーションも出来る静的サイトジェネレーター「Wikismith」

```
この記事は 2014-08-13 JST 付で Qiita に投稿したものです。
```

## このまとめについて

2014年8月1日にリリースされたばかりの静的サイトジェネレーター「Wikismith」に触れていきます。

## Wikismithとは

WikismithのGitHubリポジトリの説明に依ればWikismithとは下記のようなものらしい。

> Wikismith is a fun static site generator for fans of gulp and bower

曰く、Wikismithとは`gulp`と`bower`のファンの為の楽しい静的サイトジェネレーターです、と。

公式サイトでは例として、[公式サイト](http://www.wikismith.com/)自身のスクロールに追随するナビゲーションを、そして[Bespoke Presentations](http://www.wikismith.com/bespoke_presentations/)のようなものを、マークダウンから生成することが出来る、としています。

> Transform markdown to use more of a front-end's capabilities. Take for example the scroll-spy on this page, or nifty Bespoke Presentations -- both are generated from markdown using Wikismith's built-in themes.

ようはマークダウンファイルをいい感じにアウトプットするツールということなので、あっちこっちに散らばるドキュメントをまとめる等々に向いているのではないかと思う次第。

### GitHub

https://github.com/wikismith/wikismith

### 公式サイト

http://www.wikismith.com/

## Wikismithのはじめかた

百聞は一見に如かず、なにはともあれ実際に動かしてみるに限る。

まず、作業用ディレクトリを作成して移動しておく。

```
mkdir my-first-wikismith
cd my-first-wikismith
```

次に、npmでWikismithのyeoman-generatorをインストールする。

```
npm install generator-wikismith
```

そして、yoコマンドでWikismithをインストールする。

```
yo wikismith
```

最後にgulpコマンドでローカルサーバーを立ち上げて`http://localhost:9292`を叩いてみる。

```
gulp
```

正常に起動していれば下記の画面と相対することになる。

![BS3 Theme (1).png](https://qiita-image-store.s3.amazonaws.com/0/9946/60fa2c42-f951-ea99-f6c6-bcc7914b6b4b.png)

## Wikismithで出来ること

### Pages

### Themes

### Wiki

### Blogging

### Presentations

詳細は後日追記します^w^
