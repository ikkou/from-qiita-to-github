# iOS 12.2で半ば終わったWebVRとWebARがiOS 13でどうなったか

```
この記事は 2019-04-16 JST 付で Qiita に投稿したものです。
```

## TL;DR

2019-09-20 JSTにiOS 13のアップデートが開始されました。
この文章は「[iOS 12.2でWebVRとWebARが半ば終わった件について](https://qiita.com/ikkou/items/a193d13250d9e3d51473)」の続報にあたります。
やや煽り気味のタイトルですが、経緯を知らない方は先に目を通しておくと捗ります。

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">iOS 13 の配信が始まっていた！<br>一般公開前は NDA の兼ね合いでスクショ付きの発言が 🆖 だった iOS 12.2 で半ば終わった WebVR/WebAR がどうなるかのアンサーが言える！<br>設定→Safariから「モーションと画面の向きへのアクセス」が消えました！<br>ユーザーに設定を変更してもらえれば論も終わりました！ <a href="https://t.co/qNnCfG4tQQ">pic.twitter.com/qNnCfG4tQQ</a></p>&mdash; ikkou / いっこう (@ikkou) <a href="https://twitter.com/ikkou/status/1174869256833421312?ref_src=twsrc%5Etfw">September 20, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## iOS 12.2でなにが起きたのか3行で

* iOS 12.2からデフォルトでSafariのモーションデータを取得出来なくなった
* モーションデータを取得出来ないと動かなくなるWebVRとWebARコンテンツがある
* ユーザーにSafariの設定を変更してもらうことで動くようになる

## iOS 13でどうなったのか3行で

* iOS 13でもデフォルトでモーションデータを取得できない
* ユーザーに変更してもらうSafariの設定項目がなくなった
* 新APIの `DeviceMotionEvent` と `DeviecOrientationEvent` を叩く必要がある

## 改めて、これから`WebVR`を使ったウェブマーケティング施策はどうなるのか

例えば 2019-09-20 現在では期待する動作をしない`A-Frame`を用いたWebVR施策を継続する場合、`A-Frame`そのものに手を入れる必要があります。

現実問題として、iOS 12.2がリリースされたタイミングで関連するIssueは起票されていますが、まだ対応されていません。

> Sensor access disabled by default in Safari from iOS 12.2+
> https://github.com/aframevr/aframe/issues/3976

Issueを読み進めていくと7月3日時点で、iOS 13がリリースされれば今まで通りに戻る(超意訳)というコメントがあるものの……。

> Once iOS 13 ships things will be back to normal. A-Frame will be able to
request access to devicemotion API without any manual steps. https will be
required though
> https://github.com/aframevr/aframe/issues/3976#issuecomment-508110150

`A-Frame`はGitHub上で開発が進められているオープンソースなので、やる気がある人はぜひPull Requestを送ってください。

## 改めて、これから`WebAR`を使ったウェブマーケティング施策はどうなるのか

WebVRコンテンツを開発するための定番が`A-Frame`であるように、WebARコンテンツを開発するための定番は`8th Wall`だと感じています。

そしてこの`8th Wall`をWebで扱うためのライブラリとして`8th Wall Web`がありますが、実はiOS 13 Betaの時点から対応を加えていました。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">実は WebAR コンテンツを開発するなら安定の 8th Wall は iOS 13 beta 時点から対応を入れていました！<br>Safari 13 から追加された<br>・DeviceMotionEvent <br>・DeviecOrientationEvent<br>の許可を求める API を叩く形です！<a href="https://t.co/eaU5u0DMhQ">https://t.co/eaU5u0DMhQ</a> の WEB AR DEMO で確認できます！ <a href="https://t.co/EriRxnMOT1">pic.twitter.com/EriRxnMOT1</a></p>&mdash; ikkou / いっこう (@ikkou) <a href="https://twitter.com/ikkou/status/1174872983241777152?ref_src=twsrc%5Etfw">September 20, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

奇しくも`8th Wall WEb`はiOS 13のリリースと同じタイミングで1周年を迎えました。

> A Year in WebAR: Celebrating the First Anniversary of 8th Wall Web
> https://medium.com/8th-wall/a-year-in-webar-celebrating-the-first-anniversary-of-8th-wall-web-d0bae7a6a02a

ページを読み進めると分かりますが、COACHやAdidasなどの有名ブランドのWebAR施策に使われているようです。少なくとも現時点で`Web AR`を用いた施策を実施したいのであれば、`8th Wall Web`を使うのが最善策かもしれません。

## で、iOS 13で動かなくなったWebVR/WebARはどうすれば良いのか?

よっしゃiOS 13がリリースされたから書くぞ！と思っていたら株式会社palanさんのメディアに先を越されたので引用します:D

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">今日リリースされたiOS13でのsafariデバイスモーション対応（WebAR・WebVR）の記事を書きましたー。<a href="https://t.co/qwk2yG1tOt">https://t.co/qwk2yG1tOt</a></p>&mdash; eishis / 今月 75.44 km (@kinnikujst) <a href="https://twitter.com/kinnikujst/status/1174915017717637121?ref_src=twsrc%5Etfw">September 20, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

ポイントはiOS 13から追加された`DeviceMotionEvent`と`DeviceOrientationEvent`の`permission API`を叩くことです。

```js
$('.request_permission').on('click', function() {
  if (
    DeviceMotionEvent &&
    DeviceMotionEvent.requestPermission &&
    typeof DeviceMotionEvent.requestPermission === 'function'
  ) {
    DeviceMotionEvent.requestPermission();
  }
  if (
    DeviceOrientationEvent &&
    DeviceOrientationEvent.requestPermission &&
    typeof DeviceOrientationEvent.requestPermission === 'function'
  ) {
    DeviceOrientationEvent.requestPermission();
  }
})
```

引用元: [iOS13でWebARとWebVRのデバイスモーション設定が改善しました！](https://bagelee.com/webar-vr/ios13-webar-webvr/)

注意点としては、まず`HTTPS`通信であることが求められます。常時HTTPS化の進んだ現在の世界線では苦もなく実現可能かもしれませんが、ローカル環境での開発時には注意が必要です。

そして`permission API`を叩くには、ユーザーによる何らかのアクションが必要です。前述の`8th Wall Web`では「ARにはセンサーが必要だからContinueをタップしてね」というポップアップを出した上で、ユーザーによるContinueボタンのタップによってonClickを発火させています。

![IMG_4190 2.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/f40c79d0-3ae5-7f66-c436-90ebff90b299.jpeg)

ref. https://apps.8thwall.com/8w/jini/ にスマホでアクセスすると表示されるポップアップ

`8th Wall Web`の場合はContinueをタップ後にアクセスを求めるダイアログが表示されます。

![IMG_4194.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/92db1699-a2b5-45e9-dce7-fa35e459e63f.jpeg)

ref. https://apps.8thwall.com/8w/jini/ でContinueタップ後に表示されるダイアログ(※言語設定が日本語の場合)

## これで万事解決なのか?

iOS 13から`DeviceMotionEvent`と`DeviceOrientationEvent`が使えるようになり、万事解決かと思いきや、実はそうとも言い切れません。

アクセスを求めるダイアログで、意図的であろうと偶然だろうと「キャンセル」を選択してしまうと、当然ながらDeniedされてしまうため、期待する挙動になりません。

そればかりか『誤って「キャンセル」してしまったのでもう一度やり直そう』とページをリロードをしてもDeniedされたままとなります。これは何度リロードしても状況は変わりません。

![IMG_4195.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9946/737e2cc2-b5fa-46ba-09f4-d8afeadd0b50.jpeg)

ref. https://apps.8thwall.com/8w/jini/ で許可を求められた際に「キャンセル」を選択してしまった後の画面

少なくとも 2019-09-20 現在では`8th Wall Web`ですら、この問題をソフトウェア的に解決できない状態にあります。8th Wallのトップページに戻り、ゼロからWEB AR DEMOに入り直しても継続的にDeniedされた状態となっていますが、一度タブを閉じて、もう一度開き直すことで再びポップアップが表示されるようになります。

一般的なユーザーの導線を考えると、誤って、あるいはよく分からず「キャンセル」を選択してしまうことは往々にして起こりえます。少なくとも`8th Wall Web`のようにDeniedされていることを示せればまだマシですが、何も考慮していないと「ユーザーは選択したにも関わらず動作しない」という状況が発生してしまいます。

かといって「動作しない場合は一度タブを閉じて開き直してください」とお願いするのもスマートではなく、なかなか難しいと感じています。

## さいごに

iOS 12.2がリリースされた時点ではわりと本気でWebVRもWebARも終わったのではないかと思いましたが、iOS 13の世界線はまだ課題はありそうなものの、未来はあるように感じました。

そもそもWebARの文脈で言えばiOSの`AR Quick Look`もAndroidの`Scene Viewer`も影響を受けていません。iOS 12.2がリリースされようと問題なく動作していますし、後者の`Scene Viewer`に関してはiOSでもAndroidでも、さらに言えばMagic Leap Oneでも動作するようになっています。

ウェブの進化とともにデバイスも進化しています。パワーアップした端末によってSafariで出来ることもきっと増えることでしょう。というわけで今後もWebVRとWebAR、そしてWebXRを応援しています:D
