<p>LESSはCSSのエクステンションとしてCSSへの後方互換だけではなく追加機能もCSSの<em>既存</em>シンタックスを利用します。そのためLESSは<em>非常に簡単</em>に覚えることができるし、もし確信が持てなくてもCSSに立ち戻ることができます。</p>

<h2>変数</h2>

<p>以下の例は一目瞭然:</p>
{% highlight text %}
@nice-blue: #5B83AD;
@light-blue: @nice-blue + #111;

#header { color: @light-blue; }
{% endhighlight %}
<p>コンパイル後:</p>
{% highlight text %}
#header { color: #6c94be; }
{% endhighlight %}

<p>変数名を使って変数を定義することもできます:</p>
{% highlight text %}
@fnord: "I am fnord.";
@var: 'fnord';
content: @@var;
{% endhighlight %}

<p>以下のようにコンパイルされます:</p>
{% highlight text %}
content: "I am fnord.";
{% endhighlight %}

<p>LESSにおける変数は実際には「定数」となり、定義できるのは1度のみとなります。</p>

<h2>ミックスイン</h2>

<p>LESSではスタイルのルールセット内にある宣言を別のルールセットに含むことができます。例えば以下のようなクラスがあったとします:</p>
{% highlight text %}
.bordered {
	border-top: dotted 1px black;
	border-bottom: solid 2px black;
}
{% endhighlight %}
<p>そしてこれらのプロパティを他のルールセットで再利用したいとした場合、このルールセットで定義した<br>
宣言を他のルールセットで再利用する場合は以下のように記述します:</p>
{% highlight text %}
#menu a {
	color: #111;
	.bordered;
}
.post a {
	color: red;
	.bordered;
}
{% endhighlight %}
<code>.bordered</code>クラスの宣言ブロックは <code>#menu a</code>と<code>.post a</code>でも宣言されます:
{% highlight text %}
#menu a {
color: #111;
	border-top: dotted 1px black;
	border-bottom: solid 2px black;
}
.post a {
color: red;
	border-top: dotted 1px black;
	border-bottom: solid 2px black;
}
{% endhighlight %}
<p>どのCSSの *クラス*や*id*の宣言ブロックでも上記のようにミックスインすることができます。</p>

<h2>ミックスインの引数利用</h2>

<p>LESSにはクラスのようにミックスインすることができる上、パラメータを渡すこともできる特別なルールセットがあります。以下はその標準的な例です:</p>
{% highlight text %}
.border-radius (@radius) {
	border-radius: @radius;
	-moz-border-radius: @radius;
	-webkit-border-radius: @radius;
}
{% endhighlight %}
<p>複数のルールセット内で使用する例:</p>
{% highlight text %}
#header {
	.border-radius(4px);
}
.button {
	.border-radius(6px);  
}
{% endhighlight %}
<p>パラメータが利用できるミックスインはデフォルト値を定義することもできます:</p>
{% highlight text %}
.border-radius (@radius: 5px) {
	border-radius: @radius;
	-moz-border-radius: @radius;
	-webkit-border-radius: @radius;
}
{% endhighlight %}
<p>と記述すると以下のようにすることができます:</p>
{% highlight text %}
#header {
	.border-radius;  
}
{% endhighlight %}
<p>上記の例では5pxの<code>border-radius</code>を宣言したことになります。</p>
<p>またパラメータが利用できるミックスインではパラメータを利用しないこともできます。<br>
ここで定義した宣言ブロックを他のルールセットで利用はしたいが、定義したルールセット自体はCSSに出力する必要がない場合に便利です:</p>
{% highlight text %}
.wrap () {
	text-wrap: wrap;
	white-space: pre-wrap;
	white-space: -moz-pre-wrap;
	word-wrap: break-word;
}

pre { .wrap }
{% endhighlight %}
<p>上記の例は以下のように出力されます:</p>
{% highlight text %}
pre {
	text-wrap: wrap;
	white-space: pre-wrap;
	white-space: -moz-pre-wrap;
	word-wrap: break-word;
}
{% endhighlight %}
<h3><code>@arguments</code>変数</h3>

<p><code>@arguments</code>にはミックスインの中で定義した引数を渡すことができます。これにより個別に値の定義をする必要がなくなります:</p>
{% highlight text %}
.box-shadow (@x: 0, @y: 0, @blur: 1px, @color: #000) {
	box-shadow: @arguments;
	-moz-box-shadow: @arguments;
	-webkit-box-shadow: @arguments;
}
.box-shadow(2px, 5px);
{% endhighlight %}
上記の例は以下のように出力されます:
{% highlight text %}
box-shadow: 2px 5px 1px #000;
-moz-box-shadow: 2px 5px 1px #000;
-webkit-box-shadow: 2px 5px 1px #000;
{% endhighlight %}

