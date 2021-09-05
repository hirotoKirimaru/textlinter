# CLIでgrepしたGitのtagをリモート・ローカルともに削除する(Mac, WindowsのPowershell)

Gitのtagを一斉に消そうとしたかったので、CLIで実施できないか模索したときのメモ。
  
# 環境
- Git
- Mac
  - HighSiera
- Windows
  - Powershell

# ゴール

1. CLIでGitのtagをリモート・ローカルともに削除する。
1. grepした結果を元に一斉削除する。
1. Mac, Linux, Windowsのどの環境でも実行できるようにする。

環境に関しては個人の好みです。WSLもあるので、無理にWindowsで行う必要はないと考えています。

# 書かないこと

基本的なコマンドの組み合わせですので、コマンド説明はしません。
  
WindowsのPowershellに関しても詳しく理解していないので、参考程度に見てください。

# 事前条件

```git tag```を実行すると、次のタグ一覧が取得でき、リモートにも同様のタグがPushされている状態であること。
  
```bash
git tag

0.0.1
0.0.2
0.0.1-it
0.0.2-it
```

# 事後条件

```it```と名がついているtagがリモート・ローカルともに削除されていること。
  
```bash
git tag

0.0.1
0.0.2
```

# 対応
# Mac, Linux

```bash
# ローカルのtag一覧をgrepで絞り込み、絞り込んだtagでリモートのtagを削除する
git tag | grep "it" | xargs git push -d origin

# 削除されたリモートのタグをローカルに反映する
git fetch origin --prune 'refs/tags/*:refs/tags/*'
```

# Windows

コマンドプロンプトではgrepに相当する機能がありませんので、Power Shellで実行してください。
  
```powershell
# ローカルのtag一覧をslsで絞り込み、絞り込んだtagでリモートのtagを削除する
git tag | sls "it" | %{ git push -d origin $_}

# 削除されたリモートのタグをローカルに反映する
git fetch origin --prune 'refs/tags/*:refs/tags/*'
```
# 終わりに

Gitのtagを一斉削除するユースケースは基本的にはないと思います。ですが、1つ1つ手動で消すのも嫌だったので、調べてみました。
  
未来の自分はこの記事を読むのか分かりませんが、役に立ったらいいと思います。

---

この記事お役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

# 参考記事

- [https://tech.sanwasystem.com/entry/2016/07/05/185717:title]
- [https://qiita.com/lni_T/items/e5eefbff90de8fdf802b:title]
