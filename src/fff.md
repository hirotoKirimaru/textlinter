# SpringのSpELでネストしたクラスの値をリフレクションして取得する
  
Java単体でもリフレクションで値を取得できます。しかし、ネストされた値を取得することは非常に面倒です。
  
Springではもっと簡単に値を取得することができます。それは、Spring式言語(Spring Expression Language、以後 SpELと表現します)を使用した方法です。
  
今回の記事では、SpELを活用してネストしたクラスの値をリフレクションして取得することを目指します。
  
# 環境
- Java
  - 15
- Spring Boot
  - 2.4.5
- Lombok

# ゴール
- 別のクラスからリフレクションを使って値を取得する

# 前提
- Getterがprivateではないこと

# ユースケース
- ネストするクラスから目的の変数を取得したい

例えば、「メールに必要な文言を巨大なネストするクラスから取得したい。しかも、簡単に書き換えられるようにマスタで管理したい。」という要件があったとします。
  
この要件を満たすために、マスタでは```child.grandChild.tax```という文言だけを管理しておいて、その値を元にリフレクションをする。
  
といったケースに使えると思います。

# クラス構成

この記事ではユースケースを意識するために、親子関係をもつクラスを使用します。
  
```java
- Parent
  - Child
    - GrandChild
```
  
```java
@Value
@Builder
public class Parent {
  private Child child;
  private List<Child> children;
}
```
  
```java
@Value
@Builder
public class Child {
  private GrandChild grandChild;
  private List<GrandChild> grandChildren;
}
```
  
```java
@Value
@Builder
public class GrandChild {
  private String tax;
  private Map<String, Integer> animals = 
    Map.of("dog", 1, "cat", 3, "mouse", 10);  
}
```

# ネストしないクラスをリフレクションで取得する

まずはもっとも簡単な方法です。```GrandChild```クラスの```tax```変数を取得します。
  
リフレクション対象のインスタンスをまずは生成します。
  
その後、```StandardEvaluationContext```のコンストラクタにインスタンスを渡して生成します。
  
最後に```SpEL```を解釈するための```ExpressionParser```を生成し、リフレクションします。
  
```java
@Test
void test_01_01() throws Exception {
  // リフレクション対象インスタンス生成
  var target = GrandChild.builder()
          .tax(123)
          .build();

  // StandardEvaluationContextを生成する
  StandardEvaluationContext context = new StandardEvaluationContext(target);
  // ExpressionParserを生成する
  ExpressionParser expressionParser = new SpelExpressionParser();
  // インスタンスの変数名「tax」を指定して取得する
  Object tax = expressionParser.parseExpression("tax").getValue(context);

  assertThat(tax).isEqualTo(123);
}
```

# ネストするクラスをリフレクションで取得する

```Parent```クラスの変数の```Child```クラスの変数の```GrandChild```クラスの```tax```変数を取得します。```SpEL```としては```child.grandchild.tax```で表現します。
  
ネストしていない場合と処理はまったく変わりません。
  
```java
@Test
void test_01() throws Exception {
  Parent target = Parent.builder()
      .child(Child.builder()
          .grandChild(GrandChild.builder()
              .tax(123)
              .build())
          .build())
      .build();

  StandardEvaluationContext context = new StandardEvaluationContext(target);
  ExpressionParser expressionParser = new SpelExpressionParser();
  Object tax = expressionParser.parseExpression("child.grandChild.tax").getValue(context);

  assertThat(tax).isEqualTo(123);
}
```

# リストやマップの項目をリフレクションで取得する

処理は同じですので、```SpEL```のみ記載します。Java単体だとコレクション処理が面倒なので、非常に嬉しいですね。
  
## リストの項目を取得する

配列の順番を指定して取得できます。
  
- ```children[0].grandChildren[0].tax```
  
## マップの項目を取得する

マップのKeyを指定することで、対応するValueを取得することができます。
  
- ```children[0].grandChildren[0].animals['dog']```

# SpEL式をもっと使いこなす

単純なリフレクションだけでなく、```SpEL```を使用することができます。無理に使うケースは無いと考えていますが、一応覚えておきましょう。
  
次の例は、マップのサイズが1より大きい場合はtrueを返却するSpELです。
  
- ```children[0].grandChildren[0].animals.size() > 1```
  
SpELをもっと知りたい場合は、こちらのページが参考になりました。
  
[https://www.baeldung.com/spring-expression-language:embed:cite]

# ソースコード

実装コード
[https://github.com/hirotoKirimaru/cucumber-sample/blob/63230957325a487f23c6800457f5565506e9c551/src/test/java/kirimaru/biz/domain/ReflectionTests.java#L26]
  

# 終わりに

Springの機能を使用すると簡単にリフレクションをすることができます。リフレクション = SpELではないので、なかなか検索されないワードだと思いますが、便利ですのでぜひ使ってみてください。
  
---

この記事お役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

# 参考

baeldung: Spring Expression Language Guide
[https://www.baeldung.com/spring-expression-language:embed:cite]