# SVG運用ガイドライン
## SVG作成時に注意すること
SVGとして使うアイコンやロゴなどを*作るとき*に気をつけることをまとめています。Illustratorで作成することを前提に書いているので、SketchなどIllustrator以外のツールを利用する場合は適宜読みかえてください。

通常の画像と違いコード(XML)として書き出されるので、作り方によってはIllustratorの指定を無視したり、変換されることによって意図しないデザインとして出力される、ファイルサイズを増やしてしまうといった原因になります。

### Illustratorの基本設定

Illustratorで作成する場合の基本的な設定です。一部は使いやすいように変更してもかまいません。

環境設定の初期設定。

- [単位]はすべてピクセル
- [一般]の[キー入力]は0.5px
- [一般]の[プレビュー境界を使用]にチェックを入れる（［整列］パネルからでも設定可能）
- [テキスト]のトラッキングは10/1000em（`letter-spacing:0.1;`に相当）
- [ガイド・グリッド]の[グリッド]を10px、[分割数]を2px

新規ドキュメントの初期設定。

- [単位]はピクセル
- [カラーモード]はRGB
- [ラスタライズ効果]はスクリーン（72ppi）
- [プレビューモード]はピクセル
- [新規オブジェクトをピクセルグリッドに整合]のチェックを外す

アートボードの初期設定。

- [ウィンドウ]メニューの[ワークスペース]を[Web]
- [表示]メニューの[定規]の[定規を表示]のチェックを入れる（command + R）
- [表示]メニューの[定規]の[アートボード定規に変更]のチェックを入れる（command +　option + R）
- [表示]メニューの[スマートガイド]のチェックを入れる（command + U）
- [表示]メニューの[ピクセルプレビュー]のチェックを入れる（command + option + Y）
- [アートボード]パネルの[アートボードオプション]の[X:]と[Y:]をそれぞれ0pxにする（viewBoxの起点を左上にするため）
- [アピアランス]パネル（shift + F6）の［新規アートに基本アピアランスを適用］のチェックを外す
- [線]パネルは[線を中央に揃える]で作業をする（内側と外側に揃えると書き出し時に分割されるため）
- [変形]パネルの[線幅と効果を拡大縮小]のチェックを外す

### SVG作成時のルールと注意点

基本的なルール。

- 単位はピクセルで、なるべく端数を出さない（端数の桁数はデザイナーと要相談）
- アートボードの座標は0,0（左上）を起点にする
- [オブジェクト][パス][単純化]でアンカーポイントの数を最低限にする
- 非表示にしているレイヤーも書き出されてしまうため削除する（サイズ削減のため）
- レイヤー名とグループ名、SVGフィルターは適切な単語（半角英数字）に変更する（[レイヤー_1_]などと日本語で書き出されてしまう）

出力時に書き出されないこと。

- アートボードの外にある要素は書き出されない

変換されて出力されること。

- 文字データはアウトライン化する（アウトライン化をしないと`<text>`要素として書き出されるため、環境によって指定したフォントで表示されない場合がある）
- [線]以外のアピアランスはうまく書き出されない
- アピアランス、ブラシ、ブレンド、エンベロープは分割されてしまうため最低限にする（サイズ削減のため）
- メッシュグラデーションと画像ブラシ、演算、レイヤー効果はラスタライズされてしまうため最低限にする（サイズ削減のため）
- 同じ図形はシンボル登録してから配置する（`<symbol>`要素と`<use>`要素で自動的にモジュール化してくれる）
- 1回しか使わないシンボル図形は[シンボルへのリンクの解除]をする（サイズ削減のため）
- 変形や回転、拡大縮小などを最低限にする（`transform`属性として書き出されるため、その分ファイルサイズが増えてしまう）

## SVG書き出し時に注意すること
Illustratorを使った場合の書き出しに関する注意点をまとめています。レイアウト崩れの原因を取り除いたり、不要なコードを最低限にします。一部の設定は変更してもかまいません。

1. [別名で保存]をクリック（Macの場合はcommand + shift + S）
2. [ファイル形式]は[SVG(.svg)]を選択
3. [詳細オプション]をクリックしてオプションを開く
4. 以下のリストを参考にオプションを指定する

