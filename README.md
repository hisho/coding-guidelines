# coding-guidelines
# <a href="https://hishohub.github.io/coding-guidelines/" target="_blank" rel="noopener noreferrer">コーディングガイドライン</a>

## コマンド
****

### インストール
```node
$ npm ci
```
### 実行
```node
$ npm run watch
```

## CSS
****

### 0. 初期設定のTODOリスト

### 1. ディレクトリ構造
**CSS設計 - FLOUとSMACSSの独自拡張**
```text
root
 └ src
   └ @scss
      ├── @foundation
      │     ├── _@base.scss
      │     ├── _@variables.scss
      │     ├── _animation.scss
      │     ├── _foundation.scss
      │     ├── _functions.scss
      │     ├── _mixins.scss
      │     └── _reset.scss
      │
      ├── layout
      │     ├── _footer.scss
      │     ├── _header.scss
      │     └── _sections.scss
      │
      ├── object
      │     ├── _breadcrumbs.scss
      │     ├── _burger.scss
      │     ├── _buttons.scss
      │     ├── _heading.scss
      │     └── _icons.scss
      │
      ├── page
      │     └── _hoge.scss
      │
      ├── utility
      │     ├── _@modifier.scss
      │     ├── _fonts.scss
      │     ├── _margin.scss
      │     ├── _padding.scss
      │     └── _text-align.scss
      │
      ├── vendor
      │     ├── _hoge.css.scss
      │     └── _hoge.scss
      │
      └── common.scss
```

### 1.1 common.scss
### 1.2 foundation層
* 共通の変数の定義
* ブラウザデフォルトスタイルを初期化
* 基本的にコードを追記しない
#### _@variables.scss
1. 色の定義
```scss
$colors: (
  key1: value1,
  key2: (
    deepkey1: deepvalue1,
    deepkey2: deepvalue2,
  )
);
```
scssのmapを使って`$colors`の中に`key:value`または`key:deepkey:deepvalue`を定義する。    
呼び出し方    
`functions.scss`の`color`関数を呼び出す
```scss
//一つネストが深いmapをgetする関数
@function map-deep-get($map, $keys...) {
  $value: $map;
  @each $key in $keys {
    $value: map-get($value, $key);
  }
  @return $value;
}

//色の関数
@function color($keys...) {
  @return map-deep-get($colors, $keys...);
}
```

