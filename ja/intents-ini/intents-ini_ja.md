---
layout: page_toc
title: "intents.ini 仕様"
---

# 概要
Map2Geoでは地理情報の送信インテントについて、ユーザー独自のフォーマットを定義できます(Ver4.00以降)。
定義ファイル "intents.ini" を用意することで、Map2Geoが標準でサポートしている送信対象アプリ以外に対して独自にサポートを追加することが可能となります。

# intents.ini
ユーザー独自の追加フォーマットの定義ファイル。
* <端末内部ストレージ>/map2geo/intents.ini
* テキストファイル
* 改行コード :CR \| LF \| CRLF
* エンコード :UTF-8を推奨

## ファイル内容の構成
* キー=値 のエントリの羅列で構成される
* [セクション名]でアプリ毎の定義を分ける
* 行頭、行末のスペース、タブは無視される
* ";"(セミコロン)で始まる行はコメント行として無視される
    * 行頭以外ではコメントとみなされない

```
[セクション名1]
;コメント
キー=値
 :
 :
[セクション名2]
;コメント
キー=値
 :
 :
```
* 送信対象アプリ毎にセクションを設ける
    * 最初のセクションの前に書かれたエントリはセクション名""(空文字列)のエントリとして扱われる
    * セクション名が重複する場合、同一セクションとして結合される
* セクション名は任意の文字列
* キーは既定された文字列(後述)
* 値は任意の文字列

## 例
```
[0]
;Googleマップを常にズームレベル20で、地名をExampleとして起動する
data=geo:{lat},{lng}?q={lat},{lng}(Example)&z=20
package=com.google.android.apps.maps
```
上記の内容をテキストファイルにコピペして、端末内部ストレージ/map2geo/intents.ini として置くことで試すことができる。

# ファイル解釈のフロー
次のルールで解釈される:
* セクションの出現順に解釈される
* セクション毎のdataエントリで示されるURIを受け付けるアプリの存在が検査され、存在するなら送信対象アプリとされる
    * identifier,package,class の各エントリが副次条件として評価される在がリストアップされる
* 送信対象アプリの検出有無にかかわらず、全てのセクションが順に解釈される
* すでにリストアップ済みのアプリは重複してリストアップされない
    * 同一パッケージかつ検出に用いられたclass、iconactivityエントリの内容が同一なら重複と判断される
* この intents.ini の解釈の後でMap2Geoの標準対象アプリ定義が解釈される
    * つまりこのファイルで標準の動作をオーバーライドできる

# キー
* 大文字小文字は区別される
* キー,=,値の間にスペースを入れてはならない
* 値をダブルクォーテーションなどで囲ってはならない

キーの一覧は次の通り:

|キー|意味|デフォルト値|例|
|---|---|---|---|
|data|送信されるインテントのURIテンプレート|空文字列|geo:{lat},{lng}|
|package|送信先アプリのパッケージ名|空文字列|com.google.android.apps.maps|
|class|送信先アプリのアクティビティ名|空文字列|com.google.android.maps.MapsActivity|
|identifier|送信先の存在確認用URIテンプレート|空文字列|google.navigation:?ll={lat},{lng}|
|iconactivity|送信先として表示するアイコン、ラベルを持つアプリのアクティビティ名|空文字列|com.google.android.maps.driveabout.app.DestinationActivity|
|action|インテントのアクション文字列|android.intent.action.VIEW|android.intent.action.SEND|
|label|送信先アプリの表示名|アプリ名|Google Maps on Web|

## data
* ここで定義されたURIテンプレートに座標値などを当てはめたものが送信先アプリに送られる
* また、この値を元に送信可能なアプリが検索される
    * packageが指定されている場合はそちらが優先される

構文詳細は後述。

## package
* 送信対象アプリを特定したい場合はこのエントリでパッケージ名を指定する
* このエントリがない場合はdata(あるいはidentifier)に基づくURIに反応するアプリ(複数)が送信対象としてリストアップされる

## class
* 送信対象アプリのアクティビティまでを特定したい場合は、このエントリでアクティビティのクラス名を指定する
* packageの指定がない場合は無効となる

## identifier
* 送信対象アプリを検索するために用いるURIのテンプレート
* 実際に送信されるデータと異なるURIで対象アプリを検索したい場合に用いる
    * 具体的には、データ送信をandroid.intent.action.SENDで行う(=送信データがURIではない)場合に利用する

## iconactivity
* 送信先一覧として表示されるアイコンとラベルに特定のアクティビティのものを利用したい場合に用いる
    * アクティビティは送信先アプリのパッケージに含まれるものでなければならない
    * アクティビティが存在しない場合はデフォルトのアイコンとラベルが使用される

## action
* インテントのアクションを標準(android.intent.action.VIEW)以外にしたい場合に指定する
    * 具体的には、データ送信をandroid.intent.action.SEND(テキストの送信)で行う場合に利用する

## label
* 送信先アプリの表示名を指定する
    * labelが異なる送信先は同一アプリでも別の転送先とみなされる
    * 例えばChromeでWebページを開くような送信先を複数設けたい場合には、それぞれに別のlabelを与えることで実現できる

# URIテンプレートの構文
intents.ini の data,identifierエントリの構文は次の通り:

## 基本構文
* 文字列、値、条件句の連結で構成される
* 条件句はネストできる
* 1行で完結しなければならない。複数行に分割はできない
    * ただし{\n}を記述すると改行に展開されるので、複数行形式の結果を得ることは可能

## 要素
テンプレートの要素は次の通り:

|要素|用途|
|---|---|
|文字列|そのままURIに転写される|
|値|座標値などに展開され、URIに転写される|
|条件句|値の有無などでURIへの転写を制御する|

