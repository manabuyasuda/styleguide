# CSSコーディングルール
## CSSプリプロセッサー
CSSプリプロセッサーにはSassのscss記法を使います。CSSをそのまま書けること、CSSプリプロセッサーのデフォルトスタンダードになっているので参考になるコードが豊富にあることがその理由です。

## ファイル構成
詳細度の強さやスコープの範囲を管理するために[FLOCSS](https://github.com/hiloki/flocss)をベースにしたファイル構成にします。

### FLOCSS

FLOCSSはFoundation、Layout、Objectの3つのレイヤーとComponent、Project、Utilityの3つの子レイヤーから構成されます。

1. Foundation - Normalize.cssなどのリセットCSSや要素セレクタ・属性セレクタのような最低限のスタイルを指定します。詳細度を極力弱く、影響範囲を極力狭くします。
1. Layout - ワイヤーフレームに定義されるような大きな単位のコンテナブロックが該当します。このレイヤー以降はクラスセレクタのみを指定します。例外として、このLayoutレイヤーにだけは必要に応じてidセレクタを指定することもできます。
1. Object - OOCSSのコンセプトをもとにしたビジュアルパターンが主に該当します。ObjectレイヤーはComponent、Project、Utilityの3つの子レイヤーにわけられます。
1. Component - 多くのプロジェクトで横断的に再利用できるような小さな単位のモジュールが該当します。OOCSSの構造の機能を担い、固有のサイズや装飾的なスタイルを極力含まないようにします。
1. Project - プロジェクト固有のスタイルが該当します。プロジェクトで使い回すスタイルのほとんどはProjectレイヤーに追加することになります。
1. Utility - いわゆる汎用クラスのことで、ほとんどの場合で単一のスタイルを持っています。確実にスタイルを適応させるために`!important`を指定します。

あるページでしか使わないようなユニークなスタイルは、共通化させずにそのページにだけ読み込ませるようにします。共通のCSSファイルより後に読み込むことで、優先的に適応されるようにします。

ファイル構成としては、FLOCSSで構成されたCSSファイルをルートディレクトリに置き、ページ専用のCSSファイルは必要なディレクトリに置きます。

### レイヤーの追加
FLOCSSのファイル構成案にはレイヤーを追加することもできます。

* Variable - プロジェクトで使用されるグローバル変数
* Function - プロジェクトで使用されるグローバルなfunction
* Mixin - プロジェクトで使用されるグローバルなmixin
* Vendor - Normalize.cssやBootstrapのような外部のライブラリやフレームワーク
* Vendor-extension - Vendorレイヤーの上書き（オーバーライド）
* Theme - SMACSSのThemeモジュールに該当するテーマによる色の切り替えなど
* Scope - ブログの投稿用スタイルなどのスコープを作る
* QA/Test - Quality Assurance（品質保証）、もしくはテストのための一次的なスタイル

レイヤーの追加は以下のリンクを参考にします。

* [More Transparent UI Code with Namespaces](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/)
* [Managing CSS Projects with ITCSS](http://csswizardry.net/talks/2014/11/itcss-dafed.pdf)

レイヤーを追加した場合は以下のようになります。Vendorレイヤーは含んでいるライブラリなどの特徴（主に詳細度）によって読み込む順番を変更します。

1. Foundation
 1. Variable
 1. Function
 1. Mixin
 1. Vendor
 1. Vendor-extension
 1. Base
1. Layout
1. Object
 1. Component
 1. Project
 1. Theme
 1. Scope
 1. Utility
 1. QA/Test
 
### モジュール化
Sassのパーシャルと`@import`を使って、機能や用途ごとにファイルをモジュール化します。例えばグリッドレイアウトは`_grid.scss`に、変数は`_variable.scss`のようにまとめます。ファイル名とディレクトリ名はgridsやvariablesのように複数形にはしません。

クラス名とファイル名は一致するようにします。例えば`_grid.scss`の中には`.grid`や`.grid__item`を指定します。

ページ専用のCSSファイルは量が少なければモジュール化はしません。

## 命名規則
ルールを決めずに安易にコードを増やしてしまうと、名前が重複し、意図しないスタイルが適応されてしまう恐れがあります。ライブラリやフレームワークを併用する場合も同様です。また、クラス名で役割を示すことでコードの理解を助けることもできます。

### 接頭辞（Prefix）
役割を示したり、名前の重複をさせないために接頭辞をつけます。FLOCSSで推奨されている接頭辞といくつかの接頭辞を使用します。

* `.l-` Layoutレイヤー
* `.c-` Componentレイヤー
* `.p-` Projectレイヤー
* `.u-` Utilityレイヤー
* `.t-` Themeレイアー
* `.s-` Scopeレイヤー
* `.qa-` `.te-` QA/Testレイヤー
* `.is-` クリックなどのイベントが発生している要素に付与する
* `.js-` JavaScriptから参照される要素
* `.cp-` あるHTML専用のCSSファイル（current pageの略称で名前を変更することもできます）


### 接尾辞（Suffix）
ブレイクポイントの指定しているクラス名の最後には`-sm`や`-md`のようにブレイクポイントのキーワードをつけます。

ブレイクポイントはグローバル変数として定義しておき、そのキーをクラス名にも使うようにします。

```scss
$_breakpoint-up: (
  'sm': 'screen and (min-width: 400px)',
  'md': 'screen and (min-width: 768px)',
  'lg': 'screen and (min-width: 1000px)',
  'xl': 'screen and (min-width: 1200px)',
) !default;
```

```scss
@media (min-width: 400px) {
  .u-dn-sm { display: none; }
}

@media (min-width: 768px) {
  .u-dn-md { display: none; }
}

@media (min-width: 1000px) {
  .u-dn-lg { display: none; }
}

@media (min-width: 1200px) {
  .u-dn-xl { display: none; }
}
```

メディアクエリは`min-width`を優先的に使うので`-sm`などとします。`max-width`のパターンも作る場合は`-sm-down`などとします。

### 単語の省略を制限する
基本的にセレクタ名の単語は省略しないようにします。省略する場合もセレクタ名が何をあらわしているのかを分かるようにします。

例えば`navigation`を`nav`と省略することはできますが、`title`を`ttl`としても単語数が大きく減っていることもなく、意味が汲み取りにくくなるので省略しません。

```scss
// Good
.navigation {}
.title {}
.text {}
.icon {}

// allow
.nav {}

// Bad
.ttl {}
.txt {}
.ico {}
```

### 絶対値をあらわすクラス名を避ける
`.mb10`（`margin-bottom:10px;`）のような値を固定した名前をつけることは避けます。固定値ではレスポンシブなどで柔軟に対応することが難しいからです。相対的な名前か、そのコンポーネント専用の名前をつけます。

相対的な名前は`.mbs`（margin-bottom smallの意味）のようにします。small, medium, largeのようなパターンに制限するルールを設けることで、際限なくサイズ違いが増えてしまうことを防ぐこともできます。

コンポーネント専用の名前は`component-name__title01`のようにコンポーネントの名前を引き継ぎ、パーツの名前と連番で管理します。必ずコメントをつけて、なぜ必要なのかを書いておきます。

カラーネーム（`.red`や`.blue`）のようなクラス名も禁止します。`.success`や`.brand`のようなスタイルを変更しても意図が変わらない名前にしましょう。

```scss
// Good
.brand {}
.success {}

// Bad
.red {}
.blue {}
```

### BEM（MindBEMding）
名前は[BEM](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)をベースにした[MindBEMding](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)を使用します。BEMには役割や関係性を明確にできるメリットがあります。

BEMはBlock、Element、Modifierの3つの概念から構成され、以下のようなクラス名になります。

```scss
.block-name {}
.block-name__element {}
.block-name--modifier-name {}
.block-name__element--modifier-name {}
```

* 小文字の英単語とハイフン1つ（`-`）とハイフン2つ（`--`）、アンダースコア2つ（`__`）で構成されます
* BlockとElementとModifierの単語はハイフン（`-`）でつなぎます
* BlockとModifierはできるだけ2つ以上の単語を合わせてユニークな名前にします
* ElementとModifierは1つの単語だけを使ってもかまいません
* ElementとModifierはBlockの名前を受け継ぎます
* BlockとElementはアンダースコア2つ（`__`）でつなぎます
* BlockとModiferもしくはElementとModiferはハイフン2つ（`--`）でつなぎます

基本的にクラスセレクタ1つだけにスタイルを指定しますが、`.block--modifier`の指定でElementにもスタイルを指定したい場合にはクラスセレクタを2つ使って指定することもできます。ただし、直近の子要素に限定するなど、影響範囲をできるだけ狭くしておきます。

```scss
// Good
.block--modifier > .block__element {}

// Bad
.block--modifier .block__element {}
```

#### BEMの注意点
Elementを入れ子にするときに`.block__element__element`のような名前にならないようにします。HTMLの構造を示すのではなく、Blockに対するElementとModifierの関係性を示します。

```scss
// Good
.block__element {}
.block__sub-element {}

// Bad
.block__element {}
.block__element__element {}
```

OOCSSのようにマルチクラスで指定するのを基本とします。基本的にはBlockは独立して機能するようにしますが、汎用性を考慮したり、クラス名が長くなりすぎる場合には、Modifierだけでなくユーティリティークラスを組み合わせることもあります。

```scss
// ユーティリティークラスで`width`を指定している。
<div class="c-grid">
  <div class="c-grid__item u-8of12-md"></div>
  <div class="c-grid__item u-4of12-md"></div>
</div>
```

Blockを親セレクタにしてElementとModifierを指定しません。スコープは命名規則によって擬似的に作ります。スタイルは柔軟に上書きができるように、詳細度をできるだけ弱くしておきます。

```scss
// Good
.block {}
.block__element {}
.block--modifier {}

// Bad
.block {}
.block .block__element {}
.block .block--modifier {}
```

Blockのスタイルを上書きするときに詳細度を変えないようにします。例えば、`.block1`のスタイルを変更するために`.block2`を親セレクタにして`.block1`を上書きするような指定はできるだけ避けます。同じ詳細度のセレクタであとから読み込ませることで上書きするようにします。（例外としてComponentレイヤーのスタイルをProjectレイヤーのスタイルで上書きすることはできます。）

```scss
// Good
.block1 {}
.block__element {}

.block2 {}

// Bad
.block1 {}
.block__element {}

.block2 .block1 {}
```

Sassの入れ子とアンパサンド（`&`）でBEMの記述を短縮することができますが、基本的に使わないようにします。1つ目の理由は入れ子のElementが増えると、今のアンパサンドがどこまでのElementとModifierを含んでいるのかを把握しにくくなるからです。

```scss
// Bad
.block {
  &__element {
    ...
  }
  &__element {
    ...
  }
  &__element {
    ...
  }
  &--modifier {
    ...
  }
  &__element {
    ...
  }
  &__element {
    &--modifier {
      ...
    }
  }
}
```

2つ目の理由は実際に指定しているセレクタ名で検索できなくなるからです。CSSにコンパイルされてはじめて`.block__element`のように生成されるので、Sass編集時に検索することができません。

入れ子を使うのは擬似要素やステートクラス、ブレイクポイントの挿入などに制限します。

```scss
// Good
.block__element:hover {}
.block__element.is-active {}

.block__element {
  @media (min-width: 768px) {

  }
}

// Bad
.block {
  &__element {

    &:hover {

    }
    &.is-active {

    }
  }
}
```

早く書けることも必要ですが、修正や運用がしやすいことのほうがずっと重要です。

## コメント
コメントにはコードを理解しやすくするような補足情報を書きます。例えば、全体像を把握するための目次や区切りを分かりやすくする見出し、コード自体の補足、なぜそうしたのか、今後どう変えていきたいのかといった情報です。

### 目次（Table of Contents）
全体のボリュームやどんなスタイルが用意されているのかを知らずに、スタイルを追加することほど怖いことはありません。スタイルシートのはじめには、用意されているスタイルと説明を目次にして書いておきます。

```scss
/**
 * FUNCTION
 * strip-unit...pxやremなどの単位を取り除きます。
 * color...明度を基準に色を変更します。
 * z-index...z-indexの並び順を管理します。
 * tracking...Photoshopのカーニングをemに変換します。
 *
 * VARIABLE
 * common...グローバルに使用するサイズや数値に関する変数です。
 * breakpoint...メディアクエリで使用するブレイクポイントです。
 * font-family...font-family指定をまとめています。
 * color...グローバルに使用する色指定です。
 * z-index...z-indexの並び順を管理します。
 * easing...cubic-bezier関数を使用したタイミング関数を定義しています。
 *
 * MIXIN
 * media-query...メディアクエリを呼び出します。
 * responsive...レスポンシブ対応クラスを生成します。
 * clearfix...floatの解除をします。
 * on-event...:hover, :active, :focusをまとめて指定します。
 * sr-only...非表示にしてスクリーンリーダーにだけ読み上げさせます。
 *
 * VENDOR
 * normalize...Normalize.cssをインポートしています。
 *
 * BASE
 * base...タイプセレクタと属性セレクタのデフォルトスタイルです。
 *
 * LAYOUT
 * header...ヘッダーエリアのコンテナブロックのスタイルです。
 * footer...フッターエリアのコンテナブロックのスタイルです。
 * main...コンテンツエリアのコンテナブロックのスタイルです。
 * sidebar...サイドバーエリアのコンテナブロックのスタイルです。
 *
 * COMPONENT
 * wrapper...最大幅を指定します。
 * grid...グリッドレイアウトを指定します。
 * media...画像とテキストが横並びになるオブジェクトです。
 * card...カードタイプのオブジェクトです。
 * split...定義リストをブロックからインラインにするオブジェクトです。
 * button...ボタンのデフォルトスタイルです。
 * embed...Youtubeなどをアスペクト比を固定してレスポンシブ対応させる場合に使用します。
 *
 * PROJECT
 * icon...アイコンフォントです。
 * breadcrumb...パンくずリストです。
 * label...インラインのラベルです。
 * badge...投稿数のような数値を表示するバッジコンポーネントです。
 *
 * UTILITY
 * text...テキストのスタイルに関する汎用クラスです。
 * display...要素の表示や改行をコントロールする場合に使用します。
 * width...おもにグリッドで使用する`width`を指定する汎用クラスです。
 */
```

### モジュールタイトル
スタイルや機能はモジュール化して分離しておきます。それぞれのモジュールには`#モジュール名`のように見出しをつけることで検索できるようにします。

```scss
 /* #Grid
   -------------------------------------------------------------------------- */
```

### レイヤータイトル
FLOCSSは6つのレイヤーをベースにしていますが、今どこのレイヤーにいるのか、どういった特徴のあるレイヤーなのかなどを残しておくと把握しやすくなります。

```scss
/* -----------------------------------------------------------------------------
   #Component
   -------------------------------------------------------------------------- */
/**
 * Componentレイヤーは多くのプロジェクトで横断的に再利用のできるような、小さな単位のモジュール（機能）です。
 * OOCSSの構造（structure）の機能を担うため、装飾的なスタイルをできるだけ含めないようにします。
 * また、`width`や`margin`といったレイアウトに影響を与えるプロパティもできるだけ含めないようにします。
 * 例えばbuttonのベーススタイルやgridやmediaといったレイアウトパターンが該当します。
 * プレフィックス（接頭辞）として`c-`をつけます。
 */
@import "object/component/_wrapper";
@import "object/component/_grid";
@import "object/component/_media";
@import "object/component/_card";
@import "object/component/_split";
@import "object/component/_button";
@import "object/component/_embed";
```

### コードの説明と参照リンク
そのコードが一部のひとにしか正しく理解されないと感じたらコメントを残します。詳しくコメントで説明できない場合は参照となるリンクも貼っておきます。

```scss
/**
 * ボックスモデルを`border-box`にリセットします。
 * https://css-tricks.com/inheriting-box-sizing-probably-slightly-better-best-practice/
 */
html {
    box-sizing: border-box;
}

*,
*:before,
*:after {
    box-sizing: inherit;
}
```

### 注意事項とTODO
あとで処理をしたいことや、把握しておいてほしい注意事項はコメントに残して、いつでも検索できるようにします。

* `NOTE:` コード上で確認できない制限などの共有
* `TBD:` 今後どうしていくか検討したいこと
* `TODO:` 動作はするけれど改修したい箇所

### マークアップ例
再利用されるコンポーネントにはマークアップ例を書きます。スタイルガイドジェネレーターを使用する場合は、その記述方法で書いておきます（以下はHologramの場合）。

```yaml
/*doc
---
title: grid
name: grid
categories: [component]
---

グリッドレイアウトオブジェクトです。`width`の変更はUtilityレイヤーで指定します。

Modifierは以下のとおりです。

* ガターの追加 `--small`, `--medium`, `--large`
* text-alignの変更 `--middle`, `--bottom`
* 配置を反転 `--rev`

｀｀｀block
<div class="c-wrapper">
  <div class="c-grid c-grid--medium">
    <div class="c-grid__item u-8of12-md"></div>
    <div class="c-grid__item u-4of12-md"></div>
  </div>

  <div class="c-grid c-grid--rev c-grid--medium">
    <div class="c-grid__item u-8of12-md"></div>
    <div class="c-grid__item u-4of12-md"></div>
  </div>

  <div class="c-grid c-grid--center c-grid--medium">
    <div class="c-grid__item u-8of12-md"></div>
  </div>
</div>
｀｀｀
*/
```

### Sassのコメント
SassはCSSをプログラム的に拡張できるプリプロセッサーです。複雑になりがちな機能や使い方を伝えるためにコメントで補足をしておきます。

```scss
// @desc - メディアクエリを挿入します。
// @param {String} - 引数に変数のキーワードを渡します。
// @see - foundation/variable/_breakpoint.scss
// @example scss - Usage
// .foo {
//   color: red;
//   @include _mq-up(sm) {
//     color: blue;
//   }
// }

// @example css - CSS output
// .foo {
//   color: red;
// }
// @media screen and (min-width: 400px) {
//   .foo {
//     color: blue;
//   }
// }

// @desc - メディアクエリを`min-width`で挿入します。
// @param {String} - 引数に変数のキーワードを渡します。
// @see - $_breakpoint-up
@mixin _mq-up($breakpoint) {
  @if map-has-key($_breakpoint-up, $breakpoint) {
    @media #{inspect(map-get($_breakpoint-up, $breakpoint))} {
      @content;
    }
  } @else {
    @warn "Unfortunately, no value could be retrieved from `#{$breakpoint}`. "
    + "Please make sure it is defined in `$_breakpoint-up` map.";
  }
}
```

## フォーマットとプロパティの宣言順
CSSの構文はセレクタとブレース、プロパティと値で構成されます。このルールセットに読みやすさを確保したフォーマット（書式）を定義します。

### ルールセットのフォーマット
ルールセットの基本的なフォーマットは以下の通りです。

* 複数のセレクタは別の行に（関連性があって1行が長くなり過ぎなければ同じ行に指定してもいい）
* 最後のセレクタとオープニングブレースの間にスペースを1つ
* それぞれの宣言は別々の行に
* ルールセット内に空行は入れない
* プロパティの前にスペースを2つ
* コロン（`:`）と値の間にスペースを1つ
* クロージングブレースは独立した行に
* 各ルールセットの間に空行を1つ
* 関数はカンマ（`,`）と引数の間にスペースを1つ
* ローカル変数は最初に定義
* extendはローカル変数の次に指定
* mixinはextendの次に指定
* contentを使用しているmixinは最後に指定
* 演算は常に括弧（`()`）で囲む
* 括弧内のカンマ（`,`）と値の間にスペースを1つ

```scss
/* Good */
.foo, .foo--bar,
.baz {
  $_padding: 1em;
  @extend %base-unit;
  @include _clearfix;
  display: block;
  margin-right: auto;
  margin-left: auto;
  padding-right: $_padding;
  padding-left: $_padding;
  backgrouond-color: rgba(0, 0, 0, 0.7);
  @include _mq-up(md) {
    padding-right: ($_padding * 2);
    padding-left: ($_padding * 2);
  }
}

