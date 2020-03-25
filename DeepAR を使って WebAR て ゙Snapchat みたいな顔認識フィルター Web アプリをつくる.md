# DeepARを使ってWebARでSnapchatみたいな顔認識フィルターWebアプリをつくる

```
この記事は 2020-03-16 JST 付で Qiita に投稿したものです。
```

WebAR というと 8th Wall Web や最近 Image Tracking に対応した AR.js が有名ですが、DeepAR を使うことでそれらが対応していない「顔認識」系の WebAR を実現できます。

DeepAR でググると [Amazon SageMaker の DeepAR 予測](https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/deepar.html) がトップに出てきますが、本記事で取り扱うのは Augmented Reality の方の DeepAR です。

## DeepARとは

DeepAR は海外では Snapchat 日本では SNOW をはじめとする顔認識系アプリを実現するための SDK です。

> DeepAR.ai is an SDK for Snapchat face filters, face lenses and effects for any iOS, Android, Unity or HTML5 app. Full face detection, face tracking software for AR and Augmented Reality advertising

Ref. https://www.deepar.ai/

ネイティブアプリとなる iOS/Android の SDK だけでなく、JavaScript ライブラリも用意されているので、Web における AR である WebAR を実現できます。

### どんなことができるのか?

プラットフォームによって異なりますが、本記事で扱う WebAR では、顔認識からのマスク、エフェクト、複数人の顔認識、画像認識ができると説明されていますが、[Background Segmentation](https://help.deepar.ai/en/articles/3539592-background-segmentation) と [Hair Segmentation](https://help.deepar.ai/en/articles/3539456-hair-segmentation)、そして [Emotion Tracking](https://help.deepar.ai/en/articles/3672563-emotion-blend-shapes-example) には対応していないとされています。

> It supports face masks, effects, multiple face tracking, natural image tracking. The background and hair segmentation and emotion tracking are not supported.

Ref. https://help.deepar.ai/en/articles/3545207-getting-started

※ マスクとエフェクトは試していますが、複数人の顔認識と、特に気になる画像認識はまだ試していません。

#### 実際に動かしている様子

Twitter に動画をアップしています。
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr"><a href="https://twitter.com/hashtag/WebAR?src=hash&amp;ref_src=twsrc%5Etfw">#WebAR</a> で Snapchat みたいなことを実現する <a href="https://t.co/BC0PM8Jkhx">https://t.co/BC0PM8Jkhx</a> の Qiita 記事埋め込み用動画です🕶 <a href="https://t.co/TP0dPtPMg7">pic.twitter.com/TP0dPtPMg7</a></p>&mdash; ikkou / いっこう (@ikkou) <a href="https://twitter.com/ikkou/status/1240250264843505667?ref_src=twsrc%5Etfw">March 18, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
Qiita で動画が展開されないときは、遷移して確認後、戻ってきてください:)

### でもお高いんでしょう?

商用利用となると有償ですが、10 MAU 以下、とりあえず自分で触って試してみる分には無償です。

|    Price    |      MAU |
|-----------:|---------:|
| $0/month    |       10 |
| $25/month   |    1,000 |
| $100/month  |    5,000 |
| $500/month  |   30,000 |
| $1000/month |  100,000 |
| Custom      | 100,000+ |

Ref. https://developer.deepar.ai/pricing

MAU 10 以降は課金が発生しますが、値付け感は 8th Wall よりも優しいと感じます。

なお、有償契約すると iOS, Android, Web のどれもが MAU 上限まで使えますが、同時に使った場合 MAU は合算されるので注意が必要です。
![IMG_2715.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/c8974e1c-4741-8fac-0f60-6eadc32ab1b4.jpeg)
また、無償版は上記のように画面上に `DeepAR.ai` の透かしが入ります。

## 実際にDeppARを動かしてWebARをやってみる

### 1. アカウントの作成

https://developer.deepar.ai/ にアクセスして `SIgn Up` からアカウントを作成します。

### 2. 新しいプロジェクトの作成

