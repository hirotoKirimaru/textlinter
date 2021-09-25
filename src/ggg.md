# ピュアなJavaScript（ECMAScript）でQRコードを生成する（qrcodejs）

仕事で必要になったので、QRコード生成ライブラリのqrcodejsを素振りします。

Star数が10.5Kと調べたQRコード生成ライブラリの中でも多かったです。懸念点は2015年に更新が止まっていることですね。
# ゴール

QRコード生成ライブラリのqrcodejsを素振りする。


# 環境
- Windows
  - Chrome
- JavaScript
- qrcodejs

# 使い方

[公式のREADME.md](https://github.com/davidshimjs/qrcodejs)に載っているとおりです。
  
指定したDOMに対してQRコードを上書きします。
  
注意点は、基本的なことですが次のとおりです。
  
1. qrcode.min.jsを同一ディレクトリに配備する
1. scriptの読み込むタイミングを最後（後ろの行）にする
  1. DOMを先に読み込むことができない為
  
  
```html
<div id="qrcode"></div>

<script type="text/javascript" src="qrcode.min.js"></script>
<script type="text/javascript">
var qrcode = new QRCode(document.getElementById("qrcode"), {
	text: "https://github.com/hirotoKirimaru",
	width: 128,
	height: 128,
	colorDark : "#ffffff",
	colorLight : "#000000",
	correctLevel : QRCode.CorrectLevel.H
});
</script>
```
  
# 各パラメータの説明
## text 

URLのこと。

## width & height

横幅と縦幅のこと。初期値はどちらも256。

## colorDark & colorLight

colorDarkがQRコードを描画する色、colorLightがQRコードを描画する背景色です。
  
初期値はcolorDarkが```#ffffff```、colorLightが```#000000```です。


## correctLevel

正直よく分かりません。README等にも記載はありませんでした。「L:1,M:0,Q:3,H:2」の4種類があるそうです。
  
数字は特に深い意味を持っていないようで、順番が右になるにつれてQRコードが細かい模様になります。
  
ToDo:905。

# 各メソッドの説明
## clear

挙動不明。QRコードが消えると思ったら、特に動きはないようです。

```js
qrcode.clear();
```

## makeCode

描画した後にQRコードのURLを書き換えます。URLが変わるので、QRコードも書き換わります。
  
```js
qrcode.makeCode("新しいURL");
```

# 備考

依存関係がないので、```qrcode.min.js```だけ読み込めば動きます。
  
しかし、GitHubにアップロードされているminifyされたコードにはライセンスが記載されていません。ですので、ライセンスを追記する必要があります（この追記方法でよいかは不明です）
  
- [https://github.com/davidshimjs/qrcodejs/blob/master/qrcode.min.js]

他のライセンスを回避する方法もあります。Source Mapを使用するとSource Mapから元のライセンスファイルを確認できるため、minifyされたコードに直接ライセンスを追記する必要はないようです。
  
- [https://tofucodes.hatenablog.jp/entry/2014/04/16/000000:title]

# ソースコード

GitHub Pagesで公開しているので、ブラウザで挙動確認できます。

- [https://hirotokirimaru.github.io/javascript-practice/QR/:title]
  
公開しているソースコード。

- [https://github.com/hirotoKirimaru/javascript-practice/blob/main/QR/index.html:title]

# 終わりに

QRコードを表示するのは簡単に終わりました。ただ、各パラメータの意味が分からなかったので、そちらを調査することに時間がかかってしまいました。
  
また、minifyされたソースコードにライセンスが入っておらず、その対応の調べ方にも時間かかってしまいました。相談に乗っていただいた[Yoichiro Shimizu](https://twitter.com/budougumi0617)さんには感謝です。

---

この記事がお役に立ちましたら、各種SNSでのシェアや、今後も情報発信しますので[フォロー](https://twitter.com/nainaistar)よろしくお願いします。

- [技術ブログはこちら](https://nainaistar.hatenablog.com)
- [雑記ブログはこちら](https://nainaistar.hateblo.jp)

# 参考情報

- [https://github.com/davidshimjs/qrcodejs:title]
- [https://tofucodes.hatenablog.jp/entry/2014/04/16/000000:title]

