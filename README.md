# Javascript初学者（初級者）から中級者へのステップアップに必要な基本

基本すぎることは書きませんが、ちょっとこれは知っておくといいなとか、知っておくとJavascript自体やコードの理解が進むと思われるものをピックアップしておこうと思います。ちなみに知らなくてもJavascriptは書けると思います。ECMAScriptとか使うとそもそもネイティブであれこれ書かないし！となると思いますが、Javascriptの記事なのでそれはそれと言うことで。
Javascript中級者です！と言えばこれぐらいは知ってるよね？って言われそうなことをメインに書いていきます。
という理由付けをして自分の理解度チェックをしていこうと思います。

## 変数

varを使わずに宣言した変数はグローバル変数になります。
```
function func() {
    hoge = 'hoge';
    var piyo = 'piyo';
}
func();
console.log(hoge); // hoge
console.log(piyo); // error!!
```

## array(hash)

おなじみarrayですが、キーを数字にした場合のみ以下のようにアクセスできません。
```
a = {
    '1': 1,
    'a': 'a'
};
console.log(a.a); // a
console.log(a.1); // error
```
数字から始まるプロパティがNGではなくドット演算子が数字から始まるプロパティを扱えないのが原因となります。

## 小数点を含む演算による誤差

javascriptで計算をしたことがある人は「あー、はいはいｗ」となると思いますが、Javascriptでは演算を行うと少なくない誤差が発生することがあります。
```
console.log(0.1 * 6); // 0.6000000000000001
console.log((0.1 * 10) * 6 / 10); // 0.6
```
内部的に2進数で演算を行っているのが原因となります。小数点を整数に変換して演算することで対策が可能です。

## 論理演算子

'&&', '||'は複数の条件を論理的に結合し結果を返しますが、Javascriptでは左辺だけ評価されるケースがあります。
```
function t() {
    console.log(true);
    return true;
}

function f() {
    console.log(false);
    return false;
}

console.log('t() && f()');
if (t() && f()) {} // true false
console.log('f() && t()');
if (f() && t()) {} // false
console.log('t() || f()');
if (t() || f()) {} //true
console.log('f() || t()');
if (f() || t()) {} // false true
```
処理結果で判定したい場合はこんな感じで書くと危ういですねというお話。
サーキット評価とか呼ばれるやつです。

## try - catch - finally

javascriptもできます。
```
try {
    throw new Error('hogeError');
} catch (e) {
    console.log('catch');
} finally {
    console.log('finally');
}
```

## オブジェクト

### プロパティとメソッド
```
var obj = {};
obj.name = 'my object';
obj.getName = function () {
    return "My name is " + obj.name;
}
console.log(obj.name); // my object
console.log(obj.getName()); // My name is my object
```
こんな感じで書けます。
```
var obj = {
    name: 'my object',
    getName: function () {
        return "My name is " + obj.name;
    }
}
console.log(obj.name); // my object
console.log(obj.getName()); // My name is my object
```
こんな感じでも書けます。ここまでは特になんてことはないですね。

### new演算子

newってなんぞ？を確認していきます。
先のコードを少し書き換えてみましょう。
```
var obj = function () {
    this.name = 'my object';
    this.getName = function () {
        return "My name is " + this.name;
    };
}
console.log(obj.name); // obj
console.log(obj.getName()); // error
```
thisを追加し、少し形を変えました。これを更に書き換えます。
```
var obj = function () {
    this.name = 'my object';
    this.getName = function () {
        return "My name is " + this.name;
    };
}
var hoge = new obj();
console.log(hoge.name); // my object
console.log(hoge.getName()); // My name is my object
```
JavaScriptでは、関数内で呼び出すthisは関数呼び出し元のオブジェクトを参照しますが、new演算子を使ってインスタンス化した場合thisはそのインスタンス自身を指します。
わかりやすいように以下に例を記しておきます。
```
var obj = function () {
    this.hoge = 'hoge';
    return 'piyo';
}
var obj1 = new obj();
var obj2 = obj();

console.log(obj1); // obj { hoge: 'hoge' }
console.log(obj2); // piyo
```

### コンストラクタ