/* Bad */
.foo,
.foo--bar, .baz{
    @include _mq-up_(md) {
        padding-right: $_padding * 2;
        padding-left: $_padding * 2;
    }
    display: block;margin-right: auto;
    backgrouond-color: rgba(0,0,0,0.7);
    margin-left:auto;
    @extend %base-unit;
    @include _clearfix;
    $_padding: 1em;}
```

### 単一行ルールセットのフォーマット
原則的には複数行でルールセットを記述しますが、ユーティリティクラスなどの単一のスタイルの場合は1行で記述することもできます。

単一行のフォーマットは以下の通りです。

* 最後のセレクタとオープニングブレースの間にスペースを1つ
* オープニングブレースとプロパティの間にスペースを1つ
* コロン（`:`）と値の間にスペースを1つ
* セミコロン（`;`）とクロージングブレースの間にスペースを1つ
* 各ルールセットの間には空行を入れない

```scss
// Good
.u-text-right { text-align: right !important; }
.u-text-center { text-align: center !important; }
.u-text-left { text-align: left !important; }
```

### プロパティの宣言順
ルールセット内の宣言は重要なプロパティから書き始め、その機能ごとに固めて記述することで意図を素早く理解できるようにします。プロパティの宣言順は以下のようなルールで記述します。

* ボックスモデルの種類や表示方法を示すプロパティ（`box-sizing`, `display`, `visibility`, `float`など）
* 位置情報に関するプロパティ（`position`, `z-index`など）
* ボックスモデルのサイズに関するプロパティ（`width`, `height`, `margin`, `padding`, `border`など）
* フォント関連のプロパティ（`font-size`, `line-height`など）
* 色に関するプロパティ（`color`, `background-color`など）
* それ以外

アルファベット順には記述しません。整列ではなく、理解しやすいように分類することが目的だからです。

```scss
// Good
.foo {
  display: block;
  position: absolute;
  right: 0;
  bottom: 0;
  width: 100%;
  margin: 0;
  padding: 0;
  font-size: 0.75em;
  color: #000;
  background-color: #fff;
}

