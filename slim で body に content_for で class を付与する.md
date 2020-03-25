# slim で body に content_for で class を付与する

```
この記事は 2015-01-07 JST 付で Qiita に投稿したものです。
```

ERB or HAML の書き方は見掛けますが、slim での書き方を見掛けない感があったので備忘録を兼ねて。

```views.html.slim
- content_for(:body_class, "page")
.wrapper
  ...
```

```application.html.slim
body class="#{yield(:body_class)}"
  = yield
```

```application.html
<body class="page">
  <div class="wrapper">
  ...
  </div>
</body>
```

body に margin なり padding が適用されていて、上書きしたい時などに。
