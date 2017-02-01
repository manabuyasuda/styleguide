# ECSS
[Enduring CSS](http://ecss.io/)

このドキュメントは「Enduring CSS」（以降ECSSと表記）のアプローチや考え方をまとめたものです。意訳や独自に追加した箇所もあります。正確な情報は[公式サイト](http://ecss.io/)を確認してください。

Enduringは、「長続きする、永続的な、不朽の、我慢強い、辛抱強い」という意味を持っています。  
OOCSSのようなオブジェクト指向のCSSアプローチは再利用性やファイルサイズの削減というメリットはありますが、「小さなコンポーネントを組み合わせる複雑さ」「コンポーネントの影響範囲が大きいので変更・削除がしにくい」というデメリットもあります。

ECSSでは複雑で抽象度の高いオブジェクト指向ではなく、複雑さをなくして、管理をしやすく、CSSの寿命を長くすることを目指したアプローチをとります（イメージとしては100を70にするのではなく、100は100のままで管理をする。大きな塔ではなく中規模のビル群を作っていく。）。  
OOCSSが[DRY](https://www.google.co.jp/search?q=Don%27t+repeat+yourself&gws_rd=cr&ei=BkiRWOqJF8uh8QXZzrOAAg)（Don't repeat yourself：繰り返しを避けること）を目指しているのとは対照的に、ECSSでは繰り返しを許容し、適切に分離（decoupling）することを目指します。

ECSSは（例えば、ファイルサイズ、UIの数、開発者の数などが）大規模なWebアプリケーションのCSSを合理的に書くためアプローチです。すべてのケースには当てはまらないので、[Atomic Design](http://atomicdesign.bradfrost.com/)や[OOCSS](https://github.com/stubbornella/oocss)、[SMACSS](https://smacss.com/ja)や[BEM](https://en.bem.info/)のようなアプローチも検討してください。

## ECSSの用語の定義
同じ言葉でも違った意味で解釈されてしまうことがあります。これを避けるためにECSSで使用される言葉とその意味を定義します。

- キーセレクタ：任意のCSSルールの右端の実際にスタイルが適応されるセレクタ
- 上書き（オーバーライド）：キーセレクタの値が継承に基づいて意図的に修正される状況
- 接頭辞：ベンダープレフィックス。 例：`-webkit-transform`
- オーサリングスタイルシート：スタイリングルールを作成するファイル（PostCSSやSassなど）
- CSS：ツールによって生成され、最終的にブラウザで解釈されるCSSファイル
- コンテキスト：前後の文脈や状況
- スコープ：有効になる範囲を限定すること

## モジュールと命名規則
他のCSSの手法と同じく、ECSSも特定のスコープでUIを分割します。

- namespace（名前空間） - 名前の衝突を防ぐためのコンテキストに合わせた接頭辞
- Module（モジュール） - 視覚的に認識できる個別の機能領域のもっとも大きな区分
- Component（コンポーネント） - Moduleに含まれる機能性を持つ部品
- ChildNode（子ノード） - Componentを構成する独立した部品
- variant（バリアント） - Module内の部品が恒久的、もしくは動的に変化した状態

これはBEMの命名規則と似ています。

- ModuleとComponent - Block
- ChildNode - Element
- variant - Modifier

### 命名規則
モジュールは以下のようなシンタックスで表現されます。

```css
namespace-ModuleOrComponent_ChildNode-variant
```

区切りを分かりやすくすると以下のようになります。

```css
.[namespace][-ModuleOrComponent][_ChildNode][-variant]
```

namespaceとvariantはローワーキャメルケースで、それ以外はアッパーキャメルケースになります。

`-ModuleOrComponent`はModuleかComponentのいずれかになるので、実際には以下の2つのいずれかになります。

```css
namespace-ModuleName_ChildNode-variant
namespace-ComponentName_ChildNode-variant
```

ECSSのComponentはModuleの中にある、一定以上の大きさや機能をもったUIのことを指します。例えば、以下のようなものが考えられます。

- ヘッダー内のロゴやナビゲーション、検索フォーム（`.st-Logo`, `.st-Nav`, `.st-Search`）
- トップページ内のaboutエリア、商品リスト、関連リンク（`.tp-About`, `.tp-ProductList`, `.tp-RelatedLink`）
- `hero`名前空間に属したスタンダートなメインビジュアル（`.hero-Standard`）
- `doc`名前空間に属したドキュメンテーションエリア（`.doc-Main`）

クラス名だけでは、それがModuleなのかComponentなのか分からない場合もありますが、ECSSではそれも許容しています。必要なことはnamespaceでコンテキストごとに分割して、その中で重複しないことです。重複しないのであれば、細かな組み立て方は設計者に委ねられます。


### 名前空間（namespace）
それぞれのアセットにあるCSSは最終的に1つのCSSファイルに結合されます。クラス名の重複などでスタイルの意図せぬ衝突を避けるために、ECSSでは名前空間（namespace）をつけます。

- トップページ → `TopPage` → `.tp-`
- カテゴリートップ → `CategoryTop` → `.ct-`
- ショッピングカート → `ShoppingCart` → `.sc-`

省略は必須ではないので、`.home-`のようにしてもいいでしょう。  
また、省略したときに名前が重複しないように、特にコンテキストの数の多い案件では注意が必要かもしれません。

名前空間は`.hero-`や`.doc-`といったモジュールのような名前になることもあります。これは比較的シンプルでバリエーションを想定していない場合だと考えられるので、`.hero-Title`ではなく`.hero-Standard_Title`のようにChildNodeとしてスコープを作ると重複を避けることができます。もしくは、`.tp-Hero`のようにトップページの`Hero`モジュールという意味合いを持たせてもいいでしょう。

```css
/* `hero`名前空間を持つComponent */
.hero-Standard {}
.hero-Title {}
.hero-Strap {}

/* `hero`名前空間を持つComponentとChildNode */
.hero-Standard {}
.hero-Standard_Title {}
.hero-Standard_Strap {}

/* `tp`名前空間を持つComponentとChildNode */
.tp-Hero {}
.tp-Hero_Title {}
.tp-Hero_Strap {}
```

#### 共通の構造的なModule
ヘッダーやフッターのようなサイト共通で使われる構造的なModuleは名前空間に「Structure」という意味の`.st-`を使うといいでしょう。

- グローバルヘッダー → `.st-Header`
- グローバルフッター → `.st-Footer`

#### 改修・バージョン管理
ECSSではModuleは（例えばトップページやカテゴリートップページのような）特定のコンテキストを持っています。

例えばトップページの改修があったとしても、`.tp-`（TopPage）という名前空間を持ったModuleのアセットが不要になるのは分かるので、迷わず削除することができます。改修後のModuleは同じ`.tp-`名前空間を使ってもいいですし、`.tp2-`（TopPage2）という名前空間に変更してもいいでしょう。

### サイト共通の汎用的なModule

サイト内で使われる汎用的なModuleは名前空間に「SiteWide」という意味の`.sw-`を使うといいでしょう。例えばボタンやリストのような小さなコンポーネントです。

- サイト共通のボタン → `.sw-Button`
- サイト共通の順序つきリスト → `.sw-OrderedList`

ただし、ECSSでは汎用的なModuleは極力もつべきでないとされています。

汎用的なModuleを作るメリットは

- デザインの再利用ができる（工数の削減）
- 小さなModuleごとにテストができる
- 同じルールセットを減らすことができる

汎用的なModuleを作るデメリットは

- 影響範囲が大きく変更しにくい・削除しにくい
- 異なるnamespaceが混ざるので複雑になる

サイト共通として見た目が同じであることと、実装として同じコードであることは違います。例外を含んでいないか、サイト共通のコードとして実装してもいいのかを合意形成してから共通化する必要があります。

## ディレクトリ構造
ECSSではサイト全体で使えるコンポーネントを作って組み合わせることは基本的に考えません。ページやカテゴリーのようなスコープでコンポーネントを作って、CSS・JS・画像をスコープごとにまとめて管理します。

```
/assets/
  /TopPage
    /css
    /js
    /img
  /CategoryTop
    /css
    /js
    /img
```

スコープごとに必要なものを分けることで、見つけやすく、削除しやすくします。スコープごとにまとめられたフォルダをアセットと呼びます。

## 状態変化
Moduleの状態変化は[WAI-ARIA](https://github.com/momdo/momdo.github.io/wiki#aria%E9%96%A2%E9%80%A3)を使うことが推奨されています。

| 属性          	| 意味                                	|
|---------------  |-------------------------------------  |
| aria-checked    | trueで選択、falseで非選択を示す       |
| aria-disabled   | trueで無効、falseで通常を示す         |
| aria-busy       | trueで読込中、falseで通常を示す       |
| aria-expanded   | trueで展開、falseで格納を示す         |
| aria-hidden     | trueで非表示、falseで表示状態を示す   |

## ツール
ECSSではCSSプリプロセッサとLintの使用を推奨しています。どのCSSプリプロセッサやタスクランナーを使うのかといったことは、プロジェクトの要件によっても違いますし、時期によっても最適なものは違います（ECSS本ではPostCSSとGulp、Stylelintを使用）。必要な機能は以下の通りです。

- 変数：色を取得したり、定数化することによるヒューマンエラーの軽減
- mixins：フォントファミリーなどの特定の設定のマクロのようなもの
- アンパサンド記号：（`&`）でキーセレクタを参照する
- loop：長いルールセット（例えば、100種類の異なる色のヘッダー）のループを書く機能
- partials：オーサリングスタイルシートを特定のスコープごとに分割
- Lint：適切でないコードをデプロイするのを防ぐ
- 積極的なMinify：コードの最適化
- Autoprefixer：正確にベンダープレフィックスを付与し、オーサリングスタイルシートにベンダープレフィックスが存在しないようにする

### Stylelint
PostCSSではStylelintを使います。`.stylelintrc`には以下のように設定します。

```
// Config for linting
module.exports = { 
    "rules": {
        "block-no-empty": true,
        "color-no-invalid-hex": true,
        "declaration-colon-space-after": "always",
        "declaration-colon-space-before": "never",
        "function-comma-space-after": "always",
        "function-url-quotes": "double",
        "media-feature-colon-space-after": "always",
        "media-feature-colon-space-before": "never",
        "media-feature-name-no-vendor-prefix": true,
        "max-empty-lines": 2,
        "number-leading-zero": "never",
        "number-no-trailing-zeros": true,
        "property-no-vendor-prefix": true,
        "declaration-block-no-duplicate-properties": true,
        "block-no-single-line": true,
        "declaration-block-trailing-semicolon": "always",
        "selector-list-comma-newline-after": "always-multi-line",
        "selector-no-id": true,
        "string-quotes": "double",
        "value-no-vendor-prefix": true,
        "function-linear-gradient-no-nonstandard-direction": true,
        "selector-no-universal": true,
        "declaration-block-no-shorthand-property-overrides": true,
        "indentation": 4,
        "selector-max-specificity": "0,2,0"
    } 
}
```

'selector-max-specificity'という「セレクタが持つことができるセレクタ特異性の最大レベルを制御する」ルールもあります。その他のECSSプロジェクトに有効なルールは以下の通りです。

- オーバーライドとメディア照会のみをネストすることができます（親（`&`）セレクターを使用しないネストを防止します）。
- キーセレクタがECSS命名規則に合っていることを確認してください（これを行うには、Stylelintに ['selector-class-pattern'](https://stylelint.io/?/src/rules/selector-class-pattern/README.md)ルールが追加されました）。
- キーセレクタが複合化されないようにします（例：`.ip-Selector.ip-Selector2 {}`）。
- キーセレクタが特異であることを確認します（例：`.ip-Thing`ではなく`.a-Parent .ip-Thing {}`）。


## 健全なスタイルシートの十戒
- すべてのキーセレクタは"Single Source of truth"であること
- 入れ子にしてはいけない（メディアクエリ、上書き、本当に必要であると感じない限りは）
- IDセレクタを使用しない（必要だと感じていても）
- オーサリングスタイルシートにベンダープレフィックスを書いてはいけない
- サイズや色、z-indexに変数を使用する
- モバイルファーストで書く（max-widthを避ける）
- mixinを控えめに使用する（extendを避ける）
- すべてのマジックナンバーとブラウザハックに対してコメントを書く
- インラインイメージを使わない
- 複雑なCSSを書かず、同じように動くシンプルなCSSを書きます

### 1. すべてのキーセレクタは"Single Source of truth"であること
キーセレクタ（実際にスタイルが適応されるセレクタ）に対するスタイルの指定は1つの`{}`内で完結するようにします。これは重複をなくし管理しやすくするためと、アセットを削除しやすくするのと同じようにキーセレクタも削除しやすくするためです。

`{}`内は常にキーセレクタを参照した処理をおこないます。

```css
.key-Selector {
  width: 100%;

  @media (min-width: $M) {
    width: 50%;
  }

  .an-Override_Selector & {
    color: $color-grey-33;
  }
}
```

ネストをしたときのパターンとして、キーセレクタを`&`で参照したり、メディアクエリで上書きする場合などが考えられます。

許容されるネストは3つです。

1.標準的な上書き  
この結果、`ip-HomeCallouts`のクラスを持つ要素の内部にある場合、`ip-Carousel`のフォントサイズが大きくなります。

```css
.ip-Carousel {
  font-size: $text13;

  .ip-HomeCallouts & {
    font-size: $text15;
  }
}
```

2.要素にクラスを追加してオーバーライドする  
`.ip-ClassificationHeader`を追加された`.ip-Carousel`の背景色は変更されます。

```css
.ip-Carousel {
  background-color: $color-green;
  
  &.ip-ClassificationHeader {
    background-color: $color-grey-a7;
  }
}
```

3.メディアクエリによる上書き  
横幅は50%に上書きされます。

```css
.key-Selector {
  width: 100%;
  
  @media (min-width: $M) {
    width: 50%;
  }
}
```

いずれもキーセレクタは`{}`内でカプセル化されます。他のキーセレクタが混ざり込むこともありません。

### 2. 入れ子にしてはいけない（メディアクエリ、上書き、本当に必要であると感じない限りは）
オーサリングスタイルシートでセレクタは常に単一のものにします。これは、CSSのルールをできるだけフラットにするためです。（例えば`h1.yes-This_Selector`のように）キーセレクタに他のセレクタを追加する必要はありません。

- 望ましくない特異性（詳細度の上昇）を作り出す
- より具体的なセレクタにする（詳細度を上げる）必要があるため、維持するのが難しくなります
- 生成されたCSSファイルに不必要な膨らみを追加する
- 要素タイプの場合、ルールを特定の要素や構造に結びつけます

子要素をネストすることによって別のセレクタを生成することは禁止します。  
キーセレクタ`.key-Selector`の中に`.key-Selector_Item`を生成してしまう。`.key-Selector_Item`に関する情報が重複する可能性がある。

```css
/* Good */
.key-Selector {}
.key-Selector_Item {}

/* Bad */
.key-Selector {

  &_Item {

  }
}
```

子要素をネストすることによって詳細度を上げることは禁止します。  
キーセレクタ`.key-Selector`の中に`.key-Selector_Item`が存在することで、`.key-Selector_Item`に関する情報が重複する可能性があり、詳細度も上げてしまう。

```css
/* Good */
.key-Selector {}
.key-Selector_Item {}

/* Bad */
.key-Selector {

  .key-Selector_Item {

  }
}
```

### 3. IDセレクタを使用しない（必要だと感じていても）
ECSSではIDセレクタの使用を禁止します。

- クラスセレクタよりも具体的で上書きを難しくする
- ページ内で一度しか使えない

どうしてもIDを使わなければいけない場合は、属性セレクタを使ってクラスセレクタと同じ詳細度にします。

```html
[id="Thing"] {
  /* Property/Values Here */
}
```

### 4. オーサリングスタイルシートにベンダープレフィックスを書いてはいけない
PostCSSのAutoprefixerによって必要なベンダープレフィックスを自動で処理することができます。

ただし、以下のような独自実装のプロパティは手動で追加する必要があります。

```css
.ui-ScrollPanel {
  -webkit-overflow-scrolling: touch;
}

.ui-Component {
  &::-webkit-scrollbar {
    -webkit-appearance: none;
  }
}
```

### 5. サイズや色、z-indexに変数を使用する
サイズは通常何らかのパターンを持ちます。時間の節約になりますし、平準化を促すことができます。色に関しても同じことが言えます。

z-indexも変数として定義しておきます。スタッキングコンテキストを完全にはカバーできませんが、いくらかは平準化できます。

### 6. モバイルファーストで書く（max-widthを避ける）
最小のビューポートを基準にすることで、スタイルの継承を促し、必要なときにだけ上書きをすることができます。

基本的にはmax-widthを使うことはありませんが、中間の範囲にだけスタイルを当てたいときに使うことがあります。

```css
.med-Video {
  position: relative;
  background-color: $color-black;
  font-size: $text13;
  line-height: $text15;

  /* Between medium and large sizes we want to bump the text up */
  @media (min-width: $M) and (max-width: $L) {
    font-size: $text15;
    line-height: $text18;
  }
}
```

### 7. mixinを控えめに使用する（extendを避ける）
mixinを使った抽象化はなるべく避けてください。有用なケースとしては

- テキストの切り捨て（@mixin Truncate）
- iOSスタイルの慣性スクロールパネル
- さまざまなブラウザに適した数の擬似セレクタ
- 複雑なフォントスタック

単純なフォントスタックは変数で定義します。それ以外の複雑なフォントスタックはmixinで定義すると手間を省けます。

プロジェクトのmixinは10個以下になるようにします。それより多い場合は不必要に抽象化されている可能性があります。

Sassの@extendディレクティブは、あるセレクタを別のセレクタに継承させます。ファイルサイズの削減が期待できますが、オーサリングの時点では予測ができない処理をしてしまうため、デバッグが難しくなります。

Minify時はextendのほうがmixinよりもファイルサイズは削減できますが、Gzipをするとそれほど変わらなくなります。mixinによって管理しやすさが向上するのであれば、extendで得られるはずだったファイルサイズの削減は気になりません。

### 8. すべてのマジックナンバーとブラウザハックに対してコメントを書く
ピクセルベースで、まだ変数として定義されていない値を入力するときには注意が必要です。オーサリングスタイルシートにマジックナンバー（第三者は数字の意味が分からないが正常に動作すること）を入力するときは、上の行にコメントを追加して理由を説明してください。今は余分に思えるかもしれませんが、他の人や1か月後の自分が見たときのことを考えましょう。

```css
.med-Video {
  position: relative;
  background-color: $color-black;
  font-size: $text13;
  line-height: $text15;
  /* 絶対配置されたアイコンに対応するためには、上記のスペースが必要です */
  margin-top: 20px;
}
```

特定のブラウザに対するハックを書くときは`/ * HHHack：* /`をコメントに追加します。

```css
.med-Video {
  background-color: $color-black;
  font-size: $text13;
  line-height: $text15;
  /*HHHack Windows Phone 8.1をフルサイズで表示させるために必要, reference ticket SMP-234 */
  width: 100%;
}
```

この種のハックは可能な限りルールの最下位に記述します。

### 9. インラインイメージを使わない
インラインイメージはHTTP環境ではHTTPリクエストを減らすことができるメリットがあります。ただし、オーサリングスタイルシートにインラインアセットを展開することは避けます。そのアセットが何であるかを把握しにくいからです。

代わりにツールを使ってインラインイメージに変換しましょう。例えば[postcss-assets](https://github.com/borodean/postcss-assets)です。

```css
.rr-Outfit {
  min-height: $size-quadruple;
  background-image: inline("/path/to-image/relevant-image-name.png");
}
```

### 10. 複雑なCSSを書かず、同じように動くシンプルなCSSを書きます
できるだけ多くの人が理解できるように、できるだけシンプルなCSSを書きましょう。ループ、mixin、関数を書くのは最小限にします。原則として、ルールのバリエーションが10未満の場合は手で書きます。30以上のスプライト画像であればツールを使っていきましょう。

シンプルさはレイアウトにも適応されます。DOM構造が同じであれば、よりサポートされているレイアウトアルゴリズムを使いましょう。

## 参考リンク
- [Enduring CSSの設計思想 - ECSSが目指す設計 | CodeGrid](https://app.codegrid.net/entry/2016-ecss-1)
- [抽象化を避けるCSS設計方法論「Enduring CSS」 第1回 | HTML5Experts.jp](https://html5experts.jp/takazudo/21946/)