# Omeka S のデバッグ

## 設定

開発サーバー用に Omeka S を設定するためには、以下の変更/追加が必要です。

`.htaccess` ファイルの変更

```
SetEnv APPLICATION_ENV "production"
```

から

```
SetEnv APPLICATION_ENV "development"
```

へ

`config/local.config.php` に追加

```php
'logger' => [
'log' => true,
],
```

ログメッセージは `logs/application.log` に書き込まれます。

内部アセット、例えば jQuery のローカルコピーを使用するためには、`local.config.php` に以下を追加します。
php
'assets' => [
'use_externals' => false,
],

## コントローラ内でのロギング

Omeka S はコントローラ内の `logger()` プラグインを提供しています。これによって、Omeka S のロギングシステムにアクセスできます。例えば、`$this->logger()->info("ステータス: 良好");` でログにメッセージを書き込むことができます。

`Omeka\Mvc\Controller\Plugin\Logger` オブジェクトは `Zend\Log\LoggerInterface` からのメソッドを使用しており、様々なログレベルでのメッセージを簡単に記載できます:

- emerg()
- alert()
- crit()
- err()
- warn()
- notice()
- info()
- debug()

## ジョブ内でのロギング

バックグラウンドで実行されるジョブは `logger()` プラグインにアクセスできません。代わりに、ジョブクラスのどこでも ServiceManager からロガーを取得できます:

```php
$logger = $this->getServiceLocator()->get('Omeka\Logger');
```

上記のように使用します。例 `$logger()->info('1ページ目をインポート中')`

ジョブの内部から `application.log` にログメッセージは書き込まれません。代わりに、ジョブの記録に書かれます。管理画面でジョブの記録を見ることにより情報を確認することができます。

## ビュー内でのロギング

## その他でのロギング

エンティティ内など、他の場所でデバッグ作業を行う必要がある場合は、オブジェクトに対するファクトリーを介して Logger を注入する必要があります。詳細については [サービスとファクトリー](configuration/services_and_factories.md) を参照してください。

ファクトリの `__invoke()` メソッド内で

```php
$logger = $serviceLocator->get('Omeka\Logger');
```

を追加し、クラスのプロパティとして追加します。
