<h2>変数</h2>

<p>変数は広範囲で利用している値を1ヶ所で定義することができ、その値をスタイルシート内で再利用することができます。
これにより全体の変更を1行のコードの編集で行うことなうことができます。</p>

<table class="code-example" cellpadding="0">
  <tr><td>
<pre class="less-example"><code>// LESS

@color: #4D926F;

#header {
  color: @color;
}
h2 {
  color: @color;
}
</code></pre>
  </td><td>
  <pre class="css-output"><code>/* コンパイル後のCSS */

#header {
  color: #4D926F;
}
h2 {
  color: #4D926F;
}</code></pre></td>
  </tr>
</table>

<h2>ミックスイン</h2>

<p>
ミックスインはクラス名をプロパティの一部のように記述することで、そのクラス内で設定したすべてのプロパティをほかのクラスに埋め込むことができます。<br>
編集と同じような機能をクラスでも利用できるということです。
<br>
またミックスインは下記の例のように関数と同じく引数を設定できます。
</p>

<table class="code-example" cellpadding="0">
  <tr><td>
  <pre class="less-example"><code>// LESS

.rounded-corners (@radius: 5px) {
  -webkit-border-radius: @radius;
  -moz-border-radius: @radius;
  -ms-border-radius: @radius;
  -o-border-radius: @radius;
  border-radius: @radius;
}

#header {
  .rounded-corners;
}
#footer {
  .rounded-corners(10px);
}</code></pre></td>

<td>
<pre class="css-output"><code>/* コンパイル後のCSS */

#header {
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  -ms-border-radius: 5px;
  -o-border-radius: 5px;
  border-radius: 5px;
}
#footer {
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  -ms-border-radius: 10px;
  -o-border-radius: 10px;
  border-radius: 10px;
}</code></pre>
  </td></tr>
</table>

<h2>入れ子ルール</h2>

<p>LESSではセレクタをほかのセレクタに入れ子にすることで継承を指定するために長いセレクタを組み立てる必要をなくしています。
こうすることでスタイルの継承を明確にし、さらにスタイルシートを短く記述することができます。</p>

<table class="code-example" cellpadding="0">
  <tr><td>
  <pre class="less-example">
<code>// LESS

#header {
  h1 {
    font-size: 26px;
    font-weight: bold;
  }
  p { font-size: 12px;
    a { text-decoration: none;
      &:hover { border-width: 1px }
    }
  }
}

</code></pre></td>

<td>
  <pre class="css-output"><code>/* コンパイル後のCSS */

#header h1 {
  font-size: 26px;
  font-weight: bold;
}
#header p {
  font-size: 12px;
}
#header p a {
  text-decoration: none;
}
#header p a:hover {
  border-width: 1px;
}

</code></pre>
  </td></tr>
</table>

<h2>関数と演算</h2>

<p>スタイルシート内の要素でほかの要素と比例する要素がありますか？
演算は色や数値の対して足し算、引き算、割り算、かけ算を行うことができます。
これによりプロパティ間に複雑な関係性を持たせることができるようになります。
演算はCSSとの互換性を保つため括弧内でのみ実行されます。
関数はJavaScriptと同じ機能を持つため値の操作を自由に行うことができます。</p>

<table class="code-example" cellpadding="0">
  <tr><td>
  <pre class="less-example">
<code>// LESS

@the-border: 1px;
@base-color: #111;
@red:        #842210;

#header {
  color: @base-color * 3;
  border-left: @the-border;
  border-right: @the-border * 2;
}
#footer { 
  color: @base-color + #003300;
  border-color: desaturate(@red, 10%);
}

</code></pre></td>

<td>
  <pre class="css-output"><code>/* コンパイル後のCSS */

#header {
  color: #333;
  border-left: 1px;
  border-right: 2px;
}
#footer { 
  color: #114411;
  border-color: #7d2717;
}

</code></pre>
  </td></tr>
</table>
