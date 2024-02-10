# ビュー・ヘルパー

ビュー・ヘルパーは、HTMLの生成や修正のためのコードを集積し、ビューを簡素化し、冗長性を最小限に抑えるために使用されます。例えば、HTMLのエスケープ、日付のフォーマット、よく使われるインターフェース・コンポーネントの作成などのタスクは、ビュー・ヘルパーによって処理されます。

より完全な導入については、[Zend Frameworkのドキュメント](https://docs.zendframework.com/zend-view/helpers/intro/)を読んでください。
本ページは、以下にリストされているOmeka Sのビュー・ヘルパーの使用に焦点を当てています。Omeka SはZend Frameworkのネイティブなビュー・ヘルパーも多用しているので、それらについての詳細はそのドキュメントを参照してください。

## ビュー・ヘルパーの使用

ヘルパーは通常、ビュー・スクリプトから呼び出されます。Zend Frameworkのビュー・ヘルパーを使った一般的な例は、ルートとアクションのURLを生成する`Url`ヘルパーです:
php
$this->url(null, ['action' => 'log'], true);

ビュー・ヘルパー名は大文字で始まりますが、`$this`（ビュー・オブジェクト）から呼び出されるメソッド名は小文字で始まります。各ヘルパーは独自のシグネチャを使用するので、その`__invoke`メソッドについては各ヘルパーのドキュメントを参照してください。

## モジュール内でのビュー・ヘルパーの作成

ビュー・ヘルパーは、モジュールのディレクトリ構造の以下に配置する必要があります:

MyModule/
  src/
    View/
      Helper/
        MyHelper.php

ビュー・ヘルパーは`Zend\View\Helper\AbstractHelper`から継承し、`Zend\View\Helper\HelperInterface`を実装します。

したがって、基本構造は以下のとおりです
php
namespace MyModule\View\Helper;

use Zend\View\Helper\AbstractHelper;

class MyModuleViewHelper extends AbstractHelper
{
    public function __invoke()
    {
        $view = $this->getView(); // AbstractHelperによって提供される
        // HTMLを返します。
    }
}

`__invoke`のシグネチャには制限がないので、ビューから必要なデータを必要に応じて追加することができます。上記の`Url`の最初の例のように。したがって、その`__invoke`メソッドは以下のようになります:
php
// 例示されていないコードがここにある予定です。

### 設定ファイル

Omeka Sがあなたのビュー・ヘルパーを認識するためには、そのモジュールの`config/module.config.php`配列に追加する必要があります。このファイルにはこの情報以外にも多くの内容が含まれますが、ヘルパーに関連するのは以下の情報です:
php
return [
    'view_helpers' => [
        'invokables' => [
            'myModule' => 'MyModule\View\Helper\MyModuleViewHelper',
        ],
    ],
]

`invokables`キーは、ビュー・ヘルパークラスを直接インスタンス化できることを示しています（invokablesとfactoriesについては以下を参照）。後続の配列の各値は、参照するドメイン固有のクラスを指します。

### Invokables vs Factories

場合によっては、ヘルパーが追加のサービスやサービス経由でのみアクセス可能なデータにアクセスする必要があります。この場合、ビュー・ヘルパーは`config.php`ファイルで定義されているinvokableではなく、ファクトリ経由で作成する必要があります。([サービスとファクトリ](../configuration/services_and_factories.md)も参照してください)。

ビュー・ヘルパーのためのファクトリを作成するには、次のディレクトリにファクトリを置きます:
php
MyModule/
  src/
    Service/
      ViewHelper/
        MyModuleViewHelperFactory.php

`module.config.php`の配列で`invokable`を宣言する代わりに、ファクトリを宣言します:
php
return [
    'view_helpers' => [
        'factories'  => [
            'myModule' => 'MyModule\Service\ViewHelper\MyModuleViewHelperFactory',
        ],
    ],
];

ファクトリのファイル構造は以下のようになります:
php
namespace MyModule\Service\ViewHelper;

use MyModule\View\Helper\ViewHelper;
use Zend\ServiceManager\Factory\FactoryInterface;
use Interop\Container\ContainerInterface;

class MyModuleViewHelperFactory implements FactoryInterface
{
    public function __invoke(ContainerInterface $services, $requestedName, array $options = null)
    {
        $config = $services->get('Config');
        $mediaAdapters = $config['my_module_media_adapters'];
        return new MyModuleViewHelper($services->get('Omeka\MediaIngesterManager'), $mediaAdapters);
    }
}
この例では、ビュー・ヘルパーに必要な情報がconfig配列に追加され、`Omeka\MediaIngesterManager`サービスが必要です。これらはビューヘルパー内で直接使用できないため、ファクトリを使用してビューヘルパーに注入します。

したがって、ビュー・ヘルパーの`__construct`メソッドはデータを扱う必要があります
php
namespace MyModule\View\Helper;

use Zend\View\Helper\AbstractHelper;

class ViewHelper extends AbstractHelper
{
    protected $mediaIngester;

    protected $mediaAdapters;

    public function __construct($mediaIngestManager, $mediaAdapters)
    {
        $this->mediaAdapters = $mediaAdapters;
        $this->mediaIngester = $mediaIngestManager;
    }

    public function __invoke()
    {
        $mediaForms = [];
        // ... メディアの設定情報の取得などを行うコード ...
    }
}

### ビュー・ヘルパー内でのパーシャルの使用

Omeka Sでは、ビュー・ヘルパーを呼び出してHTMLコンテンツを作成することがよくありますが、そのヘルパー自体がビュー・ページを使用することがあります。これは、ヘルパー内でパーシャルを使用することによって達成されます。

これらは通常、プラグインの`common`ディレクトリ内に見られます:

MyModule/
  views/
    common/
      my-module-view-helper-partial.phtml

実際のモジュールの可読性のために、もっと短く、読みやすい名前が適切です。

ビュー・ヘルパーは次のようにHTMLを作成するかもしれません:
php
namespace MyModule\View\Helper;

use Zend\View\Helper\AbstractHelper;

class MyModuleViewHelper extends AbstractHelper
{
    protected $user;

    public function __construct($user)
    {
        $this->user = $user;
    }

    public function __invoke()
    {
        $userRole = $this->user->getRole();
        return $this->getView()->partial(
            'common/my-module-view-helper-partial',
            [
                'userRole' => $userRole,
            ]
        );
    }
}

`__invoke`メソッドは、`common`内のパーシャルにHTMLの生成を依存します。第二のパラメータは、それを使用するための`$userRole`変数をパーシャルに渡します。モジュールのニーズに応じて、それが必要かどうかは異なります。

## 組み込みのビュー・ヘルパー

Omeka Sには、かなりの数のビュー・ヘルパーが付属しています:

### api

APIの読み取りと検索操作への直接アクセス

`api()`ヘルパーは引数を取りませんが、いくつかのチェーンメソッドを呼び出すことができます:

- `$this->api()->read()`: IDによる単一リソースの取得
- `$this->api()->search()`: 検索パラメーターに一致するリソースの配列の取得
- `$this->api()->searchOne()`: 検索パラメーターに一致する単一リソースの取得

APIの使用についての詳細は、[PHP APIドキュメント](../api/php_api.md)を参照してください。

### assetUrl

静的アセット（CSS/JSなど）へのパスを返します。

ヘルパーは5つの引数を取りますが、最初の引数のみが必須です:

- `$file`: (string) 読み込むアセットのファイル名（"asset"フォルダーの後のパスの部分）
- `$module`: (string) アセットを読み込むモジュール。省略するかnullにすると、現在のテーマから読み込まれます。特別なモジュール名 `Omeka` はコアを参照します。
- `$override`: (bool, default false) テーマがこのアセットを上書きまたは置き換えすることを許可するかどうか。ここにtrueが渡され、テーマに同じ名前のアセットが含まれている場合、モジュールやコアのコピーの代わりに使用されます。これは`$module`が渡された場合にのみ意味があります。
- `$versioned`: (bool, default true) デフォルトでは、Omeka Sはキャッシュバスティングの目的でバージョン番号を含むクエリストリング`?v=`をテーマ、モジュール、またはコアに追加します。これを無効にするには、ここにfalseを渡します。
- `$absolute` (bool, default false) 絶対URLを生成するためにここにtrueを渡します。（4.0.0で追加）

以下に残りのビュー・ヘルパーの説明は続きますが、長さの都合上、このビュー・ヘルパー一覧を一度切ります。全てのビュー・ヘルパーの説明と使用方法については、Omeka Sのドキュメントまたは具体的なビュー・ヘルパーのドキュメントを参照してください。

### blockAttachmentsForm

ブロックアタッチメントの追加/編集のためのフォームをレンダリングします。

これは、サイトページのブロックの管理フォームを実装する際にのみ使用されることを意図しています。詳細については、[ページブロックのドキュメント](page_blocks.md)を参照してください。

3つの引数があり、最初の引数が必須です:

- `$block`: (SitePageBlockRepresentation) 現在のブロック（まだ追加されていない場合はnullにできます）。
- `$itemOnly`: (bool, default false) アタッチメントがアイテムのみで、メディアやキャプションの選択オプションをスキップするかどうか。
- `$itemQuery`: (array, default empty) 選択できるアイテムをフィルタリングするための検索クエリ。

### blockLayout

ブロックレイアウトをレンダリングするためのヘルパーです。

これは主に、サイトページの公開ページと管理ページの内部で使用されます。

チェーンメソッド呼び出しで利用可能な機能:

- `getLayouts()`: すべての登録されたレイアウト名の配列を取得
- `getLayoutLabel($layout)`: 与えられたレイアウトのラベルを取得
- `prepareForm()`: すべてのレイアウトの`prepareForm`メソッドを実行
- `forms($sitePage)`: ページ内のブロックのすべてのフォームをレンダリング
- `form()`: ブロックフォームをレンダリングします。既存のブロックのためにSitePageBlockRepresentationを渡すか、または新しいブロックのために($layout, $site, $page)を渡します。フォームをレンダリングするために使用されるパーシャルを上書きするには、4番目の引数としてパーシャル名を渡します。
- `prepareRender($layout)`: レイアウトの`prepareRender`メソッドを実行
- `render($block)`: ブロックをレンダリングします。

### blockShowTitleSelect

アタッチメントのタイトル表示セレクト要素をレンダリングします。

詳細については、[ページブロックのドキュメント](page_blocks.md)を参照してください。

1つの引数を取ります:

- `$block`: (SitePageBlockRepresentation) 現在のブロック（まだ追加されていない場合はnullにできます）。

### blockThumbnailTypeSelect

サムネイルタイプのセレクト要素をレンダリングします。

詳細については、[ページブロックのドキュメント](page_blocks.md)を参照してください。

1つの引数を取ります:

- `$block`: (SitePageBlockRepresentation) 現在のブロック（まだ追加されていない場合はnullにできます）。

### browse

（4.0.0で追加）

これは、特にOmeka S 4.0.0で追加された管理ブラウズページ設定を使用するためのブラウズページをレンダリングする際に有用な方法を提供するヘルパーです。

チェーンメソッド呼び出しで利用可能な機能:

- `renderSortSelector($resourceTypeOrSortConfig)`: ブラウズページのソート選択フォームをレンダリングします。現在のブラウズページのリソースタイプを示す文字列（例: `'items'`）または、`value => label`ペアで両方が文字列であるソートオプションの配列を渡すことができます。

  これは、公開ページで通常使用されるこのヘルパ