- [SVG プロファイル]はSVG1.1（最新版を指定）
- [フォント]の[文字]は[SVG]、[サブセット]は[なし（システムフォントを使用）]
- [Illustrator の編集機能を保持]をチェックしない（サイズ削減のため）
- [CSS プロパティ]はスタイル要素を指定する（サイズ削減のため）
- [未使用グラフィックスタイルを含める]をチェックしない（サイズ削減のため）
- [小数点以下の桁数]を1もしくは2にする（サイズ削減のため）
- [エンコーディング]は[UTF-8]
- [`<tspan>` エレメントの出力を制御]をチェックにする（サイズ削減のため）
- [パス上テキストに <textPath> エレメントを使用]をチェックにする（サイズ削減のため）
- [レスポンシブ]のチェックを外す（`width`と`height`属性がないとIEとAndroidで表示がおかしくなる）
- [スライスデータを含める]をチェックしない（サイズ削減のため）
- [XMP を含める]をチェックしない（サイズ削減のため）
- svgzでminifyしない（再編修正を考えて）

インラインSVGで使う場合はパスをコピーして、テキストエディタにペーストすることもできます。

Illustrator CCを使っている場合は[画像アセットとして書き出す](https://helpx.adobe.com/jp/illustrator/using/collect-assets-export-for-screens.html)ことができます。

## SVGをimg要素で表示させる
pngで表示していたような画像の代わりとしてSVGを使うのがいちばん手軽な方法です。通常の画像と同じように`<img>`要素で指定します。

```html
<img src="image.svg" alt="">
```

使用するSVGファイル内には`viewBox`属性、`width`属性と`height`属性を必ず指定します（IEとAndroid対応）。

### バグフィックス

SVGファイル内で`width`と`height`属性が指定されていない場合にIEとAndroidでアスペクト比がおかしくなることがあります。`width`と`height`属性を指定し直します。

`preserveAspectRatio="none"`をSVGファイルに指定して直す方法もあります。仕様上、viewBoxのアスペクト比と`width`と`height`属性でのアスペクト比が異なる場合、アスペクト比と維持しながらスケーリングし、中央寄せで表示されます。`preserveAspectRatio`属性はこの挙動を変更するもので、`none`と指定することでHTML側で指定したサイズでviewBoxがフィットするようになります。

### フォールバック
フォールバックは処理が冗長になるので、IE8に対応するのであればSVGは使わないようにするのがベターです。

SVG非対応ブラウザ向けのフォールバックにはHTMLでの対応とJavaScriptでの対応の2パターンがあります。

HTML側で対応する場合は`<img>`要素ではなく、`<object>`要素を使います。`<object>`要素は下記のようにフォールバックを含めた記述ができます。

```html
<object data="image.svg" type="image/svg+xml" width="100" height="100">
　<object data="fallback.png" type="image/png" width="100" height="100">
　</object>
</object>
```

JavaScript側で対応する場合は、SVG非対応ブラウザのときには.svgを.pngに置換します。

```js
if(!window.SVGSVGElement){ //SVG非対応ブラウザの判別
  $('img[src*="svg"]').attr('src', function() {
    return $(this).attr('src').replace('.svg','.png'); //拡張子を置換
  });
}
```

フォールバック画像の作成は[svg2png](https://www.npmjs.com/package/gulp-svg2png)などで自動的に生成することができます。

`<a>`要素を`<object>`要素でラップする場合、リンクがクリックできなくなります（`<img>`要素は問題ありません）。

```html
<a href="#">
  <object data="image.svg" type="image/svg+xml" width="100" height="100">
　  <object data="fallback.png" type="image/png" width="100" height="100">
　  </object>
  </object>
</a>
```

CSSでは以下のように指定するとリンクをクリックできるようになります。

```css
a {
　　display: inline-block; /* もしくは`display:block;` */
}

object {
　　pointer-events: none; /* IE10以下未対応 */
}
```

### フルードイメージ
`<img>`要素と`<object>`要素をフルードイメージにする場合はCSSで以下のように指定します。

```scss
img[src$=".svg"],
object[src$=".svg"] {
  max-width: 100%;
  width: 100%; /* IE対応 */
  height: auto;
}
```

## 背景画像としてSVGを表示させる
通常の画像と同じく`background-image`プロパティでSVGも表示させることができます。

```css
.bg {
  background-image: url("image.svg");
}
```

フォールバックをする場合はフォールバック用のpng画像を先に指定します。

```css
.bg {
  background-image: url("fallback.png");
  background-image: url("image.svg"), none;
}
```

使用するSVGファイル内にはviewBox属性、width属性とheight属性を必ず指定します（IEとAndroid対応）。

## SVGをアイコンフォントで使う
SVGのアイコンの数が多い、色を変更する必要がある、複数箇所で指定する場合はアイコンフォントとして使います。

### アイコンフォントの生成
Gulpなどで自動でアイコンフォントを生成する環境を作るのが理想的です。

- [gulp-iconfont](https://www.npmjs.com/package/gulp-iconfont)
- [gulp-iconfont-css](https://www.npmjs.com/package/gulp-iconfont-css)

Gulpを使わない場合は以下のようなツールを使って管理します。

- [icoMoon](https://icomoon.io/)
- [Font Awesome](http://fontawesome.io/)

### マークアップ
マークアップ時の注意点として、アイコンフォントを擬似要素で表示させる場合にスクリーンリーダーが`content`内の意味のない単語を読み上げてしまう問題があります。WAI-ARIAを使用して以下のような対応をします。

- `aria-hidden="true"`を指定して読み上げを防止します
- アイコンとテキストをセットにして、アイコンが表示できない場合でも意味が伝わるようにします
- テキストを非表示したうえで読み上げはできる`.sr-only`のようなクラスを使用します
- `aria-label=""`で読み上げさせるテキストを記述します

```html
<span class="p-icon p-icon--home" aria-hidden="true"></span>ホーム

<span class="p-icon p-icon--home" aria-hidden="true"></span>
<span class="u-sr-only">ホーム</span>

<span class="p-icon p-icon--home" aria-label="ホーム"></span>
```

```css
.u-sr-only {
  overflow: hidden !important;
  clip: rect(0, 0, 0, 0) !important; 
  position: absolute !important;
  width: 1px !important;
  height: 1px !important;
  margin: -1px !important;
  padding: 0 !important;
  border: 0 !important;
}
```

`aria-hidden="true"`はテキストを併記できる場合やアイコン自体の意味を伝える必要がない（装飾的な場合）に指定します。  
`aria-label=""`はアイコンだけで意味を伝える必要がある場合に指定します。例えばパンくずリストでホームアイコンだけを表示している場合などがそれに当たります。

読み上げをさせなくさせるCSSの`sperk:none;`はほとんどの視覚系ブラウザ（ChromeやIEなど）やスクリーンリーダーで対応していません。なので、`<span>`で空タグを作ってアイコンを表示、`aria-hidden="true"`や`aria-label=""`で読み上げに対応するというのが現時点でのベタープラクティスです。

HTML5の仕様を踏まえて、`<i>`要素ではなく`<span>`要素でマークアップします。理由は以下の通りです。

1. `aria-hidden="true"`で読み上げを防止するようなアイコンは装飾的だと考えられるので`<span>`要素が適切
1. `<i>`要素自体にはテキストは記述しないため、具体的な意味を持つ要素でマークアップするのは適切でないと考えられる（`before`擬似要素はHTML5の範囲に入るのか？）
1. `<i>`要素は日本語の文章ではあまり使用されるシチュエーションがない意味（「技術用語、外国語のフレーズ、または架空の人物の思考など、何らかの理由で他のテキストと離して配置して区別されるテキスト」）を持つため、アイコンで使うのは適切でないと考えられる
1. アイコンの意味を読み上げた場合（テキストとセットで読み上げた場合も含めて）、 アイコンを「何らかの理由で他のテキストと離して配置して区別されるテキスト」とするのは不自然だし、斜体として表現されるようなものでもない

## インラインSVGで表示させる
HTML内に`<svg>`要素で記述するインラインSVGはSVGの機能のすべてを使うことができます。ただし、HTMLファイル内にSVGコードを貼る必要がありファイルサイズを増やしてしまうので、`<img>`要素や`background-image`プロパティ、アイコンフォントで指定するのが適切でない場合に使うようにします。

### 不要なコードの削除
インラインSVGとして使う場合に不要なコードが書き出されます。随時削除（省略）するか、Gulpなどのツールで自動的に削除できるようにしておきます。

- `<?xml`から始まるXML宣言の`version="1.0"`（バージョンが1.0であれば省略可）
- `<?xml`から始まるXML宣言の`encoding="utf-8"`（UTF-8で書き出している場合は省略可）
- `<?xml`から始まるXML宣言の`standalone="no"`
- `<!-- Generator: Adobe Illustrator`から始まるコメント（不要）
- `<!DOCTYPE svg PUBLIC`から始める文書型宣言（SVG1.1では非推奨）
- `<svg>`要素の`xmlns:a=""`
- `<svg>`要素の`x=""`と`y=""`が両方とも0(px)の場合
- `<svg>`要素の`enable-background=""`
- `<svg>`要素の`xml:space="preserve"`
- `id`属性が日本語になっている場合は適切な半角英数字に修正

### 必須なコード
ルート要素となる`<SVG>`要素の属性。

- `xmlns="http://www.w3.org/2000/svg"`（SVG名前空間宣言）
- `xmlns:xlink="http://www.w3.org/1999/xlink"`（XLink名前空間宣言）
- `version`属性
- `width`属性と`height`属性（pxは省略可）
- `viewBox`属性

```html
<!-- 修正前 -->
<?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 16.0.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg version="1.1" id="レイヤー1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
	 x="0px" y="0px" width="600px" height="600px" viewBox="0 0 600 600" enable-background="new 0 0 600 600" xml:space="preserve">
<path fill="#040000" d="M492,300c0,17.695-1.536,32-19.232,32H127.231C109.567,332,108,317.695,108,300c0-17.696,1.567-32,19.231-32
	H472.8C490.464,268,492,282.304,492,300z"/>
</svg>
```

```html
<!-- 修正後 -->
<svg version="1.1" id="question_x5F_answer" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="600px" height="600px" viewBox="0 0 600 600">
<path fill="#040000" d="M492,300c0,17.695-1.536,32-19.232,32H127.231C109.567,332,108,317.695,108,300c0-17.696,1.567-32,19.231-32
	H472.8C490.464,268,492,282.304,492,300z"/>
</svg>
```

### アクセシビリティ

- [&lt;title&gt;](https://developer.mozilla.org/ja/docs/Web/SVG/Element/title)要素（タイトル）と[&lt;desc&gt;](https://developer.mozilla.org/ja/docs/Web/SVG/Element/desc)要素（説明）を指定する
- `<title>`要素と`<desc>`要素を読み上げさせるための`aria-labelledby="title desc"`を指定する
- 適切な`role`属性（`role="img"`や`role="link"`など）を指定する

### SVGスプライト（svgstore）

SVGは`<symbol>`要素と`<use>`要素でコンポーネント化ができます。これをSVGスプライト（svgstore）と呼びます。

`<symbol>`要素は`<svg>`要素の中に記述し、その中にコンポーネント化したい要素を置きます。`<symbol>`要素には固有のid属性を指定しておきます。`<symbol>`要素内に記述したSVGは表示されず、`<use>`要素で参照することで表示することができます。

```scss
<svg>
  <symbol id="svg-title-facebook" viewBox="0 0 110 110">
    <title id="svg-title-facebook">Facebook</title>
    <path d="M55 0C24.6 0 0 24.6 0 55s24.6 55 55 55 55-24.6 55-55S85.4 0 55 0zm14.6 54.8H60v34H45.9v-34h-6.7v-12h6.7V35c0-5.6 2.6-14.2 14.2-14.2h10.5v11.6H63c-1.2 0-3 .6-3 3.3v7.1h10.8l-1.2 12z"></path>
  </symbol>
</svg>
```

`<use>`要素を使ってSVGを呼び出します。`role`属性や`aria-labelledby`属性でアクセシブルにしている点に注意してください。

```html
<svg viewBox="0 0 110 110" role="img" aria-labelledby="svg-title-facebook">
  <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#svg-title-facebook"></use>
</svg>
```

SVGスプライトはEJSやJadeのようなテンプレートエンジンで外部ファイル化をすると管理がしやすくなります。

注意点。

- IEとEdgeは`<use>`要素による外部SVGファイルの参照ができないため、`<body>`要素直下にSVGファイルを直書きする
- `<defs>`要素よりも`<symbol>`要素の方が柔軟に指定できる（サイズの変更ができる）ためSVGスプライトは`<symbol>`要素で定義する

### CSS

SVGで指定できるCSSは限定されています。またSVG独自のプロパティも用意されています。例えば以下のようなプロパティがあります。

- stroke（線の色）
- stroke-width（線の幅）
- fill（塗りの色）
- fill-opacity（塗りの透明度）
- stroke-dasharray（線の長さ）
- stroke-dashoffset（線の開始位置）

インラインSVGには`height:auto;`が効かないので、フルードイメージにする場合は以下のCSSを指定します。

```scss
.c-embed {
  display: block;
  overflow: hidden;
  position: relative;
  height: 0;
  margin: 0;
  padding: 0;
  .c-embed__item,
  iframe,
  embed,
  object,
  video {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    width: 100% !important;
    height: 100% !important;
    border: 0;
  }
}

/**
 * アスペクト比 16:9
 */
.c-embed--16to9 {
  /* （横幅 / 高さ） * 100% */
  padding-bottom: percentage(9 / 16) !important;
}
```

### リンク
インラインSVGにリンクを貼る場合`<a>`要素で`<svg>`要素を囲んでも動作しません。`<svg>`要素内のタグを`<a>`要素で囲み、`xlink:href`属性でリンク先を指定します。

```html
<svg>
  <a xlink:href="#">
    <path></path>
  </a>
</svg>
```
