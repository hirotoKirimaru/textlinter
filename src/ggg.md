# IntelliJ IDEAでmavenの依存関係をbuild.gradleにコピペするとGradle用に自動変換される

超小ネタ。Mavenでライブラリが書いてあるから、それをGradle用に書き直したいとコピペしていた時に気づきました。
  
なお、IntelliJ IDEAでこの機能名を探したのですが、見つかりませんでした。
  
# 環境
- Intellij IDEA Ultimate
    - 2021.1.2

# 機能

[Spring Boot](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot/2.5.2)を例に出します。Mavenだと次の書き方になります。
  
```XML
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot</artifactId>
    <version>2.5.2</version>
</dependency>
```
  
これをコピーして、build.gradleにペーストするとGradle用に自動変換されます。
  
```groovy
implementation 'org.springframework.boot:spring-boot:2.5.2' 
```
  
scopeをtestに変更すると、Gradle側も```testImplementation```になります。
  
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot</artifactId>
    <version>2.5.2</version>
    <scope>test</scope>
</dependency>
```
  
```groovy
testImplementation 'org.springframework.boot:spring-boot:2.5.2'
```
  
なお、```groupId```、```artifactId```、```version```が全て揃っていることが条件のようです。```version```が存在しない場合は、XMLのままペーストされました。
  
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot</artifactId>
</dependency>
```
  
```groovy
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot</artifactId>
</dependency>
```

# 備考

MavenからGradleへの変換が用意されていたので、逆にGradleからMavenに変換してくれるかと思いましたが、それはありませんでした。
  
MavenからGradleへの一方通行のようですね。
  
# 終わりに

超地味なネタですが、見つけたのでブログネタにしました。実はEclipseもそういうことやってくれるんですかね…。
  
あまり積極的に使うような機能ではありませんが、一々```groupId```と```artifactId```と```version```をそれぞれ別にコピペして文字列結合していた人にとっては朗報だと思います。
  
---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

- https://qiita.com/yoshida-hi/items/055c36e015f0bf8fe4f6

