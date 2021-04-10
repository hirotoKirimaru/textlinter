# JavaでNullPointerException回避をする方法をまとめる

今日も小ネタ。
  
NullPointerExceptionを回避するのは簡単だけど、もうちょっとスマートに書けない？
  
ということを纏める記事。
  
余談ですが、NullPointerExceptionは「ぬるぽ」とか大文字取って「NPE」とか呼んでます。以後、「NPE」で統一します。
# 環境
- Java
  - 16

# データ構造

次のような親子孫関係を持つ。
  
リストでのNPE回避も表現したいので、リストも持たせています。
  
- Parent
  - Child
    - GrandChild

ToDo:201。
# 一番シンプル

```!= null```で回避するのが一番簡単です。
  
```null```は基本型なのでIDEで色分けされて見やすいので、よく使用しています。
  
```java
Parent parent = null;

if(parent != null) {
  // 以後の処理
}
```

ただし、入れ子が複雑になると、都度null回避をしなければならないので面倒です。
  
```java
if (parent != null ||
  parent.getChild() != null ||
  parent.getChild().getGrandChild() != null) {

  BigDecimal zero = parent.getChild().getGrandChild().getRate();
}
```


# Optional型を使用する

Optional型に設定する方法があります。

```java
Parent parent = null;

Optional.ofNullable(parent)
  .orElse(null); // もしparentがnullならnullを返却する
```
  
入れ子構造が複雑になっても、中間でNPEが発生しないのでコードが書きやすいです。
  
```java
Optional.ofNullable(parent)
    .map(Parent::getChild)
    .map(Child::getGrandChild)
    .map(GrandChild::getRate)
    .orElse(BigDecimal.ZERO)
```
  
型変換したい時にもOptional型を使うと、安全にNPE回避できて便利ですね。
  
```java
BigDecimal a = null;
Optional.ofNullable(a).map(BigDecimal::toPlainString).orElse(null);
```


# Objects型を使用する

```Objects```型に```isNull```と```nonNull```メソッドがあります。
  
```java
Parent parent = null;

if(Objects.nonNull(parent)) {
  // 以後の処理
}
```
  
私は単体で扱うことはあまりなく、Stream処理のfilterでNPE回避のためによく使用します。メソッド参照を使用すると、思考コストが減るので便利です。
  
```java
// 親と子はいるが、孫がいないので、NPE回避を導入しないとエラーになる
ArrayList<Child> baseDetailList = new ArrayList<>();
baseDetailList.add(Child.builder().build());

Parent parent = Parent.builder()
  .children(baseDetailList)
  .build();

assertThat(
  Optional.ofNullable(parent.getChildren())
    .stream().flatMap(Collection::stream)
    .map(Child::getGrandChildren)
    .filter(Objects::nonNull) // NPE回避用ロジック
//  .filter(e -> e != null) 一瞬、何がNullではないものかがわからない。  
//  .filter(e -> e.tax != null) 実はtaxがNull以外かもしれない、という思考になる
    .flatMap(Collection::stream)
    .map(GrandChild::getRate)
    .collect(Collectors.toList())
).isEmpty();
```

# どのNPE回避方法がオススメ？

NPE回避できればどれでもいいです。どれを使っていたらカッコイイ、とかも無いと思います。
  
次のコードは同じ処理をしていますが、はっきり言うとどれもイケてないな、と感じてしまいます。
  
```java
BigDecimal a = null;

String hoge = a == null ? null : a.toPlainString();
String fuga = Optional.ofNullable(a).map(BigDecimal::toPlainString).orElse(null);
```
  
TypescriptにはOptional Chaining、KotlinにはSafe CallというNPE回避する方法があります。```?.```と書くだけです。これで書けたら一番カッコイイのですが、Javaにはありません。
  
```Java
String hoge = a?.toPlainString();
```
  
---

個人的には次の手癖でコーディングしています。参考程度に見てください。
  
- 入れ子構造と型変換が少ない
  - ```== null```
- 入れ子構造と型変換が多い
  - ```Optional```型を使う
- Stream APIで処理する
  - ```Objects```型を使う

# 終わりに

Optional型は自分で生成するものではない、というイメージからなんとなく避けていましたが、複雑になったケースだと一考の余地ありますね。今回、Optional型で色々検証して安全にデータを取り出せることに気づけて良かったです。
  
まぁ、気を付けていても結構な頻度でNPEは起こしちゃうんですけどね…。
  
できれば、```@Nullable```や```@NonNull```とか表現はしてほしいのですが、自分も付与し忘れることはたくさんあるので人のことは言えません。
  
NPEの回避って地味に面倒なのであんまりやりたくはないのですが、起こしてしまうと恥ずかしいので今後も地道に頑張りたいです。

---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

# 類似記事

JavaでNullを意識せずにNullableな配列項目をStream APIで処理する
[https://nainaistar.hatenablog.com/entry/2021/04/10/120000:embed:cite]