// Bad
.foo {
  background-color: #fff;
  bottom: 0;
  color: #000;
  display: block;
  font-size: 0.75em;
  margin: 0;
  padding: 0;
  position: absolute;
  right: 0;
  width: 100%;
}
```

### 個別指定プロパティの宣言順

`margin`や`position`のように上下左右に個別に指定できるプロパティは時計回りで記述をすることで規則性をもたせます。

```scss
// Good
.foo {
  margin-top: auto;
  margin-right: auto;
  margin-bottom: auto;
  margin-left: auto;
}

// Bad
.foo {
  margin-left: auto;
  margin-right: auto;
  margin-top: auto;
  margin-bottom: auto;
}
```

## CSSベタープラクティス

### セレクタ
#### インライン記述を使用しない
HTMLのstyle属性にスタイルを記述することは禁止します。HTMLには見た目に関する情報を極力書かないようにします。クラスを振ったほうが柔軟にスタイルを変更することができます。JavaScriptを使う場合もstyle属性を使いません。

```html
<!-- Bad -->
<div style="color:red; background-color: black;"></div>
```

#### HTMLの構造に依存させない
要素セレクタに対してスタイルを指定すると、構造が変更されたときにスタイルが崩れてしまう恐れがあります。

```scss
// Bad
article h2 {}
```

クラスセレクタであれば、指定している要素に関係なくスタイルが適応されるので、使い回しがしやすくなります。ただし、要素によってデフォルトのスタイルがちがうこともあるので、スタイルが崩れないようにリセットしておく必要がある場合があります。

```scss
// `article`要素の見出しに指定するクラス
.article__title {}

