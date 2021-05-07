# Javaで最近使っているParameterizedTest【2021年版】

※ JUnit5のアップデートを知るための記事ではありません。私の知識アップデートをお伝えする記事になっています。
  
---

以前、ParameterizedTestに関する記事を書きました。その時は、テストコードの表現力を意識して、```ArgumentsSource```を使ったほうが良いという結論を出していました。
  
しかし、もっとよりよい表現ができるテストが書けることを知ったので、それをアップデートした記事となります。
  


# 環境
- Java
    - 15

# ParameterizedTestのテスト表現方法を変更する

```ParameterizedTest```にはname変数があります。デフォルトでは、「[{index}]{argumentsWithNames}」となります。
  
ToDo：画像301.
  
name変数に値を設定することで、テストを表現できます。
  
```java
@ParameterizedTest(name = "契約日が{0}, 解約日が{1}の時、{2}")
```
  
ToDo：画像302.

# staticが必要なクラスでstaticを使わずに済むようにする

デフォルトではテストのインスタンスはメソッドごとに作成します。それは、並列に実行できるメリットにもなります。（※違ったら申し訳ないです）
  
しかし、



```BeforeAll```や```AfterAll```、
  
```java
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
```







# ソースコード

テストコード：
[https://github.com/hirotoKirimaru/cucumber-sample/blob/289f2fc4ed814e0af3feb8e27f2671e884e1aef9/src/test/java/kirimaru/biz/domain/ContractTest.java#L18:embed:cite]

# 終わりに

  
---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

# 類似
パラメータだけ変えて中身は同じテストをしたい(ParameterizedTest)
[https://nainaistar.hatenablog.com/entry/2019/08/08/000603:embed:cite]
  
StackOverflow: What use is @TestInstance annotation in JUnit 5?
[https://stackoverflow.com/questions/52551718/what-use-is-testinstance-annotation-in-junit-5:embed:cite]