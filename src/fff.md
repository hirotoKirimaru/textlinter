# TypeScriptでPythonのキーワード引数みたいな挙動をさせる（ROROパターン）

TypeScriptを書いていて、Pythonでいうキーワード引数（名前付き引数）のような挙動をしたいとTwitterに呟いたところ、[【公式】皮しばき](https://twitter.com/r315dr)さん、[suin](https://twitter.com/suin)さんに反応していただけました。
  
その中でsuinさんに教えていただいたデザインパターンのROROパターン（Receive an object, return an object）が自分の期待した動きをしたので、ブログにまとめます。
  
[https://twitter.com/suin/status/1440468357845712901?s=20:embed:cite]
  
なお、今回の記事はsuinさんに紹介していただいたmediumの記事をローカルで検証した結果ですので、より詳細なメリットは原文を読んだ方が理解できます。

# 環境
- TypeScript
  - 不明
  - PlayWrightのv1.15.0ごしにTypeScriptを検証していました

# ゴール

- TypeScriptでもPythonのキーワード引数（名前付き引数）に似た挙動をさせる

# そもそも、キーワード引数とは

Pythonにはキーワード引数という機能があります。この機能はメソッド呼び出しでパラメータ前に識別できるパラメータ名を一緒に渡すことで、順番に依存せず、メソッドにパラメータを渡せる機能です。
  
```python
# メソッドの定義
def send_mail(mail_from='example@co.jp', mail_to='example@co.jp', text='テストメール', name='運営'):

# メソッドの呼び出し
send_mail(mail_to='hogehoge@co.jp')
# mail_fromは「exmaple@co.jp」 
# mail_toは「hogehoge@co.jp」
# textは「テストメール」
# nameは「運営」
# のパラメータで渡される
```  
  
メリットとしては、いくつかあります。

- 必要な箇所にのみパラメータを渡すことができる
- パラメータを増やしても呼び出し元に一切影響を与えない
- Javaでいうオーバーロードせずに表現できる
  - シンプルに表現できる

なお、順番で識別されるパラメータのことをPythonでは位置引数と呼びます。

# ROROパターン（Receive an object, return an object）とは

メソッド呼び出し時に```object```でパラメータを渡し、返却値も```object```で返却するデザインパターンです。なお、今回私が感銘を受けたのはメソッド呼び出し時の挙動ですので、返却値の話はしません。
  
```typescript
// 既存の呼び出し
send_mail('example@co.jp', 'example@co.jp', 'テストメール', '運営');
// ROROパターンの呼び出し
send_mail({ mail_from='example@co.jp', mail_to='example@co.jp', text='テストメール', name='運営'});
```

```typescript
// 既存の呼び出し元
function send_mail(mail_from, mail_to, text, name) {}
// ROROパターンの呼び出し元
function send_mail({ mail_from, mail_to, text, name }) {}
```

# 必須チェックと不要な変数チェック

現状のままでは、キーワード引数のメリットを享受できません。型を用意しましょう。今回はメール送信者の名前である```name```を非必須とします。
  
```typescript
// メソッドの型
type EMailType = {
  mail_from: string; // メール送信元
  mail_to: string; // メール送信先
  text: string; // 本文
  name?: string; // メール送信者の名前
};
// 型を付与する
function send_mail({ mail_from, mail_to, text }: EMailType) {}
```
  
この状態で必須項目が足りない、または定義していない変数名を指定した場合、エラーを出力してくれるので間違いに気付きやすくなります。
    
ToDo: 201。

# 初期値の設定

非必須項目に初期値を与えられます。
  
```typescript
function send_mail({ 
  mail_from, 
  mail_to, 
  text, 
  name = '公式'
}) {}
```

```typescript
// メソッドの呼び出し
send_mail({ mail_from: "a", mail_to: "b", text: "c" });
// nameには初期値の「公式」

send_mail({ mail_from: "a", mail_to: "b", text: "c", name: "d" });
// nameにはパラメータで渡した「d」
```
# ソースコード
  
- [https://codesandbox.io/s/roro-patterncheck-umeq2]

# 終わりに

```object```でメソッドに渡して、メソッドで```object```を展開できるとは知りませんでした。
  
Mediumの記事では今回紹介しなかったROROパターンでのメリット・デメリットが紹介されています。今回、私の記事で紹介していないのは、ユースケースが分からなかったものと、再現ができなかったからです。
  
私の希望するPythonのキーワード引数みたいな挙動をTypeScriptでもできる、という最低条件は確認しましたので、ブログに残しました。

---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

# 参考情報

- [https://docs.python.org/ja/3.10/glossary.html]
  - argumentにキーワード引数、位置引数の説明
- [https://medium.com/free-code-camp/elegant-patterns-in-modern-javascript-roro-be01e7669cbd:title]
- [https://twitter.com/suin/status/1440468357845712901:title]