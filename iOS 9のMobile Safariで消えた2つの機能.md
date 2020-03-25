# iOS 9のMobile Safariで消えた2つの機能

```
この記事は 2015-09-25 JST 付で Qiita に投稿したものです。
```

日本時間の9月17日深夜2時、ついにiOS9がリリースされました!!
[こんな投稿]もあるように、地味ながら色々と便利なアップデートが加えられたiOS9のMobile Safariですが、実はひっそりと消えてしまった機能もあります。

2016/03/22 追記
本日、03/22にiOS 9.3正式版が公開されました。
早速アップデートして試したところ、iOS 9.2同様に`apple-mobile-web-app-status-bar-style`は動作するが`apple-touch-startup-image`は動作しないという挙動に変更はありませんでした。

2015/12/09 追記
本日、12/09にiOS 9.2正式版が公開されました。
早速アップデートして試したところ、前評判通り`apple-mobile-web-app-status-bar-style`は再び動作するようになりましたが、残念ながら`apple-touch-startup-image`は引き続き動作しませんでした。

2015/10/30 追記
先日、10/28にiOS 9.2 betaが開発者向けに公開されました。
まだbeta版ですが、`apple-mobile-web-app-status-bar-style`が使えなくなった問題が修正される見込みです。仕様変更ではなくバグだったのでしょうか……。
この問題にぶつかった人は、同様にiOS 9で消えた`apple-touch-startup-image`も一緒に修正されていることを期待しながら、全ユーザー向けの公開を待ちましょう。

---

## TL;DR

- iOS 9で`apple-mobile-web-app-status-bar-style`が死んだ!
- iOS 9で`apple-touch-startup-image`が死んだ!
- iOS 9.0.1時点では対処方法がなさそう

## apple-mobile-web-app-status-bar-style is dead :skull:

### apple-mobile-web-app-status-bar-styleとは

`apple-mobile-web-app-status-bar-style`は`meta`要素の指定のひとつで、Webサイトをフルスクリーンモードにしてネイティブアプリのように見せる`apple-mobile-web-app-capable`と併用することで、Mobile Safariのステータスバーをアレするやつです。

