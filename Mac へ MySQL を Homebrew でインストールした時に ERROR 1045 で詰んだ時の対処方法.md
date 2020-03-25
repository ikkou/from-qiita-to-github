# Mac へ MySQL を Homebrew でインストールした時に ERROR 1045 で詰んだ時の対処方法

```
この記事は 2015-01-07 JST 付で Qiita に投稿したものです。
```

# 前置き

Mac に MySQL を Homebrew でインストールしました。

```
$ brew install mysql
$ unset TMPDIR
$ mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
```

起動と終了も問題ありませんでした。

```
$ mysql.server start
$ mysql.server stop
```

この状態でログインしようとすると ERROR 1045 が出ました。

```
$ mysql -uroot
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
```

root ユーザーの権限設定がされていない為に発生するエラーなので、一般的には MySQL の権限システムを使用せずに起動して乗り切ります。

```
$ mysqld_safe --skip-grant-tables &
$ mysql -u root
```

しかしここで ERROR 1045 がまたも出ました(´・ω・`)

```
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
```

その後、`brew uninstall mysql` してから改めて `brew install mysql` するなどしましたが、ERROR 1045 が解消することはなく詰みました。


# 対処方法

`mysql` に関連するファイルを削除して、`mysql` のサービスを立ち上げてから、初期化スクリプトである `mysql_install_db` を走らせることで修正することが出来ました。

```
$ launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
$ rm -r /usr/local/var/mysql/
$ launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
$ unset TMPDIR
$ mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
```

これでログインすることが出来るようになりました！

```
$ mysql -u root
```

無事にログイン出来るようになった後は `mysql_secure_installation` を叩いて、よしなにセキュリティ設定を行なっておきます。

```
$ mysql_secure_installation
```

いくつかの質問に `Y/n` で答えていきます。基本的にすべて `Y` で答えて問題ありません。

現場からは以上です。
