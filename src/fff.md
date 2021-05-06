# Javaでリフレクションを素振りする
  
基本的にはコードが読みづらくなるので、リフレクションは使ってはいけません。しかし、非推奨であることと、使えないことは違います。
  
当記事では、Javaでリフレクションを使ってprivateメソッドを呼び出せることを目標とします。

# 環境
- Java
  - 15
- Lombok


# ゴール
- 別のクラスからリフレクションを使ってprivateメソッドを呼び出す

# ユースケース
- テストクラスでprivateメソッドを実行したい
  - そもそも、テストしたいメソッドはパッケージプライベートか、protectedにしましょう。
- 巨大なネストするクラスから目的の変数を取得したい

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
}
```
  
```java
@Value
@Builder
public class Child {
  private GrandChild grandChild;
}
```
  
```java
@Value
@Builder
public class GrandChild {
  private String description;
  
  // 引数無し
  private String getDescription() {
    return description;
  }

  // 引数あり
  private int computeMultiple(int num1, int num2) {
    return num1 * num2;
  }

  // 可変長引数の引数あり
  private int computeMultipleArray(int... nums) {
    int tmp = 1;
    for (int num : nums) {
      tmp *= num;
    }
    return tmp;
  }
```


# 引数無しのprivateメソッドを実行する

まずはリフレクション対象のインスタンスを生成します。
  
次にインスタンスから、リフレクションしたいprivateメソッドを取得します。一度```getClass```でクラスを取得してから、```getDeclaredMethod```でメソッドを取得できます。
  
次に、```setAccessible```で可視性を変更します。実行しない場合は、```java.lang.IllegalAccessException```が発生し、可視性を変更する必要があるとメッセージが出力されます。
  
最後に、取得したprivateメソッドの```invoke```にインスタンスをパラメータ渡すと実行することができます。戻り値がある場合は、Object型で返却されるので必要に応じてキャストしてください。
  
```java
@Test
void test_01() throws Exception {
  // リフレクション対象のインスタンス生成
  GrandChild target = GrandChild.builder()
      .description("説明")
      .build();

  // メソッドを取得する
  Method method = target.getClass().getDeclaredMethod("getDescription");
  // privateメソッドにアクセスできるようにする
  method.setAccessible(true);
  // インスタンスからメソッドを実行して値を取得する
  String result = (String) method.invoke(target);
  assertThat(result).isEqualTo("説明");
}
```

# 引数ありのprivateメソッドを実行する

引数無しのprivateメソッドの実行との比較差分は2つです。
  
- メソッドを指定するときにパラメータの数と型を指定すること
- invokeメソッドに実際のパラメータを渡すこと
  
Javaには同名クラスでパラメータが異なるオーバーロードがありますので、面倒くさがらずに設定する必要があります。
  
```java
@Test
void test_03() throws Exception {
  // リフレクション対象のインスタンス生成
  GrandChild target = GrandChild.builder()
      .build();

  // メソッドを取得する
  Method method = target.getClass()
    .getDeclaredMethod("computeMultiple", int.class, int.class);
  // privateメソッドにアクセスできるようにする
  method.setAccessible(true);
  // インスタンスからメソッドを実行して値を取得する
  int result = (int) method.invoke(target, 100, 20);
  assertThat(result).isEqualTo(2000);
}
```
  
なお、可変長引数の場合は、内部的には配列になっていますので、配列を渡しましょう。リフレクションを使わない普通のメソッドであれば可変長でパラメータを渡せますが、リフレクション時には明示的に配列にしなければいけない点がポイントです。
  
```java
@Test
void test_04() throws Exception {
  GrandChild target = GrandChild.builder()
      .build();

  Method method = target.getClass()
    .getDeclaredMethod("computeMultipleArray", int[].class);
  method.setAccessible(true);
  int result = (int) method.invoke(target, new int[]{100, 20, 3, 4});
  // この渡し方はできない
// int result = (int) method.invoke(target, 100, 20, 3, 4);
  assertThat(result).isEqualTo(24000);
}
```

# 巨大なネストするクラスから目的の変数を取得したい

この項目の新規性はありません。ユースケースを意識したものとなります。
  
例えば、「メールに必要な文言を巨大なネストするクラスから取得したい。しかも、簡単に書き換えられるようにマスタで管理したい。」という要件があったとします。
  
この要件を満たすために、マスタでは```getChild.getGrandChild.getTax```という文言だけを管理しておいて、その値を元にリフレクションをするようにします。
  
今回の例では、ParentクラスからChildクラスを取得し、ChildクラスからGrandChildクラスを取得し、GrandChildクラスのtax変数を取得します。
  
```java
@Test
void test_02() throws Exception {
  Parent target = Parent.builder()
      .child(Child.builder()
          .grandChild(GrandChild.builder()
              .tax(123)
              .build())
          .build())
      .build();

  Object child = target.getClass().getDeclaredMethod("getChild").invoke(target);
  Object grandChild = child.getClass().getDeclaredMethod("getGrandChild").invoke(child);
  Object tax = grandChild.getClass().getDeclaredMethod("getTax").invoke(grandChild);

  assertThat(tax).isEqualTo(123);
}
```

# ソースコード

実装コード
[https://github.com/hirotoKirimaru/cucumber-sample/blob/f0c29859acb37d5f94561d606db7670a1d61918a/src/test/java/kirimaru/biz/domain/ReflectionTests.java#L81a]
  

# 終わりに

リフレクションは基本使いたくありませんが、たまに使いたくなる時があります。
  
実際は、Springの機能でもうちょっと簡単にリフレクションで値を取得できたりするのですが、それはまた別の記事にてご紹介させてください。
  
---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

# 参考

何かの時にスッと使える力技 - Reflection 編
[https://qiita.com/KeithYokoma/items/9e692808095acf560bc9:embed:cite]
  
リフレクション
[https://www.ne.jp/asahi/hishidama/home/tech/java/reflection.html:embed:cite]