アカウントを作成すると [Projects](https://developer.deepar.ai/projects) にアクセスできるようになります。
このページの + マークから新しいプロジェクトを作成します。
![developer.deepar.ai_createproject.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/167e5137-6e9e-b4ca-44b6-d41b05bb5408.png)
Price calculator の初期値は 100,000 になっているので、とりあえず試してみたい場合はスライダーを左端まで持っていき、Free $0 / month になっている状態で `Get started` をクリックしてください。

次のページで Project name に任意の名前を入れ、`I agree to terms and conditions` のチェックボックスにチェックを入れた状態で `Create Project` ボタンをクリックすると `Your projects` にプロジェクトが作成されます。
![developer.deepar.ai_projects_145a67c2-47cc-4c55-af72-36f1169a10fc.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/5fbbd7ec-6f99-8b8e-8e64-979051c0afff.png)
最後に、今回は WebAR を試すので、`Add web app` をクリックし、任意の `App name` と `Domain` を入力します。ローカル環境で試す場合は `localhost` と入力します。その後、`Create app` をクリックすると重要な `App key` が発行されます。

ここで発行される `App Key` は重要なので控えておいてください。

### 3. DeepAR Web SDK のダウンロード

Downloads ページから DeepAR Web SDK をダウンロードします。記事公開の 2020-03-18 時点では `v1.4.6` が最新でした。

必要な JavaScript ライブラリを除いたサンプルは GitHub にも公開されています。こちらを使う場合は script の読み込みなど最低限の追記が必要です。

DeepARSDK/quickstart-web-js
https://github.com/DeepARSDK/quickstart-web-js

### 4. index.html の書き換えと表示

141 行目付近の `licenseKey` の `your_license_key_goes_here` を 2. で作成した `App Key` に差し替えます。

```javascript
var deepAR = DeepAR({
  canvasWidth: canvasWidth,
  canvasHeight: canvasHeight,
  licenseKey: 'your_license_key_goes_here',
```

その上でローカルサーバを立ち上げるとブラウザで確認できます。

Python が動作する環境であれば、`黒い画面` で `python server.py` を叩いて http://localhost:8888/ にアクセスすれば OK です。

#### 💡スマートフォンから確認したい

一般的にスマートフォンから `http://localhost:8888/` は表示できません。スマートフォンから確認したい場合は HTTPS 環境が用意されている Glitch や GitHub Pages を使うと良いでしょう。その場合は環境に合わせて `domain` を書き換えて `App key` を再発行する必要があります。現時点では `domain` を編集できないようです。

## フィルターの作り方

本記事では詳細な使い方そのものには触れませんが、DeepAR で顔を認識した後に表示するフィルター全般は DeepAR Studio を使います。

DeepAR Studio
https://help.deepar.ai/en/articles/3493100-deepar-studio

DeepAR のフィルターは FBX を読み込んだ後、編集したものを Export する形で作成します。リファレンスとなる FBX ファイルが用意されているので、まずはそれを使って試してみるのが良いでしょう。
<img width="1680" alt="スクリーンショット 2020-03-18 20.29.27.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/3ca77eef-fe3e-46c4-3ded-4a021a021041.png">
^ [Reference mask model](https://help.deepar.ai/en/articles/3493102-reference-mask-model) の FBX を DeepAR Studio に `Import FBX` した直後の様子

この状態で `Export Model` で出力した拡張子のないファイルを読み込ませると、Export したフィルターが表示されるようになります。
<img width="287" alt="スクリーンショット 2020-03-18 20.28.07.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/b5441e81-5a9d-ce2c-a4f2-6c0c12ce8908.png">
Snapchat で言うところの Lens Studio あるいは instagram で言うところの Spark AR Studio と比べると、機能面ではまだまだ足りない部分がありますが、最低限必要になりそうなドキュメントは整備されているので、興味のある方はオリジナルフィルターを作ってみてください。

また、私が見始めた頃にはなかったですが、最近公開された Free Filter Pack というページでは、フィルターとなる Export されたファイルと、その元となる FBX などのアセットをまとめてダウンロードできるので、これらを参考に手を加えてみるのも良いかもしれません。
https://help.deepar.ai/en/articles/3580432-free-filter-pack

### DeepAR Asset Store という存在

DeepAR には DeepAR Asset Store という自分のフィルターを販売できる場所が用意されています。
<img width="997" alt="スクリーンショット 2020-03-18 20.50.33.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/4895ebad-242d-f784-6388-5beafd39f812.png">
Ref. https://www.store.deepar.ai/

ここでは文字通り DeepAR 用のフィルターが「販売」されています。1つあたり £49.00 つまり ¥6,250 程度 ( 2020/03/18 時点のレート ) となかなかのお値段が設定されていますが、Free Filter Pack 同様に編集元となる FBX などのアセットも含まれているので、真似したいものがある場合などには適しているかもしれません。
なお 2020/03/18 時点では自分自身が作成したフィルターを販売できる仕組みはありませんでした。

## まとめ

DeepAR という顔認識系アプリを実現するための SDK を紹介した上で「とりあえず動かしてみる」ところまでを説明しました。

サンプルのままでは使いにくいかもしれませんが、例えばスタンプ機能やスクショ撮影などを組み込むことで、キャンペーンサイトでの利用などが出来るかもしれないと感じました。用途によっては一定のポテンシャルがあるのではないでしょうか！

同じ商用 SDK でも 8th Wall Web と DeepAR では実現したいことが異なるため、比較するものではありませんが、こうして WebAR でもできる可能性が拡がっていくのは素敵なことだと感じています。

現場からは以上です！