// 汎用的な見出しのクラスを用意
.u-h2 {}
```

クラスセレクタに対して要素セレクタを連結させるのも意味がありません。

```scss
// Good
.foo {}

// Bad
h2.foo {}
```

特定の要素で指定してほしいのであれば、コメントやクラス名でそれを示します。

```scss
/* Good */
/* ulタグかolタグに指定してください。 */
.foo {}

/* ul, ol */.foo-list {}
```

#### IDセレクタを多用しない
IDセレクタはスタイルの上書きが難しくなるので、複雑なセレクタを指定しなければいけなくなったり、詳細度を強くしてしまう原因になります。IDセレクタは使わないほうがベターです。

ただし、ページ内リンクやJavaScriptのフックとして使うのは問題ありません。

#### メディアクエリをまとめて管理しない
メディアクエリをまとめて指定しません。そのセレクタがどのように変化していくのかを把握しにくくしてしまいます。

```scss
// Good
.foo {}

@media (min-width: 768px) {
  .foo {}
}

// Bad
@media (min-width: 768px) {
  .foo {}
  .bar {}
  .baz {}
}
```

#### セレクタを短くする
セレクタは必ずしもHTML構造に沿う必要はありません。場合によってはセレクタを短くすることで詳細度を低く抑えることができます。

```scss
// Good
ul a {}
th {}

