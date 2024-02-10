# Doctrine ORM

Omeka Sはオブジェクト関係マッパーとして[Doctrine](http://www.doctrine-project.org/)を使用しています。以下はOmeka Sがどのように使用しているかの概要です。詳細はDoctrineのドキュメンテーションを参照してください。

## エンティティの定義

データベースに保存するタスクには、`Omeka\Entity\AbstractEntity` クラスを拡張する必要があります。Doctrineはアノテーションを使用してデータベース関連を定義し、必要なスキーマを作成するデータベースクエリを作成できます。`Omeka\Entity\Module`は基本的な例を提供しています。
php
<?php
namespace Omeka\Entity;

/**
 * @Entity
 */
class Module extends AbstractEntity
{
    /**
     * @Id
     * @Column(type="string", length=190)
     */
    protected $id;

    /**
     * @Column(type="boolean")
     */
    protected $isActive = false;

    /**
     * @Column
     */
    protected $version;
    
    // 以下に関数の定義が続きます
    
}

トップレベルのコメントブロックでの`@Entity`宣言は、このクラスがデータベースに表されるべきであることをDoctrineに伝えます。そのクラスのインスタンスはデータベースの行になります。コメントブロック内に`@Column`宣言を持つ各保護プロパティは、プロパティ内のデータが行の列として表されるべきであることをDoctrineに伝えます。

したがって、`module`テーブルには3つの列があります：文字列型の`id`、ブール型の`isActive`、および`version`です。

Doctrineの`orm:schema-tool:update --dump-sql`ツールを使って、このようにテーブルを作成するために使用されるSQLクエリを確認することができます。もちろん、Omeka Sはこのプロセスをインストールプロセスに統合しています。モジュールがこのプロセスを実装すべき方法については、[モジュールのデータモデルページ](../modules/data_model.md)を参照してください。

## コアデータモデルの更新

Omekaの開発を進める中で、データモデルを変更する理由を見つけることがあるでしょう。エンティティの追加や削除、または列の追加、削除、変更を行った場合、これらの変更をデータベースに反映する必要があります。

まず、既存のインストールされたデータベースを更新するために必要なSQLを取得します：

    $ vendor/bin/doctrine orm:schema-tool:update --dump-sql

結果として得られるステートメントをコピーします。次に、変更のためのマイグレーションを作成する必要があります：

    $ gulp db:create-migration

これにより、マイグレーションの名前を入力するように求められます。名前を入力してエンターキーを押してください。タスクが終了したら、新しく作成されたマイグレーションファイルをdata/migration/内で開きます（現在日付スタンプでプレフィックスされています）。そして、先にコピーしたSQLを使用して必要な変更を行うために提供された接続オブジェクトを使ってください。

次に、静的なデータベース関連ファイルを更新する必要があります：

    $ gulp db

これにより、既存のインストールスキーマがデータモデルの最新バージョンに上書きされ、Doctrineのプロキシクラスが再生成されます。

Omekaが新しいマイグレーションを検出するためには、Omekaのバージョン番号を更新する必要があります。`application/Module.php`を開いて、`VERSION`定数をインクリメントします。

今度は、結果として得られた変更をステージングしてコミットします。