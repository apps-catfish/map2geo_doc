---
layout: page
title: "外部デコーダー 仕様"
---

* Table of contents
{:toc}

----

# 概要
Map2Geo Ver6.00以降では、マップアプリ等からMap2Geoへ共有を行った際の座標の解析処理をユーザーが独自にプログラムできるようになっています。

JavaScriptを記述したファイル(以降スクリプトファイルと呼びます)を端末の内部共有ストレージに置くことで、マップアプリ等から共有されたデータをそのJavaScriptでデコードし、他のアプリへ転送する座標を得ることができます。

# 動作の概略
マップアプリから共有されたデータは通常、複数行の文字列で構成されています。
通例では、各行に場所の名前、その場所をブラウザで開くためのURL等が記載されており、それらがスクリプトファイル内の関数 `decode()` に渡されます。

受け取った関数はそれらの各行から地名、座標を取得して、アプリ側により用意された関数 `setResult()` に渡してデコードを完了、あるいは中止します。

URLに座標値が書かれている場合はそこから座標を取得できますが、そうではない場合はURLで示されるページの内容を取得してそれを解析し座標を得ることになります。

# ファイルの仕様

|   |   |
|---|---|
|ファイル名|<任意の名前>.html|
|ファイルの場所|<内部共有ストレージ\>/map2geo/decoder/|
|ファイルフォーマット|html|
|文字エンコード|UTF-8推奨|

JavaScript部分はhtmlの書式に従い、 `<script>...</script>` で囲まれている必要があります。
`<html>,<head>,<body>` 等、ドキュメントの記述に関するタグの一切は不要ですが、記載されていても動作します。

# ファイル適用のルール
## 組み込みデコーダーとの適用順
スクリプトファイルによるデコードが先に行われ、すべてのスクリプトファイルについてデコード対象外であれば次にMap2Geoに組み込みのデコーダーによる処理が行われます。

## 複数のスクリプトファイル
複数のスクリプトファイルが利用できます。
送信元となる1つのアプリに対して1つのスクリプトファイルを用意するような運用が可能です。

## スクリプトファイルの適用順
* ファイル名の昇順で順次適用されます。
* そのスクリプトファイルの処理対象ではないデータを渡された場合は順次次のスクリプトファイルによる解析が行われます
* いずれのスクリプトファイルでも処理されなかった場合Map2Geoの内蔵デコーダーで処理されます。

### 適用順の変更
ファイル名を記述したテキストファイルを用意することで特定のスクリプトファイルを先に適用することができます

|   |   |
|---|---|
|ファイル名|order.cfg|
|ファイルの場所|<内部共有ストレージ>/map2geo/decoder/|
|文字エンコード|UTF-8推奨|
|書式|1行に1ファイル名を記載|

order.cfgに記載されたファイルが上から順に優先適用されます。

フォルダ内のスクリプトファイルが
```
1.html
2.html
3.html
4.html
```
で、order.cfgが
```
4.html
3.html
```
の場合、適用順は
```
4.html
3.html
1.html
2.html
```
となります。

# スクリプトの仕様
`decode()` で渡されたテキストを解析して `locationDecoder.setResult()` で結果を返すのが処理の骨格です。

##スクリプト内に必須の関数
次の関数の実装が必須です:
`function decode(text)`
* **引数 text** :共有で送られてきた文字列。改行を含み得る
* **戻り値** :なし

## Map2Geoから提供されるオブジェクト
JavaScriptからオブジェクト `locationDecoder` が参照可能です:

このオブジェクト `locationDecoder` のメンバ関数
* `setResult(result,latlng,zoom,title,url)` …地点を返す
* `setResult(result,waypoints,zoom,title,url)` …ルートを返す

のいずれかの呼び出しが必須です。
これらを呼び出すことによりそのスクリプトでのデコード処理を完了または中止します。

次のメンバ関数が用意されています:
### setResult(result,latlng,zoom,title,url)
デコード結果の地点情報をMap2Geoに返します。

**戻り値** :なし

**引数**

|引数|型|意味|
|---|---|---|
|result|整数|デコード結果を表す定数:0=成功,1=デコード対象ではない,-1=デコード失敗|
|latlng|文字列|”緯度,経度"で地点を表す文字列
|zoom|数値|地図のズームレベル<br>-1なら未指定|
|title|文字列|地名(nullでもよい)|
|url|文字列|decode()で渡されたデータから抽出した、場所をブラウザで表示するためのURL(nullでもよい)|

latlng,zoom,title,url はresultが0(成功)でなければ無効となり参照されません。その場合の各値はnullでも構いません。

引数urlは Map2Geo URLインジェクターを利用した場合に、URLをブラウザ等で開く際に利用されます。

### setResult(result,waypoints,zoom,title,url)
デコード結果のルート情報をMap2Geoに返します。

**戻り値** :なし

**引数**

|引数|型|意味|
|---|---|---|
|result|整数|デコード結果を表す定数:0=成功,1=デコード対象ではない,-1=デコード失敗|
|waypoints|文字列配列|”緯度,経度,地名"の文字列の配列でルートの出発地,経由地,目的地を表す<br>地名は省略可能<br>要素がnullまたは緯度経度を表さない場合は現在地として扱われる
|title|文字列|ルートのタイトル(nullでもよい)
|url|文字列|decode()で渡されたデータから抽出した、ルートをブラウザで表示するためのURL(nullでもよい)|

waypoints,title,url はresultが0(成功)でなければ無効となり参照されません。その場合の各値はnullでも構いません。

引数urlは Map2Geo URLインジェクターを利用した場合に、URLをブラウザ等で開く際に利用されます。

### getContent(url)
urlで指定されたページのコンテンツを同期的に取得します。
処理が完了するまで関数から戻りません。

