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
      ├── roots
      │     └── _@roots.scss
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
**INDEX**
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
scssのmapを使って`$colors`の中に`key:value`または`kye:deepkey:deepvalue`を定義する。    
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
色の変数と同じくscssのmapを使って`$zindex`の中に`key:value`または`kye:deepkey:deepvalue`を定義する。    
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
$font-mincho: serif;
$font-gosick: sans-serif;
$font-english: Arial;
```
明朝体、ゴシック体、英自体の変数で管理    
※map化も検討    
呼び出し方


```scss
.test {
  font-family: $font-mincho; //font-family: serif;
  font-family: $font-gosick; //font-family: sans-serif;
  font-family: $font-english; //font-family: Arial;
}
```

#### _functions.scss
#### _mixins.scss
#### _foundation.scss
#### _reset.scss
#### _animation.scss
#### _@base.scss


### 1.3 layout層
### 1.4 object層
### 1.5 page層
### 1.6 roots層
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