# BEM(MindBEMding)によるCSS設計

## BEMとは？

[BEM](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)は[Yandex](https://www.yandex.com/)という主にロシア人で構成される、検索エンジンなどを作っている企業が使っているCSSの設計方法です。[BEM Tools](https://github.com/bem/bem-tools)という、jsonでデータを管理してBEMの規則に則ったHTMLを生成する仕組みによってサイトが管理されているようです。

<a href="http://geckotang.tumblr.com/post/68662389684/bem%E3%83%84%E3%83%BC%E3%83%AB%E3%81%AB%E8%A7%A6%E3%82%8C%E3%81%A6%E3%81%BF%E3%82%8B">BEMツールに触れてみる - < /gecko ></a>

命名規則はBEMから派生した[MindBEMding](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)の方が広く使われているので、MindBEMdingをベースに考えていきます。

MindBEMdingは[csswizardryことHarry Roberts](https://github.com/csswizardry)が提唱したCSSの命名規則のことです。Harry RobertsはCSS界では世界的に有名で、コンサルタントやスピーカーとして世界中で活躍されています。

* [CSS-Guidelines](http://cssguidelin.es/)
* [csswizardry-grids](https://github.com/csswizardry/csswizardry-grids)
* [inuitcss](https://github.com/inuitcss/inuitcss)

---

これ以降の内容は忠実なBEMの方法論ではなく、BEMの考え方にいくつかのアイデアを加えたものになります。  
公式ドキュメントは日本語に翻訳された[bem-methodology-ja](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)が公開されています。

## BEMの概念
BEMはブロック（Block）、エレメント（Element）、モディファイア（Modifier）の頭文字をとったものです。  
Blockはあるパーツ（コンポーネント）の親要素です。BlockはElementと呼ばれる子孫要素を持つことができます。Modifierはバリエーションや状態を変化させるときに指定するもので、BlockかElementと同階層に指定します。

命名規則はハイフン2つやアンダースコア2つでつなげます。

* `.Block`
* `.Block__Element`
* `.Block--Modifier`
* `.Block__Element--Modifier`

詳しい説明は後述します。

### Block（親要素）
Blockはサイトを構成するパーツのことです。例えばボタン、グリッド、タブ、見出し、画像などで、簡単に言ってしまえばパーツの親要素です。BEMではBlockを起点として考え、サイトはBlockを組み合わせることで構成していきます。

例えばブロック要素で余白を持ったシンプルなBlockの`.box`を作りました。

```scss
.box {
  display: block;
  padding: 1em;
}
```

ボタンもBlockとして考えることができます。ここではボタンのベースになるスタイルだけを持っています。

```scss
.button {
  display: inline-block;
  margin: 0;
  padding: 0.75em;
  border: none;
  border-radius: 3px;
  color: inherit;
  font-family: inherit;
  font-size: inherit;
  line-height: 1;
  text-align: center;
  text-decoration: none;
  background: transparent;
  cursor: pointer;
  appearance: none;
  &:hover,
  &:active,
  &:focus {
    text-decoration: none;
  }
  &:disabled,
  &.is-disabled {
    opacity: 0.5;
    pointer-events: none;
  }
}
```

2つのBlock（`.box`と`.button`）を組み合わせて1つのパーツを作ることもできます。

```html
<div class="box">
  <a href="#" class="button"></a>
</div>
```

### Element（子孫要素）
ElementはBlockを構成するパーツの1つ1つのことです。例えばグリッドを構成するカラム、タブのボタンやコンテンツ部分、見出しや画像などです。

BEMではBlock（親要素）から見て下の階層にいる要素はすべてElement（子孫要素）として扱います。なので、Blockは1つしかありませんし、Elementは0個以上から構成されます。

クラス名は`.block__element`のようにBlockの名前を引き継いでアンダースコア2つでつなぎます。

例えばグリッドレイアウトであれば、`.grid`がBlockになり、`.grid__item`がElementになります。

```scss
.grid {
  display: block;
  margin: 0;
  padding: 0;
  font-size: 0;
  list-style-type: none;
}

.grid__item {
  display: inline-block;
  width: 100%;
  font-size: medium;
  font-size: 1rem;
  vertical-align: top;
}
```

先ほどの`.box`というBlockのElementとしてボタンを定義しました。Block特有のスタイルがある場合は`Block__Element`として定義します。

```html
<!-- OK -->
<div class="box">
  <div class="box__button">
    <a href="#" class="box__button-item">button</a>
  </div>
</div>
```

ボタンのベーススタイルを持っている`.button`というBlockをマルチクラスで指定しました。`.box__button-item`は色やサイズなどの装飾的な指定をするだけでよくなりました。

```html
<!-- Good -->
<div class="box">
  <div class="box__button">
    <a href="#" class="button box__button-item">button</a>
  </div>
</div>
```

### Modifier（バリエーション）
ModifierはBlockとElementのバリエーションや状態の変化をつくるときに指定します。

クラス名は`.block__element--modifier`か`.block--modifier`のようにBlockとElementの名前を引き継ぎ、ハイフン2つでつなぎます。Modifier名は以下のようにつけるといいでしょう。

* `Block--small` - あるBlockの余白やサイズなどが小さいバージョン
* `Block--pad-small` - あるBlockの`padding`が小さいバージョン
* `Block--item-small` - Block内の`item`というElementが小さいバージョン

例えば`.box`というBlockに背景色をつけたパターンが出てきたとします。

```scss
.box {
  display: block;
  padding: 1em;
}
```

Block自体ではなく、Blockに背景色を持たせるためのModifierを作ります。

```scss
.box--highlight {
  background-color: gray;
}
```

これで、元のBlockのスタイルを変更することなく、背景色をつけたパターンを作ることができました。HTMLはこのようにマルチクラスで指定することになります。

```html
<div class="box box--heightlight">
</div>
```

基本的にリセットしていくよりも、足していくようにすると管理しやすくなります。

```scss
/* NG */
.box {
  display: block;
  padding: 1em;
  background-color: gray;
}

.box--no-highlight {
  background-color: transparent;
}
```

このパターンはOOCSSの原則のひとつである「構造と見た目の分離」と同じことをしています。つまり、BEMとOOCSSは組み合わせることもできます。

## BEMのメリット

### 採用するメリットと導入コスト
BEMの概念や命名規則の方法など、導入するためのコストは必要です。ですが、BEMのルールは基本的に案件ごとに変わることはないので、導入コストは最初の案件だけに限られると思います。

また、逆にルールがあるということは、自分で考える必要がないということです。自分で考えるはずだった時間を、他の重要なことに使えます。後述する命名規則の明確さもメリットのひとつです。

基本的なルールが決まっていること、ルールを共通言語にすることでコミュニケーションを円滑にできること。BEMを採用するメリットはこのあたりにあるのかなと思います。

### 命名規則の明確さ
BEMを使うメリットのひとつにクラス名からクラスが持っている役割が伝わりやすいことがあります。

クラス名の単語をハイフンやアンダースコアで区切ると、

```scss
.widget-list {}
.widget_list {}
```

のようになり、ウィジェットリストなのか、ウィジェットの中にあるリストなのか、人によって解釈が変わってしまう恐れがあります。

BEMであれば、ウィジェットリストなら

```scss
.widget-list {}
```

になりますし、ウィジェットの中にあるリストなら、

```scss
.widget__list {}
```

のようにクラス名だけで判断することができます。

---

BootstrapやFoundationがハイフン区切りでも問題がないのは、

* フレームワークとして完成した状態で使うことを前提としているのでブレない
* ドキュメントが充実している

ことがあげられます。実際の案件ではじめからこの条件を満たすことはまずできません。いちから組んでいってもブレにくいルールが必要になります。

#### 命名規則のアレンジ
BEMのクラス名はBlockの名前を引き継いだりハイフン2つやアンダースコア2つで区切ること、マルチクラス（複数のクラスを指定すること）を前提していることから、class属性の指定が長くなりがちです。

クラス名をなるべく短くするために、命名規則を案件ごとに微調整することがよくあります。命名規則には3つのパターンがあります。

1つ目、通常は`block-name`のように単語をハイフンで区切ります。

```scss
.block-name {}
.block-name__element {}
.block-name--modifier {}
```

2つ目、単語をローワーキャメルケースにした場合。

```scss
.blockName {}
.blockName__element {}
.blockName--modifier {}
```

3つ目、単語をローワーキャメルケースにして、区切り文字を1つにした場合。

```scss
.blockName {}
.blockName_element {}
.blockName-modifier {}
```

Elementが複数の単語からなっている場合はより短くできます。

```scss
.block-name__element-name {}
.blockName__elementName {} // 2文字少ない
.blockName_elementName {} // 3文字少ない
```

## BEMの基本ルール

### クラスセレクタ単体に指定する
BEMではIDセレクタや要素セレクタは使用せず、クラスセレクタで指定します。

```scss
/* Good */
.block-name {}
.block-name__element {}
.block-name--modifier {}
```

クラスセレクタ単体にスタイルを指定していくので、詳細度は0,0,1,0のままになります。`!important`を使うこともほとんどありません。

IDセレクタを使わないのはページ内で使い回せるようにするためです。

```scss
/* NG */
#block {} // ページ内で1つしか使えない
#block__element {} // Elementが2つ以上ある場合に使えない
#block--modifier {}
```

要素を使わないのは影響範囲を限定させるためです。HTMLタグを限定させないことで使い回しをしやすくできるメリットもあります。

```scss
/* NG */
.block > p {} // Block直下の`<p>`すべてに適応されてしまう
```	

案件によってルールを緩くした方が運用しやすいこともあります。どこまで厳密にするかは案件の規模、制作者の人数やレベル、運用をするひとのレベルなどから決めるといいでしょう。

### ElementとModiferを単体で使用しない
「ここは`.box__element`と同じデザインだから、`.box__element`だけ持ってこよう。」と考えてはいけません。ElementとModifierは親となるBlockの中でだけ指定することができます。

```html
<!-- NG -->
<div class="foo">
  <div class="box__element"></div>
  <div class="box__element"></div>
</div>
```

`.box__element`が汎用的に使えるのであれば、`.box__element`ではなく、別のBlockとして定義しましょう。今は`.box__element`は汎用的に使えるかもしれませんが、スタイルが変更される可能性もあります。

## BEMのよくある問題

### Element__Element
BEMでは必然的にElementの数が多くなります。Blockから見て孫要素ができることもあります。このときに`.block__element__element`のようなクラス名にしているケースがあります。

```html
<!-- NG -->
<div class="box">
  <div class="box__element">
    <div class="box__element__element">
    </div>
  </div>
</div>
```
BEMはHTMLの構造をElementによって表す必要はありません。`child-element`のような名前にしても伝わりますし、より具体的です。

```html
<!-- Good -->
<div class="box">
  <div class="box__element">
    <div class="box__child-element">
    </div>
  </div>
</div>
```

例えば多階層のナビゲーションを`element__element`でマークアップした場合、極端な例かもしれませんがこのように階層が深くなるごとに名前が長くなってしまいます。

```html
<!-- NG -->
<ul class="nav">
  <li class="nav__item">
    <ul class="nav__item__items">
      <li class="nav__item__items__item"><a href="#" class="nav__item__items__link"></a></li>
      <li class="nav__items__items__item"><a href="#" class="nav__item__items__link"></a></li>
    </ul>
  </li>

  <li class="nav__item">
    <ul class="nav__item__items">
      <li class="nav__item__items__item"><a href="#" class="nav__item__items__link"></a></li>
      <li class="nav__items__items__item"><a href="#" class="nav__item__items__link"></a></li>
    </ul>
  </li>

</ul>
```

僕なら以下のようにマークアップします。`.nav__items`の子要素は`.nav__child-items`のように`child`をつけています。`.nav__child-items`の子要素は`.nav__child-item`のように`s`を抜いて名前をつけています。


```html
<!-- Good -->
<ul class="nav">
  <li class="nav__items">
    <ul class="nav__child-items">
      <li class="nav__child-item"><a href="#" class="nav__child-link"></a></li>
      <li class="nav__child-item"><a href="#" class="nav__child-link"></a></li>
    </ul>
  </li>

  <li class="nav__items">
    <ul class="nav__child-items">
      <li class="nav__child-item"><a href="#" class="nav__child-link"></a></li>
      <li class="nav__child-item"><a href="#" class="nav__child-link"></a></li>
    </ul>
  </li>

</ul>
```

階層が深くなっても2つの単語で収めるようにできればコードが読みやすくなると思います。Blockという名前のスコープがあるので、意味が理解しやすい範囲でBlock内で重複しなければ十分です。

なるべく階層が深くならないようにBlockを組み合わせて使うようにすると、名前の付け方はシンプルになると思います。

例えば、上記のナビゲーションをラップする`<nav>`要素には`.g-nav`のようなレイアウトを担当するクラス、スマホでハンバーガーボタンがあるなら`.nav-toggle`のようにBlockを分けて、組み合わせることで名前やコードが複雑になるのを避けることができます。

```html
<!-- Good -->
<nav class="g-nav">
  <div class="g-nav__navigation">
    <ul class="nav">
      <li class="nav__items">
        <ul class="nav__child-items"></ul>
      </li>
    </ul>
  </div>

  <div class="g-nav__toggle">
    <div class="nav-toggle">
      <span class="nav-toggle__item"></span>
      <span class="nav-toggle__item"></span>
      <span class="nav-toggle__item"></span>
    </div>
  </div>
</nav>
```

### Block__Element--Modifier
例えばElementの`padding`を変更するModifierを作りたい場合、すべてのElementにModifierを指定するのは冗長で指定忘れが出てしまったりするかもしれません。

```html
<!-- NG -->
<div class="box">
  <div class="box__element box__element--pad-large"></div>
  <div class="box__element box__element--pad-large"></div>
</div>
```

```scss
.box__element {
  padding: 1em;
}

.box__element--pad-large {
  padding: 2em;
}
```

このパターンにはBlock--Modiferを起点にしてスタイルを指定する方が運用しやすくなります。直下の要素として指定したりして、影響範囲をできるだけ抑えるようにしておきます。

```html
<!-- Good -->
<div class="box box--pad-large">
  <div class="box__element"></div>
  <div class="box__element"></div>
</div>
```

```scss
.box__element {
  padding: 1em;
}

.box--pad-large > .box__element {
  padding: 2em;
}
```

### Blockを名前空間にする
BEMのルールではElementとModifierはBlockのなかでだけ存在することができるとあります。以下のHTMLはBlockが存在しないためルール違反になります。

```html
<!-- NG -->
<div class="box--size-large">
  <div class="box__element"></div>
  <div class="box__element"></div>
</div>
```

セレクタの結合子にBlockを常に指定することでスタイルを適応できないようにすることもできます。

```scss
.block {}
.block > .block__element {}
.block.block--modifier {}
```

```html
<!-- Blockがないので、スタイルは適応されない。 -->
<div class="box--size-large">
  <div class="box__element"></div>
  <div class="box__element"></div>
</div>
```

ただし、ここまで厳密にする必要はないと考えています。  
セレクタを複雑にして厳密さを求めるより、BEMの基本的なルールを守ってもらえるようにコミュニケーションをとる方が必要ではないかなと考えています。


### Sassの&（アンパサンド）
Sassでは入れ子と`&`（アンパサンド）を使ってBEMをショートカットで書くことができますが、BEMの命名規則の明確さを損なってしまうので、使わない方がベターだと思います。

```scss
/* Bad */
.foo {
  color: white;
  &--bar {
    color: red;
  }
  & .bar { // このセレクタは`.foo .bar`なのか？`.foo--bar .bar`?なのか？
    color: red;
  }
}
```

```scss
/* Good */
.foo {
  color: white;
}

.foo--bar {
  color: red;
}

.foo .bar {
  color: red;
}
```

検索しにくくなる（あるいはできなくなる）ことも使わない理由のひとつです。

```scss
/* Bad */
.foo {
  color: white;
  &--bar { // `.foo--bar`を検索しても引っかからない
    color: red;
  }
  & .bar { // `.foo .bar`を検索しても引っかからない
    color: red;
  }
}
```

### Blockの粒度
Blockの大きさ（粒度）をどのように考えるかは、2つの基準があると考えています。

#### コンテナとコンテンツの分離（OOCSS）

OOCSSの「コンテナとコンテンツの分離」をできるようにしておきます。

コンテナとは`.container`や`.wrapper`のような横幅を制限したり、グリッドレイアウトのような大枠のレイアウトを指定するものです。  
コンテンツとは本文やリスト、画像やテーブルなどのパーツのことです。

このデザインはどんな構造（レイアウト）が必要なのか？その中にはどんなパーツがあるのかという順番で考えていくと作りやすく、使いまわしがしやすくなると思います。

#### どこまで汎用性を持たせるか
BlockにはいくつでもModifierを足していけますが、増えるごとに複雑さが増します。自分で定義したBlockならまだしも、誰かが定義したBlockであればModifierの組み合わせを把握しきれなくなる恐れがあります。

指定が複雑になってきてしまったら、別のBlockとして定義できないか考えましょう。「このパターンではこのBlockに2つModifierを指定してください。」よりも「このパターンではこのBlockを指定してください。」の方がシンプルで伝わりやすく、運用がしやすいと思います。

### divが足りなくなってしまった
デザインの変更があり、コーディングをやり直す必要があるけどBlockを`<div>`でラップしないと難しいということもあります。そのときは、

1. できればHTMLも含めて修正する
1. Blockを組み合わせて対応できないか考える
1. `.block-wrapper`や`.block-inner`のようなクラスを作る

という順番で考えています。

```html
<div class="block-wrapper"> 
  <div class="block">
    <div class="block__item"></div>
  </div>
</div>
```

`.block-wrapper`のようなクラスはBEMにはありませんが、Blockをラップするというイレギュラーに対応するためにこのようなクラスを作ることがあります。



## BEM以外の手法との組み合わせ
BEMとBEM以外の設計手法を組み合わせることに問題はありません。例えば、

* OOCSS
* SMACSS
* 汎用クラス（ユーティリティクラス）

などです。FLOCSSがいい例ですが、BEMだけですべてを解決することは難しいです。BEM以外の設計手法を柔軟に取り入れていくのがベターだと思います。

### 汎用クラス
BEMで汎用クラスを適切に書くことはできないと思います。もし書けたとしても冗長になるでしょう。

汎用クラスはBEMとは別のルールとして適応させます。例えば`.display-none`や`.dn`のようなシングルクラスで適応できるようにします。

### SMACSS
BEMにはバリエーションや状態の変化としてModifierがありますが、状態の変化はSMACSSのStateに任せてしまうのもありです。

```scss
.tab {}
.tab__trigger {}
.tab__content {}
.tab__trigger.is-active {}
.tab__content.is-open {}
```

Modifierはパターン違い、Stateは一時的な状態変化と分けて考えると把握しやすくなります。

## リンク集
公式ドキュメント

* <a href="https://github.com/juno/bem-methodology-ja/blob/master/definitions.md">bem-methodology-ja/definitions.md at master · juno/bem-methodology-ja</a>

BEM

* <a href="https://app.codegrid.net/entry/bem-basic-1">基本概念とルール | CodeGrid</a>
* <a href="http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/">MindBEMding – getting your head ’round BEM syntax – CSS Wizardry – CSS, OOCSS, front-end architecture, performance and more, by Harry Roberts</a>
* <a href="https://github.com/inuitcss/inuitcss">inuitcss/inuitcss: Extensible, scalable, Sass-based, OOCSS framework for large and long-lasting UI projects.</a>
* <a href="http://blog.kubosho.com/entry/extend-base-ruleset-vs-define-base-class-and-modifier-class-in-html">MindBEMding の Modifier は元のクラス名と一緒に指定するべきか否か - I'm kubosho_</a>

OOCSS

* <a href="https://app.codegrid.net/entry/oocss-1">OOCSSの基本 | CodeGrid</a>

EDJO

* <a href="https://hail2u.net/blog/webdesign/every-declaration-just-once-example.html">Every Declaration Just Onceの例 - Weblog - Hail2u.net</a>
* <a href="https://hail2u.net/blog/webdesign/oocss-drawbacks-and-gifts-of-every-declaration-just-once.html">OOCSSの欠点とEvery Declaration Just Onceのもたらすもの - Weblog - Hail2u.net</a>
* <a href="http://morishitter.hatenablog.com/entry/2015/01/16/005343">OOCSSとEDJO、もしくはHTMLとCSSにおける命名 - morishitter blog</a>
* <a href="http://terkel.github.io/cargo-cult-css/">カーゴ・カルト CSS</a>

アイデア

* <a href="http://cssguidelin.es/#naming-conventions">CSS Guidelines (2.2.5) – High-level advice and guidelines for writing sane, manageable, scalable CSS</a>
* <a href="http://sass-guidelin.es/#architecture">Sass Guidelines</a>
* <a href="https://github.com/hiloki/flocss">hiloki/flocss: CSS organization methodology.</a>
* <a href="https://smacss.com/ja">Ja - Scalable and Modular Architecture for CSS</a>
* <a href="http://pepabo.github.io/docs/frontend/standard/css-framework/">CSS フレームワークとの付き合い方 - ペパボのフロントエンドスタンダード</a>
* <a href="http://pepabo.github.io/docs/frontend/standard/css-architecture/">CSS 設計の長い夢 - ペパボのフロントエンドスタンダード</a>
* <a href="http://melty.koume.in/css-architecture-for-large-scale-web-site/">不特定多数の人が更新する大規模サイトに必要な CSS 設計の思想</a>
* <a href="http://blog.kubosho.com/entry/2014/12/09/valhalla-gate-css-architecture">神獄のヴァルハラゲートのCSS設計 - I'm kubosho_</a>
* <a href="http://geckotang.tumblr.com/post/104065014321/%E4%BB%8A%E5%B9%B4%E3%81%AE%E3%83%8D%E3%83%BC%E3%83%9F%E3%83%B3%E3%82%B0%E3%83%AB%E3%83%BC%E3%83%AB-css%E8%A8%AD%E8%A8%88">今年のネーミングルール #CSS設計 - < /gecko ></a>
* <a href="http://geckotang.tumblr.com/post/69554882865/bem-words">BEMで命名する時に役に立ちそうな単語 - < /gecko ></a>
* <a href="http://qiita.com/hiloki@github/items/4fa99b8755a22878449e">少し細かいBEMい話 - Qiita</a>
* <a href="http://liginc.co.jp/web/html-css/html/173153">【CSS設計】拡張や修正に強いコンポーネントをマークアップする6つのテクニック | 株式会社LIG</a>