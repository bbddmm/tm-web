# TM�׋��� Vue #3

ECMAScript�̎��H�I�ȕ����ɂ��ď����w��ł������B

- https://jsbin.com/?html,js,console,output

JavaScript�̗��K�p�T���h�{�b�N�X�B�ȉ��̃\�[�X�R�[�h��\��t���Ď��ۂɌ��ʂ��m�F���Ă݂悤�B

## �ϐ�(var, let, const)

- ���I�^�t���F�ϐ��̌^�����s���Ɍ��肳���BJavaScript, Python, PHP�Ȃǂ��Y������B
- (lower) camel case: JavaScript�ł̕ϐ��A���\�b�h���̊���B�擪���������A�ȍ~��啶���ɂ���B

```
var hoge;
console.log(hoge);
console.log(typeof "hoge");
console.log(typeof(hoge)); 
```

`undefined`��JavaScript�ɂ�����^�̈�ŁA����`�ł��邱�Ƃ������B

`typeof`���Z�q�͑ΏۂƂȂ�I�u�W�F�N�g�̌^�𕶎���Ƃ��ĕԂ��B���Z�q�Ȃ̂Ŋ��ʂ͖����Ă悢�B

```
const hoge = null;
console.log(hoge);
console.log(typeof hoge)
```

undefined�͑�����ɂ�����null�Ǝ��Ă��邪�AJavaScript�ł�null�^���ʂɑ��݂���B

��̗�ł�null�������Ă���B�O�s�ڂ����s����ƕ����邪�Atypeof���Z�q��null�^����ʂł����Aobject�^�Ƃ��Ĕ��肳���Ƃ������j�I�ȃo�O������Ă���B���ɒm���Ă���K�v�͖������m���B

```
var fuga = 50;
console.log(typeof fuga); 
console.log(typeof 1.23); 
```

JavaScript�ł͐��������������_��������`number`�^�Ƃ��Ĉ�����B

- �v���~�e�B�u�^: number, string, boolean, undefined, null

�v���~�e�B�u�^��immutable�̐����������A�l�̕ύX���s���Ȃ��B**JavaScript�ł̓v���~�e�B�u�ȊO�͑S�ăI�u�W�F�N�g�ł���B**�����JavaScript���d�v�����ōł��d�v�ȍl�����Ȃ̂œ��ɋ������Ă��������B

## �֐��̐錾�@�A�����_

```
function hoge(x) {
    console.log(x);
}

hoge('hello');
```

�֐��̊�{�����A���̏������͎����ł͊�{�I�ɍs��Ȃ��B�֐������̂悤�ɏ����ƁA�֐��̐錾�ʒu�Ɋւ�炸�Ăяo���邽�ߌ�����悤�ł���B

```
var hoge = function(x) {
    console.log(x);
}

hoge('hello');
```

�֐����I�u�W�F�N�g�ł���̂ŁA���̂悤�Ɋ֐��𓽖��֐��Ƃ��ĕϐ��ɑ���ł���B�Ăяo�����͓����B

```
const hoge = x => { 
  console.log(x);
};

hoge('hello');
```

ES6���ƃ����_���g���Ă��̂悤�ɏ�����B

```
const hoge = x => console.log(x);

hoge('hello');
```

�����_�̖߂�l���ꕶ�ŏ�����ꍇ�͔g���ʂ��ȗ��ł���B

```
const rollDice = () => Math.floor((Math.random() * 6) + 1);

console.log(rollDice()); 
```

���������������_�͂��̂悤�ɏ����B

## JSON�z��̑���

API�̃f�[�^�����Ƃ肷��ꍇ�AJSON�f�[�^������Ă��ĉ��H����A�Ƃ����p�^�[�������ɑ����o�Ă���B

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

�����ŁA�uname = miyake��score�v���擾�������Ƃ��悤�B�ǂ�����Ηǂ����낤���H

```
let filtered = data.filter(el => el.name == 'miyake');

console.log(filtered[0].score)
```

�z��ɑ΂���filter���\�b�h���g�p����Bfilter�̓����_�������Ɏ��A�����Ɉ�v(true��Ԃ�)�����v�f��Ԃ����\�b�h�ł���B

���x�͓����f�[�^�ɑ΂��āA�S����score���v���X100�_���Ă݂�B

```
let data2 = data.map(el => el.score + 100);

console.log(data2)
```

�z��𑖍����ē����̃f�[�^���������������ꍇ��map���\�b�h���g�p����B�ł�bitch����score�����_�������Ȃ��ꍇ�́H

```
let data2 = data.map(el => {
    if (el.name == 'bitch') {
        return el.score;
    }
   return el.score + 100;
});
```

��s�ŏ����Ȃ��ꍇ�̃����_�̌�ɂ͔g���ʂ��K�v�ɂȂ�B����ɖ����I��return���������K�v������B

## �񓯊��ʐM

JavaScript���w�ԏ�Ŕ񓯊��ʐM�͔������Ȃ��B�ނ��낱��̂��߂�JavaScript���w�Ԃ悤�Ȃ��̂ł���B

�񓯊��ʐM�������O��Laravel���g�p���ĊȈՓI��API������Ă݂悤�B

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

���[�^�ɘA�z�z��i�f�B�N�V���i���j��Ԃ��悤�ɏ����������ł���BPHP�̘A�z�z��͏�̂悤�ɏ����B�A���[�̋L�����o�Ă��邪JavaScript�̃A���[�Ƃ͊֌W�����B

�Ȃ��ALaravel�ł͔z���return����Ǝ����I��JSON�ɕϊ����ĕԂ��悤�ɂȂ��Ă���B
 http://localhost/users �ɃA�N�Z�X����JSON��Ԃ��Ă��邱�Ƃ��m�F����B

���ꂾ�����Ǝ��p���͖����ɓ��������A������ꉞ��API�ł���B

�܂��͑f��JS�AjQuery, Vue�̎O��ނ̕��@�ł���API����f�[�^���擾���Ă݂悤�B

### �f��JS

#### �r���[

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

#### �r���[

```
```

#### JS

```
$('#btnsend').on('click', function(){
  $('#result').text('�ʐM��...');
  // Ajax�ʐM���J�n
  $.ajax({
    url: 'helloAjax.php',
    type: 'GET',
    dataType: 'json',
    // �t�H�[���v�f�̓��e���n�b�V���`���ɕϊ�
    data: $('form').serializeArray(),
    timeout: 5000,
  })
  .done(function(data) {
      // �ʐM�������̏������L�q
  })
  .fail(function() {
      // �ʐM���s���̏������L�q
  });
})
```

### Vue

```
```

#### �r���[

```
```

#### JS

```
```