<h2>パターンマッチングとガードエクスプレション</h2>
<p>パラメータによってミックスインの挙動を変更させたい場合があります。まずは基本から初めてみましょう:</p>
{% highlight text %}
.mixin (@s, @color) { ... }

.class {
	.mixin(@switch, #888);
}
{% endhighlight %}
<p><code>.mixin</code>の挙動を<code>@switch</code>の値によって変更させてみましょう。<code>.mixin</code>を以下のように定義したとします:</p>
{% highlight text %}
.mixin (dark, @color) {
	color: darken(@color, 10%);
}
.mixin (light, @color) {
	color: lighten(@color, 10%);
}
.mixin (@_, @color) {
	display: block;
}
{% endhighlight %}
<p>そして、以下のように呼び出します:</p>
{% highlight text %}
@switch: light;

.class {
	.mixin(@switch, #888);
}
{% endhighlight %}
<p>以下のようなCSSが出力されます:</p>
{% highlight text %}
.class {
	color: #a2a2a2;
	display: block;
}
{% endhighlight %}
<p><code>.mixin</code>に渡された色は明度が高くなりました。もし<code>@switch</code>が<code>dark</code>だった場合、結果は明度が低い色となります。</p>

<p>以下にどう処理されたかを示します:</p>

<ul>
<li>一番始めのミックスインの定義は第一引数として<code>dark</code>を要求していたため、マッチしません。</li>
<li>二番目のミックスインの定義は<code>light</code>を要求していたためマッチしました。</li>
<li>三番目のミックスインの定義はどんな引数も要求していないためマッチしました。</li>
</ul>

<p>ミックスインの定義のなかでマッチしたものだけが利用されます。変数はその変数に定義されたどの値ともマッチします。それ以外は引数として渡した値そのものにのみマッチします。</p>

<p>また引数の個数に対してマッチさせることができます。例は以下になります:</p>
{% highlight text %}
.mixin (@a) {
	color: @a;
}
.mixin (@a, @b) {
	color: fade(@a, @b);
}
{% endhighlight %}

<p><code>.mixin</code>に引数を1つ渡した場合には、一番目のミックスインの定義が出力されます。しかし、引数を2つ渡した場合は、二番目の定義が出力されます。この例では<code>@a</code>は<code>@b</code>の透明度を持つことになります。</p>

<h3>ガード</h3>

<p>ガードはシンプルな値や変数の個数ではなく式にマッチさせたい場合に有効です。関数型のプログラミング言語になじみがあれば、すでに見たことがあることでしょう。</p>

<p>LESSではCSSの元になっている宣言型言語を可能な限り無視しないようにif/else宣言ではなく <strong>ガードミックスイン</strong> を条件文として実装しています。これは<code>@media query</code> の仕様の流れと同じになるようになっています。</p>

<p>まずは例を見てみましょう:</p>
{% highlight text %}
.mixin (@a) when (lightness(@a) >= 50%) {
	background-color: black;
}
.mixin (@a) when (lightness(@a) < 50%) {
	background-color: white;
}
.mixin (@a) {
	color: @a;
}
{% endhighlight %}
<p>キーになるのは<code>when</code>キーワードです。これによりガードシーケンスが開始します(例では1つのガードのみあります)。では以下のコードを実行したとします:</p>
{% highlight text %}
.class1 { .mixin(#ddd) }
.class2 { .mixin(#555) }
{% endhighlight %}
<p>以下のように出力されます:</p>
{% highlight text %}
.class1 {
	background-color: black;
	color: #ddd;
}
.class2 {
	background-color: white;
	color: #555;
}
{% endhighlight %}
<p>ガードで利用できる比較オペレータは: <strong><code> &#62;&#32;&#62;&#61;&#32;&#61;&#32;&#61;&#60;&#32;&#60; </code></strong>となります。加えてキーワード<code>true</code>は正の場合を表し、以下の2つのミックスインの例は同じ意味になります:</p>
{% highlight text %}
.truth (@a) when (@a) { ... }
.truth (@a) when (@a = true) { ... }
{% endhighlight %}
<p>キーワード<code>true</code>以外の値は偽になります:</p>
{% highlight text %}
.class {
	.truth(40); // 上記の例のどの定義にも当てはまりません
}
{% endhighlight %}

<p>ガードはコンマ ',' によって分割することができます。どのガードが正の場合でもマッチとして扱われます:</p>
{% highlight text %}
.mixin (@a) when (@a > 10), (@a < -10) { ... }
{% endhighlight %}

<p>引数を比較することも、引数でない値を比較することもできます:</p>
{% highlight text %}
@media: mobile;

.mixin (@a) when (@media = mobile) { ... }
.mixin (@a) when (@media = desktop) { ... }

.max (@a, @b) when (@a > @b) { width: @a }
.max (@a, @b) when (@a < @b) { width: @b }
{% endhighlight %}
<p>値のタイプによってミックスインをマッチさせたい場合には<code>is&#42;</code>関数を利用できます: </p>
{% highlight text %}
.mixin (@a, @b: 0) when (isnumber(@b)) { ... }
.mixin (@a, @b: black) when (iscolor(@b)) { ... }
{% endhighlight %}
<p>タイプチェック用の関数の基本は以下です:</p>

<ul>
<li>iscolor</li>
<li>isnumber</li>
<li>isstring</li>
<li>iskeyword</li>
<li>isurl</li>
</ul>
<p>もし値が数字であるかに加えて特定のユニットであるかをチェックする場合には以下が利用できます:</p>

<ul>
<li>ispixel</li>
<li>ispercentage</li>
<li>isem</li>
</ul>

<p>最後にガードの中での条件文に追加の条件が必要な場合には `and` キーワードを利用できます: </p>
{% highlight text %}
.mixin (@a) when (isnumber(@a)) and (@a > 0) { ... }
{% endhighlight %}
<p><code>not</code>キーワードは否定の条件になります:</p>
{% highlight text %}
.mixin (@b) when not (@b > 0) { ... }
{% endhighlight %}

<h2>ルールセットのネスト</h2>

<p>LESSはカスケードの代わり、またはカスケードとのコンビネーションとして *入れ子* を利用することができます。<br>
例えば以下のCSSを見てください:</p>
{% highlight text %}
#header { color: black; }
#header .navigation {
	font-size: 12px;
}
#header .logo { 
	width: 300px; 
}
#header .logo:hover {
	text-decoration: none;
}
{% endhighlight %}
<p>LESSでは以下のように記述することができます:</p>
{% highlight text %}
#header {
	color: black;
	.navigation {
		font-size: 12px;
	}
	.logo {
		width: 300px;
		&:hover { text-decoration: none }
	}
}
{% endhighlight %}
<p>または下記のように書くこともできます:</p>
{% highlight text %}
#header        { color: black;
	.navigation  { font-size: 12px }
	.logo        { width: 300px;
		&:hover    { text-decoration: none }
	}
}
{% endhighlight %}
<p>コードは短く、<code>DOMツリー</code>の構造に近い書き方をすることができます。</p>

<p><code>&amp;</code>コンビネータは親セレクタに対して子孫セレクタとしてセレクタを入れ子にする代わりに、親セレクタに連結することができます。</br>
疑似クラスの<code>:hover</code>や<code>:focus</code>を定義する場合には特に重要になります。</p>

<p>例:</p>
{% highlight text %}
.bordered {
	&.float {
		float: left; 
	}
	.top {
		margin: 5px; 
	}
}
{% endhighlight %}
<p>は以下のように出力されます:</p>
{% highlight text %}
.bordered.float {
	float: left;  
}
.bordered .top {
	margin: 5px;
}
{% endhighlight %}

<h2>&amp;の高度な使い方</h2>

<p>
&amp;記号は複数クラスと入れ子ルールの順番を反対にするのにも利用できます。
</p>

<p>例:</p>

{% highlight text %}
.child, .sibling {
    .parent & {
        color: black;
    }
    & + & {
        color: red;
    }
}
{% endhighlight %}

<p>は以下のように出力されます:</p>

{% highlight text %}
.parent .child,
.parent .sibling {
    color: black;
}
.child + .child,
.child + .sibling,
.sibling + .child,
.sibling + .sibling {
    color: red;
}
{% endhighlight %}

<p>
また&amp;はミックスイン内でミックスイン外の入れ子ルールに対する参照としても利用できます。
</p>

<h2>演算</h2>
<p>数字、色、変数に演算を利用することができます。以下にいくつかの例をあげます:</p>
{% highlight text %}
@base: 5%;
@filler: @base * 2;
@other: @base + @filler;

color: #888 / 4;
background-color: @base-color + #111;
height: 100% / 2 + @filler;
{% endhighlight %}
<p>これらは期待した通りに出力されます。LESSは色とユニットの違いを解釈することができます。<br>  
もしユニットを演算で利用した場合:</p>
{% highlight text %}
@var: 1px + 5;
{% endhighlight %}
<p>LESSはそのユニットを結果に反映します。この例では<code>6px</code>となります。</p>

<p>カッコも演算では期待通りに解釈されます:</p>
{% highlight text %}
width: (@var + 5) * 2;
{% endhighlight %}
<p>また以下のような複合値では必須となります:</p>
{% highlight text %}
border: (@width * 2) solid black;
{% endhighlight %}

<h2>カラー関数</h2>

<p>LESSには色を変換するための関数があります。<br>
色はまず<em>HSL</em>カラースペースに変換され、チャネルレベルで操作することができます:</p>
{% highlight text %}
lighten(@color, 10%);     // @colorより10%明度が*高い*値を返します
darken(@color, 10%);      // @colorより10%明度が*低い*値を返します

saturate(@color, 10%);    // @colorに10%の彩度を*追加*した値を返します
desaturate(@color, 10%);  // @colorから10%の彩度を*削減*した値を返します

fadein(@color, 10%);      // @colorから10%透明度が*高い*値を返します
fadeout(@color, 10%);     // @colorから10%透明度が*低い*値を返します
fade(@color, 50%);        // @colorの50%の透明度を持つ値を返します

spin(@color, 10);         // @colorから10度色相が大きい値を返します
spin(@color, -10);        // @colorから10度色相が小さい値を返します

mix(@color1, @color2);    // @color1 と @color2 をミックスした値を返します

contrast(@color1, @darkcolor, @lightcolor); 
		// もし@color1が >50%以上のluma(例: 明るい色)の場合に@darkcolorを返し、
		// そうでなければ@lightcolorを返します
{% endhighlight %}

<p>使い方は簡単です:</p>
{% highlight text %}
@base: #f04615;

.class {
color: saturate(@base, 5%);
	background-color: lighten(spin(@base, 8), 25%);
}
{% endhighlight %}
<p>また色の情報を抽出することもできます:</p>
{% highlight text %}
hue(@color);        // @colorの色相チャネルの値を返します
saturation(@color); // @colorの彩度チャネルの値を返します
lightness(@color);  // @colorの明度チャネルの値を返します
alpha(@color);      // @colorの透明度チャネルの値を返します
luma(@color);       // @colorのluma値(知覚明度)を返します
{% endhighlight %}
<p>これは別のカラーチャネルを利用して新しい色を作成する場合に活用できます。例えば:</p>
{% highlight text %}
@new: hsl(hue(@old), 45%, 90%);
{% endhighlight %}

<p><code>@new</code>は<code>@old</code>の<em>色相</em>を保ちつつ、自身の彩度と明度を定義しています。</p>

<h2>Math関数</h2>
<p>LESSは数字の値に対して利用できる便利なmath関数を用意しています:</p>
{% highlight text %}
round(1.67); // `2` を返します
ceil(2.4);   // `3` を返します
floor(2.6);  // `2` を返します
{% endhighlight %}
<p>値をパーセンテージに変換させたい場合はパーセンテージ関数を利用することができます:</p>
{% highlight text %}
percentage(0.5); // `50%` を返します
{% endhighlight %}
<h2>名前空間</h2>

<p>構成やカプセル化のために変数やミックスインをグループ化する必要があることがあります。<br>
LESSではそれを直感的に行うことがでます。例えば配布目的や再利用の際に<code>#bundle</code>以下に変数やミックスインを1つにまとめたいとします:</p>
{% highlight text %}
#bundle {
	.button () {
		display: block;
		border: 1px solid black;
		background-color: grey;
		&:hover { background-color: white }
	}
	.tab { ... }
	.citation { ... }
}
{% endhighlight %}
<p><code>#header a</code>で<code>.button</code>ミックスインを利用したい場合、以下のように記述できます:</p>
{% highlight text %}
#header a {
	color: orange;
	#bundle > .button;
}
{% endhighlight %}
<h2>スコープ</h2>

