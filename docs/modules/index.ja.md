# モジュール入門

Omeka S の機能を拡張するには、 *モジュール* と呼ばれるアドオンコンポーネントを作成します。Zend Framework は [モジュールを書くための大規模なフレームワーク](https://docs.zendframework.com/zend-modulemanager/intro/) を提供しますが、Omeka S はモジュールをインストール可能、アップグレード可能、そして統合可能にする追加の構造を提供します。

## ディレクトリ構造

最も基本的なレベルで、モジュールディレクトリは以下のように構成されています：

MyModule/
    Module.php
    config/
        module.ini
        module.config.php
    src/
        <library-directories-and-files>
    asset/
        <asset-directories-and-files>/
    view/
        <module-namespace>/
            <controller-directories>/
                <action-template-files>.phtml
        common/
          mymodule-<template-files>.phtml

モジュールディレクトリの名前（上記の `MyModule`）は重要です。CamelCase 形式でモジュールの名前が適切にユニークで、簡潔、そして記述的でなければなりません。

`asset` ディレクトリには、通常は独自のサブディレクトリ内にある JavaScript、CSS、イメージファイルなどのアセットが含まれます。

`view` ディレクトリ内で共有ディレクトリ（上記の `common` など）を使用する際には、名前の衝突を避けるためにテンプレートを特定の方法で命名することが重要です。良い方法として、プラグインの名前をテンプレート名にプレフィックスとしてつけることです。

## config/module.ini

すべてのモジュールには INI ファイル、つまりモジュールに関する基本情報が含まれたファイルが必要です。ファイルは `module.ini` という名前で、モジュールの `config/` ディレクトリ内に保存される必要があります。このファイルは最初の行に `[info]` から始まる必要があります。

### 必須情報

* `name`: モジュールの人間が読むための名前
* `version`: モジュールの現在のバージョン

### 任意情報

* `author`: モジュールの著者
* `configurable`: モジュールが設定可能かどうか、true または false
* `description`: モジュールの説明
* `module_link`: モジュールについてのページへの絶対 URL
* `author_link`: 著者についてのページへの絶対 URL
* `omeka_version_constraint`: このモジュールがどのバージョンの Omeka S コアと互換性があるかを示す [Composer バージョン制約](https://getcomposer.org/doc/articles/versions.md)

例：
ini
[info]
name         = "私のモジュール"
version      = "1.0"
author       = "私の組織"
configurable = true
description  = "Lorem ipsum dolor sit amet, consectetur adipiscing elit."
module_link  = "http://my-organization.com/my-module"
author_link  = "http://my-organization.com"
omeka_version_constraint = "^1.0.0"

## Module.php

`Module.php` ファイル（及び `Module` クラス）は必須です。これには Omeka S にカスタム機能を統合するために必要なメソッドが提供されています。

### モジュール設定

モジュールの設定はモジュールの `getConfig()` メソッドから返されなければならないことに注意してください。それでも、コードをより整理するために `config/module.config.php` ファイルを含めることをお勧めします。
php
use Omeka\Module\AbstractModule;
use Zend\View\Model\ViewModel;
use Zend\Mvc\Controller\AbstractController;

class Module extends AbstractModule
{
    /** モジュールの本体 **/

    /**
     * このモジュールの設定配列を取得する。
     *
     * @return array
     */
    public function getConfig()
    {
        return include __DIR__ . '/config/module.config.php';
    }
}

この設定ファイルで、コントローラー、エンティティ、ルート、ナビゲーションの登録など、多くの必要なタスクを処理します。これは返されるべきキー付き配列です。以下に MetadataBrowse モジュールからの抜粋を例として示します：
php

return [
    'view_manager' => [
        'template_path_stack' => [
            OMEKA_PATH.'/modules/MetadataBrowse/view',
        ],
    ],
    'controllers' => [
        'invokables' => [
            'MetadataBrowse\Controller\Admin\Index' => 'MetadataBrowse\Controller\Admin\IndexController',
        ],
    ],
    'form_elements' => [
        'factories' => [
            'MetadataBrowse\Form\ConfigForm' => 'MetadataBrowse\Service\Form\ConfigFormFactory',
        ],
    ],
];

### ユーザー設定

モジュールのユーザー設定を有効にするためには、config/module.ini で `configurable = true` を設定し、以下のようにモジュールクラスで `getConfigForm()` と `handleConfigForm()` メソッドを使用してください：
php
use Omeka\Module\AbstractModule;
use Zend\View\Model\ViewModel;
use Zend\Mvc\Controller\AbstractController;

class Module extends AbstractModule
{
    /** モジュールの本体 **/

    /**
     * このモジュールの設定フォームを取得する。
     *
     * @param ViewModel $view
     * @return string
     */
    public function getConfigForm(ViewModel $view)
    {
        return '<input name="foo">';
    }

    /**
     * このモジュールの設定フォームを処理する。
     *
     * @param AbstractController $controller
     * @return bool False if there was an error during handling
     */
    public function handleConfigForm(AbstractController $controller)
    {
        return true;
    }
}

### Omeka イベントへのアタッチ

機能を拡張する主な手段は、Omeka が重要なタイミングでトリガーするイベントにリスナーをアタッチすることです。モジュールは `Module.php` ファイルで Omeka S の [サーバーサイドイベント](../events/server_events.md) にアタッチすることができます。以下のようにモジュールクラスで `attachListeners` メソッドを使用してください：
php
use Omeka\Module\AbstractModule;
use Zend\EventManager\Event;
use Zend\EventManager\SharedEventManagerInterface;

class Module extends AbstractModule
{
    /** モジュールの本体 **/

    /**
     * イベントにリスナーをアタッチする。
     *
     * @param SharedEventManagerInterface $sharedEventManager
     */
    public function attachListeners(SharedEventManagerInterface $sharedEventManager)
    {
        $sharedEventManager->attach(
            'Omeka\Controller\Admin\Item', // イベントを発行するコンポーネントの識別子
            'view.show.after', // イベント名
            function (Event $event) { // 任意のコールバック
                // `Omeka\Controller\Admin\Item` の `view.show.after` イベント中に何かをする
            }
        );
    }
}

## MVC

Omeka S は Zend Framework の [MVC レイヤー](https://docs.zendframework.com/zend-mvc/)を使用して、データ、プログラムロジック、およびプレゼンテーションコンポーネント間の調整を行います。MVC レイヤーへのイントロダクションと詳細なリファレンスを読むには、Zend Framework の[ドキュメント](https://docs.zendframework.com/zend-mvc/intro/)を参照してください。[クイックスタート](https://docs.zendframework.com/zend-mvc/quick-start/)ガイドは特に役立つもので、以下を行う方法を示しています：

- ビューマネージャーとコントローラー設定をセットアップする
- コントローラーを作成する
- ビュースクリプトを作成する
- ルートを作成してそれらを全てまとめる

上記の指示に加えて、このセクションで提供する指示に従って、独自の Omeka S モジュールを作成することができます。

## モジュールのパッケージング

* 最低限、モジュールは `Module.php` ファイルと `config/module.ini` ファイルを含む必要があり、上記で説明した要件に従っている必要があります。
* .zip ファイルとして利用可能にするべきです

## omeka.org への追加

[アドオンを登録する](../register_an_addon.md)を参照してください。