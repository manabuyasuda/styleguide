# CSS Styleguide
## はじめに
このスタイルガイドはCSSを扱う上でベターだと思われる方法をまとめたものです。必ずしも正しいものかは分かりません。このスタイルガイドの目的は共通の認識や知識を得ることにあります。

このスタイルガイドは以下の3つのドキュメントに大きく影響を受けています。

* [CSS Guidelines (2.2.4) – High-level advice and guidelines for writing sane, manageable, scalable CSS](http://cssguidelin.es/)
* [Sass Guidelines](http://sass-guidelin.es/)
* [hiloki/flocss](https://github.com/hiloki/flocss)

### 基本的な考え方
CSSという言語の特性について考える前に基本となる考え方を定義します。ベストな方法があったとしても、そのプロジェクトの規模やチームメンバーの能力によっては実現が困難であったり、より適した方法があると考えられるからです。大切なことは実現可能な方法で結果を出し続けることです。

* 一貫性を保つこと。
* いつ読んでも、誰が読んでも理解できるようにコードを書くこと。
* 共通のルールを決めて周知し、遵守すること。
* 知識の差をなるべく埋め、認識のズレを少なくすること。
* 不必要な複雑さを避け、簡潔に単純にすること（「KISSの原則」）。
* 繰り返しや重複を避けること（「DRYの原則」）。

### CSSアーキテクチャのゴール
[Philip Walton](https://github.com/philipwalton)は[CSS Architecture](http://philipwalton.com/articles/css-architecture/)（[日本語訳](http://article.enja.io/articles/css-architecture.html)）のなかで、まずゴールを定めることが大切だと語っています。そして、そのゴールは予測、再利用、保守、拡張しやすいものであるべきだとしています。

> 予測しやすいCSSとはルールが期待通りに振る舞うことを意味する。ルールを追加・更新したとき、そのルールが意図せずサイトの一部に影響を与えるべきではない。滅多に変更されない小規模なサイトであれば、このことはあまり重要ではないが、数十、数百ページの大規模なサイトであれば、予測しやすいCSSは必須といえる。
> 
> CSSのルールは抽象的で、十分に分離されているべきである。それはパターンとすでに解決した問題を書きなおす必要なく、既存のパーツから新しいコンポーネントを速くつくることができるということだ。
> 
> サイトに新しいコンポーネントと機能が追加・更新されるか、再編される必要があるとき、既存のCSSのリファクタリングを必要とすべきではない。ページにコンポーネントXを追加するときに、そのわずかな存在によってコンポーネントYを壊すべきではない。
> 
> サイトが大きく、複雑に成長していくにつれて、通常はたくさんのデベロッパがメンテナンスのために必要となる。 拡張しやすいCSSとはひとりのデベロッパか、大きなエンジニアチームかを問わず、容易に管理できることを意味する。またそのサイトのCSSアーキテクチャに、巨大な学習曲線を必要することなく容易に近づけるという意味でもある。あなたが今日CSSを触る唯一のデベロッパだからといって、先々にも常にあなただけであるというわけではない。

これらのゴールを達成するために、より適切な方法を選択しましょう。できる限り、技術的なハードルが低く、学習コストの低い方法が望ましいです。

## ファイル構成
CSSには詳細度という仕組みがあります。詳細度が同じであれば、あとで記述したスタイルが適応されますが、詳細度の強さが違う場合は必ずしも適応されるわけではありません。

理想的にいえば詳細度はほとんど一定であることが望ましいです。そうでない場合は、スタイルシートの序盤は詳細度が弱く抽象的で一般的なスタイルを持ち、終盤になるにしたがって詳細度が強く具体的なスタイルが指定されるべきです。

詳細度は[Specificity Calculator](http://specificity.keegan.st/)というサービスで確認することができます。

### FLOCSS
ファイルの構成は[FLOCSS](https://github.com/hiloki/flocss)（フロックス）をベースにするのを推奨します。FLOCSSはFoundation、Layout、Objectの3つのレイヤーから構成されます。ObjectレイヤーはさらにComponent、Project、Utilityの3つの子レイヤーに分けられます。レイヤーごとに詳細度に関するルールを定義することで、カスケーディングを管理します。以下はそれぞれの特徴を示したものです。

1. Foundation - Normalize.cssやタイポグラフィーなどプロジェクトのベースになるスタイルが該当します。詳細度は極力抑えるべきで、要素セレクタや属性セレクタなどに最低限のスタイルを指定します。
1. Layout - ワイヤーフレームに定義されるような大きなコンテナブロックが該当します。このレイヤーでのみIDセレクタを指定することが許容されます。
1. Object - OOCSSのコンセプトを元にしたビジュアルパターンが主に該当します。classセレクタのみで構成されます。
1. Component - 多くのプロジェクトで横断的に再利用できるような、小さな単位のモジュールが該当します。OOCSSでの構造を担うため、固有の幅や装飾的なスタイルは極力指定しないようにします。
1. Project - プロジェクト固有のビジュアルパターンでいわゆるUI（ユーザーインターフェイス）が該当します。ほとんどのスタイルの追加はこのレイヤーになります。
1. Utility - いわゆる汎用クラスのことで、主に単一のスタイルを持っています。ComponentレイヤーとProjectレイヤーで指定するのが適切でない例外的なスタイルや汎用的に使えるスタイルが定義されています。オプションとして`!important`を許容しています。

#### レイヤーを追加する
FLOCSSにないレイヤーを追加することもできます。ただし、詳細度の管理に支障が出ないように追加する場所には注意しましょう。例えば以下のようなレイヤーが考えられます。

* Variable - プロジェクトでグローバルに使用される変数を定義します。
* Function - プロジェクトでグローバルに使用される@functionを定義します。
* Mixin - プロジェクトでグローバルに使用される@mixinを定義します。
* Vendors - Normalize.cssやBootstrapのような外部のライブラリやCSSフレームワークです。
* Vendors-extension - Vendorsレイヤーを上書きするためのレイヤーです。
* Theme - SMACSSのThemeモジュールに該当します。defaultテーマやdarkテーマのようにページ全体のカラーを変更する場合に使用します。多言語化対応のためのスタイル変更なども該当するでしょう。
* Scope - 例えばブログの投稿用のスタイルを要素セレクタに指定したい場合などに使用します。classセレクタと要素セレクタの子孫セレクタでスタイルを指定するため、classセレクタを要素ごとに指定する必要がなくなります。
* QA/Test - Quality Assurance（品質保証）、もしくはテストのための一時的なスタイルを定義するレイヤーです。

追加するレイヤーについては以下の記事を参考にします。

* [More Transparent UI Code with Namespaces – CSS Wizardry](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/)
* [Managing CSS Projects with ITCSS](http://csswizardry.net/talks/2014/11/itcss-dafed.pdf)

レイヤーは以下のような構成にします。（Vendorsなど）レイヤーによっては子レイヤーではなく、親レイヤーとしてもいいでしょう。また、CSSフレームワークやJQueryプラグインなどの装飾的な要素を多く含むモジュールは追加する場所を変更しなければいけないかもしれません。

* Foundation
 * Variable
 * Function
 * Mixin
 * Vendors
 * Vendors-extension
 * Base
* Layout
* Object
 * Component
 * Project
 * Theme
 * Scope
 * QA/Test
 * Utility

FLOCSS以外では[ITCSS](https://speakerdeck.com/dafed/managing-css-projects-with-itcss)が候補としてあげられます。ITCSSはFLOCSSとほとんど同じ考えをもっています。ただし、英語のドキュメントであり、詳細な説明があまりないためFLOCSSのほうが導入がしやすいと考えています。

### Sassのパーシャル機能で@importする
Sassのパーシャルを使用するとファイルを機能ごとに分割して管理をして、CSSにコンパイルするときに指定した順序でアウトプットすることができます。

```scss
/* ==========================================================================
   Layout
   ========================================================================== */

@import "layout/_footer";
@import "layout/_header";
@import "layout/_main";
@import "layout/_sidebar";

/* ==========================================================================
   Object
   ========================================================================== */

/* Component
   ----------------------------------------------------------------- */

@import "object/component/_wrapper";
@import "object/component/_grid";
```

## シンタックスとフォーマッティング
CSSの構文はセレクタとブレース、プロパティと値からなっています。このルールセットに読みやすさを確保したフォーマット（書式）を定義します。

### ルールセットのフォーマット
ルールセットの基本的なフォーマットは以下の通りです。

* 複数のセレクタは別の行に（関連性があれば同じ行に指定してもいい）
* 最後のセレクタとオープニングブレースの間にスペースを1つ
* それぞれの宣言は別々の行に
* ルールセット内に空行は入れない
* プロパティの前にスペースを4つ（2つにしてもいい）
* コロン（`:`）と値の間にスペースを1つ
* クロージングブレースは独立した行に
* 各ルールセットの間に空行を1つ
* 関数はカンマ（`,`）と引数の間にスペースを1つ

```css
/* Good */
.foo, .foo--bar,
.baz {
    display: block;
    margin-right: auto;
    margin-left: auto;
    background-color: rgba(0, 0, 100, 0.8);
}

/* Bad */
.foo,
.foo--bar, .baz{
  display: block;margin-right: auto;
  margin-left:auto;
  background-color: rgba(0,0,100,0.8);}
```

### Sass使用時の追加ルール
Sassを使用している場合は以下のルールを追加します。

* ローカル変数を最初に定義します
* @extendをローカル変数の次に指定します
* @mixinを@extendの次に指定します
* @contentを使用している@mixinは最後に指定します

```scss
/* Good */
.foo, .foo--bar,
.baz {
    $padding: 1em;
    @extend %base-unit;
    @include clearfix;
    display: block;
    margin-right: auto;
    margin-left: auto;
    padding-right: $padding;
    padding-left: $padding;
    @include media-query(md) {
        padding-right: ($padding * 2);
        padding-left: ($padding * 2);
    }
}

/* Bad */
.foo,
.foo--bar, .baz{
  @include media-query(md) {
    padding-right: ($padding * 2);
    padding-left: ($padding * 2);
  }
  display: block;margin-right: auto;
  margin-left:auto;
  @extend %base-unit;
  @include clearfix;
  $padding: 1em;}
```

### 単一行ルールセットのフォーマット
原則的には複数行でルールセットを記述しますが、ユーティリティクラスなどの単一のスタイルの場合は1行で記述することを許容します。この場合は複数行よりも可読性に優れていると考えられるからです。

単一行のフォーマットは以下の通りです。

* 最後のセレクタとオープニングブレースの間にスペースを1つ
* オープニングブレースとプロパティの間にスペースを1つ
* コロン（`:`）と値の間にスペースを1つ
* セミコロン（`;`）とクロージングブレースの間にスペースを1つ
* 各ルールセットの間には空行を入れない

```css
/* Good */
.u-pos { position: static !important; }
.u-por { position: relative !important; }
.u-poa { position: absolute !important; }
.u-pof { position: fixed !important; }
```

### プロパティの宣言順
ルールセット内の宣言は重要なプロパティから書き始め、その機能ごとに固めて記述することで意図を素早く理解できるようにしましょう。プロパティの宣言順は以下のようなルールで記述するのを推奨します。

1. ボックスモデルの種類や表示方法を示すプロパティ（`box-sizing`, `display`, `visibility`, `float`など）
1. 位置情報に関するプロパティ（`position`, `z-index`など）
1. ボックスモデルのサイズに関するプロパティ（`width`, `height`, `margin`, `padding`, `border`など）
1. フォント関連のプロパティ（`font-size`, `line-height`など）
1. 色に関するプロパティ（`color`, `background-color`など）
1. それ以外

アルファベット順で記述することは禁止します。整列ではなく、理解しやすいように分類することが目的だからです。

```css
/* Good */
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

/* Bad */
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
例えば`margin`プロパティは`margin-top`や`margin-left`のように個別に方向を指定することもできます。このようなプロパティは規則性を持たせるため、時計回りに指定するのを推奨します（`position`プロパティの`top`や`left`も同様です）。

```css
/* Good */
.foo {
    margin-top: auto;
    margin-right: auto;
    margin-bottom: auto;
    margin-left: auto;
}

/* Bad */
.foo {
    margin-left: auto;
    margin-right: auto;
    margin-top: auto;
    margin-bottom: auto;
}
```

## コメント
コードの機能やルールの意図、目次やTODOを示すために積極的にコメントを使用しましょう。誰もが同じ知識を持っているわけではありませんし、必要なコードを誤って削除してしまう可能性を下げることができます。

### 目次（Table of Contents）
コードを書く前に目次を書きましょう。そしてコードを書いたら目次に追加しましょう。スタイルシートの全体像を把握するのを助けてくれますし、目次から目的のコードを検索することもできるようになります。レイヤーとその中のセクション（モジュール）を簡単な説明も加えて書いていきましょう。

```css
/**
 * VARIABLE
 * Global...............プロジェクト全体を通して使用される変数です。
 *
 * MIXIN
 * Media-query..........メディアクエリ用のmixinです。
 * Clearfix.............`float`を解除する`clearfix`のmixinです。
 *
 * BASE
 * Base.................要素セレクタと属性セレクタのデフォルトスタイルです。
 *
 * LAYOUT
 * Header...............ヘッダーエリアのコンテナブロックのスタイルを指定します。
 * Footer...............フッターエリアのコンテナブロックのスタイルを指定します。
 * Main.................メインコンテンツエリアのコンテナブロックのスタイルを指定します。
 * Sidebar..............サイドバーエリアのコンテナブロックのスタイルを指定します。
 *
 * COMPONENT
 * Wrapper..............コンポーネントをラップするオブジェクトです。`max-width`が指定されています。
 * Grid.................グリッドレイアウトのベーススタイルです。
 *
 * PROJECT
 * Tag-cloud............タグクラウドのコンポーネントです。
 *
 * UTILITY
 * Width................レスポンシブに対応した`width`を指定するためのヘルパークラスです。
 */
```

### セクションタイトル
目次とセクションタイトルは対（つい）になります。セクションタイトルに`#`を接頭辞として付けることで素早く検索することができるようになります。

```css
/**
 * #Grid
 */
```

### レイヤータイトル
FLOCSSでは3つのレイヤーと3つの子レイヤーで構成されます。Sassを使用していない場合は以下のようにコメントをします。

```css
/* ==========================================================================
   Layout
   ========================================================================== */
/**
 * #Footer
 */
 #footer {}
 
/**
 * #Header
 */
#header {}

/* ==========================================================================
   Object
   ========================================================================== */

/* Component
   ----------------------------------------------------------------- */

/**
 * #Wrapper
 */
 .c-wrapper {}
```

Sassを使用する場合は以下のようにコメントをしてインポートします。

```scss
/* ==========================================================================
   Layout
   ========================================================================== */

@import "layout/_footer";
@import "layout/_header";
@import "layout/_main";
// @import "layout/_sidebar";

/* ==========================================================================
   Object
   ========================================================================== */

/* Component
   ----------------------------------------------------------------- */

@import "object/component/_wrapper";
@import "object/component/_grid";
```

編集をしやすくするために、以下のようなルールで`@import`を使用します。

* 1つのファイルごとに@importを使用します
* @importごとに改行します
* 同じフォルダからのインポートの間には改行をしません
* 同じフォルダからの最後のインポートの後には改行をします
* ファイルの拡張子（.scss）を省略します

### コードの説明と参照リンク
そのコードが一部のひとにしか正しく理解されないと感じたらコメントを書きましょう。そして、詳しくコメントで説明できない場合は参照となるリンクを書きましょう。

```css
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

### マークアップ例
コンポーネントなど、頻繁に使用されるコードには簡単な説明とマークアップの例を書きましょう。

```css
/**
 * グリッドレイアウトオブジェクトです。
 * `width`はデフォルトで100%が指定されています。
 * ガターは左側にだけ指定されています。
 * `width`の変更は`Utility/_width.scss`のクラスを使用します。
 *
 * <div class="c-wrapper">
 *   <div class="c-grid c-grid--gutter-medium">
 *     <div class="c-grid__item u-8/12@md"></div>
 *     <div class="c-grid__item u-4/12@md"></div>
 *   </div>
 * </div>
 */
.c-grid {
    display: block;
    margin: 0;
    padding: 0;
    /* カラム間の余白を除去する */
    font-size: 0;
    list-style-type: none;
}

.c-grid__item {
    display: inline-block;
    width: 100%;
    /* フォントサイズを戻す */
    font-size: 1rem;
    vertical-align: top;
}
```

### Sassのコメント
Sassでは@functionや@mixinなど通常のCSSにはない機能があります。Sassを熟知していないひとのためにもコメントを書いて正しく理解してもらえるようにしましょう。以下は@mixinのコメント例です。

```scss
//
// #Media-query
//

// @desc - ブレイクポイントを挿入します。
// @type Mixin
// @param {String} $breakpoint - `$breakpoints`のkeyを渡します。
// @requires {Map} $breakpoints - ブレイクポイントを管理するためのmapです。
//
// @example scss - Usage
// .foo {
//     color: red;
//     @include media-query(sm) {
//         color: blue;
//     }
// }
//
// @example css - CSS output
// .foo {
//   color: red;
// }
// @media screen and (min-width: 400px) {
//   .foo {
//     color: blue;
//   }
// }
//
@mixin media-query($breakpoint) {
    @media #{map-get($breakpoints, $breakpoint)} {
        @content;
    }
}
```



## 命名規則
命名規則はBEMをベースにした[MindBEMding](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)の使用を推奨します。理由は名前から役割や関係性を明確にできるからです。これはコードを書く時でも読む時でも同様です。短く単純な名前より、長くても明快な名前の方が機能的で、誰にとっても使いやすいものになるはずです。

BEMはルールが厳格で導入が難しいと一般的には言われますが、ルールがあるからこそ理解しやすく、共通の認識を持つことができると考えています。

BEMについては以下のリンクを参考にしてください。

* <a href="https://github.com/juno/bem-methodology-ja/blob/master/definitions.md" target="_blank">bem-methodology-ja/definitions.md at master · juno/bem-methodology-ja</a>
* <a href="http://blog.ruedap.com/2013/10/29/block-element-modifier" target="_blank">BEMという命名規則とSass 3.3の新しい記法 - アインシュタインの電話番号</a>
* <a href="https://app.codegrid.net/entry/bem-basic-1" target="_blank">BEMによるフロントエンドの設計 - 基本概念とルール | CodeGrid</a>

### MindBEMding
BEMはBlock、Element、Modifierの3つの概念から構成され、以下のようなclass名になります。

```css
.block-name {}
.block-name__element-name {}
.block-name--modifierkey-value {}
.block-name__element-name--modifierkey-value {}
```

以下のようなルールがあります。

* 小文字の英単語とハイフン1つ（`-`）とハイフン2つ（`--`）、アンダースコア2つ（`__`）で構成されます
* BlockとElementとModifierの単語はハイフン1つ（`-`）でつなぎます
* Blockはできるだけ2つ以上の単語を合わせてユニークな名前にします
* ElementとModifierは1つの単語だけを使ってもかまいません
* ElementとModifierはBlockの名前を受け継ぎます
* BlockとElementはアンダースコア2つ（`__`）でつなぎます
* BlockとModiferもしくはElementとModiferはハイフン2つ（`--`）でつなぎます

よって以下のように短く書くこともできます。

```css
/* Good */
.block-name {}
.block-name__element {}
.block-name--modifier {}
.block-name__element--modifier {}
```

### BEMの注意点

#### Elementの連結をさせない
Elementの入れ子であることを示すためにElementの名前を連結させてはいけません。HTMLの構造を示すのではなく、Blockに対するElementとModifierの関係性を示すようにしましょう。例えば`sub`や`child`といった名前によって関係性を表しましょう。

```css
/* Good */
.block__element {}
.block__sub-element {}

/* Bad */
.block__element {}
.block__element__element {}
```

もしくは、入れ子のElementを別のBlockとして定義できないか考えましょう。Elementとして拡張するよりは、Blockを組み合わせたほうが拡張性が高くできることがあります。

#### マルチクラスを禁止にしない
「OOCSSはマルチクラスでBEMはシングルクラスだ」などと言われることがありますが、そういったことはありません。[Yandex](https://www.yandex.com/)のソースコードを見てもマルチクラスで指定されています。例えば同じHTML要素に異なるBlockやElementを指定したとしても妥当な指定方法です(OOCSSの構造と見た目を別々に指定したい時など)。

```html
<!-- Good -->
<div class="block1 block2">
  <div class="block1__element block2__element">
</div>
```

#### Blockを親セレクタにしない

詳細度を強くしたり、スコープを作るためにBlockを親セレクタにしてElementとModifierを指定しないようにしましょう。Blockの名前を継承することで擬似的にスコープを作るようにしましょう。詳細度はなるべくフラットに保ってカスケーディングのコントロールをしやすいようにします。

```css
/* Good */
.block {}
.block__element {}
.block--modifier {}

/* Bad */
.block {}
.block .block__element {}
.block .block--modifier {}
```

#### 他のBlockに依存させない
Blockは他のBlockに依存せず、単独で機能するようにしましょう。例えば、`Block1`のスタイルを変更するために`Block2`を親セレクタにして`Block1`を上書きするようなことはできるだけ避けましょう。同じ詳細度であれば、セレクタを加えなくても後から記述するだけで適応されます。

```css
/* Good */
.block1 {}
.block__element {}

.block2 {}

/* Bad */
.block1 {}
.block__element {}

.block2 .block1 {}
```

また、例えば`Block1`と`Block2`を左右に配置したい場合は、グリッドのようにレイアウトをするための`Block3`を定義するようにしましょう。Blockの機能を拡張しすぎたり、依存関係を作ってしまうよりも、Blockを組み合わせることでページを構築するようにします。

#### Sassのネストを制限する
Sassを使用している場合、ネストによってBEMの記述が楽になりますが、なるべく使用しないようにしましょう。理由はBlockに関連するルールセットが際限なくつながり、長くなることで可読性やメンテナンス性を損なう可能性が高いからです。

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

### 接頭辞（Prefix）
接頭辞（Prefix）をclassセレクタとIDセレクタに使用することで、名前が重複する可能性を下げたり、その役割を簡潔に示すことができます。FLOCSSで定義されている接頭辞に以下のような接頭辞を加えて使用するのを推奨します。

* `c-` Componentレイヤーのコンポーネントあることを示します
* `p-` Projectレイヤーのコンポーネントであることを示します
* `u-` Utilityレイヤーのコンポーネントであることを示します
* `js-` JavaScriptのフックとして使用されるclassやIDであることを示します（CSSでスタイルは指定しない）
* `is-`, `has-` 要素の表示の切り替えなどの状態の変化があることを示します
* `t-` Themeレイヤーのコンポーネントであることを示します。
* `s-` Scopeレイヤーのコンポーネントであることを示します。
* `qa-`, `t-` QA/Testレイヤーのコンポーネントであることを示します。

### 接尾辞（Suffix）
classに`-sm`や`-md`などを付与してブレイクポイントを指定していることを示すことがあります。規則性をもたせるためにクラス名の最後に接尾辞を付けることを推奨します。より明示的に名前をつけるために`@`を接尾辞として使用してもいいでしょう。

```css
/* Good */
@media (min-width: 768px) {
    .u-col1of3\@md { width: width: 33.33333% !important; }
    .u-col2of3\@md { width: width: 66.66667% !important; }
    .u-col3of3\@md { width: width: 100% !important; }
}
```

`@`をスタイルシートで指定する際にはバックスラッシュ（`\`）でのエスケープが必要ですが、HTMLで指定する際にはエスケープは必要ありません。詳しくは以下の記事を参照してください。

[BEMIT: Taking the BEM Naming Convention a Step Further | CSS Wizardry](http://csswizardry.com/2015/08/bemit-taking-the-bem-naming-convention-a-step-further/)

### 単語の省略を制限する
意味が汲み取れない単語の省略は禁止します。例えば`navigation`を`nav`と省略することは許容しますが、`title`を`ttl`と省略するのは意味が読み取れませんし、ほとんど短くなってもいません。基本的には省略をせずに名前をつけましょう。

```css
/* Good */
.navigation {}
.title {}
.text {}
.icon {}

/* allow */
.nav {}

/* Bad */
.ttl {}
.txt {}
.ico {}
```

省略する場合にはドキュメントに残すことを推奨します。認識を共有することもできますし、`nav`と`navi`が混在してしまうようなことを防ぐこともできます。

### 絶対値を表すクラス名を避ける
`.mb10`のような値を固定した名前をつけることを禁止します。理解はしやすい名前ですが、値を変更したい場合にHTMLに記述したclass属性も変更する必要が出てきます。こうした変更に柔軟に対応するために`.mbs`（margin-bottom smallの意味）のような相対的な名前をつけましょう。

```css
/* Good */
.mbs { margin-bottom: 10px; }

/* Bad */
.mb10 { margin-bottom: 10px; }
```

また、`small`, `medium`, `large`のようなパターンに制限するルールを設けることで、際限なくサイズ違いが増えてしまうことを防ぐこともできます。

カラーネーム（`.red`や`.blue`）のようなクラス名も禁止します。ある要素の色を変更したい場合にクラス名と値の意味が合わなくなってしまい、class属性を変更しなくてはいけなくなることが考えられるからです。`.success`や`.brand`のような値を変更しても意図が変わらない名前にしましょう。

```css
/* Good */
.brand {}
.success {}

/* Bad */
.red {}
.blue {}
```

## セレクタ
### インライン記述を使用しない
HTMLの`style`属性に直接スタイルを記述するインライン記述は使用を禁止します。理由はスタイルシートで一括管理できないこと、詳細度が高いこと、HTMLのファイルサイズを無闇に増やしてしまうこと、単一のスタイルごとしか指定できない（レスポンシブに対応できない）からです。

```html
<!-- Bad -->
<div style="color:red; background-color: black;"></div>
```

JavaScriptによるインラインでのスタイル追加もできるだけ避けて、classを追加するようにします。

### HTML（DOM）構造に依存させない
マークアップは変更される可能性があります。例えば`article`を`section`にしたり`h2`を`h3`に変更するかもしれません。特定のHTML要素に依存したセレクタを指定している場合はその都度変更しなくてはいけません。

```css
/* Bad */
article h2 {}
```

classを使ったセレクタを定義していればHTML構造が変更されたとしても、CSSを変更する必要がなくなり、可搬性が高まります。

```css
/* `article`要素の見出しに指定するクラス */
.article__title {}

/* 汎用的な見出しのクラスを用意 */
.u-h2 {}
```

### 不要な要素セレクタを連結させない
classやIDセレクタで指定する時に要素セレクタを連結させるのは詳細度を高めるだけで意味がありません。

```css
/* Good */
.foo {}

/* Bad */
h2.foo {}
```

もし、特定の要素に指定してほしいのであれば、コメントによってそれを示しましょう。

```css
/* Good */
/* h2要素に指定してください。 */
.foo {}

/* h2 */.foo {}
```

### セレクタを短くする
セレクタは必ずしもHTML構造に沿う必要はありません。場合によってはセレクタを短くすることで詳細度を低く抑えることができます。

```css
/* Good */
ul a {}
th {}

/* Bad */
ul li a {}
table th {}
```

### セマンティックでないセレクタの指定を制限する
特定の意味を持たないセレクタ（`div`, `span`）は使用される範囲も広いため、セレクタに対する影響範囲も広くなります。使用する場合はできるだけ範囲を狭くして指定してください（classセレクタで指定するのが望ましいです）。また、単体でのセレクタは禁止とします。

```css
/* Good */
.foo > div {}
.foo > span {}

/* Bad */
div {}
span {}
.foo div {}
.foo span {}
```

### 拡張できるようにオブジェクトを定義する
保守性や拡張性を高めるために構造を含んだベースになるclassを定義し、装飾的なclassで拡張するようにしましょう。ベースになるスタイルを変更したい場合でも1箇所変更するだけでよくなりますし、拡張するclassにも最低限のスタイルを追加するだけになります。

1つのclassでまとめて指定してしまうと、バリエーションが増えるごとに同じスタイルを何回も指定することになってしまいます。ベースになるスタイルを変更したい場合に何箇所も変更する必要も出てきます。

下記の例ではベースになるclassの`.p-alert`に装飾的なスタイルが指定されていますが、オブジェクトの特性上、問題はごくわずかだと判断しています。

```html
<!-- Good -->
<p class="p-alert"></p>
<p class="p-alert p-alert--success"></p>
<p class="p-alert p-alert--error"></p>
```

```css
/* Good */
.p-alert {
    padding: 1em;
    border: 1px solid #e7edf0;
    border-radius: 3px;
    color: #47525d;
    background-color: #f7f9fa;
}

.p-alert--success {
    border-color: #d9f4e1;
    color: #2c683f;
    background-color: #edfaf1;
}

.p-alert--error {
    border-color: #ffcaca;
    color: #921515;
    background-color: #ffe3e3;
}

/* Bad */
.p-alert-default {
    padding: 1em;
    border: 1px solid #e7edf0;
    border-radius: 3px;
    color: #47525d;
    background-color: #f7f9fa;
}

.p-alert-success {
    padding: 1em;
    border: 1px solid #d9f4e1;
    border-radius: 3px;
    color: #2c683f;
    background-color: #edfaf1;
}

.p-alert-error {
    padding: 1em;
    border: 1px solid #ffcaca;
    border-radius: 3px;
    color: #921515;
    background-color: #ffe3e3;
}
```

### IDセレクタを多用しない
CSSはカスケーディングをコントロールしなければ、すぐに破綻してしまいます。IDセレクタは詳細度が圧倒的に高いことや、ページ内に1度しか使用できないため再利用性にも欠けます。classではなくIDで指定する理由が明確に説明できる場合にのみ使用しましょう。

CSSのIDセレクタとHTMLのid属性とは区別して考えます。id属性はページ内リンクやJavaScriptでのフックとして利用される場合があるためです。

### メディアクエリをまとめて管理しない
ファイル容量の削減のためにメディアクエリをブレイクポイントごとにまとめて記述することを禁止します。

```css
/* Good */
.foo {}

@media screen and (min-width: 768px) {
    .foo {}
}

/* Bad */
@media screen and (min-width: 768px) {
    .foo {}
    .bar {}
    .baz {}
}
```

同じ目的のオブジェクトは同じ場所（Sassの場合は同じファイル）に記述するようにしましょう。全体像の把握を難しくさせたり、モジュール（ある機能のひとまとまり）として管理するのを難しくさせます。短期的なパフォーマンスよりも見通しの良さやメンテナンス性を重視しましょう。

## プロパティ
### !importantを多用している
`!important`は基本的に使用を禁止しますが、意図的に使用する場合は許容されます。例えばヘルパークラスは確実に適応させるために使用することがあります。

```css
/* Good */
.u-col1of12 { width: 8.33333% !important; }
.u-col2of12 { width: 16.66667% !important; }
.u-col3of12 { width: 25% !important; }
```

その場合もスタイルシートの終盤に記述をして、影響範囲を抑えましょう。

### スタイルを取り消している
スタイルの取り消しとは、あるスタイルを`0`や`none`などで値をリセットすることです。スタイルの取り消しがあった場合は多くのスタイルを持ちすぎている、スタイルを早く指定してしまっていることが考えられます（リセットCSSは除く）。

```css
/* Bad */
.foo {
    border: 1px solid #ddd;
}

/* ある要素内ではボーダーが邪魔になるためリセット */
.bar .foo {
    border: none;
}
```

Modifierで定義しておくと必要な時にだけ指定することができます。

```css
/* Good */
.foo {}
.foo--bordered {
    border: 1px solid #ddd;
}
```

### 要素セレクタに装飾的なスタイルを指定しない
`h1`や`p`といったセマンティックな要素セレクタには装飾的なスタイルを指定してはいけません。必ずあとでスタイルを取り消すことになります。

```css
/* Bad */
h2 {
    padding-bottom: 0.5em;
    border-bottom: 1px solid #888;
}
```

要素セレクタにはブラウザのデフォルトスタイルシートやリセットCSSで指定されるような最低限のスタイルにとどめておきましょう。

```css
/* Good */
h2 {
    font-size: 2rem;
    line-height: 1.2;
    margin-top: 0;
    margin-bottom: 24px;
}
```


### 高さを指定しない
閲覧されるデバイスによって同じコンテンツであっても横幅が変わるため高さも可変します。高さを指定しているとコンテンツが隠れてしまったり、余分な余白ができてしまいます。

```css
/* Bad */
.foo {
    height: 1000px;
}
```

高さは指定せず、コンテンツによって決まるものと考えましょう。ロゴなどの特定のサイズを持ったものはこれに当たりません。

### 固定の幅を持たせない
コンテンツブロックは基本的に固有の幅を持つことを禁止します。`width`を固定していると、ある場所では幅が足りず、ある場所でははみ出てしまうなどの弊害が出ます。横幅は可変するようにしておいて、コンテナブロックによってサイズが決まるようにしておきます。

幅を指定したい時は、コンテナブロックに`max-width`を指定する、`width`のヘルパークラスを使用する、Modifierでサイズを指定するなどの方法があります。

```css
/* Good */
.c-button {}

.c-wrapper {
    max-width: 1200px;
}
.c-button--size-full {
    width: 100%;
}

/* Bad */
.c-button {
    width: 300px;
}
```

### カラーコードは短縮させる
`color`プロパティなどで色を指定する時は可読性をあげるために、可能な場合は短縮しましょう。

```css
/* Good */
.foo { color: #ddd; }

/* Bad */
.foo { color: #dddddd; }
```

### 文字列には引用符（ダブルクォート）をつける
`content`プロパティやURLの指定、属性セレクタの指定をする時にはダブルクウォートを使用しましょう。

```css
/* Good */
.foo {
    content: "this is content";
    background: url("logo.png");
}
input[type="submit"] {}

/* Bad */
.foo {
    content: 'this is content';
    background: url(logo.png);
}
input[type=submit] {}
input[type='submit'] {}
```

### 0に単位をつけない
値が0の場合はpxや%といった単位は必要がないため使用しません。

```css
/* Good */
.foo { margin: 0; }

/* Bad */
.foo { margin: 0px; }
```

### 小数点のあたまの0を省略しない
`0.5em`などの小数点の前の0は省略しません。ファイルサイズの削減は考えずに、明示的に指定しましょう。

```css
/* Good */
.foo { font-size: 0.5em; }

/* Bad */
.foo { font-size: .5em; }
```

### すべて小文字で記述する
プロパティとプロパティ値は大文字でも小文字でも同様に扱われますが、小文字に統一しましょう。

```css
/* Good */
.foo { color: #fff; }

/* Bad */
.foo { COLOR: #FFF; }
```

### ショートハンドはなるべく避ける
`font-size`や`margin`などショートハンドが用意されているプロパティがありますが、ショートハンドに指定しなかったプロパティに初期値が渡されてしまうことから意図せずプロパティがリセットされてしまったり、指定する必要がないところにまで指定する必要が出てきます。明示的に何が目的なのかを示すという意味でも使用をなるべく避けましょう。

```css
/* Good */
.foo { 
    /* 中央配置にする。 */
    margin-right: auto;
    margin-left: auto;
}

/* Bad */
/* 上下を0に指定する必要がない。 */
.foo { margin: 0 auto; }
```

### ベンダープレフィックスは手動で書かない
`-webkit-`や`-moz-`などのベンダープレフィックスは手動で書かず、[Autoprefixer](https://github.com/postcss/autoprefixer)を使用しましょう。Sassの@mixinを使用する方法もありますが、@mixinを管理する手間が発生します。Gulpなどの環境が使えない場合は[Prepros](https://prepros.io/)を推奨します。


## Sass
BootstrapやFoundaitonなどのCSSフレームワークでも使われているようにSassを利用してCSSを書くことがデファクトスタンダードになっています。Sass特有の機能にいくつかのルールを設けます。

### SCSS記法を使う
Sassには拡張子が.sassのSASS記法と拡張子が.scssのSCSS記法があります。どちらも一長一短がありますが、SCSS記法を使うことを推奨します。SCSS記法であればCSSの構文とほとんど変わらないので、誰にとっても読みやすいからです。また、SCSS記法の方がよく使われている（CSSフレームワークはほとんどがSCSS記法で書かれている）ので、ソースコードの流用がしやすいこともメリットです。

### 機能ごとにコードを分割する
SassにはCSSとしてアウトプットさせないファイルを定義するパーシャルという機能があります。コードをファイルごとに分割してモジュール化して管理しましょう。scssファイルをアンダースコア(`_`)から始めることでパーシャルファイルとして認識されます。

```scss
// パーシャルファイルをインポートする
@import "_grid";

// インポートしない場合
// @import "_grid";
```

スタイルシートのアウトプットする場所を指定することでカスケーディングを管理しやすくなったり、使うファイルだけインポートすることができます。

### 変数は繰り返しや演算する場合にだけ使用する
Sassの変数は特定の値を一括で管理できる便利な機能ですが、使いすぎると可読性を損なったり変数名がバッティングする危険性が増します。同じ値を繰り返し指定する場合や四則演算などで値を変更・再計算する場合にだけ定義しましょう。

```scss
// Good
$unit-base: 1em !dafault;
.foo {
    margin-left: (-$unit-base);
}
.foo__bar {
    padding-left: $unit-base;
}

// Bad
$foo-unit: 1em !dafault;;
.foo {
    padding: $foo-unit;
}
```

CSSフレームワークでは大量の変数が定義されています。これは、コードを変更・追加せずに変数でスタイルを変更するためです。通常のスタイルシートではこういった機能はいらない場合がほとんどでしょう。

### モジュール内でだけ使用する変数は個別に定義する
モジュール内で、ある変数や値が繰り返し使用されている場合はモジュール専用の変数を定義しましょう。プロジェクト全体で使用される変数に変更があった場合でも各モジュールで対応できるようにしておきます。

```scss
// Good
$unit-base: 1em !default;

/**
 * #Media
 */
$media-gutter: $unit-base !default;
```

### 変数だけでなくキーワードや相対値を利用する
数値やカラーは必ずしも変数で固定値を指定する必要はありません。例えば親要素の計算値を継承する[inherit](https://developer.mozilla.org/ja/docs/Web/CSS/inherit)キーワードやプロパティの初期値を指定する[initial](https://developer.mozilla.org/ja/docs/Web/CSS/initial)キーワード、同じ要素の`color`プロパティを取り込む[currentColor](http://www.hcn.zaq.ne.jp/___/WEB/css-color-ja.html#currentcolor-color)キーワードなどがあります。

数値（[length](https://developer.mozilla.org/ja/docs/Web/CSS/Length)）を指定するときも`px`のような絶対値で指定するよりも、当該要素のフォントサイズを基準にする`em`やルート要素（通常は`html`要素）のフォントサイズを基準にする`rem`のような相対値で指定したほうが柔軟に対応することができます。

### 変数には!defaultフラグを必ず指定する
変数を定義する際には`!default`フラグを必ず指定するようにしましょう。その変数が定義されているファイルより先に同じ変数を定義し直すことで変数の値を確実に上書きすることができます。これによって変数をまとめて管理することが可能になります（必ずしもまとめて変更する必要はなく、あくまでオプションです）。

```scss
// Good
$unit-base: 1em !default;

// Bad
$unit-base: 1em;
```

### 直接的な変数名は用途をあらわす変数名に格納する
カラーパレットとして`$color-red`のような変数名をつける場合は（例えば`$color-error`のような）用途をあらわす変数名にいったん格納してから指定するようにしましょう。値を変更したい場合にも一箇所を書き換えるだけで済むようになりますし、変数名と値が矛盾することを避けることができます。ハードコード（特定の状況に固定してしまうこと）は避けて、常に変更がしやすいようにしておきましょう。

```scss
// Good
$color-red: red;
$color-error: $color-red;
.foo { color: $color-error; }

// Bad
$color-red: red;
.foo { color: $color-red; }
```

### 変数や関数に名前空間をつける
BootstrapやBourbonといったフレームワークやライブラリを併用した時に変数や関数の名前の衝突が起きないように、名前空間（namespace）をつけましょう。冗長にはなってしまいますが、確実に指定することができるようになります。

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

### ネストは擬似要素やメディアクエリだけに制限する
兄弟セレクタやBEMのElementやModifierを指定するためにSassのネストは便利です。ですが、ネストを制限なく使用すると際限なく縦に長くなってしまい、可読性を損ないます。ネストは擬似要素、擬似クラス、メディアクエリの使用にとどめます。

```scss
// Good
.foo {
    &:hover,
    &:focus {
        ...
    }
    @media (min-width: 768px) {
        ...
    }
}

// Bad
.foo {
    &__bar {
        ...
        ...
        ...
    }
    &__baz {
        ...
        ...
        ...
    }
}
```

ネストするセレクタが明確に関係している場合はネストの使用を許容します。別々のルールセットにして記述するよりも理解しやすいと考えられるためです。

```scss
// Good
.foo--gutter-medium {
    margin-left: - $foo-gutter;
    > .foo__item {
        padding-left: $foo-gutter;
    }
}
```

### 数値の計算には括弧（）を常に使う
Sassでは四則演算を使うことができます。複数の数値を計算させるときに想定外の計算結果になってしまうことを防ぐために括弧`（）`で囲いましょう。

```scss
// Good
.foo {
    width: (100% / 3);
}

// Bad
.foo {
    width: 100% / 3;
}
```

### extendはできるだけ使用しない
extendは継承を可能にするため、Sassファイル内を簡潔にできます。しかし、継承元のセレクタを記述した場所にまとめて出力されるため、カスケーディングを複雑にしてしまう可能性があります。また、CSSにコンパイルした時に膨大なセレクタのついたルールセットができてしまうことで可読性を失う可能性があります。

もし使用するのであれば、同じモジュール内で完結させるか、継承元のセレクタを1つのモジュールにまとめてカスケーディングを管理しやすいようにしましょう。
