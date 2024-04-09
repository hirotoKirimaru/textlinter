# WSLで起動したサーバにiPhone等の別端末からアクセスする

※ 家で検証したときはできなかったのですが、会社で検証したときはできましたので、残しておきます。

WSLで起動したサーバに対してiPhoneやAndroidからアクセスすることでモバイル端末の実機確認をしたかったのですが、単純にWSLのIPにアクセスするだけでは確認できなかったので手順を残します。

# 環境
- WSL
  - Ubuntu 22.04.4 LTS

# ゴール

iPhoneでWSL上に起動しているサーバにアクセスする。

# 詳細
## 管理者権限でコマンドプロンプトを起動します

管理者権限でコマンドプロンプトを起動します。私の過去記事に簡単に管理者権限でアプリケーションを起動する方法を残しているので、参考にしてください。

- [https://nainaistar.hatenablog.com/entry/2022/03/14/120000#title]()


## ホストマシン（Windows）でホストマシンとWSLのIPを調べます

コマンドプロンプトにてホストマシン（Windows）とWSLのIPを調べる。

```bash
ipconfig | findstr "IPv4 Address"
# ホストマシン と WSL の IPを調べる
# 例）ホストマシンのIP
# 192.168.10.106
# WSLのIP
# 172.25.208.1
```

## ホストマシンからWSLへのポートフォワードを設定する

直接モバイル端末からWSLのサーバにアクセスできないので、ホストマシンにアクセスしたらWSLに転送する設定します。次の設定だとホストマシンの4200番ポートにアクセスしたら、WSLのサーバに4200番ポートでアクセスします。

ちなみに、4200番ポートはAngularのデフォルトのポートです。

```bash
netsh interface portproxy add v4tov4 listenport=4200 listenaddress=0.0.0.0 connectport=4200 connectaddress=172.25.208.1
```

## iPhoneからWSLのサーバにアクセスする

モバイル端末からはホストマシンのIPにアクセスします。

```bash
http://192.168.10.106:4200
```

# 備考

ポートフォーワード設定を確認したかったり、削除したりしたい場合は次のコマンドを実行してください。

```bash
# 現在のポートフォワード設定を確認する
netsh interface portproxy show all

# 設定を削除する
netsh interface portproxy delete v4tov4 listenport=4200 listenaddress=0.0.0.0
```

---

自宅で動かなかった理由としては、Wifiのネットワーク分離機能が有効だったので、別端末からアクセスできないようになっていたからだと思われます。

基本は問題ないと思いますが、設定変更したことによって家の機器が接続できなくなったら困るので実施しませんでした。変にセキュリティを緩くする必要もありませんし。

# 終わりに

WSLはまだまだ難しいですね。```ChatGPT```も簡単に情報を提供してくれないので、困ったら頑張って解決する必要がありますね。

今後もWSLでハマったらブログに残していきたいです。


# 類似情報
- [https://nainaistar.hatenablog.com/entry/2022/03/14/120000#title]()
- [https://nainaistar.hatenablog.com/entry/get-ip-in-wsl#title]()