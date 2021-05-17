---
layout: page
title: "Map2Geo 使い方"
---

* 目次
{:toc}

# これはなに
Googleマップなどの地図アプリ上で指定した場所を他のアプリに転送するためのアプリです。
アンドロイドの「共有」機能を介して転送を行います。

![][whats]

# 基本的な使い方
地図アプリ上で転送したい場所を選択

![][basis01]

----

共有を実行

![][basis02]

----

共有先としてこのアプリを選択

![][basis03]

----

場所の転送先アプリの一覧が表示されます

![][basis04]

----

選択したアプリに場所が転送されます

![][basis05]

# ルートの転送
Googleマップなどで作成したルートも転送できます。

ルートの共有を実行

![][route01]

----

共有先としてこのアプリを選択

![][route02]

----

ルートの転送先アプリの一覧が表示されます

![][route03]

----

選択したアプリにルートが転送されます

![][route04]

## ルートの転送での注意点
### 経路
ルートの転送では出発地、経由地、目的地が転送されますが、それらの間を結ぶ経路は転送先アプリによって選択されます。
したがって転送元と転送先で異なる経路が提示される場合があります。

### 経由地
アプリによってルートに含むことができる経由地の数の上限が異なるため、転送したルートの経由地が多すぎる場合に転送に失敗するか、あるいは全ての経由地を転送できないことがあります。

### 条件
交通手段や有料道路の利用可否などの条件は転送されません。

# ショートカットの作成
場所/ルートをマップアプリで開くショートカットをホーム画面に作ることができます。

![][shortcut01] ![][shortcut02]

## 場所のショートカット
次の手順で作成します:
* アプリのアイコンをロングタップします
* ショートカットの名前を編集します
* ホーム画面にショートカットが作成されます

![][shortcut03]

## ルートのショートカット
ルートのショートカットも場所と同様の手順で作成できます。

![][shortcut04]

## アプリ選択画面を開くショートカット
アプリ選択画面でMap2Geoのアイコンをロングタップすることで、アプリ選択画面を開くショートカットを作成できます。

![][shortcut05]

![][shortcut06]

## ショートカットの作成の注意点
### 現在地の扱い
現在地を含むルートを転送する場合、転送元アプリによって現在地の扱いが異なります。

次の二通りの仕様があります:
* 共有操作を行った時点での現在地を保持
* 共有結果を参照する時点での現在地をその都度適用

前者の仕様のアプリから転送された「現在地を含むルート」をショートカット化した場合、ルート上の現在地は共有操作を行った時点での端末所在地で固定されます。

# 場所/ルートの情報表示
アプリ選択画面でMap2Geoのアイコンをショートタップすることで、場所/ルート経由地の情報を表示します。

![][placeinfo01]

項目をタップすると座標の表示形式が切り替わります。

# URLインジェクターとの連携
Map2Geo URLインジェクターをインストールすると、マップを表示するためのリンクから任意のマップアプリを起動することができるようになります。

![][icon_injector] Map2Geo URLインジェクター:
https://play.google.com/store/apps/details?id=catfish.android.map2geo.urlinjector

ブラウザなどで表示されるマップへのリンクを開く際に、URLインジェクターが対応しているリンクであれば座標を取得してマップアプリへ転送することができます。

![][injector01]


[icon]:images/ic_launcher.png
[icon_injector]:images/map2geo_urlinjector.png

[whats]:images/whats.png

[basis01]:images/basis01.png
[basis02]:images/basis02.png
[basis03]:images/basis03.png
[basis04]:images/basis04.png
[basis05]:images/basis05.png

[route01]:images/route01.png
[route02]:images/route02.png
[route03]:images/route03.png
[route04]:images/route04.png

[shortcut01]:images/shortcut01.png
[shortcut02]:images/shortcut02.png
[shortcut03]:images/shortcut03.png
[shortcut04]:images/shortcut04.png
[shortcut05]:images/shortcut05.png
[shortcut06]:images/shortcut06.png

[placeinfo01]:images/placeinfo01.png

[injector01]:images/injector01.png