コンストラクタに触れるのを忘れてました。
```
var obj = function (hoge, piyo) {
    this.hoge = hoge;
    this.piyo = piyo;
}
var obj1 = new obj('hoge', 'piyo');
console.log(obj1.hoge);
console.log(obj1.piyo);
```
こんな感じで書けます。特に難しいことはありませんが、constructorメソッドやオブジェクトと同名のメソッドを使うわけではないのでその点は注意です。

### オブジェクト拡張

Javascriptの良いところでもあり悪いところでもあるのですが、後付けでオブジェクトを拡張できます。Javascriptではprototypeを経由して拡張します。
```
var obj = function (hoge, piyo) {
    this.hoge = hoge;
    this.piyo = piyo;
}
var obj1 = new obj('hoge', 'piyo');
console.log(obj1.hoge); // hoge
console.log(obj1.piyo); // piyo

obj.prototype.newFunc = function () {
    return 'newFunc()';
};
console.log(obj1.newFunc()); // newFunc()

obj.prototype.newMember = 'newMember';
console.log(obj1.newMember); // newMember

delete obj.prototype.newMember;
console.log(obj1.newMember); // undefined
```
こんな感じですね。拡張できるとは言え定義以外の実装を追加するのはリスクも多いのであまり使わない方がベターかもしれません。


## オブジェクトとプリミティブ型

オブジェクトにササッと触れたのでプリミティブ型にも少し触れておこうと思います。
* 真偽値を扱うboolean
* 数値を扱うnumber
* 文字列を扱うstring
* null値を扱うnull
* 未定義を扱うundefined

通常は値そのものになります。通常は、です。
boolean, string, numberに限っては一時的にオブジェクトになってるケースがあります。
```
var n = 100;
var n2 = new Number(100);
console.log(typeof n); // numbr
console.log(typeof n2); // object
console.log(n.toString()); // 100
console.log(n2.toString()); // 100
```
先程のnew演算子で説明した通り、Numberオブジェクトのコンストラクタを呼び出しているため、Numberオブジェクトのインスタンスが返ってきていますね。boolean, string, numberにはそれぞれラッパーオブジェクトが存在しています。つまり、プリミティブ型がオブジェクトとして振る舞おうとしたした際にバックグラウンドで型に応じたラッパーオブジェクトが生成される、ということになります。
null, undefinedに関してはラッパーオブジェクトを持っていないので値そのものしか持てません。

### ラッパーオブジェクト拡張

Boolean, Number, Stringにはラッパーオブジェクトがあると言いましたが、もちろんこれもオブジェクトです。
つまりラッパーオブジェクトも拡張できるということです。
```
String.prototype.newFunc = function () {
    return this + 'newFunc';
}
console.log('hoge'.newFunc());
```
こんな感じです。やってることはオブジェクト拡張で説明したこととを変わりないですね。

## Globalオブジェクト（標準ビルドインオブジェクト）

特に意識して使うことはないと思いますが、一応書いておきます。
Globalオブジェクトはグローバルな変数や関数を利用する際に利用する便宜的なオブジェクトになります。
```
var hoge = 'hoge';
var g = new Global();
console.log(g.hoge);
```
こんな風にインスタンス化して使うことはできません。
どんな値プロパティや関数プロパティがあるかはこちらを参照ください（全部書ける程少なくない...）。
https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects

## nullとundefined

最後に持ってくる必要はなかったのですが、nullとundefinedの違いはしっかり理解しておきましょう。
nullは「何もない」ことを意味します。とは散々言われているのでもう少し詳細に言うと「プログラム上、意図して何もない状態」を表すものになります。
undefinedは文字通り「未定義」を示します。未定義ということは宣言させていなかったり定義していないものにアクセスした際に起こり得るものとなります。
そこまで難しいことじゃありませんね。

## 関数

### 定義

今までに散々関数を書いてきましたが、関数についてもおさらいをしておきましょう。関数の定義方法は3つ存在します。ここではそれぞれの定義方法について説明していきます。Function命令と関数リテラルだけ覚えておけば問題ないかと思います。

#### Functionコンストラクタでの定義

直接コンストラクタを呼んで関数を作成する方法です。evalと同じような感じです。
```
var sum = new Function('a', 'b', 'return a + b');
console.log(sum(2, 6)); // 8
```
関数の中身を動的に書けるから便利じゃーん！って思うことなかれ。evalと一緒でセキュリティリスクやパフォーマンスの低さが問題視されて実用性は皆無です。とりあえず紹介だけしておく感じです。

