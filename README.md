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
### 2.2. 極力タグセレクタを避ける。
レイアウトに変更があった場合タグセレクタでスタイルを指定していると思わぬ崩れの原因になるためなるべくclass指定しましょう。
### 2.3. Blockは1単語または、2単語(2単語の場合アンダーバーで区切る)。
少し気持ち悪いですが、アンダーバーの理由はダブルクリックで選択できるからです。
### 2.4. Elementは1単語。
Blockとの区別を付けるためElementは1単語に統一
### 2.5. Elementの中でElementまたは、BlockをネストするのはNG。
Elementの中にElement,Blockをネストするならば新しいBlockを作るほうが賢明なため。
### 2.6. 一時的な状態を示すステートはSMACSSのis-hogeで管理する。
ステートclassにはスタイルを書いてはならない。プレフィックスは`is-`。
### 2.7. BEMのmodifireは冗長なのでRSCSSの-hogeをアンダーバーに変えた_hogeにする。
BEMの`hoge__huga--piyo`は`modifire`が付いている場所まで特定できるが、そこまで厳密では無いため。