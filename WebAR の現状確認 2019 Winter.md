# WebAR の現状確認 2019 Winter

```
この記事は 2019-12-20 JST 付で Qiita に投稿したものです。
```

この記事は [ZOZOテクノロジーズ #2 Advent Calendar 2019](https://qiita.com/advent-calendar/2019/zozo_tech2) 20日目の記事です。
昨日は @mitanih さんによる「[【WEB版 WEAR】position: sticky で似ているアイテム検索の使いやすい UI/UX を実現する](https://qiita.com/mitanih/items/d4f2c2d5c464fb7c3bfd)」でした。

## はじめに

2019/11/01 より ZOZO Technologies, Inc. に参画した AR, VR, MR な xR あるいは XR 領域やっていきの [ikkou](https://twitter.com/ikkou) です。お仕事では XR × Fashion Tech を軸としてアレコレやっていく感じになりますが、個人の活動としては注目すべきウェブ技術のひとつである `WebXR` を特に推しています。

本記事では 2019/12/12 に XR-Hub Cafe で催された [WebAR TOKYO #01](https://webar-tokyo.connpass.com/event/156371/) のオープニング・セッションで喋った「[The past and future of WebAR - The WebXR Device API was shipped in Chrome 79](https://speakerdeck.com/ikkou/the-past-and-future-of-webar-the-webxr-device-api-was-shipped-in-chrome-79)」のアフターフォローとして 2019/12/20 現在の、特にモバイルにおける `WebAR` の動向を具体的な開発手法も交えながら説明します。

難易度は控えめ。初学者向けです。

### はじめて WebAR という言葉を聞いた方へ

今まで `WebAR` に触れたことがない場合、取り急ぎ iPhone ユーザーであれば Safari で、Android ユーザーであれば [ARCore 対応端末](https://developers.google.com/ar/discover/supported-devices)の Chrome で、Google 検索で「猫」と検索してみてください。

![IMG_7718.PNG](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/9f6ee2b4-2c09-66ab-c630-cd2218891978.png)
https://www.google.com/search?q=%E7%8C%AB

検索結果を少しスクロールすると出てくる「3D 表示」というボタンをタップして、表示される指示に従うと、可愛い猫が現実世界に現れます。このように『ブラウザをきっかけとした AR 表現』を本記事では `WebAR` と定義しています。

`Web AR` だったり `webAR` だったり、小文字・大文字の違い、半角スペースの有無の違いはありますが、個人的な見解として『ブラウザをきっかけとした AR 表現』は総じて `WebAR` であると考えています。少なくとも技術に対する名称であって商品名ではない認識です。

後述しますが、『ブラウザをきっかけとした VR 表現』を `WebVR` と言い、それらをひっくるめたものを `WebXR` と言います。

## もっと知られて欲しい WebXR Device API の話

`WebAR` に触れる前に、まずは `WebXR Device API` について説明します。

### The WebXR Device API was shipped in Chrome 79

US 時間で 2019/12/10 より `Chrome 79.0.3945.79` が正式公開されました 🎉
2019/12/20 現在で Chrome のバージョンが 79 になっていない人は今すぐアップデートして Chrome を再起動してください。
<img width="690" alt="スクリーンショット 2019-12-16 17.18.08.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/8da3ec1a-0055-0922-74ca-5fe2af5ed08d.png">

Chrome 79 に含まれるアップデートは次の YouTube 動画で確認可能です。
[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/L0OB0_bO5I0/0.jpg)](https://www.youtube.com/watch?v=L0OB0_bO5I0&feature=youtu.be&t=59)
source: https://www.youtube.com/watch?v=L0OB0_bO5I0&feature=youtu.be&t=59

様々なアップデートが入っていますが、Chrome 79 から `WebXR Device API` に正式対応しました。Chrome 78 以前であれば `chrome://flags` から明示的に ON にしなければならなかった `WebXR Device API` が標準で使える状態になった、記念すべきアップデートです。

### WebXR Device API とは何か

`WebXR Device API` を一言で説明すると「ブラウザ経由で (VR|AR|MR) HMD を扱いたいときに使う API」です。(VR|AR|MR) HMD の向きや加速度、コントローラーの位置を取得できます。

もともとは Oculus Rift や HTC VIVE などの VR HMD のみを対象としていた `WebVR API` が存在していて、時代の流れとともに AR HMD や MR HMD が登場し、その対象範囲は VR だけに留まるものではないという思いから `WebXR Device API` が生まれました。

2017/12/12 をもって `WebXR Device API` に置き換わり、最終バージョンを v1.1 として API の更新は停止しました。

> Development of the WebVR API has halted in favor of being replaced the WebXR Device API. Several browsers will continue to support this version of the API in the meantime.

WebVR Editor’s Draft, 12 December 2017
https://immersive-web.github.io/webvr/spec/1.1/

### Spec of WebXR Device API

ご存知の通り Web の Spec つまり仕様は W3C によって策定されていて、例に漏れず `WebXR Device API` もその対象に含まれています。

状況としては W3C の正式な策定フェーズのひとつである `Working Draft` のものと、そこに至る前段階である `Editor’s Draft` それぞれの仕様が存在しています。

* WebXR Device API, W3C Working Draft, 10 October 2019<br>
https://www.w3.org/TR/webxr/
* WebXR Device API, Editor’s Draft, 11 December 2019<br>
https://immersive-web.github.io/webxr/

各ブラウザの実装状況はお馴染みの [Can I use... Support tables for HTML5, CSS3, etc](https://caniuse.com/) に掲載されている通り、Chrome は 79 から `条件付き` で実装、他ブラウザに関しては未対応という状況です。
<img width="1275" alt="スクリーンショット 2019-12-16 17.45.06.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/9c60a7d0-62f4-44b4-4c71-13f730cc5aaa.png">
source: https://caniuse.com/#search=WebXR

条件付きというのは、一例として、前述の通り `WebXR Device API` 自体は Chrome に対して正式公開されていますが、その拡張モジュールにあたる `WebXR Augmented Reality Module` はまだ特定の状況下でフラグを立てないければ利用できないためです。

WebXR Augmented Reality Module - Level 1, W3C First Public Working Draft, 10 October 2019
https://www.w3.org/TR/webxr-ar-module-1/

### WebAR に WebXR Device API は必要か?

個人的な見解としては「必ずしも必要ではない」が回答になります。

本記事では、前述の通り `WebAR` の定義を「ブラウザをきっかけとした AR 表現」としていますが、これを実現するために必ずしも `WebXR Device API` の利用有無は問いません。

さらに言えば、この後に出てくる事例の多くは `WebXR Device API` を必要としないものばかりです。とはいえ W3C が策定する Spec のひとつに `WebXR Device API` があることは、ぜひ覚えておいてください。

## もっと知られて欲しい AR の話

`WebAR` に至る過程として `WebXR Device API` に触れましたが、そもそも「ARとは何か」についても触れておきましょう。

### AR とは何か

AR は Augmented Reality、読み方は「オーグメンテッドリアリティ」の略称で、学術的な日本訳は「拡張現実感」です。現実世界に対してコンピュータによる情報を付加、つまり現実を拡張する技術を指します。現実を拡張して情報を付加することを「重畳する」と表すこともあります。

AR の世界観を説明する際に、リファレンスとしてよく用いられる事例として「[電脳コイル](https://www.tokuma.jp/coil/)」、「[劇場版 ソードアート・オンライン -オーディナルスケール-](https://sao-movie.net/)」、「[虐殺器官](http://project-itoh.com/)」などが挙げられます。最近では「[Pokémon GO](https://www.pokemongo.jp/)」のほうがしっくりくるかもしれません。

`WebAR` の話からは離れますが、もしも AR そのものについてより深く知りたい場合は「[ARの教科書](https://book.mynavi.jp/ec/products/detail/id=91748)」がおすすめです。

### 2 分類 3 パターンに大別できる AR

AR は次の 2 分類 3 パターンに大別できます。

1. 位置情報に連動する `Location based AR`
2. `Vision based AR`
    3. 二次元バーコード、いわゆる QR コード等のマーカーを認識する `マーカー型`
    4. マーカーではなく空間/物体認識による `マーカーレス型`

#### Location based AR

主として GPS あるいは方位磁石による向きを軸としたものを指します。
`WebAR` ではないですが、分かりやすい事例として、懐かしいところでは「セカイカメラ」、誰もが知っているところでは初期の「Pokémon GO」が挙げられます。

例えば `AR.js` を利用することで開発可能です。

#### マーカー型 Vision based AR

最近では観光地で見かける機会も増えた、二次元バーコードを始めとする「マーカー」にカメラをかざすと何かが出てくるものは、このマーカー型が該当します。

これも `AR.js` を利用することで開発可能です。

#### マーカーレス型 Vision based AR

Location based AR の事例として挙げた「Pokémon GO」の「AR+」はこのマーカーレス型 Vision based AR も兼ねていると言えます。

表示するだけであれば `AR.js` で開発可能です。

あるいは平面を認識してオブジェクトを重畳するだけであれば、Android は ARCore on Chrome である `<model-viewer>` で、iOS は Android 同様に `<model-viewer>` あるいは ARKit on Safari である `AR Quick Look` で開発可能です。

冒頭で事例として挙げた「猫」はまさにこちらです。

## WebAR の利用事例

`WebAR` は今や誰もが使える技術になっています。具体的な開発の話に移る前にいくつかの「サンプルではない商用の利用事例」を実際に試してみましょう。

ここで挙げる利用事例は iOS/iPadOS あるいは Android 端末からアクセスしてください。
また、取り上げる事例は本記事の筆者と何かしらの利害関係があるわけではありません。

### 0. Google の検索結果

本記事の冒頭で挙げている「猫」の事例です。

2019/10 時点では 29 種類の動物を呼び出せるそうです。
source: [「Google検索で動物のAR」登場するのは全部で29、その種類をよく見たら](https://k-tai.watch.impress.co.jp/docs/news/1210352.html)

### 1. 自転車販売サイト Pure Cycles

> Pure Cycles
https://www.purecycles.com/products/urban-commuter-bike?variant=36496640138

Apple の [Quick Lookギャラリー](https://developer.apple.com/jp/augmented-reality/quick-look/)からもリンクされている iOS/iPadOS 向けの `AR Quick Look` 公式の利用事例です。

### 2. CITIZEN AR try

> ARリストバンドを腕につけてスマートフォンをかざすとリアルなサイズ、質感の時計を試着できるサービスです。
https://ar-try.citizen.jp/

スニーカーを試着できる WANNABY の [‎Wanna Kicks](https://apps.apple.com/us/app/wanna-kicks/id1444049305) をはじめとして AR と試着は相性が良いのですが、それを `WebAR` としてプロダクションレベルで実現するのはやや難しい状況にありました。

手っ取り早く実現できる `<model-viewer>` あるいは `AR Quick Look` は平面に対してのみ重畳可能で、マーカーレス型は 8th Wall という選択肢はあれどややお高い、となるとマーカー型を選択することになるが腕時計でそれをやるには手首にマーカーを巻く必要がある…ということをプロダクションレベルでやってのけた事例です。

CITIZEN AR try では、店頭で配布している、あるいはウェブサイトでダウンロードできる [AR リストバンド](https://ar-try.citizen.jp/assets/download/CITIZEN_ARtry.pdf)を印刷して切ってテープで腕に巻くことで、Men's であれば ATTESA または PROMASTER を、Ladies's であればXC、CITIZEN L、wicca シリーズの時計を試着できる `WebAR` コンテンツです。

### 3. ティフォニウム・チョコレート

ティフォニウム・チョコレートは、MR シアター TYFFONIUM を展開するティフォン株式会社が、渋谷 PARCO B1F にある [Tyffonium Cafe](https://www.tyffonium.com/location/cafe-shibuya/) と[ティフォニウム お台場](https://www.tyffonium.com/location/odaiba.html)で販売しているお土産のチョコレートです。
[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/UPQ9bGR98qg/0.jpg)](https://www.youtube.com/watch?v=UPQ9bGR98qg&feature=youtu.be&t=59)
source: https://www.youtube.com/watch?v=UPQ9bGR98qg
> チョコレートに描かれたイラストの部分をスマホで読み取ると、かわいいキャラクターたちが飛び出し、あなたの代わりにメッセージを伝えてくれます。出てくるメッセージは全部で４種類。食べて「おいしい」、見て「楽しい」。誰かに想いを伝える時に、こんなギフトはいかがでしょう。
https://www.tyffonium.com/location/cafe-shibuya/gift.html

Tyffonium Cafe ではネイティブアプリベースの「AR（拡張現実）とスイーツを組み合わせた新体験スイーツ『魔法パフェ®』」が有名ですが、このお土産のチョコレートはネイティブアプリを必要としない `WebAR` コンテンツとなっています。

YouTube 動画を見ると `WebAR` を試す画面まではアクセスできますが、肝心のマーカーは動画には映っていないので、試したい方は実際に手にとってみてください:)

### 4. Amazon バーチャルメイク ライブモード

AR の活用事例として ‎Wanna Kicks のようなシューズの試着系と同じくらい多いのが、メイク系です。
その多くは iOS/iPadOS・Android アプリで動作しますが、Amazon バーチャルメイク ライブモードは、顔認識に関して強みを持つ [ModiFace](http://modiface.com/) 社の技術を用いた、マーカーレス型 Vision based の `WebAR` コンテンツです。

これもまた実際に試してみるのが早いので、下記の「バーチャルメイク対象商品」ページにアクセスし、最初に表示されるリップを選択して、『メイクしてみる「試す」』ボタンをタップし、`WebAR` によるメイクアップを体験してみてください。

バーチャルメイク対象商品
https://www.amazon.co.jp/b?node=6453577051
![IMG_7719.PNG](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/7f16a7a5-1c22-3ce2-e138-07f40880b473.png)
もちろん `WebAR` なので Amazon の iOS/iPadOS・Android アプリではなく、ブラウザからアクセスしてください。

同じような仕組みは、[イヴ・サンローラン公式オンライン ブティックでも取り入れられています。](https://www.yslb.jp/makeup/makeup-lips/makeup-lipstick/rouge-volupte-shine/144YSL.html)

余談ですが、Amazon バーチャルメイクの取り組みについて興味深い note 記事を見つけたので貼っておきます。
ref. AmazonがARバーチャルメイク導入、ライブモードも。美容に注力する理由を聞く
https://beautytech.jp/n/nf92a572f12b4

### 5. AWE Portal Hunt

個人的に今まで体験してきた `WebAR` コンテンツでもっとも必然性を感じられ、楽しめたのは WebAR のソリューションを提供する 8th Wall プレゼンツの「AWE Portal Hunt」です。

> The AWE Portal Hunt commemorates 10 years of AWE with 10 unique Web AR experiences. Attendees used their smartphones to unlock the relics’ secret portals, which released content from another dimension. If you missed out, here are a few highlights:
https://medium.com/8th-wall/awe-2019-portal-hunt-web-ar-experience-a66727bb7e01

これは、AWE という AR 関連において世界でもっと大規模と言われているカンファレンスの「本拠地」である AWE USA 2019 で企画の一環として投入された事例です。

一言で表現するなら「`WebAR` を使った AR スタンプラリー」ですが、よく有りがちな「スタンプラリーのスタンプ部分を AR にしてみました！」みたいなものではなく、`WebAR` の特性を生かし、且つ裏側を WebSocket で繋いで複数人以上が同時に体験することで成立する `WebAR` コンテンツとなっていました。

先の Medium 記事にも実際の模様が残されていますが、現地での動画ツイートを残してあるので興味のある方は見てください。
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">AWE USA 2019 では 8th Wall <a href="https://twitter.com/the8thwall?ref_src=twsrc%5Etfw">@the8thwall</a> が A-Frame を使った WebAR 謎解きスタンプラリー的な施策を展開しています!!<br>早速挑戦してみましたが結果や如何に……!!<a href="https://twitter.com/hashtag/AWE2019?src=hash&amp;ref_src=twsrc%5Etfw">#AWE2019</a> <a href="https://twitter.com/hashtag/AWE2019jp?src=hash&amp;ref_src=twsrc%5Etfw">#AWE2019jp</a> <a href="https://twitter.com/hashtag/mercariAWE?src=hash&amp;ref_src=twsrc%5Etfw">#mercariAWE</a> <a href="https://t.co/PIL3Lqdl3K">pic.twitter.com/PIL3Lqdl3K</a></p>&mdash; ikkou / いっこう (@ikkou) <a href="https://twitter.com/ikkou/status/1133774874713837568?ref_src=twsrc%5Etfw">May 29, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

### 6. NIGHT SYNC YOKOHAMA (期間限定 ~12/27)

本記事の公開日である 2019/12/20 の 1 週間後である 2019/12/27 までの期間限定ですが、横浜で開催中のナイトアートプログラム [NIGHT SYNC YOKOHAMA」](https://night-sync.yokohama/cn/) で `WebAR` の特別演出を体験可能です！

> 特別演出では、スマートフォンのカメラに映る実際の風景に、デジタル画像を重ね、目の前の世界を拡張する「Web AR」の技術を活用します。
スマートフォン上のAR空間と、新港中央広場のメインステージのプロジェクションや広場の樹木、広場周辺の建物が一体となったダイナミックな演出とともに、インタラクティブなAR体験をお楽しみください。
https://night-sync.yokohama/special-contents/

裏側の仕組みは後述する、個人として使うにはちょっとお高い `8th Wall` をプロダクションレベルで使用しています。これはなかなか貴重な機会だと考えられるので、都合を合あわせられる方は全力で行くと良いのではないでしょうか！

## WebAR コンテンツの開発

ここまで様々な角度から `WebAR` を構成するものに触れてきました。ここから先は具体的な開発方法をいくつか紹介します。

### iOS における WebAR の歴史

2019/12/20 現在では特別な憂いもなく iOS/iPadOS・Android で `WebAR` を開発・体験できますが、ここに至るまでにいくつかの物語がありました。特に `iOS 12.2` を巡る物語は、渦中にいた人にとっては胃がキリキリするものでした。
本記事では深く触れませんが、当時の経緯は別途 Qiita に残してあるので興味のある方は覗いてみてください。

* [iOS 12.2でWebVRとWebARが半ば終わった件について](https://qiita.com/ikkou/items/a193d13250d9e3d51473)
* [iOS 12.2で半ば終わったWebVRとWebARがiOS 13でどうなったか](https://qiita.com/ikkou/items/5197b78d69fb6198b4d8)

### WebAR コンテンツを開発するための前提知識

前述の通り、本記事では「ブラウザをきっかけとした AR 表現」を `WebAR` としています。つまり、開発には少なからずウェブを構成する最小要素である HTML, CSS, JavaScript に対する最低限の理解が必要となります!

取っ掛かりだけであればコピペで動かせるので、先ずは気負わずにトライしてみてください！

### WebAR コンテンツをデプロイするための要件

`WebAR` を実現する重要な要素のひとつとして「ブラウザからカメラを呼び出す」というのがあります。これは `WebRTC` で `MediaDevices.getUserMedia()` を呼び出す形となりますが、実現には `HTTPS 環境` が必要です。

常時 SSL がデファクトスタンダードになりつつウェブ業界ですが、開発環境で `HTTPS 環境` を必要とするのはやや手間かもしれません。その場合は無償で `HTTPS 環境` を用意できる [Let's Encrypt](https://letsencrypt.org/ja/) や、使い方によっては [GitHub Pages](https://pages.github.com/) や [Glitch](https://glitch.com/) を用いるのも有りかもしれません。

また、`WebAR` として現実世界に重畳する 3D モデル ( `.usdz` ファイルまたは `.glb` ファイル ) も必要になりますが、本記事では 3D モデルの具体的な作成方法には触れません。

商業利用は別として、個人利用の範疇で試すだけであれば、例えば [Google Poly](https://poly.google.com/) あたりでダウンロード可能な 3D モデルを入手するのが良いかもしれません。
<img width="534" alt="スクリーンショット 2019-12-19 16.36.33.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/af39d819-c048-6dd6-9b89-5be576e5c520.png">
source: [Google Poly](https://poly.google.com/) でダウンロード可能な 3D モデルの例

### WebAR コンテンツの開発手法

いくつか方法がありますが、本記事では 2019/12/20 現在で「現実的」且つ「代表的」な例を挙げます。

1. Google の検索結果のような、現実世界に重畳するだけであれば `<model-viewer>` あるいは `AR Quick Look` を利用する
2. 二次元バーコードを読み込んで何かをしたい場合は `AR.js` を利用する
3. 「Pokémon GO」 のような位置情報ベースの何かをしたい場合も `AR.js` を利用する
4. 二次元バーコード以外のマーカー型で何かをしたい場合は `8th Wall` を利用する

### 1. `<model-viewer>` あるいは `AR Quick Look` を利用する

Google の検索結果のような、現実世界に重畳するだけであれば `<model-viewer>` あるいは `AR Quick Look` を利用するのが手っ取り早いです。
3D モデルが必要という課題はありますが、E コマースとの相性も抜群です。

#### 1.1 `<model-viewer>` の利用

`<model-viewer>` は Google 謹製の `WebAR` を簡単に始められる `Web Components` のひとつです。

> `<model-viewer>` is a web component that makes rendering interactive 3D models - optionally in AR - easy to do, on as many browsers and devices as possible. <model-viewer> strives to give you great defaults for rendering quality and performance.
https://github.com/GoogleWebComponents/model-viewer

「百聞は一体験にしかず」なので、先ずは `<model-viewer>` のサンプルを見てください。
https://model-viewer.glitch.me/

#### 1.2 `<model-viewer>` の適用範囲

`WebAR` における `<model-viewer>` の適用範囲は `iOS/iPadOS` 12 以降と `ARCore` 適用の Android、そして `Magic Leap 1` のブラウザである `Helio` です。
※ 1.1 のサンプルは `Helio` 向けの記述がないので、`Magic Leap 1` では動作しません。

#### 1.3 `<model-viewer>` の最小構成

`ARCore` 適用の Android と `Helio` 向けには `GLB` ファイルを、`iOS/iPadOS` 12 以降向けには `USDZ` ファイルを表示させる最小構成です。

```model-viewer.html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="utf-8">
    <title>Deomo: model-viewer</title>
    <script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.js"></script>
  </head>
  <body>
    <model-viewer
      src="path/to/modelName.glb"
      ios-src="path/to/modelName.usdz"
      ar magic-leap>
    </model-viewer>
  </body>
</html>
```

`<model-viewer>` で `WebAR` 向けに指定する必須項目は `src` と `ar` だけです。
オプションとして `iOS/iPadOS` 向けにも適用させたい場合は `ios-src` を、`Helio` 向けにも適用させたい場合は `magic-leap` を記述してください。

#### 1.4 `AR Quick Look` の利用

`AR Quick Look` は Apple 謹製の `WebAR` を簡単に始められる仕組みです。

> Safari、メッセージ、メール、News、メモなどの内蔵のiOS Appは、Quick Lookを使ってバーチャルオブジェクトのUSDZファイルを3DやARで表示します。Quick LookビューをAppやWebサイトに埋め込むことで、キラキラしたバーチャルオブジェクトに現実世界の周囲の景色が映り込むなど、極めて詳細にレンダリングされたオブジェクトがユーザーに表示されます。
https://developer.apple.com/jp/augmented-reality/quick-look/

前述している Pure Cycles の事例もそうですが、サンプルを含む事例は公式が参考になります。

ref. Quick Lookギャラリー - 拡張現実 - Apple Developer
https://developer.apple.com/jp/augmented-reality/quick-look/

#### 1.5 `AR Quick Look` の適用範囲

モバイル向け OS をまたいで利用可能な `<model-viewer>` に対して `AR Quick Look` は `iOS/iPad` のみにしか適用できません。

ただし `<model-viewer>` と違い、`iOS/Android` の標準昨日である `ARKit` を利用するため、外部 JavaScript を必要としないことが特長です。

#### 1.6 `AR Quick Look` の最小構成

`iOS/iPadOS` 12 以降向けに `USDZ` ファイルを表示させる最小構成です。

```ARQuickLook.html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="utf-8">
    <title>Deomo: AR Quick Look</title>
  </head>
  <body>
    <a rel="ar" href="path/to/modelName.usdz">
      <img src="path/to/modelName.png">
    </a>
  </body>
</html>
```

`<model-viewer>` と違い外部 JavaScript を読み込む必要性はありませんが、3D モデルの画像を用意し、`<a>` タグで括って `.usdz` ファイルを読み込ませる形になります。

環境によっては `MIME Type` の追加が必要になるので、もし動かない場合は `MIME Type` の追加も試してみてください。

```httpd.conf
AddType model/vnd.pixar.usd .usdz
AddType model/usd usdz
```

### 2. 二次元バーコードを読み込んで何かをしたい場合は AR.js を利用する

`<model-viewer>` と `AR Quick Look` は現実世界の平面に重畳することを目的としていましたが、例えば二次元バーコードをはじめとする何かしらのマーカーを基準として表示したい場合は、「マーカー型 Vision based AR」を実現する `AR.js` を使うのが手っ取り早いです。

#### 2.1 `AR.js` の利用

「AR 年賀状」や「AR 名刺」でお馴染みの `AR.js` は `WebAR` のための軽量 JavaScript ライブラリです。

> AR.js is a lightweight library for Augmented Reality on the Web, coming with features like Marker based and Location based AR.
https://github.com/jeromeetienne/AR.js

昨今の `WebAR` コンテンツの多くは、この `AR.js` によって成り立っています。

2017/02/17 に [v0.9.0](https://github.com/jeromeetienne/AR.js/releases/tag/v0.9.0) でリリースされたのを皮切りに、精力的にアップデートが加えられ 2019/10/09 にリリースされた [v2.0.0](https://github.com/jeromeetienne/AR.js/releases/tag/2.0.0) から、後述する `Location Based AR` が可能になりました。

これまた「百聞は一体験にしかず」なので、公式サンプルである [hiro marker image](https://github.com/jeromeetienne/AR.js) を PC などで表示し、スマートフォンから [augmented reality webapps](https://codepen.io/nicolocarpignoli/full/vMBgob) にアクセスし、`AR.js` による `WebAR` を体験してみてください。
![IMG_7726.PNG](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/09bc1794-8500-04f6-150c-ff2884daa2b8.png)
このサンプルでは `hiro marker image` の上に黄色のボックスが表示されます。
source: https://codepen.io/nicolocarpignoli/full/vMBgob

#### 2.2 `AR.js` の背景

`WebAR` コンテンツの開発には直接関係のない余談ですが、`AR.js` は内部的には `ARToolKit` の仕組みを利用しています。

`ARToolKit` そのものは C 向けに書かれ、そこから Java, Processing, C# 向けの `NyARToolkit5` が生まれ、さらにそこから AS3 向けの `FLARToolKit` が生まれ、さらにさらにそこから JavaScript 向けの `JSARToolkit` が生まれました。

`AR.js` は `ARToolKit` を Emscripten で直接 JavaScript 化した `JSARToolKit v5` を利用、さらに `WebXR` の実現を容易にする、[最近ついに v1.0.0 のリリースを迎えた](https://aframe.io/blog/aframe-v1.0.0/) JavaScript フレームワーク `A-Frame` と、ウェブにおける 3D 描画のデファクトスタンダードである JavaScript ライブラリ `three.js` によって、とても使いやすい JavaScript ライブラリとなっています。

#### 2.3 `AR.js` を使ったマーカー型 `Vision Based AR` の最小構成

```ARjs.html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="utf-8">
    <title>Deomo: Vision Based AR using AR.js</title>
    <script src="https://aframe.io/releases/1.0.0/aframe.min.js"></script>
    <script src="https://raw.githack.com/jeromeetienne/AR.js/2.0.8/aframe/build/aframe-ar.js"></script>
  </head>
  <body>
    <a-scene vr-mode-ui="enabled: false" embedded arjs>
      <a-marker preset="hiro">
          <a-box position='0 0.5 0' material='color: yellow;'></a-box>
      </a-marker>
      <a-entity camera></a-entity>
    </a-scene>
  </body>
</html>
```
source: https://codepen.io/nicolocarpignoli/pen/vMBgob

`AR.js` は `A-Frame` と組み合わせて利用します。はじめに `A-Frame` を読み込み、その後で `AR.js` を読み込みます。原則として `WebAR` に関連する HTML の記述は `A-Frame` 固有の `<a-*>` タグで構成されます。

肝となるのは、`<a-marker>` でマーカーを指定し、重畳させたいものを括ることです。

マーカーのプリセットは `ARToolKit` の生みの親である加藤博一先生の名前からとった `hiro` と、漢字の「人」を模した `kanji` の他、例えば [AR.js Marker Training](https://jeromeetienne.github.io/AR.js/three.js/examples/marker-training/examples/generator.html) で独自のマーカーを作って指定することも可能です。

```ARjs-makerPattern.html
<!-- preser="hiro" -->
<a-marker preset="hiro">

<!-- preser="kanji" -->
<a-marker preset="kanji">

<!-- preset="pattern" で .patt ファイルを指定 -->
<a-marker type="pattern" url="path/to/pattern-marker.patt">
```
独自のマーカーを指定する場合は、`preset` を `pattern` として、`url` で `.patt` ファイルまでのパスを指定します。

### 3. 「Pokémon GO」 のような位置情報ベースの何かをしたい場合も `AR.js` を利用する

`AR.js` は `v2.0.0` から `GeoAR.js` が統合され、`Location Based AR` を実現できるようになりました。

> AR.js, on its aframe implementation, comes with few custom components that make possible to integrate data from GPS sensors.
https://github.com/jeromeetienne/AR.js#what-location-based-means

`WebAR` における `Location Based AR` とは何かを示す note 記事が公式からリンクされているので、`WebAR` で「Pokémon GO」のような `Location Based AR` を実現したい方は参照してみてください。

ref. Location Based (GPS) Augmented Reality on the Web
https://medium.com/chialab-open-source/location-based-gps-augmented-reality-on-the-web-7a540c515b3c
ref. Build your Location-Based Augmented Reality Web App
https://medium.com/chialab-open-source/build-your-location-based-augmented-reality-web-app-c2442e716564

#### 3.1 `AR.js` を使った `Location Based AR` の最小構成

```ARjs-locationBased.html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="utf-8">
    <title>Deomo: Vision Based AR using AR.js</title>
    <script src="https://aframe.io/releases/1.0.0/aframe.min.js"></script>
    <script src="https://raw.githack.com/jeromeetienne/AR.js/2.0.8/aframe/build/aframe-ar.js"></script>
  </head>
  <body>
    <a-scene vr-mode-ui="enabled: false" embedded arjs>
      <a-entity gltf-model="https://modelviewer.dev/examples/assets/Astronaut.glb"
                gps-entity-place="latitude: 35.665251; longitude: 139.712092;"/>
      <a-camera gps-camera rotation-reader></a-camera>
    </a-scene>
  </body>
</html>
```

3度目の「百聞は一体験にしかず」として、「表参道」交差点に 3D モデルが表示される ( はずの ) サンプルを試してみてください。
e.g. https://clean-enigmosaurus.glitch.me

マーカー型 `Vision Based AR` との大きな違いは、`gps-entity-place` で現実世界に重畳する座標となる緯度 `latitude` と経度 `longitude` を指定することと、`a-camera` 要素に `gps-camera` 属性と `rotation-reader` 属性を追加することです。

指定する緯度と経度は、住所から割り出す [Geocoding](https://www.geocoding.jp/) などで検索できます。ちなみに、このサンプルコードの座標は「表参道」交差点付近を指しています。

この最小のサンプルでは、ハードコーディングされた座標に指定の何かを重畳するだけですが、位置情報サービス系の API と繋ぐことで、`Location Based AR` の真価を発揮できるようになります。あるいは `WebAR` で街ナカ AR 謎解きゲームを作ることすら可能です。ドキュメントを含めて、まだ多くの実例がないので、今後増えてくることを期待しています。

### 4. 二次元バーコード以外のマーカー型で何かをしたい場合は 8th Wall を利用する

気軽に実現できる `<scene-viewer>` や `AR Quick Look`、そして `WebAR` を実現するためにお世話になることの多い `AR.js` を使った方法を紹介してきましたが、2019/12/20 現在、それらを使っても実現できないのが、例えば何らかの「イラスト」や「写真」をきっかけにして発動する「二次元バーコード以外のマーカー型 AR」です。

この「二次元バーコード以外のマーカー型 AR」を `WebAR` で実現するのが `8th Wall` です。他と異なり、プロダクション利用はもちろんのこと、デプロイしての実機確認から「有償」となります。

例によって公式のサンプルを覗いてみてください。
https://apps.8thwall.com/8w/jini/

「有償」なだけあって出来ることは多く、平面検出たる `World Tracking` はもちろんのこと、本記事で取り上げている他の手段では実現できない画像認識たる `Image Targets` が使えます。先行事例として LEGO や addidas といった大企業のものもあります。また、2019/12/20 現在は見れなくなっていますが、映画「Spider-man: Into the Spider-verse」に合わせた `WebAR` 施策をこの `8th Wall` を使っていたことで、一部の界隈では話題になりました。

「Spider-man: Into the Spider-verse」施策は公式のサンプル動画が残っています。
[![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/B7qj4qPRIj8/0.jpg)](https://www.youtube.com/watch?v=B7qj4qPRIj8)
source: https://www.youtube.com/watch?v=B7qj4qPRIj8

#### 4.1 8th Wall を使ったマーカー型 Vision Based AR の最小構成

8th Wall は `Image Targets` 用の画像登録をはじめとしてウェブ上で完結するようになっています。コードが記録される Web Editor も用意されていますが、後述する通り、課金が必要となります。

#### 4.2 8th Wall を利用するために必要なお金

Plans and Pricing に掲載されています。
<img width="844" alt="スクリーンショット 2019-12-20 2.36.00.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/6b02dca8-069b-4fc2-8a0e-52aaf62724b1.png">
source: https://www.8thwall.com/pricing

大前提として、Publish してウェブ上で誰でも見れる状態にするには、1 ヶ月 $99 の Agency Plan に課金しなければなりません。且つプロダクションレベルで使うのであれば、トラフィック量に応じて料金の異なる Commercial License を追加する必要があります。

主に開発中やテスト中など、ウェブ上で誰でも見れる状態が必要ない、つまり `localhost` からのみアクセスできる状態であればお金はかかりませんが、1,000 View を超える場合は少なくとも Agency Plan に課金する必要があります。
<img width="991" alt="スクリーンショット 2019-12-20 2.42.55.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/8ea0b4d7-0886-6763-f4ab-d3276d76474a.png">
source: https://www.8thwall.com/ ログイン後の管理画面

#### 4.3 8th Wall は課金すべき?

個人で「遊ぶ」ために Adobe CC Plan を超える $99 を毎月課金するのはなかなかお高い感もありますが、`WebAR` だけでなく `WebVR` まで対応し始めたので、ちょっと本気で `WebXR` やってみようかな、という気持ちがあるのであれば、意外と元が取れるかもしれませんし、そうでないかもしれません。むしろ無課金で「遊ぶ」のは機能面でもなかなか厳しいものがあるので、とりあえず 1 ヶ月程度課金してみるのが良いのではないでしょうか。

もちろん、プロダクションレベルで使うなら迷わず課金しましょう。

ref. If You Can WebAR, You Can WebVR
https://medium.com/8th-wall/if-you-can-webar-you-can-webvr-dbd658831f8c

## まとめ

`WebAR` に触れる前段として `WebXR Device API` について、そして `AR` について説明した上で、今日現在の `WebAR` の利用事例を紹介し、具体的な開発方法の概要と触りの部分を説明しました。

本記事を読んだだけでは、インタラクションがバリバリのかっこいい `WebAR` コンテンツは作れませんし、そもそも 3D モデルの作成方法に触れていないので、自分の好きなキャラクターを `WebAR` でバーンみたいなことはできません。

Qiita にもいくつか関連記事がありますが、サンプル以上のことを本腰を入れてやるには、`A-Frame` や `three.js` の取り扱いはもちろん、当然ピュアな JavaScript とりわけ `WebGL` 周辺の知識も必要になりますし、Blender のような 3DCG を扱うソフトに対する知識も必要になってきます。

プロダクションレベルで利用するにはパフォーマンスとの戦いがあったり、色々とつらいこともたくさんありますが、それでもなにかのきっかけとなって、ちょっと `WebAR` やってみようかな、という人が増えれば幸いです！良き `WebAR` ライフを！

---

### おわりに

この記事を書き始めて公開までの短い間に、Chrome 79 の公開とともに `WebXR Device API` がデフォルト機能になり、`WebVR` な `A-Frame` は正式公開版にあたる `v1.0.0` を迎え、2D VTuber さん御用達の Live2D は突然 `WebAR` 対応を実現する `Cubism WebAR` を公開し、そして `WebAR` な `8th WAll` は `WebVR` への対応を発表しました。これはもう `WebVR` と `WebAR` をひっくるめた `WebXR` のムーブメントがきているとしか思えないので、みんなみんなやっていこうな！

---

明日は @shimajiro1111 さんです！お楽しみに！
