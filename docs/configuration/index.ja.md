# Omeka S の設定

Omeka S を設定するには `config/local.config.php` ファイルを編集します。アプリケーションおよびモジュールの設定ファイルから設定がローカル設定に統合され、Omeka S サービスがアプリケーションを操作するために使用されます。デフォルト設定とその形式を参照する場合は、以下のファイルを参照してください（これらのファイルを変更しないでください）：

- `application/config/module.config.php`：読み取り専用のデフォルト設定（[設定設定](config_reference.md)を参照）
- `application/config/navigation.config.php`：読み取り専用のナビゲーション設定（[zend-navigationドキュメント](https://docs.zendframework.com/zend-navigation/pages/#mvc-pages)を参照）
- `application/config/routes.config.php`：読み取り専用のルーティング設定 （[zend-mvcドキュメント](http://zendframework.github.io/zend-mvc/routing/)を参照）

ローカル設定には、インストールごとに変更される可能性が最も高い設定が含まれています。システムの要件に応じて、さらに追加してください。

モジュールはそれぞれの `config/module.config.php` ファイルを介して独自の設定を追加します。これらの設定設定はローカル設定に統合され、コア設定を拡張し、場合によっては変更することができます。詳細については [モジュール](../modules/index.md) を参照してください。

## Invokables と Factories

設定ファイルの `invokables`、`factories` などのサブキーをよく見かけます。これらは、Omeka S のさまざまなサービスマネージャーが新しいサービスを作成する方法を指します。サービスの構成方法については、[サービスとファクトリ](services_and_factories.md) および [zend-servicemanager ドキュメント](https://docs.zendframework.com/zend-servicemanager/configuring-the-service-manager/) を参照してください。
