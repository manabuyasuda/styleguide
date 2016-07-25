# BEM(MindBEMding)によるCSS設計

[BEM](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)は[Yandex](https://www.yandex.com/)という主にロシア人で構成される、検索エンジンなどを作っている企業が使っているCSSの設計方法です。[BEM Tools](https://github.com/bem/bem-tools)という、jsonでデータを管理してBEMの規則に則ったHTMLを生成する仕組みによってサイトが管理されているようです。

<a href="http://geckotang.tumblr.com/post/68662389684/bem%E3%83%84%E3%83%BC%E3%83%AB%E3%81%AB%E8%A7%A6%E3%82%8C%E3%81%A6%E3%81%BF%E3%82%8B">BEMツールに触れてみる - < /gecko ></a>

命名規則はBEMから派生した[MindBEMding](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)の方が広く使われているので、MindBEMdingをベースに考えていきます。

MindBEMdingは[csswizardryことHarry Roberts](https://github.com/csswizardry)が提唱したCSSの命名規則のことです。Harry RobertsはCSS界では世界的に有名で、コンサルタントやスピーカーとして世界中で活躍されています。

* [CSS-Guidelines](http://cssguidelin.es/)
* [csswizardry-grids](https://github.com/csswizardry/csswizardry-grids)
* [inuitcss](https://github.com/inuitcss/inuitcss)

---

これ以降の内容は忠実なBEMの方法論ではなく、BEMの考え方にいくつかのアイデアを加えたものになります。  
BEMに関しては、公式ドキュメントを翻訳した[bem-methodology-ja](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)が公開されています。

## BEMの概念
BEMはブロック（Block）、エレメント（Element）、モディファイア（Modifier）の頭文字をとったものです。  
Blockはあるパーツ（コンポーネント）の親要素です。このBlockの中にはElementと呼ばれる子孫要素があります。Modifierはバリエーションを変えるときに指定するもので、BlockかElementと同階層に指定します。詳しい説明は後述します。

命名規則はハイフン2つやアンダースコア2つでつなげます。

* `.Block`
* `.Block__Element`
* `.Block--Modifier`
* `.Block__Element--Modifier`

### Block（親要素）
Blockはサイトを構成するパーツのことです。例えばボタン、グリッド、タブ、画像などで、簡単に言ってしまえばパーツの親要素です。BEMではBlockを起点として考え、サイトはBlockを組み合わせることで構成していきます。

例えば`.box`というシンプルなBlockを作りました。

```scss
.box {
  display: block;
  padding: 1em;
}
```

ボタンもBlockとして考えることができます。

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
ElementはBlockを構成する小さなパーツの1つ1つのことです。例えばグリッドを構成するカラム、タブのボタンやコンテンツ部分などです。

BEMではBlock（親要素）から見て下の階層にいる要素はすべてElement（子孫要素）として扱います。なので、Blockは1つしかありませんし、Elementは1つ以上から構成されます。

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

ボタンを`box`というBlockのElementとして定義してみました。Block特有のスタイルがある場合はBlockの中で意義します。

```html
<!-- OK -->
<div class="box">
  <a href="#" class="box__button"></a>
</div>
```

よりベターなのはBlockの中に他のBlockを入れることです。こうすることでBlockを使い回すことができます。


```html
<!-- Good -->
<div class="box">
  <a href="#" class="button"></a>
</div>
```

### Modifier（バリエーション）
ModifierはBlockとElementのバリエーションをつくるときに指定します。

クラス名は`.block__element--modifier`か`.block--modifier`のようにBlockとElementの名前を引き継ぎハイフン2つでつなぎます。Modifierは`small`でも、`pad-small`のように複数の単語をつなげる方法でも、どちらでもかまいません。

例えば`.box`というBlockがあります。

```scss
.box {
  display: block;
  padding: 1em;
}
```

このBlockに背景色をつけたModifierを作ります。

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

このパターンはOOCSSの「構造と見た目の分離」と同じことをしています。つまり、BEMとOOCSSは組み合わせることもできます。

## 命名規則の明確さ
BEMを使うメリットのひとつに名前からクラスが持っている役割が伝わりやすいことがあります。

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

## 命名規則のアレンジ
BEMのクラス名はBlockの名前を引き継いだりハイフン2つやアンダースコア2つで区切ること、マルチクラス（複数のクラスを指定すること）を前提しているのでクラスの指定が長くなりがちです。

なのでBEMを使う場合には命名規則を案件ごとに変えることがよくあります。

例えば、通常は`block-name`のように単語をハイフンで区切ります。

```scss
.block-name {}
.block-name__element {}
.block-name--modifier {}
```

単語をローワーキャメルケースにした場合。

```scss
.blockName {}
.blockName__element {}
.blockName--modifier {}
```

単語をローワーキャメルケースにして、区切り文字を1つにした場合。

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

## BEMはクラスセレクタ単体にスタイルを指定するのが基本
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

## Element__Element問題
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
BEMはHTMLの構造をElementによって表す必要はありません。`child-element`のような名前にしても伝わります。

```html
<!-- Good -->
<div class="box">
  <div class="box__element">
    <div class="box__child-element">
    </div>
  </div>
</div>
```

<a href="http://qiita.com/hiloki@github/items/4fa99b8755a22878449e">少し細かいBEMい話 - Qiita</a>

例えば多階層のナビゲーションをマークアップした場合はこのようになります。`.nav__items`の子要素は`.nav__child-items`のように`child`をつけています。`.nav__child-items`の子要素は`.nav__child-item`のように`s`を抜いて名前をつけています。


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

極端な例かもしれませんが、これを`element__element`のように親要素の名前を引き継いだ場合はかなり冗長になってしまいます。

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

階層が深くなっても2つの単語で収めるようにできればコードが読みやすくなると思います。

ナビゲーションは難しいかもしれませんが、階層が深くならないようにBlockを組み合わせて使うようにすると、名前の付け方はシンプルになると思います。

---

ちなみにYandexでは`element__element`になっているところがありました。これはjsonの階層構造からクラス名をつけた結果なのかなと思います。

```html
<div class="tabs">
  <div class="b-tabs__item b-tabs__item_images">
    <a href="#" class="link link_black_novisit link__tab" id="tab-images">
      <div class="tabs__item__image tabs__images"></div>
      <div class="tabs__item__text"></div>
    </a>
  </div>
  <div class="b-tabs__item b-tabs__item_images">
    <a href="#" class="link link_black_novisit link__tab" id="tab-images">
      <div class="tabs__item__image tabs__images"></div>
      <div class="tabs__item__text"></div>
    </a>
  </div>
</div>
```

## block__element--modifier問題
例えばElementの`padding`を変更するModifierを作りたい場合、すべてのElementにModifierを指定するのは冗長で指定忘れが出てしまったりするかもしれません。

```html
<!-- NG -->
<div class="box">
  <div class="box__element box__element--size-large"></div>
  <div class="box__element box__element--size-large"></div>
</div>
```

```scss
.box__element {
  padding: 1em;
}

.box__element--size-large {
  padding: 2em;
}
```

公式ドキュメントにはこのようなパターンは出ていませんでした。

このパターンにはBlock--Modiferを起点にしてスタイルを指定する方が運用しやすくなります。

```html
<!-- Good -->
<div class="box box--size-large">
  <div class="box__element"></div>
  <div class="box__element"></div>
</div>
```

```scss
.box__element {
  padding: 1em;
}

.box--size-large > .box__element {
  padding: 2em;
}
```

## Sassの&（アンパサンド）を使わない
Sassでは入れ子と`&`を使ってBEMを早く書くことができますが、読みにくくなるので使わないようにするのがベターだと考えています。

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

検索しにくくなる（あるいはできない）ことも使わない理由のひとつです。

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


## ElementとModifierはBlockのスコープに入れない
BEMのルールではElementとModifierはBlockのなかでだけ存在することができるとあります。以下のHTMLはBlockが存在しないためルール違反になります。

```html
<!-- NG -->
<div class="box--size-large">
  <div class="box__element"></div>
  <div class="box__element"></div>
</div>
```

厳密にする場合はセレクタの結合子にBlockを常に指定します。

```scss
.block {}
.block > .block__element {}
.block.block--modifier {}
```

Blockを名前空間にすることで、Blockを指定しないマークアップを防ぐことができます。

```html
<!-- Blockがないので、スタイルは適応されない。 -->
<div class="box--size-large">
  <div class="box__element"></div>
  <div class="box__element"></div>
</div>
```

ただし、個人的にはここまで厳密にする必要はありません。セレクタを複雑にして厳密さを求めるより、BEMの基本的なルールを守ってもらえるようにコミュニケーションをとる方が必要ではないかなと考えています。