<p>LESSにおけるスケープはほかのプログラム言語と似ています。変数やミックスインはローカル内をまず検索し、見つからなければ
親スコープを検索します。</p>
{% highlight text %}
@var: red;

#page {
	@var: white;
	#header {
		color: @var; // white
	}
}

#footer {
	color: @var; // red  
}
{% endhighlight %}

<h2>コメント</h2>

<p>CSSと同じコメントルールをLESSでも利用できます:</p>
{% highlight text %}
/* Hello, I'm a CSS-style comment */
.class { color: black }
{% endhighlight %}
<p>または1行コメントもLESSでは利用できますがこのコメントはコンパイル後のCSSには表示されません:</p>
{% highlight text %}
// Hi, I'm a silent comment, I won't show up in your CSS
.class { color: white }
{% endhighlight %}

<h2>インポート</h2>

<p><code>.less</code>ファイルはCSSと同じようにインポートすることができ、変数やミックスインはメインファイルでも利用することができます。<br>
<code>.less</code>拡張子は任意のため以下の2行とも同じ意味となります:</p>
{% highlight text %}
@import "lib.less";
@import "lib";
{% endhighlight %}
<p>通常のCSSファイルをインポートし、LESSでの処理が必要ない場合、<code>.css</code>拡張子を利用してください:</p>
{% highlight text %}
@import "lib.css";
{% endhighlight %}