```scss
.test {
  color: color($key1); //color: value1
  color: color($key2,deepkey1); //color: deepvalue1
}
```
****
2. z-indexの定義
```scss
$zindex: (
  key1: value1,
  key2: (
    deepkey1: deepvalue1,
    deepkey2: deepvalue2
  ),
);
```
z-indexの階層を可視化するため    
色の変数と同じくscssのmapを使って`$zindex`の中に`key:value`または`key:deepkey:deepvalue`を定義する。    
呼び出し方    
`functions.scss`の`zindex`関数を呼び出す
```scss
//一つネストが深いmapをgetする関数
@function map-deep-get($map, $keys...) {
  $value: $map;
  @each $key in $keys {
    $value: map-get($value, $key);
  }
  @return $value;
}

//zindexの関数
@function zindex($keys...) {
  @return map-deep-get($zindex, $keys...);
}
```
```scss
.test {
  z-index: zindex(key1); //z-index: value1
  z-index: zindex(key2,value1); //z-index: deepvalue1
}
```
****
3. ブレークポイントの定義   
```scss
//カンプのインナー横幅
$inner-width: 1000;
//スマホ時の左右のpadding
$wrapper-padding: 15;
//メディアクエリの変数
$breakpoints: (
  xs: 0,
  sm: 720,
  md: $inner-width,
  lg: 1200,
);
```
色、z-indexを同じくscssのmapを使って`$breakpoints`の中に`key:value`を定義する。
* xs：スマホ
* sm：スマホとタブレット切り替え
* md：タブレットとPC切り替え
* lg：PCとワイドPC切り替え   
呼び出し方    
`mixin.scss`の`media`を`@include`する。    
引数1に`$breakpoints`の`$key`を引数2に`min` or `max`を    
※初期値は`sm`の`min-width`   
```scss
/* functions.scss */
//pxの単位を付ける関数
@function px($target, $size: 1) {
  @return $target * ($size * 1px);
}
/* mixins.scss */
@mixin media($breakpoint: 'sm', $key: min) {
  @if $key == max {
    @media (#{$key}-width: (px(map-get( $breakpoints, $breakpoint )) - .02px)) {@content;}
  }
  @if $key == min {
    @media (#{$key}-width: px(map-get( $breakpoints, $breakpoint ))) {@content;}
  }
}
```
```scss
.test {
  @include media(sm) {
    display: block;
  }
  @include media(sm,max) {
    display: block;
  }
}
```
リザルト
```css
/* @include media(sm) {} */
@media(min-width: 720px) {
  .test {
    display: block;
  }
}
/* @include media(sm,max) {} */
@media(max-width: 719.98px) {
  .test {
    display: block;
  }
}
```
****
4. フォントの定義
```scss
$fonts: (
  ja: (
    sans: sans-serif,
    serif: serif,
  ),
  en: (
    sans: sans-serif,
    serif: serif,
  )
);
```
色、z-indexを同じくscssのmapを使って`$fonts`の中に`key:value`を定義する。    
呼び出し方   
`functions.scss`の`font`関数を呼び出す
```scss
//一つネストが深いmapをgetする関数
@function map-deep-get($map, $keys...) {
  $value: $map;
  @each $key in $keys {
    $value: map-get($value, $key);
  }
  @return $value;
}

//fontの関数
@function font($keys...) {
  @return map-deep-get($fonts, $keys...);
}
```

