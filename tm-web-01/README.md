# TM勉強会（第一回）

令和二年三月某日開催

## この勉強会の目指すところ

Vueについて一通り理解する。できればクライアントサイドに限定せず、サーバサイドと関連した内容にする。

### 短期的な目標

- ES6について多少学ぶ
- ウェブ周りのツール、概念について学ぶ

### 中期的な目標

- Vueの文法を一通り学ぶ
- 特にVue Router, Vuexが重要

### 長期的な目標

- 簡単なSPA(single page application)の構築。サーバサイドのAPIはLaravelで作る？

## 参考テキスト、資料

- これからはじめるVue.js実践入門（山田祥寛）
- 改訂新版JavaScript本格入門（山田祥寛）

以下はLaracastsの無料動画。

- https://laracasts.com/series/php-for-beginners 

PHP初心者講座。日本語で書かれているどのPHP本よりも難しい。

- https://laracasts.com/series/laravel-6-from-scratch  

Laravel初心者講座。上のPHP講座を終えられないレベルだと無理。

- https://laracasts.com/series/es6-cliffsnotes

ES6講座。非常に良く纏まっている。

- https://laracasts.com/series/learn-vue-2-step-by-step 

Vue講座。Laravel学習サイトなのでLaravel知ってるのが前提なところが多々ある。

## 環境作り

HTMLにVueを書く方法は簡便ではあるが、話がクライアントサイドに限定されてしまうため面白くない。そのため最初からサーバサイドを意識した環境を構築する。

### sandbox（ちょっとした練習用）

- https://playcode.io/
- https://jsfiddle.net/

JS等に使えるサンドボックス。

- https://3v4l.org/

PHPだとこの辺。

### Chocolatey

Windowsにおけるapt. Windowsのソフトウェアをchocolateyで管理する習慣を付けておくとアップデートやPC買い替え時の移行が楽。

```
choco install php -y
choco install composer -y
choco install nodejs -y
```

必要なパッケージはPHP本体, Composer（後述）, Node.js（後述）の三つ。

### Chrome拡張

- https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd?hl=ja

vue devtoolsは必須。

### IDE

Sublime, VSCodeなどご自由に。

### Laradock

実際に開発する場合はローカル環境には余計なものを入れない方がよい。

Laravelの開発環境としては Laradock(Docker), Homestead(Vagrant)などを使用するのが主流。練習の段階で入れると煩雑なので飛ばす。

## Composer

PHPのパッケージ管理ソフト。Laravel自体もcomposerパッケージの一つとして提供されている。

パッケージにはグローバルインストールとプロジェクト（フォルダ）毎のインストールの二種類ある。全プロジェクト共通で使用するものはグローバル、それ以外はプロジェクト毎に個別にインストールする。

```
composer global require laravel/installer
```

Laravelをグローバルインストールする。

```
laravel new tm-vue
```

適当なフォルダ上で、適当なプロジェクト名を入れて`laravel new`する。必要なcomposerパッケージがインストールされた状態でプロジェクトが作成される。

```
cd tm-vue
php artisan serve &
```

プロジェクト内に移動し、`php artisan serve`するとウェブサーバが立ち上がる。artisanというのはLaravelにおいてあらかじめ用意された便利なコマンド群のこと。

http://localhost:8000/ にアクセスし、Laravelのトップページが表示されていることを確認する。

```
composer require laravel/ui
php artisan ui vue --auth
```

続いてプロジェクト内で上のコマンドを打つと、Vueの雛形が導入される。

この段階で先程のLaravelのページを見ると、右上にLOGIN, REGISTERリンクが作られている。しかしクリックしてもデザイン無しの素のページが表示される。これはアセットのコンパイルが行われていないからである。

## npm

npmはNode.jsを入れると付いてくるパッケージ管理ソフトである。Vueのパッケージもnpmパッケージとして存在する。

グローバルインストールやプロジェクト単位のパッケージ管理が可能であり、Composerと概念的にはほぼ同じ。

### アセットのコンパイル

フロント界隈における「コンパイル」とは、元のソースをウェブブラウザが読める形に変換したり(例: .vue -> .js)、ファイルを一つにまとめたり、空白の除去等を行い、容量を最小化(minify)することを指す。

この界隈は特に流行り廃りが著しい。現在では Laravel Mix を使うのが主流か？ MixはWebpackのラッパー（使いやすくしたもの）であり、特にLaravelに限定せず使用できる。

コンパイルの内容

- HTML:  HTMLで書かれている場合は何もしない（場合によってはpug -> htmlの変換）
- CSS: SCSS, Sass, Less, Stylusなどで書かれたCSSを、通常のCSSに変換して一つにまとめる
- JavaScript: ESで書かれたソースや、vueファイル等を通常のjsに変換して一つにまとめる

他にも画像をBase64エンコードしたり、jsやcssのバージョニング（.js?hogehogeのように名前を変えてキャッシュに残ることを回避する）、開発中のconsole.logを本番環境で消したりなど色々できる。

### npmパッケージのインストールとコンパイル

```
npm install
npm run dev
```

プロジェクト内で`npm install`すると、そのプロジェクト内にnpmパッケージがインストールされる。

インストールされるパッケージはpackage.json内に記述されている。

なお、Composerの場合は composer.json内にインストールされるパッケージが記述されており、殆ど同じ方法でパッケージ管理されている。`laravel new`の際にcomposerパッケージの方は自動的にインストールされるため、`composer install`は不要。

二つ目のコマンドの`npm run dev`に関してはエイリアスとなっており、実体はpackage.jsonに記述されている。

```
    "scripts": {
        "dev": "npm run development",
        "development": "cross-env NODE_ENV=development node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
```

つまり、「webpackをlaravel-mixの開発環境用の設定を使ってコンパイルする」というコマンドである。

一般的には開発環境でminifyは行わず、本番環境用のコンパイルとは区別する。本番環境用にコンパイルする場合、`npm run prod`を使う。

再度Laravelのlogin, registerページにアクセスし、デザインが正しく表示されていることを確認すれば準備完了。

## Laravelのディレクトリ構成

全部書くと非常に煩雑になるため、Vueを学ぶ上で最低限必要な部分についてのみ書く。

- .env: 環境設定。DB絡みのことを行う際には弄るかも。
- resources/js: コンパイル前のjsやvueファイルを配置する。app.jsがエントリーポイントになっており、他のjsを読みに行く流れ。
- resources/sass: コンパイル前のsass, scssなどを配置する。
- resources/views: ここにview（見た目担当のPHP, HTMLなど）用のファイルを配置する。Laravelではviewの記述を簡単にするために、Bladeテンプレートというテンプレートエンジンを使用しており、Bladeを使用する場合は拡張子を.blade.phpにする。
- public/css/app.css: コンパイルされたcss. 実際に読み込まれているcssの実体はこれ。 
- public/js/app.js: コンパイルされたjs. 実際に読み込まれているjsの実体はこれ。
- routes/web.php: ルータ。エンドポイント(例えば /login)に対して表示するファイル名や処理を紐付ける。
- database: モデル。MySQL等も使用できるが、練習目的だとSQLiteを使うと簡単。モデルについては機会があればそのうち説明する。
- app/Http/Controllers: コントローラ。APIの処理などはここに書く（ルータにも書ける）。機会があればそのうち説明する。 