// Bad
ul li a {}
table th {}
```

#### divやspanをセレクタに使用しない
`div`や`span`は使用頻度が高いので、セレクタに指定すると意図しないところにスタイルが当たってしまう恐れがあります。直接スタイルを指定せずに、クラスを振って指定します。

```scss
// Bad
div {}
span {}
.foo div {}
.foo span {}
```

#### 擬似クラスを活用する
CSS3の擬似クラスを使うとセレクタを柔軟に指定することができます。

* x番目の要素 - `:nth-of-type(x)`
* x番目以降の要素 - `nth-of-type(n+x)`
* 奇数の要素 - `:nth-of-type(odd)` `nth-of-type(2n+1)`
* 偶数の要素 - `:nth-of-type(even)` `:nth-of-type(2n)`
* 最初の要素 - `:first-of-type`
* 最初の要素以外の要素 - `:not(:first-of-type)` `:nth-of-type(n+2)`
* 最初から数えてx番目までの要素 - `:nth-of-type(-n+x)`
* 最後の要素 - `:last-of-type`
* 最後の要素以外 - `:not(:last-of-type)`
* 最後から数えてx番目の要素 - `:nth-last-of-type(x)`
* 最後の要素でなおかつ奇数の要素 - `:nth-of-type(odd):last-of-type`
* 最初の子要素 - `> :first-child`
* 最後の子要素 - `> :last-child`
* あるクラス名がfooで始まる要素のx番目 - `> [class^="foo"]:nth-child(x)`

CSS3の擬似クラスを使う場合は`nth-child`のような`-child`ではなく、`nth-of-type`のような`of-type`を使います。`-child`は親要素から見た子要素を要素のタイプに関係なく数えるので、HTMLの構造によっては期待どおりにならないことがあります。

#### セレクタの詳細度を管理する
セレクタの詳細度は基本的にスタイルシートの序盤は詳細度を弱く、それから徐々に詳細度を高くしていくほうが管理がしやすくなります。

セレクタの工夫で詳細度を変える方法もあります。

* `.foo`(0,0,1,0)の詳細度を強くする場合は`.foo.foo`(0,0,2,0)
* `#bar`(0,1,0,0)の詳細度を弱くする場合は`[id="bar"]`(0,0,1,0)
* `html`(0,0,0,1)の詳細度を強くする場合は`:root`(0,0,1,0)
* `a`(0,0,0,1)の詳細度を強くする場合は`a:link`(0,0,1,1)