#### Function命令での定義

一番わかりやすく使われている関数定義方法だと思います。Function命令を使っての定義方法は以下の通り。
```
function hoge() {
    return 'hoge';
}
function piyo(str) {
    return 'piyo: ' + str;
}
console.log(hoge());
console.log(piyo('piyo'));
```
特に何も補足する必要はないですね。

#### 関数リテラルでの定義

関数リテラルでの定義は「Javascriptっぽい」関数の定義方法になります。と言っても今まで散々書いてきたのですが...
```
var hoge = function () {
    return 'hoge';
}
console.log(hoge());
```
こういうやつです。変数に関数を突っ込んでます。Javascriptっぽいですね。
変数として扱えるということは別の関数の引数や戻り値にできるということです。
すなわちJavascriptにおいて「関数はデータ型である」と言えるわけです。

### 引数

Javascriptの関数は引数チェックを行いません。どういうことかと言うと...
```
var hoge = function (str) {
    return str + 'hoge';
}
console.log(hoge()); // undefinedhoge
console.log(hoge('hoge')); // hogehoge
console.log(hoge('hoge', 'piyo')); // hogehoge
```
こういうことです。引数がない場合は未定義として扱われて過多な引数は無視されます。
argumentsオブジェクトを利用すれば引数チェックとかできますので、もし必要なら使ってみて下さい。ただし、エラーハンドリングとセットでの実装になるのでオーバーヘッドにご注意を。

### 高階関数

「関数それ自体を引数や戻り値として利用する関数」を高階関数と呼びます。と言葉で説明してもピンと来ないと思うので...
```
var outerFunc = function (data, func) {
    for (v in data) {
        func(data[v]);
    }
}
var innerFunc = function (val) {
    console.log(val);
}
outerFunc([1, 2, 3, 4, 5], innerFunc); // 1 2 3 4 5
outerFunc([1, 2, 3, 4, 5], function (val) {
    console.log(val)
}); // 1 2 3 4 5
```
コードで書くとこういう感じです。outerFuncの第二引数に関数が指定されてますね。
Array.forEach()に似てる...と思った方、その通りです。

## スコープチェーン

スコープチェーンとは変数の値を解決する時に適用されるルールになります。
グローバルとローカルに同名の変数が定義されていたらローカルから参照するのはローカルに定義されている変数です。
```
var hoge = 'hoge';
function func() {
    var hoge = 'hogehoge';
    console.log(hoge); // hogehoge
}
func();
```
そういうルールだから、で覚えてしまっても良いっちゃ良いんですが、改めて「何でこうなるのか」を紐解いて行きましょう。

### Callオブジェクト

スコープチェーンの話になると必ず出てくるのがこのCallオブジェクトです。一言で説明するなら「関数を呼び出す度に生成されるオブジェクト」で「関数内のローカル変数を管理する」オブジェクトになります。コーディングする上で特に意識する必要はありませんが、後に出てくるクロージャを理解するためにCallオブジェクトも理解する必要が出てきます。
なんて説明しても少なくとも自分はチンプンカンプンなのでもう少し噛み砕くと「GlobalオブジェクトとLocalオブジェクトを生成した順に連結したリスト」という感じです（もう少しわかりやすい例えがあるかもしれませんね）。
CallオブジェクトはGlobalオブジェクトを基点として生成されたLocalオブジェクトを先頭に追加していきます。
arrayのunshiftみたいな感じです。

### 変数の値解決

で、肝心の値の解決方法ですが、Callオブジェクトを先頭から見ていき、最初に見つかった値を参照する、という形になります。
ということで早速例を見ていきましょう。
```
var x = 'Global';
var y = 'Global';
var outerFunc = function () {
    // timing 2
    var x = 'Outer';
    // timing 3
    var innerFunc = function () {
        var y = 'Inner';
        // timing 4
        console.log('innerFunc(x): ' + x); // InnerFunc(x): Outer
        console.log('innerFunc(y): ' + y); // InnerFunc(y): Inner
    }
    console.log('outerFunc(x): ' + x); // OuterFunc(x): Outer
    console.log('outerFunc(y): ' + y); // OuterFunc(y): Global
    innerFunc();
    // timing 5
}
console.log('Global(x): ' + x); // Global(x): Global
console.log('Global(y): ' + y); // Global(y): Global
// timing 1
outerFunc();
// timing 6
console.log('Global(x): ' + x); // Global(x): Global
console.log('Global(y): ' + y); // Global(y): Global
```
この例だと「Globalオブジェクト」「outerFuncオブジェクト」「innerFuncオブジェクト」が存在します。
先程リストで説明しましたので、リストで考えて順を追ってみましょう。

