# Rubyで複数行文字列を使う

小ネタ。
  
Rubyで複数行文字列（マルチラインテキスト）を定義できる方法は調べればできるものの、複数のやり方が見つかったので違いを残すための記事です。

# 環境
- Ruby
    - 3.0.2p107
- Rails
    - 6.0.3.7
- RSpec

# 要約

複数行文字列を定義したければ、```<<~EOS EOS```を使用しましょう。
  
```rb
<<~EOS
  aiueo xyz 
  12345
EOS
```

# 検証

Rubyでは複数行文字列は```%| |```（```%{ }```）と、```<<~EOS EOS```を使用して定義する方法がありました。
    
```%| |```で定義した場合、厳密に```%| |```の内部の文字列を取得します。改行コードやインデントも厳密に取得します。
  
```<<~EOS EOS```で定義した場合、```<<~EOS EOS```内部で定義した最初に出現する文字から取得し、その文字列に合わせたインデントで後続の文字列も取得します。
  
具体的にはコードを見てもらうと分かりますが、次のコードが等価となります。
  
```rb
    actual =
      %|aiueo xyz
12345
|

    expected = <<~EOS
      aiueo xyz
      12345
    EOS

    expect(actual).to eq expected
    # "aiueo xyz\n12345\n"
```

```%| | ```を```<<~EOS EOS```と似たような感じで定義したときの文字列に違いが出てくるので、分かりやすいです。

```rb
    actual = %|
      aiueo xyz
      12345
    |

    expected = <<~EOS
      aiueo xyz
      12345
    EOS

    expect(actual).not_to eq expected
    # "\n      aiueo xyz\n      12345\n    "
    # "aiueo xyz\n12345\n"
```  

# ソースコード

- [https://github.com/hirotoKirimaru/ror-practice/blob/99a500b794376483b6d3d4ebad306f2b36ff86a9/sandbox/spec/models/multiline_text_spec.rb:title]


# 終わりに

コードリーディングしている際に、```%| |```で定義している複数行文字列を見かけて気になって検証してみました。正直、```String#%```にブロック文を渡しているんだと思いますが、普通に調べても全く出てきませんでした。
  
なお、調べたうえで普通に```<<~EOS EOS```使った方がいいということが分かったので、それも含めて収穫です。
