# AR.js ではじめる Image Tracking

```
この記事は 2020-03-18 JST 付で Qiita に投稿したものです。
```

@ikkou です。みんな大好き AR.js が新しくなり、今まで通りの Marker Tracking と新しい Image Tracking の2つの異なるビルドが存在するようにしました。本記事では新しい Image Tracking について概要を説明します。

## TL;DR

[サンプルページ](https://ar-js-org.github.io/AR.js/aframe/examples/image-tracking/nft/) にアクセスして、用意されている[恐竜の画像](https://raw.githubusercontent.com/AR-js-org/AR.js/master/aframe/examples/image-tracking/nft/trex-image-big.jpeg)にかざすと、恐竜が飛び出してくる、そんな QR コードなどのマーカーを使わない、画像認識型の WebAR を新しい AR.js で実現できるようになりました。

## はじめに

以下、前提知識を揃えるための説明です。
AR.js のことを知っている人や、前提知識が不要な人は本編まで読み飛ばしてください。

### AR.js とは

WebAR を実現するための汎用 JavaScript ライブラリです。
https://github.com/AR-js-org/AR.js

元々は開発者である Jerome Etienne さん ( [@jerome_etienne](https://twitter.com/jerome_etienne) ) の GitHub 個人リポジトリである [jeromeetienne/AR.js](https://github.com/jeromeetienne/AR.js) で管理されてきましたが、2020-02-28 JST より [AR-js-org](https://github.com/AR-js-org) という Organization で管理されるようになりました。
移動の詳細は旧リポジトリの [Why move AR.js](https://github.com/jeromeetienne/AR.js#why-move-arjs) を参照してください。

リポジトリの移動に伴い、AR.js 公式のドキュメント集である AR.js Documentation が用意されました。
https://ar-js-org.github.io/AR.js-Docs/

### AR.js でできること

* Marker Based WebAR
    * なにかしらのマーカーにかざすと AR なものが表示される、よく見るタイプの AR を実装できます。
    * マーカーとしてあらかじめ用意されている Hiro, Kanji の他、カスタムマーカーを使用できますが、マーカーには必ず黒い枠線が必要となります。
* Location Based WebAR
    * 位置情報をもとにして AR なものが表示される、例えば Pokémon GO の AR+ のような AR を実装できます。
* Image Tracking WebAR 🆕
    * マーカーではなく写真やイラストにかざすと AR なものが表示される、よく見るタイプの AR を実装できます。
    * これまでは 8th Wall Web のみが実用的な Image Tracking WebAR のソリューションでしたが、新たに AR.js でも実現できるようになりました。

前述の通り、現在の AR.js は Image Tracking 向けのものと、Marker Tracking 向けのものがそれぞれ個別のライブラリとして存在しています。Location Based 向けはどちらのライブラリとも合わせて使えます。

### WebAR とは

WebAR はウェブ技術で AR 表現を実現するものです。
iOS/Android のネイティブアプリとして AR アプリを開発することに比べ、アプリのインストールが不要という絶対的な優位点がありますが、ネイティブアプリでできることのすべてを WebAR として実装できるわけではありません。特にパフォーマンス面ではネイティブアプリに及ばない部分もありますが、ウェブ技術の進化とともにできることが増えている技術です。

手前味噌ですが 2019/12 時点の WebAR をまとめた記事を書いているので、興味のある方は覗いてみてください。

WebAR の現状確認 2019 Winter
https://qiita.com/ikkou/items/c32d57e30b28068b268d

### WebXR とは

ウェブ技術で AR を実現する WebAR の他、ウェブ技術で VR を実現する WebVR があります。また、AR や VR の総称として XR という言葉が用いられています。そういった背景から、ウェブ技術を策定する W3C は WebAR や WebVR に必要な API として WebXR Device API を策定しています。
ただし、WebAR、WebVR ともに必ずしも WebXR Device API を必要とするわけではありません。

### NFT ( Natural Feature Tracking ) とは

Image Tracking の話になると NFT という言葉が出てきますが、これは Natural Feature Tracking の略称で、Natural Feature つまり自然特徴点をもとにして認識する技術を指します。NFT ではない場合は Marker Based となります。

## AR.js ではじめる Image Tracking

以下、本編となります。

### 1. NFT Marker Creator で Image Descriptors を作成する

AR.js では画像に対する固有の特徴点である `Image Descriptors` を NFT Marker Creator で作成します。

NFT Marker Creator は [Web バージョン](https://carnaux.github.io/NFT-Marker-Creator/)と Node バージョン ( [Carnaux/NFT-Marker-Creator](https://github.com/Carnaux/NFT-Marker-Creator) ) が用意されていますが、本記事では『公式が推奨している Web バージョン』を使用します。

※ 2020-03-17 15:13 追記
NFT Marker Creator の README では Node バージョンが `preferred` とされていますが、AR.js Documentation では Web バージョンが `recommended` とされているので、本記事では　AR.js Documentation　を公式として『公式が推奨している Web バージョン』と表記しています。
![carnaux.github.io_NFT-Marker-Creator_.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/456aa1ec-5ae4-b4a9-755f-d3d3e5f846b0.png)
Ref. [NFT Marker Creator](https://carnaux.github.io/NFT-Marker-Creator/)

[NFT Marker Creator](https://carnaux.github.io/NFT-Marker-Creator/) にアクセスし、`Upload Image` から Image Tracking として使いたい任意の画像をアップロードします。

画像をアップロードし、適切な画像であればページ下部の Confidence の☆に色が付きます。例えば以下のような何ら特徴点のない単色の `#FF69B4` で塗りつぶした画像は☆0で不適切です。
![carnaux.github.io_NFT-Marker-Creator_ (4).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/ae3b0cbf-9354-daec-0833-c0f1b3d6507a.png)
Twitter のヘッダに設定している画像 ( 1500px × 500px ) は長辺解像度が増えていることと背景の複雑さも相まってか☆4でした。
![carnaux.github.io_NFT-Marker-Creator_ (5).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/4fd6e3ac-6c40-4ad7-bf97-574ea7f1926c.png)
所属企業のコーポレートサイトに載っているオフィスの模様を示した画像 ( 1280px × 854px ) は Twitter のヘッダ画像より長辺こそ短いものの、総画素数で言えば上回ることも相まってか ☆5でした。
![aaaacarnaux.github.io_NFT-Marker-Creator_.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/bb7e1e6a-23c1-9352-b500-63dee6b43425.png)
Ref. [私たちについて - 株式会社ZOZOテクノロジーズ](https://tech.zozo.com/aboutus/)

今回は Twitter のヘッダ画像を使います。

`Generate` ボタンを押下すると `Image Descriptors` として拡張子 `.iset`, `.fset`, `.fset3` の3ファイルをダウンロードできます。

#### 💡Image Tracking 向けとしての画像として必要な要素

NFT Marker Creator のページ下部に注意書きとして `Warning: Images with width and/or height above 1000px might take 20min+ to generate.` と記載されている通り、幅や高さが 1000px を超える画像は作成に 20 分以上かかるとされていますが、だからといって小さな画像を使っても正しく認識されません。

どのような画像が適切かは GitHub NFT-Marker-Creator の Wiki ページ [Creating good markers](https://github.com/Carnaux/NFT-Marker-Creator/wiki/Creating-good-markers) で説明されています。ポイントは一定の解像度があること且つ特徴量が多いことです。

### 2. aframe-ar-nft.js を使って Image Tracking を実現する WebAR ページを作る

公式サイトから完成形を抜粋します。

```html
<script src="https://cdn.jsdelivr.net/gh/aframevr/aframe@1c2407b26c61958baa93967b5412487cd94b290b/dist/aframe-master.min.js"></script>
<script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar-nft.js"></script>

<style>
  .arjs-loader {
    height: 100%;
    width: 100%;
    position: absolute;
    top: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.8);
    z-index: 9999;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .arjs-loader div {
    text-align: center;
    font-size: 1.25em;
    color: white;
  }
</style>

<body style="margin : 0px; overflow: hidden;">
  <div class="arjs-loader">
    <div>Loading, please wait...</div>
  </div>
  <a-scene
    vr-mode-ui="enabled: false;"
    renderer="logarithmicDepthBuffer: true;"
    embedded
    arjs="trackingMethod: best; sourceType: webcam; debugUIEnabled: false;"
  >
    <a-nft
      type="nft"
      url="https://arjs-cors-proxy.herokuapp.com/https://raw.githack.com/AR-js-org/AR.js/master/aframe/examples/image-tracking/nft/trex/trex-image/trex"
      smooth="true"
      smoothCount="10"
      smoothTolerance=".01"
      smoothThreshold="5"
    >
      <a-entity
        gltf-model="https://arjs-cors-proxy.herokuapp.com/https://raw.githack.com/AR-js-org/AR.js/master/aframe/examples/image-tracking/nft/trex/scene.gltf"
        scale="5 5 5"
        position="50 150 0"
      >
      </a-entity>
    </a-nft>
    <a-entity camera></a-entity>
  </a-scene>
</body>
```

Ref. https://github.com/AR-js-org/AR.js#-image-tracking

以下、ポイントをかいつまんで説明します。

#### Step 1. Script の読み込み

```html

<script src="https://cdn.jsdelivr.net/gh/aframevr/aframe@1c2407b26c61958baa93967b5412487cd94b290b/dist/aframe-master.min.js"></script>
<script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar-nft.js"></script>
```

Image Tracking 向けの AR.js は A-Frame 版と three.js 版が用意されています。公式ページでは A-Frame 版が使われているので、一緒に A-Frame 自体も読み込んでいます。読み込む順番は A-Frame → AR.js です。

#### Step 2. `.arjs-loader` の記述

```html
<style>
  .arjs-loader {
    height: 100%;
    width: 100%;
    position: absolute;
    top: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.8);
    z-index: 9999;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .arjs-loader div {
    text-align: center;
    font-size: 1.25em;
    color: white;
  }
</style>
```

```html
  <div class="arjs-loader">
    <div>Loading, please wait...</div>
  </div>
```

ページにアクセスした後、`Image Descriptors` をロードするまで一定の時間がかかるので、公式サイトのサンプルでは読み込みが完了するまで `Loading, please wait...` の文字列を表示しています。
![IMG_2688.PNG](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/662bb4bf-478d-e58b-9c40-c01fef940f66.png)
Ref. https://ar-js-org.github.io/AR.js/aframe/examples/image-tracking/nft/

不要だと感じるのであれば思い切って削除しても正常に動作しますし、説明書きを日本語に変更したい場合は文字列を書き換えるだけです。

#### Step 3. `Image Descriptors` の読み込みと呼び出す `glTF` モデルの読み込み

```html
<a-nft
  type="nft"
  url="https://arjs-cors-proxy.herokuapp.com/https://cdn.glitch.com/8d99eec0-4479-4e11-9a80-9993963fc1ea%2FNFT"
  smooth="true"
  smoothCount="10"
  smoothTolerance=".01"
  smoothThreshold="5"
>
    <a-entity
      gltf-model="https://arjs-cors-proxy.herokuapp.com/https://raw.githack.com/AR-js-org/AR.js/master/aframe/examples/image-tracking/nft/trex/scene.gltf"
      scale="5 5 5"
      position="50 150 0"
    >
    </a-entity>
</a-nft>
```

肝となる部分です。Image Tracking のために新たに `a-nft` という Custom Component が用意されています。

Attribute|Description
---|---
type|Image Tracking の場合は nft 一択です。
url|Image Descriptors の拡張子を除いたパスを記述します。
smooth|camera smoothing をオンにします。<br>True が推奨されていますが、なぜかデフォルトは false です。
smoothCount|画像が動いたときの Tracking をスムーズにします。デフォルトは 5 です。<br>数字が増えれば増えるほどスムーズになる反面、重くなります。
smoothTolerance|平滑化の距離の許容値です。デフォルトは 0.01 です。
smoothThreshold|平滑化のしきい値です。デフォルトは 2 です。

公式サイトのデモでは `smoothCount` と `smoothThreshold` をデフォルト値から変更しています。これらの数字は実際に検証しながら調整するのが良いでしょう。

```html
<a-entity
  gltf-model="https://arjs-cors-proxy.herokuapp.com/https://raw.githack.com/AR-js-org/AR.js/master/aframe/examples/image-tracking/nft/trex/scene.gltf"
  scale="5 5 5"
  position="50 150 0"
>
</a-entity>
```

`a-nft` の中で呼び出すオブジェクトをおなじみの `a-entity` で定義します。
公式サイトのデモでは恐竜の画像にあわせて恐竜の 3D モデル ( .glTF ) を呼び出しています。

なお `Image Descriptors` と 3D モデルの読み込み URL の頭に付いている `https://arjs-cors-proxy.herokuapp.com/` はクロスオリジンの問題を回避するためのプロキシです。

### 3. 認識させる画像を変えたり、呼び出す 3D モデルを変えてみよう

Glitch に呼び出す画像 @ikkou の [Twitter ヘッダ画像](https://twitter.com/ikkou/header_photo) に変更したバージョンを用意しました。
https://enchanting-ten-people.glitch.me/

スマートフォンから https://enchanting-ten-people.glitch.me/ にアクセスして、Mac/PC のブラウザに表示した @ikkou の [Twitter ヘッダ画像](https://twitter.com/ikkou/header_photo) にスマホをかざすと、サイズと位置の異なる恐竜の 3D モデルが表示されます。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <title>Sample: AR.js with Image Tracking</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="/style.css">
  <script src="https://cdn.jsdelivr.net/gh/aframevr/aframe@1c2407b26c61958baa93967b5412487cd94b290b/dist/aframe-master.min.js"></script>
  <script src="https://raw.githack.com/AR-js-org/AR.js/master/aframe/build/aframe-ar-nft.js"></script>
</head>
<body>
  <div class="arjs-loader">
    <p>
      Now Loading...<br>
      読み込み完了までお待ちください
    </p>
  </div>
  <a-scene
    vr-mode-ui="enabled: false;"
    renderer="logarithmicDepthBuffer: true;"
    embedded
    arjs="trackingMethod: best; sourceType: webcam;debugUIEnabled: false;"
  >
    <a-nft
      type="nft"
      url="https://arjs-cors-proxy.herokuapp.com/https://cdn.glitch.com/8d99eec0-4479-4e11-9a80-9993963fc1ea%2FNFT"
      smooth="true"
      smoothCount="10"
      smoothTolerance=".01"
      smoothThreshold="5"
    >
      <a-entity
        gltf-model="https://arjs-cors-proxy.herokuapp.com/https://raw.githack.com/AR-js-org/AR.js/master/aframe/examples/image-tracking/nft/trex/scene.gltf"
        scale="10 10 10"
        position="100 0 100"
      >
      </a-entity>
    </a-nft>
    <a-entity camera></a-entity>
  </a-scene>
</body>
</html>
```

```css
body {
  font-family: helvetica, arial, sans-serif;
  margin: 0;
  overflow: hidden;
}
.arjs-loader {
  height: 100%;
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background-color: rgba(0, 0, 0, 0.8);
  z-index: 9999;
  display: flex;
  justify-content: center;
  align-items: center;
}
.arjs-loader p {
  text-align: center;
  font-size: 1.25em;
  color: white;
}
```

## まとめ

[AR.js Documentation](https://ar-js-org.github.io/AR.js-Docs/) の [Image Tracking](https://ar-js-org.github.io/AR.js-Docs/image-tracking/) に記述されていることをなぞり、自分で設定した画像で 3D オブジェクトが表示されるように書き換えました。

今回は『本当になぞっただけ』なので、次は各種パラメータの調整や表示されるオブジェクトをより実践的なものにしてみます。

また、Image Tracking ができるようになったことで、AR.js を使った「WebAR 名刺」や、着ているものに反応するような WebAR がこれまで以上に捗るかもしれませんが、前者で言えば名刺サイズに収まる画像且つ一定の特徴点を持つ画像を適切に配置できるか、後者で言えば皺の影響がどこまで出るかなどを検証してみたいと考えています。

現場からは以上です。
新しくなった AR.js で素敵な Image Tracking ライフを！
