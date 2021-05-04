# Springで同一クラス別コンポーネントをDIしたい（nameGenerator）
  
Springで開発していると、別のコンポーネントだが、同一のクラス名を作りたくなることがあります。この同名クラスをDIしない場合は、特に問題は発生しません。しかし、同一クラス名をDIしてしまうと、```BeanDifinitionStoreException```が発生してしまいます。
  
これは、クラスをパッケージプライベートにして可視性を狭めても、発生してしまいます。
  
クラス名を変更する、Beanの名前を変更する等々で簡単に回避することはできますが、クラス名が長くなったり、DIする際に変更した名前を指定する必要もあり、非常に面倒です。
  
それを簡単に解決できるのが、```nameGenerator```と```FullyQualifiedAnnotationBeanNameGenerator```です。
  
当記事では、```nameGenerator```を使用して別パッケージに配備した同一クラスの別コンポーネントを簡単にDIすることを目標とします。
  
# 環境
- Java
  - 15
- SpringBoot
  - 2.4.5

# ゴール
- 別パッケージに同一のクラス名を定義しても、エラーが発生しない

対応しない場合は次のエラーが発生するので、それを回避します。
  
```log
org.springframework.beans.factory.BeanDefinitionStoreException: Failed to parse configuration class [kirimaru.DemoApplication]; nested exception is org.springframework.context.annotation.ConflictingBeanDefinitionException: Annotation-specified bean name 'conflictService' for bean class [kirimaru.biz.domain.conflict.update.ConflictService] conflicts with existing, non-compatible bean definition of same name and class [kirimaru.biz.domain.conflict.register.ConflictService]

...中略...

Caused by: org.springframework.context.annotation.ConflictingBeanDefinitionException: Annotation-specified bean name 'conflictService' for bean class [kirimaru.biz.domain.conflict.update.ConflictService] conflicts with existing, non-compatible bean definition of same name and class [kirimaru.biz.domain.conflict.register.ConflictService]

```

# 要約

- デフォルトのBean名を変更する
  - ```ComponentScan```のnameGeneratorに```FullyQualifiedAnnotationBeanNameGenerator```を指定する


# パッケージ構成

registerとupdateパッケージの下に、Bean名が被るように同一クラス名のConflictService.javaが存在させます。
  
```markdown
- Appllication.java
  - registerパッケージ
    - ConflictService.java
  - updateパッケージ
    - ConflictService.java
```
  
## ConflictServiceの中身

DIができるように、```Service```アノテーションを付けただけのファイルを作成します。DI対象に含められれば良いので、```Controller```, ```Component```でも問題ありません。
  
```java
@Service
public class ConflictService {
}
```

# 対応

```SpringBootApplication```を指定しているクラスに、```ComponentScan```を指定します。
  
次に、```ComponentScan```のnameGeneratorに```FullyQualifiedAnnotationBeanNameGenerator.class```を指定します。
  
```java
@SpringBootApplication
@ComponentScan(nameGenerator = FullyQualifiedAnnotationBeanNameGenerator.class)
public class Application {
  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }
}
```
## 説明

デフォルトのBean名はクラス名になります。今回の場合、```ConflictService```です。
  
今回の```FullyQualifiedAnnotationBeanNameGenerator```を指定することで、生成されるBean名が完全修飾クラス名になります。今回の場合、```register.ConflictService```と```update.ConflictService```の2つに分かれます。
  
Javaでは同一パッケージ、同一クラスは配備できないので、どのパターンでも対応できると思います。
  
なお、```FullyQualifiedAnnotationBeanNameGenerator```はSpringFrameworkの[5.2.3](https://docs.spring.io/spring-framework/docs/5.2.3.RELEASE/spring-framework-reference/)に導入されました。このリリースは2020年1月14日と最近のリリースです。もしSpringのアップデートできないが、同様の処理をしたいという場合には[こちらの方の記事](https://layerprogram.com/springcontrollerdi/)を参考にしてください。


## 備考

もし、SpringBootApplicationに```scanBasePackages```を指定している場合は、```ComponentScan```のbasePackagesにも同様に指定してください。
  
こちらを指定しないと、探索するパッケージが異なるために目的通りのDIができないことがあります。
  
```java
@SpringBootApplication(scanBasePackages = {"kirimaru"})
@ComponentScan(basePackages = "kirimaru", nameGenerator = FullyQualifiedAnnotationBeanNameGenerator.class)
public class Application {
  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }
}
```


# テストする方法

※ ここからは一般論ではなく、私のローカル環境で発生したものです。
  
---
  
```FullyQualifiedAnnotationBeanNameGenerator```を使用し、Bean名が変更されたので、DIできるように設定する必要があります。
  
私の場合は、こちらのアノテーションを追加すると、読み込むことができるようになりました。
  
- @AutoConfigureWebClient
  
WebMvcTestを使用していれば、次のアノテーションを含んでいるので意識して追加する必要は少ないと思います。
  
- @AutoConfigureCache
- @AutoConfigureWebMvc
- @AutoConfigureMockMvc
  
意識的に読み込まないといけないので、読み込むアノテーションの数が増えたら、新しいアノテーションを作ってもいいかもしれません。
  
---

```SpringJUnitConfig```を使用している場合は、ComponentScanを併用していると思いますので、こちらにnameGeneratorを追加すると動くようになると思われます。
  
```java
@ComponentScan(value = {"kirimaru.biz.domain"}, nameGenerator = FullyQualifiedAnnotationBeanNameGenerator.class)
static class Config {
}
```

# ソースコード

実装コード
[https://github.com/hirotoKirimaru/cucumber-sample/blob/36d3cec61c97321dadb744ce70a15b5f35e67730/src/main/java/kirimaru/DemoApplication.java]
  

# 終わりに

テストができなくて記事にできませんでしたが、teratailで回答いただけでようやくテストができるようになりました。```WebMvcTest```のアノテーションを使っていたので、十分インポートできていると思っていたのですが、まだ足りなかったとは…。
  
この辺の設定周りは苦手ですね。精進していきたいです。
  
---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)


# 経緯

```FullyQualifiedAnnotationBeanNameGenerator```を知った経緯。いろふさんありがとうございました。
  
[https://twitter.com/nainaistar/status/1375250554801115136?s=20:embed:cite]
  
[https://twitter.com/irof/status/1375253835510247430:embed:cite]

# 参考

【Java・Spring Boot】別パッケージにて、同じクラス名でDIしたいとき
[https://layerprogram.com/springcontrollerdi/:embed:cite]
  
teratail: デフォルト以外のBean名生成をしつつ、WebMvcTest等でDIを行うテストをしたい
[https://teratail.com/questions/330073:embed:cite]
  
Spring JavaDoc: FullyQualifiedAnnotationBeanNameGenerator
[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/FullyQualifiedAnnotationBeanNameGenerator.html:embed:cite]
  
ImportAutoConfigurationのパッケージ
[https://spring.pleiades.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/class-use/ImportAutoConfiguration.html:embed:cite]
  
自動構成アノテーションのテスト
[https://spring.pleiades.io/spring-boot/docs/current/reference/html/appendix-test-auto-configuration.html#test-auto-configuration:embed:cite]