#### timing 1

この時点ではouterFuncもinnerFuncも呼ばれていないので「Globalオブジェクト」しかない状態です。
Globalオブジェクトには...
```
'x': 'Global'
'y': 'Global'
```
が存在していますね。

#### timing 2

この時点でouterFuncが呼ばれているので「Globalオブジェクト」と「outerFuncオブジェクト」が存在します。
リストっぽくすると「outerFuncオブジェクト->Globalオブジェクト」こういう感じでしょうか。
ただし、この時点では変数定義が行われていないのでouterFuncオブジェクトに変数は展開されていません。

#### timing 3

timing2とCallオブジェクトの状態に変化はありませんが、変数定義が行われているのでouterFuncオブジェクトの中身がtiming2とは異なります。中身は以下の通りです。
```
'x': 'Outer'
```
console.logで出力しているところで変数解決が行われます。現状のCallオブジェクトは「outerFuncオブジェクト->Globalオブジェクト」となります。先に書いた通り先頭から見ていくと...outerFuncの中に'x'がありますね。しかしouterFuncには'y'はありません。無い場合は次のオブジェクトを見に行きます。つまりGlobalオブジェクトですね。Globalオブジェクトには'y'があるので、yが参照された場合、この'y'の値を参照します。
```
// outerFuncオブジェクト
'x': 'Outer' <- これが参照される
// Globalオブジェクト
'x': 'Global'
'y': 'Global' <- これが参照される
```

#### timing 4

timing3と同じ容量ですね！
```
// innerFuncオブジェクト
'y': 'Inner' <- これが参照される
// outerFuncオブジェクト
'x': 'Outer' <- これが参照される
// Globalオブジェクト
'x': 'Global'
'y': 'Global'
```

#### timing 5

このタイミングでは既にinnerFuncは呼び出しが終わり、処理自体も完了している段階です。その場合、Callオブジェクト内部では処理が完了した関数のオブジェクトは破棄されます。Callオブジェクトはtiming3と同じになります。

#### timing 6

このタイミングではouterFuncも処理が終わっているのでCallオブジェクトはtiming1と同じになりますね。

## クロージャ

スコープチェーンは大体理解できたでしょうか。続いて脱初級者の最難関...クロージャです。と言っても理解するだけならそう難しくありません。スコープチェーンを理解したのならなおさらです。
よくある例ですが、カウントアップしていく実装を書いてみましょう。
```
var gNum = function (i) {
    var cnt = i;
    var countUp = function () {
        return ++cnt;
    };
    return countUp;
}
var num = new gNum(0);
console.log(num); // [Function: countUp]
console.log(num()); // 1
console.log(num()); // 2
```
もちろんオブジェクトにしてこんな感じで書いてもOKです。
```
var gNum = function (i) {
    this.cnt = i;
    this.countUp = function () {
        return ++this.cnt;
    };
}
var num = new gNum(0);
console.log(num);
console.log(num.countUp());
console.log(num.countUp());
```
ということは...クロージャって必要なくない？と思うじゃないですか。その通りです。クロージャなんて書かなくてもコードは書けます。が、それでお終いにすると脱初心者できないのでクロージャの説明を続けていきます。

### クロージャの解説

Callオブジェクトのところで整理しましたが、関数ってその処理が終了するとローカル変数は破棄されますね。でもクロージャはメンバ変数がクリアされていないですね...。理由はgNumがローカル変数cntを参照しているメソッドcountUpを返すためで、「Global変数にローカル変数cntを参照しているメソッドcountUpが格納され存在しているから」となります。
見方が逆になりますが、「num変数がローカル変数cntを参照しているメソッドcountUpを参照し続けているため、ローカル変数cntが破棄されない」となります。
スコープチェーンで覚えたことを再度頭から引っ張り出すと少し頭の中の整理が進むかもしれません。スコープチェーンやCallオブジェクトを理解しているとクロージャ自体を「理解」することはそう難しくないはずです。

