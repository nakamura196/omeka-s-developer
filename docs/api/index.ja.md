# APIへの導入

Omeka Sは、そのリソースに対して[CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)
操作を可能にするアプリケーションプログラミングインタフェース（API）を提供しています。APIリソースはタイプ、関連データ、
他のリソースとの関連性、およびそれらに対して操作する一連のメソッドを持つオブジェクトです。OmekaのAPIにアクセスする方法は2つあります：

- OmekaのPHP環境内部からプログラム的に（[PHP APIドキュメント](php_api)を参照）
- Omekaの[REST](https://en.wikipedia.org/wiki/Representational_state_transfer)ウェブサービスを使用して（[REST APIドキュメント](rest_api)を参照）

## 操作

ほぼ全てのAPIリソースに対してこれらのAPI操作を実行できます：

| 操作 | 説明 |
| --- | --- |
| search | クエリパラメーターを指定してリソースリストを取得 |
| read | ユニークな識別子を指定して個別のリソースを取得 |
| create | 新しいリソースを作成 |
| update | ユニークな識別子を指定して既存のリソースを更新 |
| delete | ユニークな識別子を指定して既存のリソースを削除 |

## リソース

Omekaには以下のAPIリソースが付属しています：

| リソース | 説明 |
| --- | --- |
| users | ログイン資格情報を持つユーザー |
| vocabularies | OmekaにインポートされたRDF語彙 |
| resource_classes | 語彙に属するRDFクラス |
| properties | 語彙に属するRDFプロパティ |
| items | Omekaの基盤となるアイテムRDFリソース |
| media | アイテムに属するメディアRDFリソース |
| item_sets | アイテムの包括的セットであるアイテムセットRDFリソース |
| resource_templates | RDFリソースの記述方法を定義するテンプレート |
| sites | Omekaサイト、Omekaのパブリックコンポーネント |
| site_pages | サイト内のページ |
| modules | Omeka機能を拡張するモジュール（検索および読み取りアクセスのみ） |
| api_resources | このインストールで利用可能なAPIリソース（検索および読み取りアクセスのみ） |

「APIリソース」と「RDFリソース」を混同しないでください。RDFリソースはRDF語彙を使用して記述されリンクされうるリソースです。
Omekaにおける唯一のRDFリソースはアイテム、メディア、およびアイテムセットです。（[リソース記述フレームワーク](https://en.wikipedia.org/wiki/Resource_Description_Framework)を参照。）