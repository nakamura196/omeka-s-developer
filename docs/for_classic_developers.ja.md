# Omeka Classic 開発者向け

Omeka S は文化遺産機関がそのコレクションをウェブ上で公開する手助けをするためのオープンソースソフトウェアという Omeka Classic の精神と原則を引き続き守っています。しかし、コードベースはまったく異なります。以下に大まかな違いをガイドとして示します。詳しくは「キーコンセプト」セクションをお読み下さい。

## 基盤技術

### Zend Framework

Omeka Classic は Zend Framework 1 に基づいて構築されましたが、これはすでにサポートが終了しています。 Omeka S は全く異なるアーキテクチャの Zend Framework 3 上に構築されています。Omeka S が活用しているいくつかの重要な点は以下の通りです。

* サービスに大きく依存している
* 工場（ファクトリー）の広範な利用
* `module.config.php`ファイル内の配列による設定
* 本物の PHP 名前空間の使用

### Doctrine ORM

Omeka S はデータベースクエリとレコードの定義に Doctrine Object Relation Mapper を採用しました。それにより、レコードはもはや `Omeka_Record_AbstractRecord`クラスから派生していません。代わりに `Omeka\Entity\AbstractEntity` から拡張されます。

これによって、Omeka S は `エンティティ(Entity)`、`API アダプタ(Api Adapters)`、そして `表現(Representations)` の間で関心を分離する事ができます。これは Omeka Classic がモデルとしてレコードやオプションのテーブルクラスを定義するシステムを置き換えます。

## 用語

上記に基づき、Omeka Classic のいくつかの共通用語には Omeka S の新しい類似があります。

| Omeka Classic | Omeka S        |
|---------------|----------------|
|フックとフィルター|イベント        |
|プラグイン        |モジュール      |