`.foo`と`[class="foo"]`は同じ詳細度（0, 0, 1, 0）になりますが、属性セレクタは「`.foo`で始まるクラス名」のような柔軟な指定ができます。

* `[class^="foo"]` class属性の値がfooで始まる（前方一致）要素
* `[class$="foo"]` class属性の値がfooで終わる（後方一致）要素
* `[class*="foo"]` class属性の値がfooを含む（部分一致）要素

[Specificity Calculator](http://specificity.keegan.st/)

#### ステートクラス単体にスタイルを指定しない
SMACSSというCSSの設計手法にState（ステート）という状態をあらわすルールがあります。例えばクリックやマウスオーバーのようなイベントが発生している要素に指定することでスタイルを変更します。

このステートクラスは単体でスタイルを持たせず、コンポーネントと一緒に指定することでスコープを作ります。

```scss
// Good
.foo.is-active {}

// Bad
.is-active {}
```

#### 構造とスキン（見た目）を分離する
いくつかのバリエーションを含んでいるコンポーネントはOOCSSの構造とスキン（見た目）の分離ができるようにします。構造とはボックスモデルやレイアウトなどで、スキンとは色や線などの見た目を整えることです。

以下のように構造を担う`.box`と、スキンを担う`.box--success`と分けて指定することで、共通のスタイルを多く持つコンポーネントにすることができます。余白を変更する場合でも構造を持つ`.box`を変更するだけで他のバリエーションにも適応されます。

```html
<!-- Good -->
<p class="box"></p>
<p class="box box--success"></p>
<p class="box box--error"></p>
```

```scss
// Good
.box {
  margin-bottom: 1rem;
  padding: 1em;
  border: 1px solid #e7edf0;
  color: #47525d;
  background-color: #f7f9fa;
}

.box--success {
  border-color: #d9f4e1;
  color: #2c683f;
  background-color: #edfaf1;
}

.box--error {
  border-color: #ffcaca;
  color: #921515;
  background-color: #ffe3e3;
}

// Bad
.box {
  margin-bottom: 1rem;
  padding: 1em;
  border: 1px solid #e7edf0;
  color: #47525d;
  background-color: #f7f9fa;
}

.box--success {
  margin-bottom: 1rem;
  padding: 1em;
  border-color: #d9f4e1;
  color: #2c683f;
  background-color: #edfaf1;
}

.box--error {
  margin-bottom: 1rem;
  padding: 1em;
  border-color: #ffcaca;
  color: #921515;
  background-color: #ffe3e3;
}
```

### プロパティ
#### !importantを多用しない
`!important`の使用は禁止しませんが、多用するべきではありません。例えばシングルクラスのヘルパークラスを確実に適応されるために指定したり、外部ライブラリがJavaScriptでstyle属性を指定してしまうのを上書きするためといった場合に限って使用します。

#### スタイルをリセットしている
スタイルのリセットとは、`0`や`none`などで値を上書きすることです。スタイルのリセットが多いときは、要素にスタイルを持たせすぎていたり、早く指定しすぎている恐れがあります。

```scss
// Bad
.foo {
  border: 1px solid #ddd;
}

// ある要素内ではボーダーが邪魔になるためリセット
.bar .foo {
  border: none;
}
```

Modiferで必要なときにだけ指定するとリセットが起きにくくなります。

```scss
// Good
.foo {}
.foo--bordered {
  border: 1px solid #ddd;
}
```

#### 要素セレクタに装飾的なスタイルを指定しない
`h1`や`p`といったセマンティックな要素セレクタには多くのスタイル（特に装飾的なスタイル）を指定しません。必ずあとでスタイルをリセットすることになります。

```scss
// Bad
h2 {
  padding-bottom: 0.5em;
  border-bottom: 1px solid #888;
}
```

要素セレクタにはブラウザのデフォルトスタイルシートやリセットCSSで指定されるような最低限のスタイルにとどめておきます。

```scss
// Good
h2 {
  font-size: 2rem;
  line-height: 1.2;
  margin-top: 0;
  margin-bottom: 24px;
}
```

#### 高さを指定しない
レスポンシブでは横幅も高さも変化します。高さを指定していると余分な余白ができてしまったり、逆にコンテンツが隠れてしまう恐れがあります。

```scss
// Bad
.foo {
  height: 1000px;
}
```

#### 固定の幅を持たせない
コンテンツは基本的に親要素に対して横幅100%の表示になるようにします。横幅を固定すると、横幅が足りなかったり、はみ出してしまう恐れがあります。

幅を変更するときは、コンテナブロックにmax-widthを指定する、widthのヘルパークラスを指定する、Modifierでサイズを指定するなどの方法があります。

```scss
// Good
.button {}

.wrapper {
  max-width: 1200px;
}

.c-button--size-full {
  width: 100%;
}

// Bad
.button {
  width: 300px;
}
```

#### カラーコードは短縮する
colorプロパティなどで色を指定する時は可読性をあげるために、可能な場合は短縮します。

```scss
// Good
.foo { color: #ddd; }

// Bad
.foo { color: #dddddd; }
```

#### 文字列には引用符（ダブルクォート）をつける
contentプロパティやURLの指定、属性セレクタの指定をする時にはダブルクウォートを使用します。

```scss
// Good
.foo {
  content: "this is content";
  background: url("logo.png");
}
input[type="submit"] {}

// Bad
.foo {
  content: 'this is content';
  background: url(logo.png);
}
input[type=submit] {}
input[type='submit'] {}
```

#### 0に単位をつけない
値が0の場合はpxや%といった単位は必要がないため指定しません。

```scss
// Good
.foo { margin: 0; }

// Bad
.foo { margin: 0px; }
```

#### 小数点のあたまの0を省略しない
0.5emなどの小数点の前の0は省略しません。ファイルサイズの削減は考えずに、明示的に指定します。

```scss
// Good
.foo { font-size: 0.5em; }

// Bad
.foo { font-size: .5em; }
```

#### すべて小文字で記述する
プロパティとプロパティ値は大文字でも小文字でも同様に扱われますが、小文字に統一します。

```scss
// Good
.foo { color: #fff; }

// Bad
.foo { COLOR: #FFF; }
```

#### ショートハンドはなるべく避ける
font-sizeやmarginなどのショートハンドプロパティの使用は必要がなければ避けます。ショートハンドプロパティに渡さなかったプロパティに初期値が指定されてしまい、思わぬスタイルが当たってしまう恐れがあります。

必要なプロパティにだけ指定するほうがコードの目的も分かりやすくなります。

```scss
// Good
.foo { 
  // 中央配置にする。
  margin-right: auto;
  margin-left: auto;
}

// Bad
// 上下を0に指定する必要がない。
.foo { margin: 0 auto; }
```

#### ベンダープレフィックスは手動で書かない
-webkit-や-moz-などのベンダープレフィックスは手動で書かず、[Autoprefixer](https://github.com/postcss/autoprefixer)を使います。自動化したほうが確実で、管理する必要がなくなります。

### Sass
#### 変数を作りすぎない
CSSフレームワークではコンポーネントごとに大量の変数が定義されています。これはコードを変更せずに、変数で見た目を変更するというCSSフレームワーク特有の使い方です。通常の案件では必要ない場合が多いです。

変数を定義する条件は以下の2つです。

1. 同じ値を繰り返し使う
1. 四則演算などで値を変える必要がある

```scss
// Good
$unit-base: 1em !dafault;
.foo {
  margin-left: (-$unit-base);
}
.foo__bar {
  padding-left: $unit-base;
}
```

#### 変数にはdefaultフラグを指定する
変数を定義するときには`!default`フラグも指定します。同じ変数名の場合、先に定義した変数が適応されるというシンプルなルールになるので管理がしやすくなります。

```scss
// Good
$unit-base: 1em !default;

// Bad
$unit-base: 1em;
```

#### 変数名と関数名のつけかた
`$color-red`のような直接的な名前の変数は`$color-error`のような変数に代入してから指定します。`$color-red`を直接指定すると、変数の値を変えたときに変数名と値の意味が合わなくなってしまう恐れがあります。指定した変数を付け替えるのではなく、定義した変数の値で一括管理できるようにします。

```scss
// Good
$color-red: red;
$color-error: $color-red;
.foo { color: $color-error; }

// Bad
$color-red: red;
.foo { color: $color-red; }
```

変数や関数には接頭辞をつけます。CSSフレームワークやライブラリを併用する場合に名前が重複する可能性があるからです。プロジェクト名から2文字をとって`.pj-`のようにするといいでしょう。

```scss
// Good
$my-variable: "variable";
@funciton my-function() {}
@mixin my-mixin() {}

// Bad
$variable: "variable";
@funciton function() {}
@mixin mixin() {}
```

Sassの変数と関数ではハイフン（`-`）とアンダースコア（`_`）は同じものとして扱われるので注意します。例えば以下の変数名は同一視されるので、`$foo--base`は常に上書きされて適応されません。

```scss
$foo--base: black; // 上書きされる
$foo__base: red;
```

!defaultフラグを指定すると逆に`$foo__base`が上書きされて適応されません。

```scss
$foo--base: black !default;
$foo__base: red !default; // 上書きされる
```

#### extendはできるだけ使わない
extendはCSSに継承の機能を持ち込むことができますが、継承元のセレクタがある場所に継承先のセレクタも指定されるのでカスケーディングを複雑にしてしまう恐れがあります。CSSにコンパイルされたときに、ルールセットに大量のセレクタが指定されてしまい、コードを理解しにくくなってしまう恐れもあります。

extendを使う場合は以下の点に気をつけます。

* 同じモジュール内で完結させる
* コメントによって補足する
