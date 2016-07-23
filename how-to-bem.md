# BEM(MindBEMding)によるCSS設計

[BEM](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)は[Yandex](https://www.yandex.com/)というロシアの検索エンジンなどを作っている企業が提唱しているCSSの設計方法です。[BEM Tool](https://tech.yandex.com/bem/)というBEMを生成するための環境を使っているようで、直接BEMを書くことはないようです。

命名規則はBEMから派生した[MindBEMding](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)をベースに説明をしていきます。

MindBEMdingはcsswizardryこと[Harry Roberts](https://github.com/csswizardry)が提唱したCSSの命名規則のことです。Harry RobertsはCSS界では世界的に有名で、スピーカーとして世界中で講演をしたりしているようです。

* [CSS-Guidelines](http://cssguidelin.es/)
* [csswizardry-grids](https://github.com/csswizardry/csswizardry-grids)
* [inuitcss](https://github.com/inuitcss/inuitcss)

## BEMの概念
BEMはブロック（Block）、エレメント（Element）、モディファイア（Modifier）の頭文字をとったものです。詳しい説明は後述します。

命名規則はハイフン2つやアンダースコア2つでつなげます。

* `.Block`
* `.Block__Element`
* `.Block--Modifier`
* `.Block__Element--Modifier`

公式ドキュメントを翻訳した[bem-methodology-ja](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)を読んでみるのが一番の近道かもしれません。

### Block（親要素）
Blockはサイトを構成するパーツのことです。例えばボタン、グリッド、タブ、画像などで、簡単に言ってしまえばパーツの親要素です。BEMではBlockを起点として考え、サイトはBlockを組み合わせることで構成していきます。

例えば`.box`というシンプルなBlockを作ります。

```scss
.box {
  display: block;
  padding: 1em;
}
```

ボタンはBlockとして考えることができます。

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

### Element（子要素）
ElementはBlockを構成する小さなパーツの1つ1つのことです。例えばグリッドを構成するカラム、タブのボタンやコンテンツ部分などです。

BEMではBlock（親要素）から見て下の階層にいる要素はすべてElement（子要素）として扱います。

クラス名は`.block__element`のようにBlockの名前を引き継いでアンダースコア2つでつなぎます。

例えばグリッドレイアウトであればこのようになります。`.grid`がBlockになり、`.grid__item`がElementになります。

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

BlockのElementとしてボタンを定義してみました。Block特有のスタイルがある場合はBlockの中で意義した方がいいかもしれません。

```html
/* OK */
<div class="box">
  <a href="#" class="box__button"></a>
</div>
```

ベターなのはBlockの中に他のBlockを入れることです。こうすることでBlockを使い回すことができます。


```html
/* Good */
<div class="box">
  <a href="#" class="button"></a>
</div>
```

### Modifier（バリエーション）
ModifierはBlockとElementのバリエーションをつくるときに指定します。

クラス名は`.block__element--modifier`か`.block--modifier`のようにBlockとElementの名前を引き継ぎハイフン2つでつなぎます。

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

元のBlockのスタイルを変更することなく、背景色をつけたパターンを作ることができました。HTMLはこのようにマルチクラスで指定することになります。

```html
<div class="box box--heightlight">
</div>
```

## 命名規則のアレンジ
BEMのクラス名は最大で`.block__element--modifier`のようになり、マルチクラス（複数のクラスを指定する）ことを前提しているのでクラスの指定が長くなりがちです。

なのでBEMを使う場合には命名規則を案件ごとに変えることがよくあります。BEMの概念さえブレていなければ問題はありません。

例えば通常は`block-name`のように単語をハイフンで区切ります。

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

## BEMはクラスセレクタ単体にスタイルを指定するのが基本
BEMではIDセレクタや要素セレクタは使用せず、クラスセレクタで指定します。

```scss
/* Good */
.block-name {}
.block-name__element {}
.block-name--modifier {}
```

クラスセレクタ単体にスタイルを指定していくので、詳細度は0,0,1,0のままになります。

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
.block {}
.block > p {} // Block直下の`<p>`すべてに適応されてしまう
```	

案件によってルールを緩くした方が運用しやすいこともあります。例えば、`<ul>`と`<ol>`の直接の子要素は`<li>`しか指定できないので、必ずもクラスを指定しなくてもいいかもしれません。

## element__element問題
BEMでは必然的にElementの数が多くなります。Blockから見て孫要素ができることもあります。このときに`.block__element__element`のようなクラス名にしているケースがあります。

```html
<div class="box">
  <div class="box__element">
    <div class="box__element__element">
    </div>
  </div>
</div>
```
BEMはHTMLの構造をElementによって表す必要はありません。`child-element`のような名前にしても伝わります。

```html
<div class="box">
  <div class="box__element">
    <div class="box__child-element">
    </div>
  </div>
</div>
```

例えば多階層のナビゲーションをマークアップした場合はこのようになります。`.nav__items`の子要素は`.nav__child-items`のように`child`をつけています。`.nav__child-items`の子要素は`.nav__child-item`のように`s`を抜いて名前をつけています。


```html
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

これをelement__elementのように親要素の名前を引き継いだ場合はかなり冗長になってしまいます。これは極端な例かもしれませんが、階層が深くなっても2つの単語で収めるようにできればコードが読みやすくなると思います。

```html
<ul class="nav">
  <li class="nav__items">
    <ul class="nav__items__items">
      <li class="nav__items__items__item"><a href="#" class="nav__items__items__link"></a></li>
      <li class="nav__items__items__item"><a href="#" class="nav__items__items__link"></a></li>
    </ul>
  </li>

  <li class="nav__items">
    <ul class="nav__items__items">
      <li class="nav__items__items__item"><a href="#" class="nav__items__items__link"></a></li>
      <li class="nav__items__items__item"><a href="#" class="nav__items__items__link"></a></li>
    </ul>
  </li>

</ul>
```

ナビゲーションは難しいかもしれませんが、階層が深くならないようにBlockを組み合わせて使うようにすると、名前の付け方は多少楽になると思います。

## block__element--modifier問題
例えばElementの`padding`を変更するModifierを作りたい場合、すべてのElementにModifierを指定するのは冗長で指定忘れが出てしまったりするかもしれません。

```html
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

## ElementとModifierはBlockのスコープに入れるか問題
BEMのルールではElementとModifierはBlockのなかでだけ存在することができるとあります。厳密にする場合はセレクタの結合子にBlockをを常に指定することもできます。

```scss
.block {}
.block > .block__element {}
.block.block--modifier {}
```

Blockを名前空間のようにすることで、以下のようにBlockを指定しないマークアップを防ぐことができます。

```html
<div class="box--size-large">
  <div class="box__element"></div>
  <div class="box__element"></div>
</div>
```

ただし、個人的にはここまで厳密にする必要はないんじゃないかと考えています。セレクタを複雑にして厳密さを求めるより、BEMの基本的なルールを守ってもらえるようにコミュニケーションをとる方が必要ではないかなと考えています。