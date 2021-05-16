# JavaでLocalDateを元に月末日を求める
  
年と月のパラメータから月末日を求める[YearMonth型の記事](https://nainaistar.hatenablog.com/entry/2021/05/11/120000)を書きました。しかし、個人的に時刻を扱うときは```LocalDate```, ```LocalDateTime```のどちらかで扱いたいです。
  
今回の記事では、```LocalDate```, ```LocalDateTime```を元に月末日を求めます。
  
# 環境

- Java
    - 15

# 前提条件
なし。

# ゴール
- ```LocalDate```から月末日を求める
- ```LocalDateTime```から月末日を求める
  
# 月末日を求める

```with```メソッドと```TemporalAdjusters.lastDayOfMonth```メソッドを組み合わせます。
  
```with```メソッドはパラメータの新しいインスタンスを作成します。例えば、```withMonth```に1を渡すと、1月のインスタンスが返却されます。```with```メソッドに```TemporalAdjusters.lastDayOfMonth```メソッドを指定すると、閏年を考慮した月末日のインスタンスになります。
  
```LocalDate```も```LocalDateTime```も```with```メソッドを持った```Temporal```インターフェースを継承していますので、どちらも同様に使うことができます。
  
```java
// LocalDate
@Test
void test_01() {
  assertThat(
    LocalDate.of(2020, 1, 15).with(TemporalAdjusters.lastDayOfMonth())
  ).isEqualTo(LocalDate.of(2020, 1, 31));
}

// LocalDateTime
@Test
void test_05() {
  assertThat(
    LocalDateTime.of(2021, 2, 15, 1, 1).with(TemporalAdjusters.lastDayOfMonth())
  ).isEqualTo(LocalDateTime.of(2021, 2, 28, 1, 1));
}
```

# インスタンスが月末であることを確認する

+1日した結果、月跨ぎが発生して1日になれば月末とみてよいでしょう。
  
```java
LocalDate.of(2021, 2, 28).plusDays(1).getDayOfMonth() == 1;
```
  
---
  
こちらを紹介した理由としては、```with```メソッドを使うよりは誤差ですが早く済むからです。10億回実行して、最も早く処理をすることができました。認知できない程度の速度差なので、お好きなやり方を使用してください。
  
1. 20ミリ秒
    - +1日したら月跨ぎが発生
1. 40ミリ秒
    - LocalDateTimeでwithメソッドを使う
1. 60ミリ秒
    - LocalDateでwithメソッドを使う
1. 65ミリ秒
    - LocalDateをYearMonthにキャストする

# ソースコード

テストコード（性能確認も含む）：
[https://github.com/hirotoKirimaru/cucumber-sample/blob/9410bd0c5820cd91cf80fbd4e6e5ef3174c2d27d/src/test/java/kirimaru/biz/domain/YearMonthTests.java#L147:embed:cite]

# 終わりに

個人的に普段から使う型での月末を求めることができて良かったです。

---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)
