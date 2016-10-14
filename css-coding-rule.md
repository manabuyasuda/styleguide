# CSSコーディングルール
## ファイル・ディレクトリ構成
ファイルの構成は[FLOCSS](https://github.com/hiloki/flocss)をベースにします。

```
root
└── assets/
    └── css/
        ├── common.scss
        ├── foundation/
        │   ├── base/
        │   ├── function/
        │   ├── mixin/
        │   └── variable/
        ├── layout/
        └── object/
        　   ├── component/
        　   ├── project/
        　   ├── scope/
        　   └── utility/
```

スタイルは基本的に`/assets/css/common.scss`で出力しますが、ページ特有のスタイルが多く出る場合は`css/pagename.scss`のようにページ専用のスタイルシートを作ることもできます。

```
root
├── assets/
│   └── css/
│       ├── common.scss
│       ├── foundation/
│       │   ├── base/
│       │   ├── function/
│       │   ├── mixin/
│       │   └── variable/
│       ├── layout/
│       └── object/
│        　  ├── component/
│        　  ├── project/
│        　  ├── scope/
│        　  └── utility/
├── css/
│   └── index.scss
├── index.html
└── page/
    ├── css/
    │   └── index.scss
    └── index.html
```

無理に1つのCSSとして管理するより、以下のようにHTMLから優先度が把握できるほうがスタイルの追加が容易になり、`common.css`の肥大化・複雑化を防ぐことができます。

```html
<link rel="stylesheet" href="/assets/css/common.css">
<link rel="stylesheet" href="/css/index.css">
```

### FLOCSSの基本
FLOCSSはFoundation、Layout、Objectの3つのレイヤーとComponent、Project、Utilityの3つの子レイヤーから構成されます。

#### 1. Foundation
Foundationレイヤーでは`html`や`body`のような広範囲にわたるベーススタイル、`h2`や`ul`のような基本的なタイプセレクタのデフォルトスタイルを定義します。装飾的なスタイルは避けて、できる限り低詳細度に保ちます。idセレクタやclassセレクタは使用しません。

Foundationレイヤーにはレイヤーを追加するため、normalize.cssやベーススタイルは`foundation/base`レイヤーに指定します。

```scss
@import "foundation/base/_normalize";
@import "foundation/base/_base";
```

#### 2. Layout
Layoutレイヤーはヘッダーやフッターのような、ページを構成するコンテナブロックのスタイルを定義します。目安としてはワイヤーフレームに書かれるような大きな単位のブロックです。汎用性のあるグリッドシステムは次のObject/Componentレイヤーで定義します。

例えば、ヘッダーにあるロゴやグローバルナビゲーションのレイアウトの役割を持つといったことができます。グローバルナビゲーションやコピーライトのようなコンポーネントは、Object/Projectレイヤーで定義します。基本的にはclass属性を使用しますが、id属性を使用することもできます。

プレフィックス（接頭辞）として`l-`をつけます。

```scss
@import "layout/_header";
@import "layout/_footer";
@import "layout/_main";
```

#### 3. Object
Objectレイヤーはプロジェクトにおけるビジュアルパターンです。抽象度や詳細度、拡張性などによって、3つのレイヤーにわけられます。

1. Component（`c-`）
2. Project（`p-`）
3. Utility（`u-`）

#### 3.1 Component
Componentレイヤーは多くのプロジェクトで横断的に再利用のできるような、小さな単位のモジュール（機能）です。

OOCSSの構造（structure）の機能を担うため、装飾的なスタイルをできるだけ含めないようにします。また、`width`や`margin`といったレイアウトに影響を与えるプロパティもできるだけ含めないようにします。

プレフィックス（接頭辞）として`c-`をつけます。

```scss
@import "object/component/_list-ordered";
@import "object/component/_media";
@import "object/component/_layout";
@import "object/component/_wrapper";
```

#### 3.2 Project
Projectレイヤーはプロジェクト固有のパターンで、複数のページで使い回せるようなコンポーネントです。具体的なスタイルを持つUI（ユーザーフェイス）で、追加されるコンポーネントのほとんどはこのレイヤーに置かれます。

構造的なパターンでも、（ボタンのように）装飾的に拡張されるのであればProjectレイヤーに置きます。

もし、このレイヤーで同じパターンが3箇所で使われていたら、別のコンポーネント（構造部分だけのパターンならComponentレイヤー、それ以外ならProjectレイヤー）としてまとめられないか検討しましょう。

プレフィックス（接頭辞）として`p-`をつけます。

```scss
@import "object/project/_icon";
@import "object/project/_icon-extend";
@import "object/project/_label";
@import "object/project/_button";
@import "object/project/_heading-h2";
@import "object/project/_heading-h3";
```

#### 3.4 Utility
Utilityレイヤーはいわゆる汎用クラスで、ほとんどの場合は単一のスタイルをもっています。コンポーネント間の間隔を調整したり、BEMのModifierが増えすぎるのを防ぎます。

共通の構造を持っていないセクションごとの余白などは、BEMのBlockとModifierで拡張することが適切でない場合があります。
そのようなモジュールは無理にBEMにせず、`.u-section`のようプレフィックスつけた汎用クラスを作ります。

`.mb10`のような具体的な名前より`.mb-small`のような相対的な名前にしたり、pxよりもemや%で指定することを推奨します。確実にスタイルを適応させるために`!important`を使用します。

プレフィックス（接頭辞）として`u-`をつけます。

```scss
@import "object/utility/_text";
@import "object/utility/_align";
@import "object/utility/_display";
@import "object/utility/_sr-only";
@import "object/utility/_width";
@import "object/utility/_margin";
@import "object/utility/_section";
```

### 追加するレイヤー
FLOCSSのファイル構成に5つのレイヤーを標準で追加します。

#### 1.1 Variable
プロジェクト全体で使われる変数を定義します。例えば以下のように、基本的な変数を定義する`global.scss`とブレイクポイント、色、font-familyを定義するファイルを分割すると見通しがよくなります。

```scss
@import "foundation/variable/_global";
@import "foundation/variable/_breakpoint";
@import "foundation/variable/_font-family";
@import "foundation/variable/_color";
```

Sass（scss記法）で変数を定義する場合は以下のことに気をつけます。

- `!default`フラグを指定することで、先に読み込んだ変数が適応されるルールに統一します。
- `$pn-`(Project name)のようにプレフィックスを付けることで名前の衝突を防ぎます。
- ハイフン（`-`）とアンダースコア（`_`）は同一と見なされるので、注意して扱います。
- 3箇所以上で使われる共通の値やプロジェクトに関わらず利用できる値だけを定義します。

#### 1.2 Function
Sassで使用できるfunctionを機能ごとに定義します。例えばpxをremに変換したり、PhotoShopのトラッキングをemに変換するfunctionなどです。
`$pn-`(Project name)のようにプレフィックスを付けることで名前の衝突を防ぎます。

```scss
@import "foundation/function/_em";
@import "foundation/function/_rem";
@import "foundation/function/_tracking";
```

#### 1.3 Mixin
Sassで使用できるmixinを機能ごとに定義します。例えばメディアクエリやclearfix、再利用できるオブジェクトのベーススタイルなどです。
`$pn-`(Project name)のようにプレフィックスを付けることで名前の衝突を防ぎます。

```scss
@import "foundation/mixin/_mq-up";
@import "foundation/mixin/_mq-down";
@import "foundation/mixin/_clearfix";
@import "foundation/mixin/_media";
```

#### 1.4 Base
normalize.cssや要素セレクタ・属性セレクタのデフォルトスタイルを指定します。

```scss
@import "foundation/base/_normalize";
@import "foundation/base/_base";
```

#### 3.3 Scope
ComponentレイヤーやProjectレイヤーのようなコンポーネント単位ではなく、ページ内の小さな範囲（スコープ）でのスタイルを定義します。繰り返し使用しないページ特有のスタイルはPageレイヤーやHTMLファイルのある各ディレクトリに定義します。

例えばブログページのスタイルとして_blog.scssを作成します。このレイヤーでは`.s-blog p`のような要素セレクタとの結合子も使うこともできます。
もし、このレイヤーで同じパターンが3箇所で使われていたら、ProjectレイヤーやUtilityレイヤーでまとめられないか検討しましょう。

直下の要素に一律の余白を指定するといった使い方をしてもいいでしょう。

```scss
.s-block {
  > {
    section, address,
    // h1, h2, h3, h4, h5, h6,
    p, ul, ol, dl,
    pre, div,
    blockquote, figure,
    table,
    form,
    a {
      &:not(:last-child) {
        margin-bottom: 1.7rem;
      }
    }
  }
}
```

プレフィックス（接頭辞）として`s-`をつけます。

```scss
@import "object/scope/_element";
@import "object/scope/_block";
@import "object/scope/_blog";
```

#### その他のレイヤー
その他にもレイヤーを追加することもできます。

- Page - ページ特有のスタイルやページ単位でObjectレイヤー内のスタイルを上書きする場合など
- Theme - SMACSSのThemeモジュールに該当するテーマによる色の切り替えなど
- QA/Test - Quality Assurance（品質保証）、もしくはテストのための一次的なスタイル

レイヤーの追加は以下のリンクを参考にします。

* [More Transparent UI Code with Namespaces](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/)
* [Managing CSS Projects with ITCSS](http://csswizardry.net/talks/2014/11/itcss-dafed.pdf)

#### モジュール化
Layoutレイヤー以下のモジュールは機能ごとにファイルに分割します。モジュールは後述するBEMをベースに名前をつけ、モジュール名とファイル名は一致するようにします。

- `.grid` => `_grid.scss`
- `.button` => `_button.scss`

#### モジュールの粒度
モジュールを適切な粒度（受け持つ機能の大きさ）にするために、以下の3つの粒度でモジュールを分類し、小さなモジュールから読み込みます。

1. Content
1. Medium Container
1. Large Container

##### 1. Content
見出しやリスト、ボタンやアイコンなどのページの内容を表現する小さなオブジェクトです。

##### 2. Medium Container
Contentを内包するオブジェクトで、Contentのスタイルを上書きすることができます。例えば、`.media`や`.button-group`のようなオブジェクトです。

##### 3. Large Container
ContentやMedium Containerを内包するオブジェクトで、内包するオブジェクトに干渉することはできません。例えば、`.grid`や`.wrapper`のようなオブジェクトです。

Large BlockはSmall BlockとMedium Blockを内包することができますが、スタイルを上書きすることはできません。

#### レイヤーの順序
カスケーディングを管理するため、レイヤーを読み込ませる順序は

- 低詳細度から高詳細度
- 抽象的なスタイルから具体的なスタイル
- カスケーディングを許容するモジュールからカスケーディングを許容しないモジュール

のようにする必要があります。例えば以下のような順番で読み込ませます。

```scss
@import "foundation/";
@import "layout/";
@import "object/component/";
@import "object/project/";
@import "object/scope/";
@import "object/utility/";
@import "page/";
@import "theme/";
@import "qa/";
```

#### 外部ライブラリのレイヤー
JQueryのプラグイン、CSSフレームワークやライブラリを読み込ませる順序は「レイヤーの順序」で示した基準を使います。外部ライブラリであっても、役割や機能が変わることはないからです。
例えば、normalize.cssは`foundation/base/`、スライダーのようなJQueryプラグインは`object/project/`のMedium Containerが適切な位置になるでしょう。

外部ライブラリは基本的にファイルを直接上書きせず、`libraryname-extend.scss`のようなファイルを作成し、上書きをします。

## 命名規則
命名規則はBEM（MindBEMding）をベースとし、レイヤーごとにプレフィックスをつけることで名前空間とし、ブレイクポイントをもつモジュールにはサフィックス（接尾辞）をつけることで名前のブレを防ぎます。

### BEM（MindBEMding）
[BEM](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)をベースにした[MindBEMding](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)を使用します。BEMには役割や関係性を明確にできるメリットがあります。

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
Elementを入れ子にするときに`.block__element__element`のような名前にできるだけならないようにします。HTMLの構造を示すのではなく、Blockに対するElementとModifierの関係性を示します。

```scss
// Good
.block__element {}
.block__child-element {}

// Bad
.block__element {}
.block__element__element {}
```

OOCSSのようにマルチクラスで指定するのを基本とします。基本的にはBlockは独立して機能するようにしますが、汎用性を考慮したり、クラス名が長くなりすぎる場合には、Modifierだけでなく汎用クラスを組み合わせることもあります。

```html
<!-- 汎用クラスで`width`を指定している。 -->
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

Blockのスタイルを上書きするときに詳細度を変えないようにします。例えば、`.block1`のスタイルを変更するために`.block2`を親セレクタにして`.block1`を上書きするような指定はできるだけ避けます。同じ詳細度のセレクタであとから読み込ませることで上書きするようにします。

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

FLOCSSにカスケーディングに関するルールを2つ追加しています。

1. 同じProjectレイヤーでもMedium BlockはSmall Blockのスタイルを一部上書きすることができます。
1. Scopeレイヤー、Themeレイヤー、QAレイヤーはそれまでのレイヤーを上書きすることができます。

Sassの入れ子とアンパサンド（`&`）でBEMの記述を短縮することができますが、基本的に使わないようにします。1つ目の理由は入れ子のElementが増えると、今のアンパサンドがどこまでのElementとModifierを含んでいるのかを把握しにくくなるからです。

```scss
// Bad
.foo {
  color: white;
  &--bar {
    color: red;
  }
  & .bar {
    color: red;
  }
}
```

2つ目の理由は実際に指定しているセレクタ名で検索できなくなるからです。CSSにコンパイルされてはじめて`.block__element`のように生成されるので、Sass編集時に検索することができません。

入れ子を使うのは擬似要素やステートクラス、ブレイクポイントの挿入などに制限します。

```scss
// Good
.block {
  &:hover {
    color: blue;
  }
  &.is-active {
    color: blue;
  }
  @include _mq-up(md) {
    color: green;
  }
}
```

早く書けることも必要ですが、修正や運用がしやすいことのほうがずっと重要です。

その他のBEMに関する注意点などは[BEM(MindBEMding)によるCSS設計](https://github.com/manabuyasuda/styleguide/blob/master/how-to-bem.md)を参照してください。

### プレフィックス（Prefix）
レイヤーごとの役割を示したり、名前の重複をさせないために名前空間としてプレフィックスをつけます。FLOCSSで推奨されているプレフィックスといくつかのプレフィックスを使用します。

- `l-` Layoutレイヤー
- `c-` Componentレイヤー
- `p-` Projectレイヤー
- `u-` Utilityレイヤー
- `t-` Themeレイアー
- `s-` Scopeレイヤー
- `qa-`, `te-` QA/Testレイヤー
- `is-` クリックやマウスオーバーなどのイベントが発生している要素に付与する
- `js-` JavaScriptから参照される要素
- `cp-` あるHTML専用のCSSファイル（current pageの略称。名前を変更することもできます。）

### サフィックス（Suffix）
ブレイクポイントを指定しているクラス名は`-sm`や`-md`のようなブレイクポイントのキーワードをクラス名の末尾につけます。
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

メディアクエリは`min-width`を優先的に使い`-sm`などとします。`max-width`のパターンも作る場合は`-sm-down`などとします。

## コメント
コメントにはコードだけでは理解できない（しにくい）情報を残します。例えば以下のようなものです。

- 全体を見渡せるような目次
- レイヤーやモジュールの区切りをわかりやすくするための見出し
- なぜその実装方法を選んだのかという理由
- コードの整理をしたい、バグを修正したいといった、コードからはわからない情報
- コード自体の補足

### 目次（Table of Contents）
スタイルシートのボリュームが一定以上になると全体の把握は難しくなります。CSSでもSassでも利用できる目次をスタイルシートのはじめに用意しておくことを推奨します。

```scss
/**
 * VARIABLE
 * global...サイト全体に使用するサイズや数値に関する変数です。
 * breakpoint...メディアクエリで使用するブレイクポイントです。
 * font-family...font-family指定をまとめています。
 * color...グローバルに使用する色指定です。
 *
 * FUNCTION
 * em...pxをemに変換します。
 * rem...pxをremに変換します。
 * tracking...Photoshopのカーニングをemに変換します。
 *
 * MIXIN
 * mq-up...メディアクエリを`min-width`で挿入します。
 * mq-down...メディアクエリを`max-width`で挿入します。
 * responsive...レスポンシブ対応クラスを生成します。
 * on-event...:hover, :active, :focusをまとめて指定します。
 * clearfix...floatの解除をします。
 * sr-only...非表示にしてスクリーンリーダーにだけ読み上げさせます。
 * list-unstyled...list-unstyledオブジェクトのベーススタイルです。
 * list-mark...list-markオブジェクトのベーススタイルです。
 * media...mediaオブジェクトのベーススタイルです。
 * flag...flagオブジェクトのベーススタイルです。
 * ratio...ratioオブジェクトのベーススタイルです。
 * layout...layoutオブジェクトのベーススタイルです。
 *
 * BASE
 * normalize...Normalize.cssをインポートしています。
 * base...タイプセレクタと属性セレクタのデフォルトスタイルです。
 *
 * LAYOUT
 * header...`<body>`タグ直下にある`<header>`タグのスタイルです。
 * footer...`<body>`タグ直下にある`<footer>`タグのスタイルです。
 * main...`<main>`タグのスタイルです。
 *
 * COMPONENT
 * list-ordered...番号付きリストを入れ子レベルに応じて、1, 1.1のように区切り文字を付け加えます。
 * media...画像とテキストが横並びになるオブジェクトです。テキストを画像に回り込みさせます。
 * layout...汎用的なレイアウトオブジェクトです。グリッドレイアウトなどに使用できます。
 * wrapper...最大幅を指定します。
 *
 * PROJECT
 * icon...アイコンフォントです。テンプレートから自動で生成されます。
 * icon-extend...アイコンフォントを拡張するModifierです。
 * label...インラインのラベルコンポーネントです。
 * button...ボタンコンポーネントです。
 * heading-h2...`<h2>`で使うような見出しです。
 * heading-h3...`<h3>`で使うような見出しです。
 *
 * SCOPE
 * element...BEMにおけるElementの余白を一括で指定します。
 * block...BEMにおけるBloclの余白を一括で指定します。
 * blog...ブログページのスタイルです。
 *
 * UTILITY
 * text...テキストのスタイルに関する汎用クラスです。
 * heading...見出しの汎用スタイルです。
 * align...画像などを中央や右側に配置します。
 * display...要素の表示や改行をコントロールする場合に使用します。
 * sr-only...要素を非表示にさせますが、スクリーンリーダーには読み上げられます。
 * width...おもにグリッドで使用する`width`を指定する汎用クラスです。
 * margin...マージンで余白の管理をします。常に下方向にだけ余白をとります。
 * section...`<section>`要素を使うようなセクションの余白を管理します。
 */
```

### レイヤータイトル
どこのレイヤーにいるのか、どういった特徴のあるレイヤーなのかなどを残しておくとコードを理解しやすくなります。ContentやMedium Containerのような粒度の分類もコメントで残しておきます。

```scss
/* -----------------------------------------------------------------------------
   #Component
   -------------------------------------------------------------------------- */
/**
 * Componentレイヤーは多くのプロジェクトで横断的に再利用のできるような、小さな単位のモジュール（機能）です。
 * OOCSSの構造（structure）の機能を担うため、装飾的なスタイルをできるだけ含めないようにします。
 * また、`width`や`margin`といったレイアウトに影響を与えるプロパティもできるだけ含めないようにします。
 * プレフィックス（接頭辞）として`c-`をつけます。
 */
// Content
@import "object/component/_list-unstyled";
@import "object/component/_list-addstyle";
@import "object/component/_list-ordered";
@import "object/component/_list-note";
@import "object/component/_embed";
// Medium Container
@import "object/component/_block";
@import "object/component/_list-inline";
@import "object/component/_media";
@import "object/component/_flag";
@import "object/component/_table";
@import "object/component/_ratio";
// Large Container
@import "object/component/_layout";
@import "object/component/_wrapper";
```

### モジュールタイトル
モジュール化したファイルにはモジュールの名前や機能、マークアップの例などを残します。下記はスタイルガイドジェネレーター「[Aigis](https://pxgrid.github.io/aigis/)」のコメントも併記した例です（シンタックスハイライトを効かせるために一部全角を使用しています）。

```scss
 /* #Label
   -------------------------------------------------------------------------- */
/*
ーーー
name: label
category: project
ーーー

インラインでメッセージを表示するコンポーネントです。

｀｀｀jade
p: span.p-label タグ名
｀｀｀
*/
.p-label {
  display: inline-block;
  padding: 0.5em;
  color: $_color-ui-bg;
  font-size: $_font-size--small;
  line-height: 1;
  text-align: center;
  white-space: nowrap;
  text-decoration: none;
  background-color: $_color-link;
  @include _on-event() {
    color: #fff;
    text-decoration: none;
  }
}
```

### 注意事項とTODO
あとで処理をしたいことや、把握しておいてほしい注意事項はコメントに残していつでも検索できるようにします。

- `NOTE:` コード上で確認できない制限などの共有
- `TBD:` 今後どうしていくか検討したいこと
- `TODO:` 動作はするけれど改修したい箇所

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

### Sassのコメント
SassはCSSをプログラム的に拡張できるプリプロセッサーです。複雑になりがちな機能や使い方を伝えるためにコメントで補足をしておきます。

```scss
// @desc - メディアクエリを`min-width`で挿入します。
// @param {String} $breakpoint [$_default-breakpoint] - 引数に変数のキーワードを渡します。
// @see - $_breakpoint-up
// @example scss - Usage
// .foo {
//   color: red;
//   @include _mq-up {
//     color: blue;
//   }
// }
// @example css - CSS output
// .foo {
//   color: red;
// }
// @media screen and (min-width: 768px) {
//   .foo {
//     color: blue;
//   }
// }
@mixin _mq-up($breakpoint: $_default-breakpoint) {
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
CSSの構文はセレクタとブレース、プロパティと値で構成されます。このルールセットに読みやすさを確保したフォーマット（書式）をルール化します。

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
原則的には複数行でルールセットを記述しますが、汎用クラスなどの単一のスタイルの場合は1行で記述することもできます。

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

アルファベット順には記述しません。プロパティをアルファベット順で書いていくのは難しいと思いますし、読み難いとも思います。例えば`position`プロパティ
で位置の指定をする場合に`top`と`left`が離れてしまうのは読みやすいでしょうか？

```scss
// Good
.foo {
  display: block;
  position: absolute; // 親要素に対して、
  top: 0;
  left: 0; // 左上を基準にする。
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
  color: #000;
  display: block;
  font-size: 0.75em;
  left: 0; // どこかにpositionがある？
  margin: 0;
  padding: 0;
  position: absolute; // positionがあった、親要素を基準にするのか
  top: 0; // 上にあわせて、他の値はなんだっただろう？
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

ただし、角度（`deg`,`grad`,`rad`,`turn`）や時間（`s`,`ms`）では単位の省略をすることができないので注意します。

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
