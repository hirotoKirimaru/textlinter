# CSSで文字の上に強調のドットを付与したい

CSSで文字の上に強調のドットを付与したかったので、それを設定した時のメモ。
  
# ゴール

- 特定の文字の上にのみドットを付与して強調する
  
ToDo: 画像901。

# 環境
- Windows
  - Chrome
- iPhone
  - Chrome
# 対応

次のCSSを設定すると、文字の上にドットを付与できます。
  
```css
padding-top: .7em; // 文字とドットの距離
background-position: top left -2px; // ドットの位置微調整
background-repeat: repeat-x; // ドットをどの方向に設定するか
background-size: 1em 1em; // 繰り返す頻度
background-image: radial-gradient(orange 20%, transparent 30%); // ドットの大きさと色
```
  
なお、ドットを繰り返す回数を指定することはできないようです。親の要素に```display: flex```を指定することで、必要な大きさのブロック要素になりますので、特定の文字の上にのみドットを表示できます。
  
また、ドットがある分、ドットがない文字との高さの位置が合わなくなるので、```align-items: flex-end;```を指定するとよいです。
  
---

ドットの大きさを％ではなく、文字の大きさで設定できそうでしたが…。
  
```css
background-image: radial-gradient(.15em .15em at center center, orange, orange 100%, transparent);
```
  
なぜか、iPhoneの実機で確認すると、文字のドットが棒になってしまいました。原因は不明ですが、文字のサイズでは指定はしない方がよいでしょう。
  
PC: 

ToDo:901。

iPhone：902。
# ソースコード

```html
<div class="flex">
  <div class="">
    ライオン
  </div>
  <div class="dot">
    いません
  </div>
</div>
```
  
```css
.flex{
  display: flex;
  align-items: flex-end;
}

.dot {
  padding-top: .7em;
  background-position: top left -2px;
  background-repeat: repeat-x;
  background-size: 1em 1em;
  background-image: radial-gradient(orange 20%, transparent 30%);
}
```

- [CodePen](https://codepen.io/nainaistar/pen/rNwMMXe)

# 終わりに

指摘されるまでドットの挙動がChromeとiPhoneで異なることに気付いていませんでした。Safariが原因かと思ったのですが…。
  
こういう、機種依存の問題まで含めると、本当にCSSは沼ですね…。

---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

# 参考情報

- [https://www.webopixel.net/html-css/1494.html:title]
- [https://www.esz.co.jp/blog/2766.html:title]
- [https://developer.mozilla.org/ja/docs/Web/CSS/background-repeat:title]

