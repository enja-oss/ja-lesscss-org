<h1>クライアントサイドでの使用法</h1>

<p>
クライアントサイドでの利用はLESSを始めるのに最も手軽な方法で、開発を行うのにも便利です。プロダクション用、とくにパフォーマンスが重要になる場合はnodeかサードパーティツールを使ってプリコンパイルを行うことを推奨しています。
</p>

<p>
	<code>.less</code>スタイルシートを<code>rel</code>の値を"<code>stylesheet/less</code>"にしてリンクします:
</p>
{% highlight text %}
    <link rel="stylesheet/less" type="text/css" href="styles.less">
{% endhighlight %}

<p>
	<code>less.js</code>をページの上記からダウンロードし、<code>&lt;head&gt;</code>内に下記のように追記します:
</p>
{% highlight text %}
    <script src="less.js" type="text/javascript"></script>
{% endhighlight %}

<p>
	スタイルシートは必ずスクリプトの<em>前</em>に記述するようにしてください。
</p>

<h3>ウォッチモード</h3>

<p>
	<em>ウォッチモード</em>はスタイルの変更が保存されるたびに自動的に更新がされるクライアントサイドの機能です。
</p>
<p>
ブラウザ上のURLの後に‘<code>#!watch</code>’を追記してページを更新するか、コマンドラインから<code>less.watch()</code>を実行すれば利用できます。
</p>

<h1>サーバーサイドでの使用法</h1>

<h3>インストール</h3>

<p>
	LESSをサーバにインストールするのにもっとも簡単な方法はnode.jsのパッケージマネージャである<a href="http://github.com/isaacs/npm">npm</a>を利用する方法です:
</p>
{% highlight text %}
    $ npm install -g less
{% endhighlight %}

<h2>コマンドラインでの利用方</h2>

<p>LESSはコマンドラインからコンパイラを起動することができます:</p>
{% highlight text %}
    $ lessc styles.less
{% endhighlight %}

<p>上記のコマンドにより<code>stdout</code>へコンパイル後のCSSを出力できます。この出力をお好きなファイルにリダイレクトすることができます:</p>
{% highlight text %}
    $ lessc styles.less > styles.css
{% endhighlight %}
<p><code>-x</code>オプションを追記することで圧縮したCSSを出力することができます。もう少し高度な圧縮を利用したい場合は<a href="http://developer.yahoo.com/yui/compressor/css.html">YUI CSS Compressor</a>を<code>--yui-compress</code>オプションを使って利用できます。</p>

<h3>node.jsでの利用方</h3>

<p>
	node.jsから下記のようにコンパイラを起動できます:
</p>
{% highlight text %}
    var less = require('less');
    less.render('.class { width: 1 + 1 }', function (e, css) {
        console.log(css);
    });
{% endhighlight %}

<p>以下のように出力されます。</p>
{% highlight text %}
    .class {
      width: 2;
    }
{% endhighlight %}

<p>以下のように手動でパーサとコンパイラを起動することもできます:</p>
{% highlight text %}
    var parser = new(less.Parser);

    parser.parse('.class { width: 1 + 1 }', function (err, tree) {
        if (err) { return console.error(err) }
        console.log(tree.toCSS());
    });
{% endhighlight %}

<h2>設定</h2>

<p>コンパイラに対していくつかのオプションを設定できます:</p>
{% highlight text %}
    var parser = new(less.Parser)({
        paths: ['.', './lib'], // @import用にパスを指定します
        filename: 'style.less' // わかりやすいエラーメッセージを出力するためファイル名を指定します
    });

    parser.parse('.class { width: 1 + 1 }', function (e, tree) {
        tree.toCSS({ compress: true }); // CSS出力を圧縮します
    });
{% endhighlight %}

<h1>サードパーティツール</h1>

<p>githubのwiki上でツールに関するセクションがドキュメントされています。</p>

<p><a href="https://github.com/cloudhead/less.js/wiki/Command-Line-use-of-LESS">コマンドラインツール</a></p>

<p><a href="https://github.com/cloudhead/less.js/wiki/GUI-compilers-that-use-LESS.js">GUIツール</a></p>
