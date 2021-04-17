# Javaのtransientで本来は関わりのないデータであることを強調する

本来の使い方とは異なります。黙って使うと怒られるかもしれないので、導入の際は念のため確認してください。
  
---
  
Javaには```transient```という修飾子があります。これを使うことでコードで表現できることが増えます。
  
# 環境

- Java
    - 15

# ユースケース

- コードの表現力を増やす
    - 関係がないデータであることを表現する

# Transientとは

Transientは、和名で一時的という意味を持ちます。
  
この意味から私は「処理のために一時的に持たせた値」であることを、表現しています。
  
## 本来のTransientの役割

transientがついているフィールドをシリアライズの対象から外します。
  
シリアライズとはデータを保存したり、送受信できる形に変換することを指します。
  
逆に保存したファイルや送受信したものを扱えるようにすることを、デシリアライズと言います。

# コード

例えば、このようなデータがあります。
  
```java
public class Base{
    private final String id;
    private final String currencyCode;

    public boolean is本則課税(String taxDiv){
        return "01".equals(taxDiv);
    }
}
public class CodeMaster{
    private final String id;
}
```
  
Baseクラスにはid, currencyCodeしか持っていません。
  
Baseクラスが持っていない税区分を元に判別する処理がある場合、毎回外から値を渡す必要があります。1回しか使用しない場合はそれでもいいでしょう。
  
しかし、親子関係を持っていて、算出するたびに税区分が必要な場合、毎回外から値を渡すのは混乱の元となります。
  
ですので、Baseクラスにセットしておき、後で使いまわすと非常に処理が見やすくなります。
  
```java
public class Base{
    private final String id;
    private final String currencyCode;
    private String taxDiv;

    public boolean is本則課税(){
        return "01".equals(this.taxDiv);
    }
}
public class CodeMaster{
    private final String id;
}
```
  
ただし、この処理を行った場合、BaseクラスがtaxDivを持つ構造になってしまいます。
  
明確な関係性がある場合はいいのですが、極稀に曖昧な関係性しか持たない場合があります。
  
その場合は、transientを持たせることで、一時的な値であることを表現できます。
  
```java
public class Base{
    private final String id;
    private final String currencyCode;
    private transient String anotherDiv;

    public boolean is本則課税(){
        return "01".equals(this.taxDiv);
    }
}
public class CodeMaster{
    private final String id;
}
```
  
同様に、currencyCodeを元に検索したマスタを処理中に扱いたいことがあるでしょう。Baseドメインに持たせることで処理が分かりやすくなることがあります。
  
```java
public class Base{
    private final String id;
    private final String currencyCode;
    private transient String anotherDiv;
    private transient CodeMaster codeMaster;

    public boolean is本則課税(){
        return "01".equals(this.taxDiv);
    }
}
public class CodeMaster{
    private final String id;
}
```

# 注意点

本来の扱い方とは違います。ですので、理解せずに使用すると意図しない挙動をすることがあります。
  
```SerializationUtils.clone```メソッドを使用するとクローン出来ますが、Transientを使用するとクローン出来ません。本来の使い方なので当たり前ですが。
  
```Lombok```のtoBuilderクラスでデータをコピーする場合は、Transientがついてもコピーできます。
  
このように、意図せずに挙動が変わってしまう可能性があるので、検証する必要があります。

# 終わりに

知らないよりは知っていた方がいい、という意味を込めてこの記事を書きました。
  
ただ、下手に思考を使ってしまうので、1月後の自分は大否定している可能性はあります。
  
インターフェースの```List```型で宣言するのか、具体的な型の```ArrayList```で宣言するのかだと、前者の方が個人的には好きですしね。
  
Effective Javaでは特にTransientの使い方等も書いてなかったですし、1つの表現方法としては一考の余地があるのではないかと考えています。
  
---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)
