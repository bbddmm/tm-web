# TM勉強会 Vue #3

ECMAScriptの実践的な部分について少し学んでおこう。

- https://jsbin.com/?html,js,console,output

JavaScriptの練習用サンドボックス。以下のソースコードを貼り付けて実際に結果を確認してみよう。

## 変数(var, let, const)

- 動的型付け：変数の型が実行時に決定される。JavaScript, Python, PHPなどが該当する。
- (lower) camel case: JavaScriptでの変数、メソッド名の慣例。先頭を小文字、以降を大文字にする。

```
var hoge;
console.log(hoge);
console.log(typeof "hoge");
console.log(typeof(hoge)); 
```

`undefined`はJavaScriptにおける型の一つで、未定義であることを示す。

`typeof`演算子は対象となるオブジェクトの型を文字列として返す。演算子なので括弧は無くてよい。

```
const hoge = null;
console.log(hoge);
console.log(typeof hoge)
```

undefinedは他言語におけるnullと似ているが、JavaScriptではnull型も別に存在する。

上の例ではnullを代入している。三行目を実行すると分かるが、typeof演算子はnull型を区別できず、object型として判定されるという歴史的なバグを抱えている。特に知っている必要は無い豆知識。

```
var fuga = 50;
console.log(typeof fuga); 
console.log(typeof 1.23); 
```

JavaScriptでは整数も浮動小数点数も同じ`number`型として扱われる。

- プリミティブ型: number, string, boolean, undefined, null

プリミティブ型はimmutableの性質を持ち、値の変更を行えない。**JavaScriptではプリミティブ以外は全てオブジェクトである。**これはJavaScriptを重要する上で最も重要な考え方なので特に強調しておきたい。

## 関数の宣言法、ラムダ

```
function hoge(x) {
    console.log(x);
}

hoge('hello');
```

関数の基本だが、この書き方は実務では基本的に行わない。関数をこのように書くと、関数の宣言位置に関わらず呼び出せるため嫌われるようである。

```
var hoge = function(x) {
    console.log(x);
}

hoge('hello');
```

関数もオブジェクトであるので、このように関数を匿名関数として変数に代入できる。呼び出し方は同じ。

```
const hoge = x => { 
  console.log(x);
};

hoge('hello');
```

ES6だとラムダを使ってこのように書ける。

```
const hoge = x => console.log(x);

hoge('hello');
```

ラムダの戻り値を一文で書ける場合は波括弧も省略できる。

```
const rollDice = () => Math.floor((Math.random() * 6) + 1);

console.log(rollDice()); 
```

引数が無いラムダはこのように書く。

## JSON配列の操作

APIのデータをやりとりする場合、JSONデータを取ってきて加工する、というパターンが非常に多く出てくる。

```
let data = [
{
  name: 'takahashi',
  score: 0
},
{
  name: 'miyake',
  score: 100
},
{
  name: 'bitch',
  score: -100
}
];
```

ここで、「name = miyakeのscore」を取得したいとしよう。どうすれば良いだろうか？

```
let filtered = data.filter(el => el.name == 'miyake');

console.log(filtered[0].score)
```

配列に対してfilterメソッドを使用する。filterはラムダを引数に取り、条件に一致(trueを返す)した要素を返すメソッドである。

今度は同じデータに対して、全員のscoreをプラス100点してみる。

```
let data2 = data.map(el => el.score + 100);

console.log(data2)
```

配列を走査して内部のデータを書き換えたい場合はmapメソッドを使用する。ではbitchだけscoreを加点したくない場合は？

```
let data2 = data.map(el => {
    if (el.name == 'bitch') {
        return el.score;
    }
   return el.score + 100;
});
```

一行で書けない場合のラムダの後には波括弧が必要になる。さらに明示的にreturn文を書く必要がある。

## 非同期通信

JavaScriptを学ぶ上で非同期通信は避けられない。むしろこれのためにJavaScriptを学ぶようなものである。

非同期通信を試す前にLaravelを使用して簡易的なAPIを作ってみよう。

```
// routes/web.php

Route::get('/users', function () {
    return [
        [
        'name' => 'takahashi',
        'score' => 0
        ],
        [
        'name' => 'miyake',
        'score' => 100
        ],
        [
        'name' => 'bitch',
        'score' => -100
        ]
    ];
});
```

ルータに連想配列（ディクショナリ）を返すように書いただけである。PHPの連想配列は上のように書く。アローの記号が出てくるがJavaScriptのアローとは関係無い。

なお、Laravelでは配列をreturnすると自動的にJSONに変換して返すようになっている。
 http://localhost/users にアクセスしてJSONを返していることを確認する。

これだけだと実用性は無いに等しいが、これも一応はAPIである。

まずは素のJS、jQuery, Vueの三種類の方法でこのAPIからデータを取得してみよう。

### 素のJS

#### ビュー

```
// resources/views/hello.blade.php

<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link href="{{ mix('css/app.css')}}" rel="stylesheet">
        <title>Hello World</title>
    </head>
    <body>
	<button 
        <div id="app">
	
        </div>
    <script src="{{ mix('js/app.js')}}"></script>
    </body>
</html>
```

#### JS

```
var request = new XMLHttpRequest();
request.onreadystatechange = function() {
    var result = document.getElementById('app');
    if (request.readyState == 4) { 
        if (request.status == 200) { 
            result.innerHTML = request.responseText;
        }
   } else {
       result.innerHTML = "loading...";
   }
}

request.open('GET', 'users', true);
request.send(null);

var data = request.responseText;
```

### jQuery

#### ビュー

```
```

#### JS

```
$('#btnsend').on('click', function(){
  $('#result').text('通信中...');
  // Ajax通信を開始
  $.ajax({
    url: 'helloAjax.php',
    type: 'GET',
    dataType: 'json',
    // フォーム要素の内容をハッシュ形式に変換
    data: $('form').serializeArray(),
    timeout: 5000,
  })
  .done(function(data) {
      // 通信成功時の処理を記述
  })
  .fail(function() {
      // 通信失敗時の処理を記述
  });
})
```

### Vue

```
```

#### ビュー

```
```

#### JS

```
```