例:
```
geo:{lat},{lng}[zoom:&z={zoom}]
```

|要素|種類|
|---|---|
|geo:    |文字列|
|{lat}   |値(緯度)|
|,       |文字列|
|{lng}   |値(経度)|
|[zoom:  |条件句開始(ズームレベルが存在するなら有効)|
|&z=     |文字列|
|{zoom}  |値(ズームレベル)|
|]       |条件句終了|

## 値
値の一覧は次の通り:

|値|意味|備考|
|---|---|---|
|{lat}     |緯度(世界測地系)||
|{lng}     |経度(世界測地系)||
|{lon}     |経度(世界測地系)||
|{latms}   |緯度(世界測地系ミリ秒形式)||
|{lngms}   |経度(世界測地系ミリ秒形式)||
|{lonms}   |経度(世界測地系ミリ秒形式)||
|{latjp}   |緯度(日本測地系)||
|{lngjp}   |経度(日本測地系)||
|{lonjp}   |経度(日本測地系)||
|{latmsjp} |緯度(日本測地系ミリ秒形式)||
|{lngmsjp} |経度(日本測地系ミリ秒形式)||
|{lonmsjp} |経度(日本測地系ミリ秒形式)||
|{zoom}    |ズームレベル(実数)|地理情報にズームレベルが存在しない場合デフォルト値18.0に展開される|
|{zoomint} |ズームレベル(整数)|地理情報にズームレベルが存在しない場合デフォルト値18に展開される|
|{label}   |地名(URIエンコード済)|地理情報に地名が存在しない場合空文字列に展開される|
|{rawlabel}|地名(URIエンコードなし)|地理情報に地名が存在しない場合空文字列に展開される|
|{\n}      |改行|  |

## 条件句
"[条件:" または "[条件!" で始まり "]" で終わる部分文字列。
"[",条件,":" "!" の間にはスペースを挟んではならない。

* 条件の評価が真の場合に条件句は有効となり、無効の場合は条件句は破棄される

* 条件句の開始トークン末尾の文字で条件の真偽を逆転できる:
    * ":" コロン :条件が真の場合に真
    * "!" びっくらメーション :条件が偽の場合に真

* 条件句には文字列、値を含むことができ、別の条件句を入れ子で含むことができる

例:
`[zoom:zoom exists]` :ズームレベルが存在する場合は文字列"zoom exists" 、存在しない場合は空文字列に評価される
`[zoom!zoom not exists]` :ズームレベルが存在しない場合は文字列"zoom not exists" 、存在する場合は空文字列に評価される
`[zoom:[label:zoom and label exist]]` ズームレベルとラベルが存在する場合は"zoom and label exist"、それ以外は空文字列に評価される

条件の一覧は次の通り:

|条件|意味|
|---|---|
|zoom|ズームレベルが存在する|
|label|地名が存在する|
|latlng|位置座標が存在する|
|latlon|位置座標が存在する|
|version=整数値|送信先アプリのバージョンコードが整数値に等しい|
|version<整数値|送信先アプリのバージョンコードが整数値より小さい|
|version>整数値|送信先アプリのバージョンコードが整数値より大きい|

## 例
```
geo:{lat},{lng}
```
>    `geo:35.660411,139.729265` のように展開される

```
geo:{lat},{lng}[zoom:?z=zoom]
```
>    `geo:35.660411,139.729265`  または `geo:35.660411,139.729265?z=18.0` のように展開される

```
geo:{lat},{lng}[label:?q={lat},{lng}({label})[zoom:&z={zoom}]][label![zoom:?z={zoom}]]
```
 >   次のように展開される:
 >    `geo:35.660411,139.729265` 
 >    `geo:35.660411,139.729265?q=35.660411,139.729265(google)` 
 >    `geo:35.660411,139.729265?q=35.660411,139.729265(google)&z=18.0` 
 >    `geo:35.660411,139.729265?z=18.0` 

# 備考
Map2Geoのルート転送機能はこの定義ファイルでは取り扱えない。
ルートの転送処理においてこの定義ファイルは参照されない。

# Tips
## Map2Geoが送信する標準のgeo URIフォーマット
Ver4.00時点では次の通り:
```
geo:{lat},{lng}[?z={zoomint}]
```
* ～Ver3.12まではz=... が付与されない
* 送信先アプリに応じて変更される場合がある

## geoインテント以外のインテントの発行
発行できるインテントはgeoインテントに限定されない。

### ブラウザの起動
例えば次の定義でブラウザのGoogleマップが開く:
```
data=https://www.google.com/maps/@{lat},{lng},{zoom}z
```
この一行をテキストファイルにして、端末内部ストレージ/map2geo/intents.ini として置くことで試すことができる。
セクションの記述は不要(名前が空文字列のセクションとして解釈される)。

### テキストの共有
action=android.intent.action.SEND を指定することでメモアプリなどへのテキストを転送することも可能。

Map2Geoは action=android.intent.action.SEND の場合、dataで生成された文字列を Intent.EXTRA_TEXT のExtraに詰めて type="text/plain" でインテントを生成して送出する。
```
data={label}{\n}https://www.google.com/maps/@{lat},{lng},{zoom}z
action=android.intent.action.SEND
```
上記の定義で
>Google
>https://www.google.com/maps/@35.660411,139.729265,16.0z

というようなテキストがメーラーなどに共有される。

ただしこの例だとすべてのandroid.intent.action.SEND対応アプリがリストアップされ、以降のandroid.intent.action.SENDを使用するセクションがマスクされてしまう。
`package=com.google.android.gm `のように追記して特定のアプリを指定することが望ましい。