{% highlight text %}
@import "common.less"; // reset, grid, layout, typography, etc
@import "module.less"; // modules
@import "style.less"; // project specific style

@import "home.less"; // styles for home
@import "about.less"; // styles for about page
@import "article.less"; // styles for article page
{% endhighlight %}


<p>ファイルは変更されず出力されます。</p>

<h2>文字列の挿入</h2>

<p>変数はrubyやPHPと同じように<code>@{name}</code>と定義することで文字列を挿入することができます:</p>
{% highlight text %}
@base-url: "http://assets.fnord.com";
background-image: url("@{base-url}/images/bg.png");
{% endhighlight %}

<h2>エスケープ</h2>

<p>CSSシンタックスにおいて有効ではないか、あるいはLESSが認識できない固有のシンタックスを利用する必要がある場合に<code>~</code>を文字列の前に含めることで出力することができます:
</p>
{% highlight text %}
.class {
	filter: ~"progid:DXImageTransform.Microsoft.AlphaImageLoader(src='image.png')";
}
{% endhighlight %}

<p>エスケープ値と呼ばれ、以下のような出力になります:</p>
{% highlight text %}
.class {
	filter: progid:DXImageTransform.Microsoft.AlphaImageLoader(src='image.png');
}
{% endhighlight %}

<h2>セレクタの挿入</h2>