**戻り値** :文字列。urlで示されるページ内容

**引数**

|引数|型|意味|
|---|---|---|
|url|文字列|ページのURL|

### getContent(url,ua)
urlで指定されたページのコンテンツを同期的に取得します。
処理が完了するまで関数から戻りません。

**戻り値** :文字列。urlで示されるページ内容

**引数**

|引数|型|意味|
|---|---|---|
|url|文字列|ページのURL|
|ua|文字列|ユーザーエージェント|

### getRedirect(url)
urlで指定されたアドレスのリダイレクト先を同期的に取得します。
処理が完了するまで関数から戻りません。
可能な限りHEADメソッドでアクセスします(メソッドの選択はAndroidのバージョンに依存します)。

**戻り値** :文字列。urlのリダイレクト先URL

**引数**

|引数|型|意味|
|---|---|---|
|url|文字列|ページのURL|

### getRedirect(url,ua)
urlで指定されたアドレスのリダイレクト先を同期的に取得します。
処理が完了するまで関数から戻りません。
可能な限りHEADメソッドでアクセスします(メソッドの選択はAndroidのバージョンに依存します)。

**戻り値** :文字列。urlのリダイレクト先URL

**引数**

|引数|型|意味|
|---|---|
|url|文字列|ページのURL|
|ua|文字列|ユーザーエージェント|

### getRedirect(url,ua,useGet)
urlで指定されたアドレスのリダイレクト先を同期的に取得します。
処理が完了するまで関数から戻りません。

**戻り値** :文字列。urlのリダイレクト先URL

**引数**

|引数 | 型 | 意味 |
|---|---|---|
| url | 文字列 | ページのURL |
| ua| 文字列| ユーザーエージェント |
| useGet | Boolean | 真の場合アクセスにHEADではなくGETメソッドを使用する |

# TIPS
* JavaScript内で`alert(),confirm(), prompt()` の各ダイアログは利用可能です
    * ただしダイアログのタイトルは変更できません
* スクリプトファイルに `<script src="..."></script>` を記述することにより、外部jsファイルを利用することが可能です
    * 指定したファイルが存在しない場合はエラーにはならずスルーされるので、デバッグ時に便利です
* スクリプト内のメインスレッドで `XMLHttpRequest#open()` による同期リクエストは利用できません
    * スクリプトの実行が終了します
    * 同期的にコンテンツを取得したいのであれば `locationDecoder.getContent()` が利用できます。アプリ的には非同期処理になるので問題はありません

# 例
これらの内容をそのままテキストファイルにして、端末の`<内部共有ストレージ>/map2geo/decoder/example.html`として置くことで動作を試すことができます。

## なにを共有しても同じ場所を返す例
```
<script>
	function decode(text) {
		locationDecoder.setResult(0,"35.6604083,139.7292602",20,"Google-Tokyo","http://www.google.com/");
	}
</script>
```

## なにを共有しても現在地から同じ場所へのルートを返す例
```
<script>
	function decode(text) {
		locationDecoder.setResult(0,[null,"35.6604083,139.7292602,Google-Tokyo"],"Route sample","http://www.google.com/");
	}
</script>
```

## なにを共有しても処理せずスルーする例
結果として別のデコーダーに解析処理が移譲されます。
```
<script>
	function decode(text) {
		locationDecoder.setResult(1,null,null,null,null);
	}
</script>
```

## なにを共有しても処理せず以降のデコードも行わない例
結果として常に座標の取得に失敗します。
```
<script>
	function decode(text) {
		locationDecoder.setResult(-1,null,null,null,null);
	}
</script>
```

## MAPS.MEからの共有をデコードする例
実際的な実装です。
共有されたテキストからタイトルとURLを抽出、URLが示すコンテンツを取得して座標を抽出しています。

アプリをインストールしていない場合、次のテキストを端末のブラウザで選択して共有することで試すことができます:

> ご覧ください
> 六本木ヒルズ森タワー
> 建物
> ge0://03g2betT9v/六本木ヒルズ森タワー
> http://ge0.me/03g2betT9v/六本木ヒルズ森タワー

```
<script type="text/javascript">
var SUCCEEDED=0;
var FAILED=-1;
var UNMATCHED=1;
var NOZOOM=-1;
var regexp_url = /https?:\/\/[^\s　]+/g;

var url;
var title;

function extractUrl(string) {
	return string.match(regexp_url);
}

function decode(text) {
	var lines=text.split("\n");
	for(var i=0;i<lines.length;i++) {
		var isUrl=false;
		if(url==null) {
			var urls=extractUrl(lines[i]);
			if(urls!=null) {
				url=urls[0];
				if(url.search(/^https?:\/\/ge0.me\/.*/)<0) url=null;
			}
		}
		if(!isUrl && i==1) title=lines[i];
	}
	
	if(url==null) {
		locationDecoder.setResult(UNMATCHED,null,null,null);
		return;
	}

	if(title==null) {
		//extract <title> from http://ge0.me/xxxx/<title>
		var parts=url.match(/(?:.+ge0\.me\/[^/]+\/)(.*)/);
		title=parts[1].replace("_"," ");
	}

	//get contents
	var content=locationDecoder.getContent(url);
	parseContent(content);
}

function parseContent(content) {
	if(content==null) {
		locationDecoder.setResult(FAILED,null,null,null,null);
		return;
	}
	
	var latlng;
	var matchResult=content.match(/var\s+coord\s+=\s+\[(.+)\]/);
	if(matchResult!=null) latlng=matchResult[1];
	
	var result=(latlng!=null) ? SUCCEEDED:FAILED;
	title="SAMPLE:"+title;
	locationDecoder.setResult(result,latlng,NOZOOM,title,url);
}
</script>
```