### クロージャの使い所

メンバ変数をシンプルに変更するオブジェクトの変わりとして使うことが多く、その最たる例がグローバル変数になるかと思います。Javascriptにおけるグローバル汚染が意識しないと酷いことになりますので、これだけでもクロージャを使う十分な理由になると思っています。それでも一番最初に書いた変数定義でvarを使わないミスを犯すとそれがグローバル汚染の第一歩になります。しっかり理解して綺麗なコードを書きたいですね。
グローバル汚染の回避にはオブジェクトを利用するのも一手です。
```
var hoge = {};
hoge.cnt = 0;
hoge.message = 'Hello world!';
```

## prototype

オブジェクトのところで少し出てきたprototypeについてもう少し触れておきましょう（これはおまけ程度に見てもらうと良いと思います）。オブジェクトにはメンバ（変数や関数）を追加するためにprototypeメソッドが用意されています。オブジェクトのところでprototypeを経由して関数を追加しましたね。
prototypeはあくまでメンバを追加するためのものになります。
```
function func() {
    this.num = 0;
    this.str = 'func!';
}
func.prototype.getNum = function () {
    return 'func.getNum: ' + this.num;
}
func.prototype.getStr = function () {
    return 'func.getStr: ' + this.str;
}
var f = new func();
console.log(f.getNum());
console.log(f.getStr());
```
こういうやつですね（オブジェクトの説明のところでも同じようなものを書きましたね）。
こんな風にも書くことができたりします。
```
function func() {
    this.num = 0;
    this.str = 'func!';
}
func.prototype = {
    getNum: function () {
        return 'func.getNum: ' + this.num;
    },
    getStr: function () {
        return 'func.getStr: ' + this.str;
    }
};
```
そんなこんなしてる内に、こうやって書いたら良いじゃんと思うわけです。
```
function func() {
    this.num = 0;
    this.str = 'func!';
    this.getNum = function () {
        return 'func.getNum: ' + this.num;
    }
    this.getStr = function () {
        return 'func.getStr: ' + this.str;
    }
}
```
（最近知ったのですが）一体何が違うのかと言うと「コンストラクタで定義されたメンバはインスタンス毎にメモリに確保される」という点です。つまりインスタンスによって値が異なる変数はコンストラクタで定義しても問題ないがインスタンスによって振る舞いが変わらないメソッドはメモリ上に確保しておく必要無いよねってことですね。prototypeプロパティに格納したメソッドに暗黙の参照を持つので、変数はコンストラクタ、メソッドはprototypeで定義するのがメモリの節約になるのでベストプラクティスかどうかは別として覚えておくと良いかもしれません。

### プロトタイプチェーン

別名「継承」です。
百聞は一見に如かずということで...
```
var parent = function (val) {
    this.val = val;
}
parent.prototype.getVal = function () {
    console.log('val is ' + this.val);
}
var child = function () {}
child.prototype = new parent('"child object"');
child.prototype.sayHello = function () {
    console.log('Hello!');
}
var c = new child();
c.sayHello();
c.getVal();
```
わかりやすくするために少しヘンテコなオブジェクトになっていますが、継承先のprototypeに継承元のprototypeをあてることを継承としているわけですね。オーバーライドとかはこんな感じです。
```
var parent = function (val) {
    this.val = val;
}
parent.prototype = {
    getVal: function () {
        console.log('val is ' + this.val);
    },
    pf1: function () {
        console.log('parent func 1');
    },
    pf2: function () {
        console.log('parent func 2');
    }
}
var child = function () {}
child.prototype = new parent('"child object"');
child.prototype.sayHello = function () {
    console.log('Hello!');
}
child.prototype.pf2 = function () {
    console.log('override');
}
child.prototype.cf1 = function () {
    console.log('child func 1');
}
var c = new child();
c.sayHello(); // Hello!
c.getVal(); // val is "child object"
c.pf1(); // parent func 1
c.pf2(); // override
c.cf1(); // child func 1
```
オブジェクト指向を何となく理解していればスッと入ってくるのではないでしょうか。

## 最後に

あくまでも個人的に脱初心者する時に覚えておいた方がいいかなーと思うことをまとめてみました。
