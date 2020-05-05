# TM勉強会（第二回）

令和二年五月某日開催

## 今回の内容

- Laradock環境構築(Laravelのトップページ表示、Vue導入まで)
- ES6について少し
- Vue講座基礎の基礎

第一回ではLaradockを入れない方針で書いたが、撤回して入れることにした。しっかり流れを追えばそこまで難しくはない。

## Laradock導入

### Laradockとは

DockerコンテナにLaravelを動かす必要なコンテナ一式が詰まった道具箱。Nginx, MySQLなど必要なものは一通り揃う。

ウェブ開発用の道具箱としての位置付けなので、Laravel以外のプロジェクトに対しても使用できる。

ということは実はLaradockを必ずしも使う必要はなく、「ぼくの考えた最強のコンテナ」を使っても良いということである。いずれDocker勉強会を開く際にはこういったことも検討してみたい。

### 導入のメリット

#### ローカルを汚さない

ローカルに余計なソフト、例えばApacheやNginxを入れる必要が無い。サーバすらローカル環境に入れなくて良いというのは仮想環境を使用する大きなメリット。

#### 起動が早い

VMを立ち上げる仮想環境(Vagrant)においては仮想環境にOS(Ubuntu)を入れて、その中にNginx, MySQL等が乗っかる形となるため起動が遅い。

DockerではVMのOSに環境一式を構築するのではなく、コンテナ毎に立ち上げる方式を取るので起動が非常に早い（数秒程度）。

#### プロジェクトをgit管理しやすい

Laradockプロジェクト全体をgit管理することにより、プロジェクトのメンバー同士でソフトウェアやパッケージのバージョンを完全に統一できるため、バージョンによる挙動の違いを考慮に入れる必要がなくなる。

一人開発だとあまりバージョン統一のメリット無いのでは？ と思われるかもしれないが、複数PC使用時においていずれのPCでも開発を行えるのと、git管理によってロールバックを簡単に行えるためメリットが非常に大きい。

### Laradock環境構築

- https://github.com/yakubms/words

上がLaradockプロジェクトの例。プロジェクト内にLaradockとLaravelでフォルダを分ける形を取る。

```
mkdir tm-laravel
```

プロジェクトフォルダを作成する。

```
$ cd tm-laravel
$ git init
$ git submodule add https://github.com/Laradock/laradock.git
```

submoduleとしてLaradockを入れる。submoduleは俺もよく知らないが、gitリポジトリの中に別のgitリポジトリを組み込んで分離するイメージか。

- https://liginc.co.jp/465420

env-exampleを.envとしてコピーして書き換える。変更点は上の四箇所だけでOK。

```
$ docker-compose up -d nginx mysql
```

phpmyadminは入れなくて良い。コンテナ初回起動は数十分掛かるので他の作業でもやりながら待つ。必要なポートが占有されているとコンテナ立ち上げに失敗するので注意。

- http://www.koikikukan.com/archives/2016/06/06-003333.php

ポートが占有されている場合は上を参考にして当該プロセスを落とす。

PHP本体は？と思われるかもしれないが、依存関係のあるコンテナも全部入るので上の三つを入れるとPHPのコンテナ(php-fpm)も立ち上がる。

```
$ docker ps --format "table {{.Names}}"
```

```
$ docker ps --format "table {{.Names}}"
NAMES
laradock_nginx_1
laradock_php-fpm_1
laradock_workspace_1
laradock_docker-in-docker_1
laradock_mysql_1
```

nginxコンテナとphp-fpmとworkspace（作業スペース）コンテナは依存関係にあるため確かにphp-fpmが入っていることが分かる。

```
$ docker-compose exec workspace bash
```

workspaceコンテナに接続する。

```
# composer create-project --prefer-dist laravel/laravel ./
```

workspace内からcomposerコマンドを実行してlaravelをインストールする。接続時のカレントディレクトリは`/var/www`となっており、これが先程の.envに記入した、`APP_CODE_PATH_HOST=../laravel`と対応している（共有されている）。

laravelディレクトリ内に確かにLaravelのファイル一式がダウンロードされていることを確認する。

第一回との大きな違いとして、PHPもComposerもNode.js(npm)もローカルに入れなくて良いという点が重要である。

つまりLaradockで開発を行う際にComposerコマンドを打ちたい場合は、Powershellからローカルに入っているcomposerコマンドを実行するのではなく、常にworkspaceに繋いでから実行するのが正しい。

この段階でnginxが既に起動しているため、ウェブブラウザからlocalhostに繋げばLaravelのトップページが表示されるはずである。

なお、次回からPC再起動後にLaradockを立ち上げたい場合、laradockディレクトリ内に移動し、

```
$ docker-compose up -d nginx mysql
```

と打つだけでサーバが立ち上がる。後は必要に応じて`docker-compose exec workspace bash`でworkspaceに繋いでComposerやnpmのパッケージを入れたりすれば良い。初回の環境構築は面倒だが、構築してしまえば非常に楽になる。

### Vue開発環境構築

以下のコマンドは全てworkspaceに繋いでから実行する。

