# Chrome 79 から WebXR Device API が有効になるも Oculus 系の HMD が認識されない件とその解決方法について

```
この記事は 2019-12-20 JST 付で Qiita に投稿したものです。
```

## TL;DR

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">WebXR 界隈的に最高のニュースだった Chrome 79 から WebXR Device API がデフォルトオンになったにも関わらず WebXR Sample Pages 等で Oculus Rift S が反応しないときは chrome://flags から次の①と②を実施すれば🆗:)<br><br>①Oculus hardware support: Enabled<br>②XR device sandboxing: Disabled</p>&mdash; ikkou / いっこう (@ikkou) <a href="https://twitter.com/ikkou/status/1207912623443865600?ref_src=twsrc%5Etfw">December 20, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Chrome 79 で WebXR Device API が正式サポートされました🎉

`WebXR Device API` はブラウザから Oculus Rift S をはじめとする (VR|AR|MR) HMD の情報を取得するための API です。

2019/12/20 現在の「メジャーなブラウザ」では Chrome 79 以降のみが「標準で」対応しています。
この「メジャーなブラウザ」というところが肝です。
<img width="1287" alt="スクリーンショット 2019-12-20 17.33.55.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/8434e1b3-cbbb-9b10-a2dd-e8d689f20ff7.png">
source. https://caniuse.com/#search=WebXR

## Chrome 78 以前はどうだったのか?

`chrome://flags` から以下のフラグを `Enable` にすると Chrome で `WebXR Device API` を通して Oculus Rift S で `WebXR` を楽しむことができました。

* `WebXR Device API`: Enabled
* `Oculus VR support`: Enabled
* `XR device sandboxing`: Disabled

ここで言う `WebXR` を楽しむというのは、[WebXR Sample Pages](https://immersive-web.github.io/webxr-samples/) で `Yes!` が表示され、各サンプルで `ENTER VR` が表示される状態を指しています。
![コメント 2019-12-20 173509.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/f811c90d-9f39-57b8-6ffa-2b7f5f98d2de.png)
`WebXR Device API` に対応している場合に表示されるテキスト
source: https://immersive-web.github.io/webxr-samples/

なお Firefox のように、まだ `WebXR Device API` に対応していない場合でも `WebVR API` へのフォールバックで概ね体験可能です。
![コメント 2019-12-20 173609.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/5ea71223-8976-b039-a9f6-43ea150c9cfb.png)
`WebXR Device API` あるいは `WebVR API` へのフォールバックで VR HMD が認識されているときに表示される状態
source: https://immersive-web.github.io/webxr-samples/immersive-vr-session.html
<img width="451" alt="スクリーンショット 2019-12-20 17.48.45.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/fb982624-feb6-80d9-920e-3bec4e728e2a.png">
VR HMD が認識されていないときは `VR NOT FOUND` が表示される
source: https://immersive-web.github.io/webxr-samples/immersive-vr-session.html

## Chrome 79 でどうなったのか?

まず `WebXR Device API` は標準対応となったため、`chrome://flags` から変更する必要がなくなりました！

標準対応となったので「これだけでいける！」と思っていましたが、実は Oculus Rift/Rift S に関しては別途フラグを立てる必要がありました。もともと `Oculus VR support: Enabled` を指定していたので、それかと思うとこのフラグは消えていていました！

結論として `Oculus VR support` に代わる `Oculus hardware support` を `Enable` にする必要がありました。

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">I figured it out. The following Chrome flags need to be set:<br><br>Oculus hardware support: Enabled<br>XR device sandboxing: Disabled</p>&mdash; Ian Kettlewell (@kettlecorn) <a href="https://twitter.com/kettlecorn/status/1207880549991366656?ref_src=twsrc%5Etfw">December 20, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Chrome 79 以降で `WebXR` を動作させるために必要なこと

* `Oculus hardware support` = `chrome://flags/#oculus-vr` を `Enabled` に変更
* `XR device sandboxing` = `chrome://flags/#xr-sandbox` を `Disabled` に変更
* Chrome を再起動

上記の設定で 2019/12/20 現在 Chrome Beta, Chromium の最新バージョンである v81 まで動作することを確認しています。

## 2019/12/20 現在、WebXR Device API に「真の標準対応」しているブラウザは Oculus Browser だけ！

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">というわけで改訂版。<br>現状ブラウザでVRをHMDで動かす環境は<br>・Chrome: WebXR (フラグでデバイスを有効にする必要あり)<br>・Firefox: WebVR<br>・Oculus Browser: WebVR,WebXR<br>・Safari: なにそれ美味いのか<br>ってことですかね。</p>&mdash; OE Waku 𛄊𛀬 (@wakufactory) <a href="https://twitter.com/wakufactory/status/1207924277044867077?ref_src=twsrc%5Etfw">December 20, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

本記事では一切のフラグ変更を必要としないことを「真の標準対応」と表現していますが、これは W3C として定義している用語ではありません。そして Oculus Browser は決してメジャーなブラウザではない異端児です。

現場からは以上です。

---

Special Thanks: OE Waku さん - https://twitter.com/wakufactory
