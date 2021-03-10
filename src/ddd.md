# 最低限覚えておけばいいGitコマンド

この記事に載ってるコマンド覚えておけばGit使える！って言えるんじゃないかなぁ、というものを纏める記事。cherry-pickとかの頭いいことは知りません。
  
基本的にはIDEに任せた方が楽な処理も多いです。
  
ですので覚える優先度を考慮して、先にIDEよりコマンドの方が簡単なコマンド、後にコマンドよりIDEの方が簡単なコマンドを紹介します。
  
役に立つかどうかは、完全に自分の主観ですので、読者が各自判断してもらえると助かります。

# 環境
- Git
  - 2.30.2.windows.1
- IDE
  - IntelliJ IDEA
  - VS Code

# 説明しないこと
- 単語の解説

# IDEよりコマンドの方が簡単なコマンド
## ローカルで行った修正を元に戻したい
---
  
新規の機能を開発するときに、上手く機能を導入できなかった時に実行しています。
  
なお、1つ1つファイルを元に戻すのであれば、IDEに任せた方が簡単です。
  
```bash
git reset --hard
```

# 強制的にローカルとリモートを一致させたい
---

コンフリクト修正やマージを行った結果、ローカル環境が壊れてしまい、解決方法が分からないときに実行しています。
  
```bash
git reset --hard origin
```
  
手元の修正は残しておきたい、というときにはブランチを切って退避させておきましょう。スタッシュでも問題ありませんが、普段使用しているIDEのIntelliJ IDEAではブランチの方が差分確認がしやすいのでお好みで選んでください。
  
```bash
# git switch -c 退避ブランチ名
git switch -c feature/conflict
git add .
git commit -m "退避"
git switch -
git reset --hard origin
```


## 
---

```bash
git stash
git pull --rebase
git stash pop
```

# コマンドよりIDEの方が簡単なコマンド
## Gitに変更を登録する
---


```bash
git add .
```

## 
---

```bash
git pull
```

## 
---




# 終わりに

  
---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)