```
composer require laravel/ui
php artisan ui vue --auth

npm install
npm run dev
```

このコマンドは第一回と同じ。コマンドの意味は第一回のレジュメを参照。

Laravelのトップページ右上にlogin, registerが追加されており、開いたときにデザインが正しく表示されていることを確認する。

### Hot Module Replacement (HMR)

重要な概念である。現状の開発では、

Vueファイル更新 -> npm run devでコンパイルしてjs生成 -> ブラウザF5を押して表示確認

という手順を踏む必要があり面倒臭い。これがVueファイル更新だけで済めば非常に楽になる。HMRにおいては上の流れが以下のようになる。

Vueファイル更新 -> 自動コンパイル、js置換

つまりF5すら押さなくて良いということである。さらに自動リロードではなくjs置換のためVueファイル更新前の値が保持される。これがhotの意味するところである。

HMRを使用するには、

```
npm run hot
```

これだけでHMRが有効になる。ただしHMRをDocker環境で使用するためにはもう一手順必要で、

```
// laravel/webpack.mix.js

mix.webpackConfig({
    devServer: {
        host: '0.0.0.0',
        port: 8080,
        proxy: {
            '*': 'http://0.0.0.0:8000'
        },
        watchOptions: {
            aggregateTimeout: 200,
            poll: 5000
        },
    }
});
```

laravel/webpack.mix.jsに以上の内容を追記する必要がある。8080番ポートへのアクセスをDocker内のHMRの8000番ポートへとフォワードしている。

### 初めてのVue

長い長い下準備が終わった。ようやくHello Worldの時間である。

laravel/resources/views下にhello.blade.phpを作成する。なお、プロジェクト内のファイル編集はWindowsで行ってよい。コマンドを打ちたいときのみworkspaceに入る。

```
// hello.blade.php
<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link href="{{ mix('css/app.css')}}" rel="stylesheet">
        <title>Hello World</title>
    </head>
    <body>
        <div id="app">
            <example-component></example-component>
        </div>
    <script src="{{ mix('js/app.js')}}"></script>
    </body>
</html>
```

以上の内容をコピペして保存する。二箇所Bladeテンプレートのmixディレクティブ`{{ }}`を使用している。

通常のコンパイルとHMRだとcssやjsのパスが変わってしまうが、この差異をmixディレクティブを使用することによりLaravel側で判断してくれる。

div要素内のidがappになっている中にVueコンポーネントを記述することによってVueコンポーネントの置換が行われる。これがVueの基本である。

ExampleComponent.vueはLaravelが用意している初期ファイルであり、/resources/js/components/内にある。

```
// /resources/js/components/ExampleComponent.vue
<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">Example Component</div>

                    <div class="card-body">
                        I'm an example component.
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        mounted() {
            console.log('Component mounted.')
        }
    }
</script>
```

template要素内の要素がdiv要素のid app内の`<example-component></example-component>`の箇所と置換される。divまみれで気持ち悪いが、今どきのウェブサイトはdivまみれになるのが割と当たり前となっている。

このように独自のHTML要素のようなものを自由に定義できるのがVueの大きな特徴である。使い回せるコンポーネントはテンプレート化して使い回すと元のblade.phpの記述量を大きく減らすことができる。

また、Vueコンポーネントの要素名とファイル名の対応形式は上の例からも分かるように、

- 要素名：kebab case（小文字ハイフン区切り）
- Vueファイル名：upper camel case（先頭大文字の大文字区切り）

の対応関係となっている。

しかし、これだけだとhello.blade.phpのファイルとルート(例：`localhost/hello.blade.php`)が紐付けられていない状態である。この紐付けのことをルーティングという。

`localhost/hello.blade.php`で表示されるようにしても良いのだが、今どきのウェブサイトでファイルの拡張子が入っているのはダサい印象を受ける。`/hello`で上のページが表示されるようにしたい。

```
// /laravel/routes/web.php

Route::get('/hello', function () {
    return view('hello');
});
```

ここでrouterの出番である。上の内容をroutes/web.phpに追記する。/helloに対してGETリクエストを送ると、helloのview, つまりhello.blade.phpを表示する。

この段階で、localhost/helloにアクセスすると先程のExampleComponentが表示されることを確認する。

さらに、ExampleComponentの`I'm an example component.`を`Hello World!`に書き換えてみよう。HMRが動いていればリロードなしに内容が書き換わるはずである。

## ES6の基本（書きかけ）

Vueで使う部分のみさらっとやる。

### let, const

```
hoge
```

varはhoistingの問題があるため一切使わない。letとconstの使い分けは曖昧。

定数であるべきもの、変わらないと見なされるものがconst, それ以外はlet程度の認識でよい。

### for ... of

```
hoge
```

`for ... in`は欠陥品のため一切使わない。

### アロー

```
hoge
```

匿名関数の簡潔な表現。ただしJavaScriptではthisのバインド先が変わるというややこしい問題がある。

### モジュール, import

他のjsファイルの内容を参照する。

### promise(async, await)

いわゆるコールバック地獄を回避する仕組み。

ES2017のasync, awaitを使うとより書きやすくなる。

## 第一章


## 第二章