```scss
.test {
  font-family: font(ja, sans);  //日本語sans-serif
  font-family: font(ja, serif); //日本語serif
  font-family: font(en, sans);  //英語sans-serif
  font-family: font(en, serif); //英語serif
}
```
***
#### _functions.scss
**共通の関数の定義**   
1. margin関数
※現在はmargin関数以外の関数を使うことはありません。
```scss
/**
 * font-sizeにlineheightをかけた分の差分を出す関数
 * @param  {Number} $lineHeight line-heightを入れる
 * @param  {Number} $fontSize font-sizeを入れる
 * @return {px} 差分にpxを付けて返す
 */
@function margin($lineHeight, $fontSize) {
  @return px(($fontSize * $lineHeight - $fontSize) / 2);
}
```
呼び出し方    
```scss
.test {
  font-size: 1.6rem;
  line-height: 1.6;
  maring-top: 20px - margin(1.6,16); // 20px - 4.8px
}
```
***
#### _mixins.scss
**全てのプロジェクトで使う共通のmixinの定義**   
※プロジェクト中で新規作成するmixinは各ファイルに追記する事    
1. IE用のブラウザハック    
`body.ie.windows`の部分はJavaScriptのUserAgentによって付与されます。
```scss
@mixin iehack {
  @at-root body.ie.windows & {
    @content;
  }
  //jsを切っているIE11用のフォールバック
  @at-root _:-ms-fullscreen, :root & {
    @content;
  }
}
```
呼び出し方
```scss
.test {
  @include iehack {
    display: none;
  }
}
```
リザルト
```scss
body.ie.windows .test {
  display: none;
}
_:-ms-fullscreen, :root .test {
  display: none;
}
```
***
2. ホバーの設定    
メディアクエリレベル4のホバー可能な時のみホバーの体裁を与える    
IEには効かないのでIEhackを用いてIEには普通にホバーさせる    
[Can I use@media: hover](https://caniuse.com/#search=%40media%20hover)
```scss
@mixin hover {
  @media (hover: hover) {
    &:hover {
      @content;
    }
  }
  @include iehack {
    &:hover {
      @content;
    }
  }
}
```
呼び出し方
```scss
.test {
  transition: background-color .5s;
  @include hover {
    background-color: red;
  }
}
```
リザルト
```scss
.test {
  transition: background-color .5s;
}
@media (hover: hover) {
  .test:hover {
    background-color: red;
  }
}
body.ie.windows .test:hover {
  background-color: red;
}
_:-ms-fullscreen, :root .test:hover {
  background-color: red;
}
```
***
3. テキストホバーの設定   
テキストをホバーした時の統一設定   
**初期設定時に変更**
```scss
@mixin hovertext($opacity: .7, $transition: .5s) {
  transition: opacity $transition;
  @include hover {
    opacity: $opacity;
  }
}
```
呼び出し方
```scss
.test {
  @include hovertext;
  //または引数を指定する
  @include hovertext(.5,.2s);
}
```
リザルト
```scss
.test {
  transition: opacity .5s;
}
@media (hover: hover) {
  .test:hover {
    opacity: .7;
  }
}
body.ie.windows .test:hover {
  opacity: .7;
}
_:-ms-fullscreen, :root .test:hover {
  opacity: .7;
}
```
***
4. 画像ホバーの設定   
画像をホバーした時の統一設定   
**初期設定時に変更**
```scss
@mixin hoverimg($gizi:before) {
  &::#{$gizi} {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 1;
    display: block;
    width: 100%;
    height: 100%;
    content: '';
    background: transparent;
    transition: background-color .5s;
  }
  @include hover {
    &::#{$gizi} {
      background: rgba(#fff,.5);
    }
  }
}
```
呼び出し方
```scss
.test {
  @include hoverimg;
  //beforeが使用されている場合は引数にafterを指定する
  @include hoverimg(after);
}
```
リザルト
```scss
.test::before {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1;
  display: block;
  width: 100%;
  height: 100%;
  content: '';
  background: transparent;
  transition: background-color .5s;
}
@media (hover: hover) {
  .test::before:hover {
      background: rgba(255,255,255,.5);
  }
}
body.ie.windows .test::before:hover {
    background: rgba(255,255,255,.5);
}
_:-ms-fullscreen, :root .test::before:hover {
    background: rgba(255,255,255,.5);
}
```
***
5. メディアクエリーの設定   
ブレークポイントの定義を参照    
***
6. positionのmixin   
使用頻度の高いposition: absolute;を楽に出すmixin
```scss
@mixin position($position: xy) {
  position: absolute;
  @if $position == xy {
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
  }
  @if $position == x {
    left: 50%;
    transform: translateX(-50%);
  }
  @if $position == y {
    top: 50%;
    transform: translateY(-50%);
  }
}
```
呼び出し方
```scss
.test {
  @include position;
  //引数にxを指定して
  @include position(x);
  //引数にyを指定して
  @include position(y);
}
```
リザルト
```scss
.test {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
}
//引数にxを指定して
.test {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
}
//引数にyを指定して
.test {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}
```
***
7. flexのmixin  
使用頻度の高いdisplay: flex;を楽に出すmixin
```scss
@mixin flex($position:xy) {
  display: flex;
  @if $position == xy {
    justify-content: center;
    align-items: center;
  }
  @if $position == x {
    justify-content: center;
  }
  @if $position == y {
    align-items: center;
  }
}
```
呼び出し方
```scss
.test {
  @include flex;
  //引数にxを指定して
  @include flex(x);
  //引数にyを指定して
  @include flex(y);
}
```
リザルト
```scss
.test {
  display: flex;
  justify-content: center;
  align-items: center;
}
//引数にxを指定して
.test {
  justify-content: center;
}
//引数にyを指定して
.test {
  align-items: center;
}
```
***
8. wrapperからの開放   
親要素のwidthからネガティブマージンを利用し擬似的に横幅を広げるmixin
```scss
@mixin overflow($padding: null) {
  $ispadding: $padding !=null;
  margin-right: calc(50% - 50vw);
  margin-left: calc(50% - 50vw);
  @if $padding {
    padding-left: calc(50vw - 50%);
    padding-right: calc(50vw - 50%);
  }
}
```
呼び出し方
```scss
.test {
  @include overflow;
  //または引数を指定して
  @include overflow(true);
}
```
リザルト
```scss
.test {
  margin-right: calc(50% - 50vw);
  margin-left: calc(50% - 50vw);
}
//または引数を指定して
.test {
  margin-right: calc(50% - 50vw);
  margin-left: calc(50% - 50vw);
  padding-left: calc(50vw - 50%);
  padding-right: calc(50vw - 50%);
}
```
***
9. 疑似要素でpadding-top   
疑似要素を消費してアスペクト比を維持するmixin    
scssの組み込み関数percentageを利用しています。
```scss
@mixin pt($yoko: 16, $tate: 9, $gizi: before) {
  &::#{$gizi} {
    display: block;
    padding-top: percentage($tate/$yoko);
    content: '';
  }
}
```
呼び出し方
```scss
.test {
  @include pt;
  //または引数を指定して
  @include pt(300/500);
  //または疑似要素を変えて
  @include pt(300/500,after);
}
```
リザルト
```scss
.test::before {
  display: block;
  padding-top: 56.25%;
  content: '';
}
//または引数を指定して
.test::before {
  display: block;
  padding-top: 60%;
  content: '';
}
//または疑似要素を変えて
.test::after {
  display: block;
  padding-top: 60%;
  content: '';
}
```
***
#### _foundation.scss   
上記の3つをまとめたscss   
```scss
@import "./@variables";
@import "./functions";
@import "./mixins";
```
****
#### _reset.scss   
全てのスタイルをリセットするscss   
重要な部分のみ紹介
```scss
* {
  padding: 0;
  margin: 0;
  font: inherit;
  letter-spacing: inherit;
  word-wrap: inherit;
  border: 0;
}
*,
::after,
::before {
  //全ての要素のbox-sizingをborder-boxにする。
  box-sizing: border-box;
}
```
* 全ての`box-sizing`を`border-box`に変更
* `font`、`letter-spacing`、`word-wrap`は親要素の値を継承する
```scss
html {
  overflow-x: hidden;
  // 10px
  font-size: 62.5%;
  line-height: 1;
  letter-spacing: .05em;
  //英単語を改行するように変更
  word-wrap: break-word;
  //文字詰め
  font-feature-settings: "palt";
  //モバイル端末のテキスト自動拡大の対策
  -webkit-text-size-adjust: 100%;
  //フォントのアンチエイリアス
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
}
```
* 横幅がはみ出た要素をカットする為の`overflow-x: hidden`
* デフォルトのフォントサイズを`62.5%`=`10px`に変更
* 全ての`line-height`の初期値を`1`に変更
* `letter-spacing`の初期値を`0.05em`に変更
* 英字で改行する用に`word-wrap`を`break-word`に変更
* `font-feature-settings`(文字詰め)を`palt`に変更
```scss
a {
  text-decoration: none;
  @include hover {
    //ホバー時もaタグの色を親要素から継承する。
    color: inherit;
  }
}
```
* ホバーした時のaタグの色を親要素から継承するように変更   
※詳細度に注意
```scss
a[href^="tel:"] {
  @media (hover:hover) {
    pointer-events: none;
  }
}
```
* ホバーできる要素では電話をかけられないように`pointer-events`を`none`に変更
```scss
p {
  line-height: 1.6;
}
```
* `line-height`の初期値を`1`に設定したが`pタグ`のみ`1.6`に
```scss
img {
  display: block;
  //IEのバグ対策
  width: 100%;
  max-width: 100%;
  height: auto;
}
```
* `imgタグ`を`inline`要素で使うことがほとんどないので初期値を`block`に変更
* `IE11`のflexのバグ対策で`width`を`100%`に変更
****
#### _animation.scss
@kyeframeの定義    
`呼び出しはどのファイルからも可能`な為ここ以外に@kyeframe書かず`_animation.scss`に集約する
****
#### _@base.scss
プロジェクト毎で使うbaseの定義    
**このファイルは初期設定必須**
```scss
@import "./reset";
@import "./_animation.scss";
```
`reset`と`animation`のimport
```scss
:root {
  //htmlにブレークポイント表示用
  --breakpoint-xs: #{px(map-get($breakpoints, xs ))};
  --breakpoint-sm: #{px(map-get($breakpoints, sm ))};
  --breakpoint-md: #{px(map-get($breakpoints, md ))};
  --breakpoint-lg: #{px(map-get($breakpoints, lg ))};
  //フォント
  --font-ja-sans: #{font(ja,sans)};
  --font-ja-serif: #{font(ja,serif)};
  --font-en-sans: #{font(en,sans)};
  --font-en-serif: #{font(en,serif)};
}
```
リザルト
```css
:root {
  --breakpoint-xs: 0px;
  --breakpoint-sm: 720px;
  --breakpoint-md: 1000px;
  --breakpoint-lg: 1200px;
  --font-ja-sans: sans-serif;
  --font-ja-serif: serif;
  --font-en-sans: sans-serif;
  --font-en-serif: serif;
}
```
`css variables`を利用し`root`に`font`と`breakpoint`を表示する
```scss
body {
  font-family: $font-mincho;
  // windowsのIE11のみメイリオにフォントを変える
  &.ie.windows {
    font-family: "メイリオ", Meiryo, sans-serif;
  }
  @at-root _:-ms-fullscreen,:root & {
    font-family: "メイリオ", Meiryo, sans-serif;
  }
}
```
フォントの初期設定
```scss
.wrapper {
  //innerがサイドタッチしないように予めスマホ時の左右padding分設定
  max-width: px($inner-width + ($wrapper-padding * 2));
  margin: 0 auto;
  @if $wrapper-padding > 0 {
    padding: 0 px($wrapper-padding) 0;
  }
  .inner {
  
  }
}
```
`.wrapper`と`.inner`の初期設定
****
### 1.3 layout層
* 全ページ共通の一番大枠のレイアウトの定義
* 基本的にバリエーションの無い大枠の定義
* 共通の大枠があるだけ増やして良い
#### _@footer.scss
共通のヘッダーのレイアウトの定義
***
#### _@header.scss
共通のフッターのレイアウトの定義
***
#### _@sections.scss
共通のセクションの余白、レイアウトの定義
***
#### _breadcrumbs.scss
パンくずのレイアウトの定義
***
#### _gnav.scss
グローバルナビゲーションのレイアウトの定義
***
#### _hamburger.scss
ハンバーガーアイコンのレイアウトの定義
***
### 1.4 object層
### 1.5 page層
### 1.6 utility層
### 1.6 vendor層

### 2. CSS命名規則 - MindBEMdingの独自拡張

#### 2.1. IDセレクタの使用は禁止。
IDセレクタは詳細度が高く、上書きが難しくなるので使わないのがベター。    
だたし、ページ内リンクや、JSのフックとしての使用は問題ありません。    
NGパターン
```html
<header id="header">ヘッダー</header>
```

```scss
// NG
#header {
  position: fixed;
}
//詳細度は変わらないが、素直にclass名を付けたほうが良い
[id="header"] {
  position: fixed;
}
```
OKパターン
```html
<header class="header" id="header">ヘッダー</header>
```

```scss
// OK
.header {
  position: fixed;
}
```

#### 2.2. 極力タグセレクタを避ける。
レイアウトに変更があった場合タグセレクタでスタイルを指定していると思わぬ崩れの原因になるためなるべくclass指定する。   
NGパターン
```html
<div class="hoge">
  <ul>
    <li>
      <section class="card">
        <img class="card__img" src="img/img.png" alt="">
        <h2 class="card__title">タイトル</h2>
        <p class="card__text">テキスト</p>
        <a class="card__link" href="/hoge/">リンク</a>
      </section>
    </li>
  </ul>
<div>
```

```scss
// NG
.hoge {
  max-width: 900px;
  margin: 0 auto;
  ul {
    display: flex;
    flex-wrap: wrap;
    li {
      width: 50%;
    }
  }
}
```
OKパターン
```html
<div class="hoge">
  <ul class="hoge__container">
    <li class="hoge__item">
      <section class="card">
        <img class="card__img" src="img/img.png" alt="">
        <h2 class="card__title">タイトル</h2>
        <p class="card__text">テキスト</p>
        <a class="card__link" href="/hoge/">リンク</a>
      </section>
    </li>
  </ul>
<div>
```

```scss
// OK
//ulの直下はliだがclass名を付けることで変更に強くなる
.hoge {
  max-width: 900px;
  margin: 0 auto;
  &__container {
    display: flex;
    flex-wrap: wrap;
  }
  &__item {
    width: 50%;
  }
}
```

#### 2.3. Blockは全て小文字の1単語または、2単語(2単語の場合アンダーバーで区切る)。
少し気持ち悪いですが、アンダーバーの理由はダブルクリックで選択できるからです。    
NGパターン
```html
<p class="Hoge">テキスト</p>
<p class="Hogehuga">テキスト</p>
<p class="Hoge-huga">テキスト</p>
<p class="Hoge-huga-piyo">テキスト</p>
<p class="Hoge_huga">テキスト</p>
<p class="Hoge_huga_piyo">テキスト</p>
<p class="hoge-huga">テキスト</p>
<p class="hoge-huga-piyo">テキスト</p>
<p class="hoge_huga_piyo">テキスト</p>
```
OKパターン
```html
<p class="hoge">テキスト</p>
<p class="hoge_huga">テキスト</p>
```

#### 2.4. Elementは1単語。
Blockとの区別を付けるためElementは全て小文字の1単語に統一し`--`でつなぐ    
NGパターン
```html
<p class="Block--hogeHuga">テキスト</p>
<p class="Block--hoge-huga">テキスト</p>
<p class="Block--hoge_huga">テキスト</p>
```
OKパターン
```html
<p class="Block--hoge">テキスト</p>
<!-- 2単語はなるべく避けたいがhogehugaのみギリギリセーフ -->
<p class="Block--hogehuga">テキスト</p>
```

#### 2.5. Elementの中でElementまたは、BlockをネストするのはNG。
Elementの中にElement,Blockをネストするならば新しいBlockを作るほうが賢明なため。    

NGパターン
```html
<p class="Block--Element--Block">テキスト</p>
<p class="Block--Element--Element">テキスト</p>
```
OKパターン
```html
<p class="Block--Element">テキスト</p>
```

#### 2.6. 一時的な状態を示すステートはSMACSSのis-hogeで管理する。
ステートclassにはスタイルを書いてはならない。プレフィックスは`is-`。    
※ここもアンダーバーに統一するか検討中    

OKパターン
```html
<p class="Block--Element is-open">テキスト</p>
<p class="Block--Element is-fixed">テキスト</p>
<p class="Block--Element is-active">テキスト</p>
```

#### 2.7. BEMのmodifireは冗長なのでRSCSSの-hogeをアンダーバーに変えた_hogeにする。
BEMの`hoge__huga--piyo`は`modifire`が付いている場所まで特定できるが、そこまで厳密では無いため。    


OKパターン
```html
<p class="Block--Element _large">テキスト</p>
<p class="Block--Element _small">テキスト</p>
<p class="Block--Element _blue">テキスト</p>
```


## 画像
****
### 1.