# 2021/03の第4週に読んで参考になった記事の紹介

今週も読んで参考になった記事をまとめます。
  
気が向いたら、同じ内容を自分のブログとしても書きます。Zennのスクラップみたいな感じにしようと思っています。カテゴリも「スクラップ」にしました。
  
# IntelliJ IDEAにて「Command line is too long」でテストが動かなくなった

GradleのタスクランナーをIntelliJ IDEAに変更したら、なぜか「Command line is too long」が出てテストができなくなったので、対応した時に参考にしました。  
  
## 記事
Intellij IDEAのgradleプロジェクトビルド時に Command line is too long が出る場合の対処
[https://qiita.com/alpha_pz/items/7c7a7a5bb69e4fb64eca:embed:cite]

# 同一クラス別パッケージのクラスをSpringでDIしたい

これは後でブログ記事にする予定です。
  
同一クラス別パッケージのクラスだと、Bean名が被ってしまいDIできないので何とかするための機能です。
  
いろふさんに教えてもらった```FullyQualifiedAnnotationBeanNameGenerator```で解決はできました。
  
しかし、SpringBootTestよりも軽量で良く愛用している```WebMvcTest```がいい感じにDIすることができませんでした。
  
同じようにComponentScanを使えばいいかと思っていましたが、WebMvcTestがのアノテーション内では```ComponentScan```ではなく```ComponentScan.Filter```しか使えないようなので対応策を悩み中です。
  
それを踏まえて、記事にする予定です。
## 記事

[https://twitter.com/nainaistar/status/1375250554801115136?s=20:embed:cite]
  
[https://layerprogram.com/springcontrollerdi/:embed:cite]
  
[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/FullyQualifiedAnnotationBeanNameGenerator.html:embed:cite]

# 4%ルールは今でも生きているのか

技術記事ではないけれど、参考になった記事。
  
FIREで扱われる4%ルールに関して、現代でも同様の成果が得られるかどうかというのをシミュレーションをしている記事。
  
記事読んだ感想だけ言うと、FIREしたら年金は無いので3%で生活しないと厳しそうだと思いました。
  
目標金額を上方修正するか、目標がたまった時点での年金も考慮しないと厳しそうですね。
  
## 記事
資産運用の出口戦略「4%ルール」をシミュレーションしてみた！
[https://nukunukusas.com/exit-strategy-for-asset-management:embed:cite]

# 終わりに

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

[f:id:nainaistar:20210320224900p:plain]