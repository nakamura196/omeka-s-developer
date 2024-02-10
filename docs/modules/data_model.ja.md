# データモデル

## データモデルのインストール

Doctrineエンティティーのためのデータベーステーブルを作成する必要があるモジュールについては、エンティティクラスを定義した後にこのプロセスに従ってください。

まず、Doctrineがエンティティとプロキシクラスを見つけられるようにモジュールを設定する必要があります。`config/module.config.php`に以下のように追加してください：
php
    'entity_manager' => [
        'mapping_classes_paths' => [
            dirname(__DIR__) . '/src/Entity',
        ],
        'proxy_paths' => [
            dirname(__DIR__) . '/data/doctrine-proxies',
        ],
    ],

次に、ターミナルで以下のコマンドを実行します：

    $ php application/data/scripts/update-db-data-module.php <ModuleName>

これにより、Doctrine "プロキシ" クラスファイルがエンティティのために作成され、「data/doctrine-proxies」にあるモジュールに配置されます。この作業中に、データモデルをインストールするために必要なSQLステートメントが出力されます。ステートメントをコピーし、`Module.php`ファイルの `install()` 関数で、データベース接続サービスを使用してそれらを実行します：
php
    public function install(ServiceLocatorInterface $services)
    {
        $connection = $services->get('Omeka\Connection');
        $connection->exec('CREATE TABLE my_entity ...');
        $connection->exec('CREATE TABLE my_other_entity ...');
        $connection->exec('ALTER TABLE my_entity ...');
        $connection->exec('ALTER TABLE my_other_entity ...');
    }

`exec()`は一度に複数のステートメントを実行できる能力を持ちますが、SQLエラーが複数行の入力によって隠されるのを防ぐため、一つのステートメントごとに一度だけ実行するべきです。

## データモデルの更新

モジュールのデータモデルに変更を加えたときは、前のセクションの最初のインストールSQLを取得するのとほぼ同じプロセスに従う必要があります。上記のように、`update-db-data-module`スクリプトを実行してSQLステートメントを取得し、モジュールの `install` メソッドを更新したステートメントを使用して更新します。

さらに、古いバージョンのモジュールをすでにインストールしているユーザーのためにデータベースを更新する必要があります（そうしなければ、データを失うことになるので、アンインストールして再インストールを強いられます）。最初のステップは、`config/module.ini`ファイル内のモジュールのバージョン番号を増やすことです。Omeka Sは、モジュールのバージョン番号が増加した場合にのみ、アップグレードが必要であることを検出できます。バージョン番号は[セマンティック バージョニング](http://semver.org/)に従うべきです。

アップグレードに必要な作業は、行った変更の種類によって異なる場合がありますが、通常は前のインストールSQLステートメントと新しいものを比較し、適切にALTERステートメントを作成して、必要なテーブル、カラム、インデックスの追加、削除または変更を行うだけで十分です。より複雑な変更では、実際の行をUPDATEまたはINSERTする必要があります。

初期のSQLがModuleクラスの `install` メソッドに入るのと同様に、アップグレードSQLは `upgrade` メソッドに入ります。ただし、`install` とは異なり、`upgrade` には重要な追加の引数があります：`$oldVersion` および `$newVersion` はそれぞれ、ユーザーがアップグレードする前のプラグインのバージョンと、アップグレードするバージョンを表します。ほとんどの場合では、`$oldVersion`がここで重要です：以前のバージョンが、今増やした新しいバージョンよりも低いかどうかを確認する必要があります。もしそうなら、アップデートSQLを実行する必要があります。

モジュールのバージョンはセマンティック バージョニングに従うため、Composerの "Semver" パッケージの [Comparator](https://github.com/composer/semver#comparator) クラスを使ってバージョン番号を比較するべきです。Omeka Sはすでにこれを依存関係として含んでいるので、単に `use` で利用します。
php
use Composer\Semver\Comparator;

// ...

public function upgrade($oldVersion, $newVersion, ServiceLocatorInterface $services)
{
    $connection = $services->get('Omeka\Connection');
    if (Comparator::lessThan($oldVersion, '1.1.0')) { 
        $sql = $sqlDump; // あなたのアップグレードSQLステートメント 
        $connection->exec($sql);
    }
}

アップデートを続けて行い、`upgrade` メソッドに追加するにつれて、ユーザーは一度に複数のバージョンをアップグレードできるようになります。