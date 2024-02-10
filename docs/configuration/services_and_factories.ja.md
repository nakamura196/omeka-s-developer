# サービスとファクトリ

## サービス

Zend Framework 3 に従って、サービスは合わせて Omeka S のコア機能のほとんどを構成します。例えば、データベースクエリを実行するには、最初に`Omeka\Connection`サービスを取得する必要があります。

```php
$connection = $serviceLocator->get('Omeka\Connection');

$sql = ''; // 実行するSQLを何でも良いです。
$connection->exec($sql);
```

多くの共通タスクについて、Omeka S は必要なサービスを取得するためのヘルパーを提供しています。よって、`Omeka\Logger`サービスを使用する必要がある場合、`$serviceLocator`を通じて取得する必要はありません（実際、多くの場合はできません -- それについては後述します）。代わりに、コントローラ内で単に以下のように実行できます。

```php
$this->logger()->warn('何か悪いことが起こっています。');
```

## ファクトリ

その他の用途の中で、ファクトリはクラスをインスタンス化し、関連するデータとクラスを注入するために使用されます。Omeka S では、これは一般的に特別な条件でサービスを利用可能にするという意味を持ちます。

例えば、Omeka2Importer プラグインを考えてみましょう。その最初の仕事は、既存の Omeka Classic サイトからデータを取得することです。Omeka Classic の API と対話するために既に開発されていたクライアントは、データのリクエストと処理のタスクを処理する必要がありました。そのクライアントは Omeka S モジュールに含めるだけで使用可能になります。それはサービスをコントローラに注入するファクトリを使用することによって行われます。

```php

namespace Omeka2Importer\Service\Controller;

use Omeka2Importer\Controller\IndexController;
use Zend\ServiceManager\Factory\FactoryInterface;
use Interop\Container\ContainerInterface;

class IndexControllerFactory implements FactoryInterface
{
    public function __invoke(ContainerInterface $container, $requestedName, array $options = null)
    {
        $client = $container->get('Omeka2Importer\Omeka2Client');
        $indexController = new IndexController($client);
        return $indexController;
    }
}
```

この時、`Omeka2Importer\Controller\IndexController\IndexController`のコンストラクタはクライアントを対応するプロパティに割り当て、必要に応じて使用します。

もう一つの一般的なファクトリのタスクは、フォームに必要なサービスを注入することです。例えば、`Zend\Event\EventManager`を使ってイベントをトリガーする必要がある場合やサイト設定にアクセスする必要がある場合、それを注入するファクトリを介してフォームを作成する必要があります。

```php

namespace Omeka\Service\Form;

use Omeka\Form\SiteSettingsForm;
use Zend\ServiceManager\Factory\FactoryInterface;
use Interop\Container\ContainerInterface;

class SiteSettingsFormFactory implements FactoryInterface
{
    public function __invoke(ContainerInterface $services, $requestedName, array $options = null)
    {
        $form = new SiteSettingsForm;
        $form->setSiteSettings($services->get('Omeka\SiteSettings'));
        $form->setEventManager($services->get('EventManager'));
        return $form;
    }
}
```

そのフォーム自体は、ゲッターとセッターを持っている必要があります。

```php
    /**
     * @param SiteSettings $siteSettings
     */
    public function setSiteSettings(SiteSettings $siteSettings)
    {
        $this->siteSettings = $siteSettings;
    }

    /**
     * @return SiteSettings
     */
    public function getSiteSettings()
    {
        return $this->siteSettings;
    }
```

それらのサービスは今やフォーム内で利用可能になります。

### ファクトリ構成

`module.config.php` では、問題のクラスがファクトリによって生成されることを明確にする必要があります。

```php
    'controllers' => array(
        'factories' => array(
            'Omeka2Importer\Controller\Index' => 'Omeka2Importer\Service\Controller\IndexControllerFactory',
        ),
    ),
```

## 参照

[構成ファイル](index.md)