<p>If you want to use less variables inside selectors, you can do this by referencing the variable using <code>@{selector}</code> as
in string interpolation. For example:</p>

<p>
もしLESSの変数をセレクタとして利用したい場合に、文字列の挿入と同じように<code>@{selector}</code>を使って変数を参照できます。例として:
</p>

{% highlight text %}
@name: blocked;
.@{name} {
    color: black;
}
{% endhighlight %}

<p>は以下のように出力されます:</p>

{% highlight text %}
.blocked {
    color: black;
}
{% endhighlight %}

<p>Note: prior to less 1.3.1 a <code>(~<span class="string">"@{name}"</span>)</code> type of selector was supported. Support for this will be removed in the near future.</p>

<p>
注意: LESS 1.3.1以前のバージョンでは<code>(~<span class="string">"@{name}"</span>)</code>のようなセレクタはサポートされていましたが、近い将来このサポートされなくなる予定です。
</p>

<h2>JavaScriptの実行</h2>

<p>
.lessファイル内でJavaScriptの式は値内で実行することができます。ただしこの機能を使う場合は注意が必要です。LESSのバージョン違いの互換性を保つことが出来ませんし、LESSそのもののメンテナンスも難しくなるからです。可能であれば、実装しようとしている関数についてはgithubにて依頼してください。現在デフォルトで利用できる関数について拡張しようとしています。しかし、それでもJavaScriptを.lessファイル内で実行したい場合は式をバックティックスで囲んでください:
</p>

{% highlight text %}
@var: `"hello".toUpperCase() + '!'`;
{% endhighlight %}

<p>上記は以下のように評価されます:</p>
{% highlight text %}
@var: "HELLO!";
{% endhighlight %}

<p>文字列の挿入やエスケープを行うこともできます:</p>
{% highlight text %}
@str: "hello";
@var: ~`"@{str}".toUpperCase() + '!'`;
{% endhighlight %}

<p>上記は以下のように評価されます:</p>
{% highlight text %}
@var: HELLO!;
{% endhighlight %}

<p>またJavaScriptの環境にアクセスすることもできます:</p>
{% highlight text %}
@height: `document.body.clientHeight`;
{% endhighlight %}
