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

## 1. ディレクトリ構造
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

## 2. CSS命名規則 - MindBEMdingの独自拡張

### 2.1. IDセレクタの使用は禁止。
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

### 2.2. 極力タグセレクタを避ける。
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

### 2.3. Blockは全て小文字の1単語または、2単語(2単語の場合アンダーバーで区切る)。
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

### 2.4. Elementは1単語。
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

### 2.5. Elementの中でElementまたは、BlockをネストするのはNG。
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

### 2.6. 一時的な状態を示すステートはSMACSSのis-hogeで管理する。
ステートclassにはスタイルを書いてはならない。プレフィックスは`is-`。    
※ここもアンダーバーに統一するか検討中    

OKパターン
```html
<p class="Block--Element is-open">テキスト</p>
<p class="Block--Element is-fixed">テキスト</p>
<p class="Block--Element is-active">テキスト</p>
```

### 2.7. BEMのmodifireは冗長なのでRSCSSの-hogeをアンダーバーに変えた_hogeにする。
BEMの`hoge__huga--piyo`は`modifire`が付いている場所まで特定できるが、そこまで厳密では無いため。    


OKパターン
```html
<p class="Block--Element _large">テキスト</p>
<p class="Block--Element _small">テキスト</p>
<p class="Block--Element _blue">テキスト</p>
```