> This meta tag has no effect unless you first specify full-screen mode as described in apple-apple-mobile-web-app-capable.
[Safari HTML Reference](https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html#//apple_ref/doc/uid/TP40008193-SW4)より

### 使い方

前述の通り、`apple-mobile-web-app-capable`の指定が前提となります。

```html:ネイティブアプリのように見せる指定
<meta name="apple-mobile-web-app-capable" content="yes">
```

その上で、`apple-mobile-web-app-status-bar-style`の指定を行ないます。

この時、この`meta`要素を記述しない、あるいは`content`に`default`を指定すると、ステータスバーの見た目は普段通りと変わりません。

`black`を指定するとアドレスバーが消え、不透過の黒背景になります。

```html:ステータスバーが不透過の黒背景になる
<meta name="apple-mobile-web-app-status-bar-style" content="black">
```

`black-translucent`を指定すると、不透過ではなく透過の黒背景となり、ステータスバーが別レイヤーとなり、表示領域がステータスバーの下に潜り込むようになります。

```html:ステータスバーが透過の黒背景になる
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

### iOS 9でどう変わったか

ネイティブアプリのように振る舞う`apple-mobile-web-app-capable`は今まで通り動作しますが、`apple-mobile-web-app-status-bar-style`の指定が効かなくなりました。

`black`を指定しても、`black-translucent`を指定しても、`default`を指定した状態、つまり何も変化しなくなりました。

思い返せば、`apple-mobile-web-app-status-bar-style`はOSのアップデートによる仕様変更の多い指定でした。

Safari HTML Referenceの[Supported Meta Tags](https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html)には[まだ掲載されている](https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html#//apple_ref/doc/uid/TP40008193-SW4)ので、一時的なバグなのかもしれませんが、初期ベータの頃から始まり、先日アップデートされたiOS 9.0.1でも変わらず動作しないので、バグではなく、仕様変更なのかもしれません。

## apple-touch-startup-image is dead :skull:

### apple-touch-startup-imageとは

`apple-touch-startup-image`も`apple-mobile-web-app-status-bar-style`同様に、`link`要素の指定のひとつで、適切な画像を指定することで、ネイティブアプリのようなスプラッシュ画面を表示することが出来ます。

> On iOS, similar to native applications, you can specify a startup image that is displayed while your web application launches. This is especially useful when your web application is offline.
[Safari HTML Reference](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html#//apple_ref/doc/uid/TP40002051-CH3-SW6)より


### 使い方

この指定もやはり`apple-mobile-web-app-capable`と併用して使います。

```html:お約束のネイティブアプリのように見せる指定
<meta name="apple-mobile-web-app-capable" content="yes">
```

その上で、メディアクエリを活用して、デバイス別、縦横別にスプラッシュ画面で表示する画像を指定します。

例えば、[Google DevelopersのWeb Fundamentals](https://developers.google.com/web/fundamentals/)では、[tfausak’s gist](https://gist.github.com/tfausak/2222823)で示された指定が例として挙げられています。

> Apple のドキュメントではこのトピックはほとんど取り上げられませんが、開発者コミュニティは 、高度なメディア クエリを使用して、すべての端末を対象にする方法を考え出しました。 これによって適切なデバイスを選択し、正しいイメージを指定することができます。 [tfausak’s gist] の好意により 、ここに具体的な解決策を示します(//gist.github.com/tfausak/2222823):

```html:example
<!-- iPad retina portrait startup image -->
<link href="https://placehold.it/1536x2008"
      media="(device-width: 768px) and (device-height: 1024px)
             and (-webkit-device-pixel-ratio: 2)
             and (orientation: portrait)"
      rel="apple-touch-startup-image">

<!-- iPad retina landscape startup image -->
<link href="https://placehold.it/1496x2048"
      media="(device-width: 768px) and (device-height: 1024px)
             and (-webkit-device-pixel-ratio: 2)
             and (orientation: landscape)"
      rel="apple-touch-startup-image">

<!-- iPad non-retina portrait startup image -->
<link href="https://placehold.it/768x1004"
      media="(device-width: 768px) and (device-height: 1024px)
             and (-webkit-device-pixel-ratio: 1)
             and (orientation: portrait)"
      rel="apple-touch-startup-image">

<!-- iPad non-retina landscape startup image -->
<link href="https://placehold.it/748x1024"
      media="(device-width: 768px) and (device-height: 1024px)
             and (-webkit-device-pixel-ratio: 1)
             and (orientation: landscape)"
      rel="apple-touch-startup-image">

<!-- iPhone 6 Plus portrait startup image -->
<link href="https://placehold.it/1242x2148"
      media="(device-width: 414px) and (device-height: 736px)
             and (-webkit-device-pixel-ratio: 3)
             and (orientation: portrait)"
      rel="apple-touch-startup-image">

<!-- iPhone 6 Plus landscape startup image -->
<link href="https://placehold.it/1182x2208"
      media="(device-width: 414px) and (device-height: 736px)
             and (-webkit-device-pixel-ratio: 3)
             and (orientation: landscape)"
      rel="apple-touch-startup-image">

<!-- iPhone 6 startup image -->
<link href="https://placehold.it/750x1294"
      media="(device-width: 375px) and (device-height: 667px)
             and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image">

<!-- iPhone 5 startup image -->
<link href="https://placehold.it/640x1096"
      media="(device-width: 320px) and (device-height: 568px)
             and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image">

<!-- iPhone < 5 retina startup image -->
<link href="https://placehold.it/640x920"
      media="(device-width: 320px) and (device-height: 480px)
             and (-webkit-device-pixel-ratio: 2)"
      rel="apple-touch-startup-image">

<!-- iPhone < 5 non-retina startup image -->
<link href="https://placehold.it/320x460"
      media="(device-width: 320px) and (device-height: 480px)
             and (-webkit-device-pixel-ratio: 1)"
      rel="apple-touch-startup-image">
```

ちなみに、Web Fundamentalsでは、iPhone 6/6 Plus発売以前のものが例として提示されていますが、最新の`tfausak’s gist`では、iPhone 6/6 Plusの`device-pixel-ratio: 3`に対応したものにアップデートされています。

### iOS 9でどう変わったか

せっかくデバイスごと、縦横ごとに適切なスプラッシュ画面を必要な枚数だけ用意したとしても、iOS 9から動作しなくなりました。

## どうすれば良いのか?

### 対応方法

9月25日現在、最新のiOS 9.0.1では真っ当な対応方法が見つかっていません。

ちなみに、Apple Press Infoにリリースが出ている通り、iOS 9への移行率は、iOS 9のリリースから数日で50%を超えています。

> Appleはまた、新しいiOSへの移行が過去最速となり、すでに50パーセント以上のデバイスがiOS 9となっていることも併せて発表しました。
http://www.apple.com/jp/pr/library/2015/09/21iPhone-6s-iPhone-6s-Plus-Arrive-on-Friday-September-25.html

本日、9月25日にはiOS 9がデフォルトとなっているiPhone 6sとiPhone 6s Plusが発売され、iOS 9の比率はさらに上がることが予想されます。

このまま仕様変更となるのか、あるいは修正されるのか、こればかりは予想出来ませんが、特に`apple-touch-startup-image`は適切に指定すれば素敵なWebアプリに仕立てあげられるので、復活すると嬉しいなーと思っています。

## リファレンス

- [In iOS 9 does "apple-mobile-web-app-status-bar-... | Apple Developer Forums](https://forums.developer.apple.com/thread/9819)
- [iphone - iOS 9 status bar meta tag and startup image links not working - Stack Overflow](http://stackoverflow.com/questions/31471603/ios-9-status-bar-meta-tag-and-startup-image-links-not-working)

[こんな投稿]:http://qiita.com/zaru/items/7bc2910b8ca4f47721c6
