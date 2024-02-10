# メディアの取り込みとレンダリング

メディアの取り込みおよびレンダリングクラスは、ユーザーが追加、更新、表示できるメディアの種類を拡張するためのクラスです。これらは、メディアの完全な流れ、つまり、アイテムへの追加、更新、画面へのレンダリング方法を定義します。重要な点は、*すべての*メディアに取り込みおよびレンダリングクラスが存在し、アップロードされたファイルおよびリモートコンテンツの両方を扱えることです。

## 組み込みの取り込みおよびレンダリング

Omeka Sには、いくつかの一般的に使用されるメディアの取り込みおよびレンダリングクラスが付属しています：

### 取り込みクラス

- **html**: HTMLメディアを追加および編集する
- **iiif**: IIIF画像URLによってメディアを追加する
- **oembed**: oEmbed URLによってメディアを追加する
- **upload**: ファイルアップロードによってメディアを追加する
- **url**: URLによってメディアを追加する
- **youtube**: YouTube URLによってメディアを追加する

### レンダリングクラス

- **file**: ファイルを表示する
- **html**: HTMLを表示する
- **iiif**: IIIF画像を表示する
- **oembed**: oEmbed画像またはHTMLを表示する
- **youtube**: YouTubeのiframeを表示する

「upload」と「url」という名前の取り込みクラスに、同じ名前のレンダリングクラスがないことに注意してください。これは、両方とも汎用の「file」レンダリングクラスを使用するためであり、ファイルはそれぞれのメディアタイプや拡張子に従ってレンダリングされます（`file_renderers`設定で設定可能）。
そのため、カスタムの取り込みクラスを書く際には、すでに登録されているレンダリングクラスを使用するだけで十分かもしれません。

## インターフェース

### 取り込みクラス

すべての取り込みクラスは`IngesterInterface`を実装しなければなりません。必要なメソッドは以下の通りです：

|メソッド|目的|コメント|
|---|---|---|
|`getLabel()`|取り込みクラスの人間が読みやすいラベルを返す。|すべてのメディア取り込みクラスの中で一意であるべきです。|
|`getRenderer()`|取り込みクラスによって取り込まれたメディアのレンダリングクラスの名前を返す。|登録済みのメディアレンダリングクラスの名前であるべきです。|
|`form()`|メディアを追加するために必要なコントロールのマークアップを返す。||
|`ingest()`|取り込み（作成）リクエストを処理する。|通常、フォームデータを検証し、ファイルやサムネイルを取得するために使用されます|

メディアが更新可能な場合、`MutableIngesterInterface`を実装する必要があります。必要なメソッドは以下の通りです：

|メソッド|目的|コメント|
|---|---|---|
|`updateForm()`|メディアを編集するために必要なコントロールのマークアップを返す。||
|`update()`|更新リクエストを処理する。||

引数の使い方については、組み込みの取り込みクラスをよく見るとよいでしょう。

### レンダリングクラス

すべてのレンダリングクラスは`RendererInterface`を実装しなければなりません。必要なメソッドは以下の通りです：

|メソッド|目的|コメント|
|---|---|---|
|`render()`|メディアをレンダリングするためのマークアップを返す。||

引数の使い方については、組み込みのレンダリングクラスをよく見るとよいでしょう。

## ファイルダウンローダーとアップローダー

取り込みの際に一部のメディアでファイルをアップロードまたはダウンロードする必要があることがわかります。通常、ファイルを取得して何かを行うため、例えばファイルを保存するかサムネイル画像を生成するかです。この目的のために、Omeka Sは2つのサービスを提供しています：

- `Omeka\File\Downloader`: リモートURIからファイルをダウンロードする
- `Omeka\File\Uploader`: ローカルクライアントからファイルをアップロードする

取り込みクラスにこれらのサービスを注入する場合は、Zendのサービスファクトリパターンに従います。

例えば、`Omeka\File\Downloader`サービスを取り込みクラスに注入した後、URLを渡すことで`IngesterInterface::ingest()`でメディアのサムネイル画像を取得することができます：
php
$url = sprintf('http://example.com/%s.jpg', $imagePath);
$tempFile = $this->downloader->download($url);
if ($tempFile) {
    $tempFile->mediaIngestFile($media, $request, $errorStore, false);
}

ダウンローダーとアップローダーの両方が、メディアファイルを取り込むために特に設計された便利な`mediaIngestFile()`メソッドを持つ一時ファイルオブジェクトを返します。
これは、ファイルを自動的に検証し、メディアにストレージIDを設定するなど、他のことを行います。メソッドのシグネチャは以下の順番です：

|引数|型|デフォルト|定義|
|---|---|---|---|
|`$media`|`Media`|n/a|メディアオブジェクト|
|`$request`|`Request`|n/a|リクエストオブジェクト|
|`$errorStore`|`ErrorStore`|n/a|エラーストアオブジェクト|
|`$storeOriginal`|`bool`|true|元のファイルを保存しますか？|
|`$storeThumbnails`|`bool`|true|サムネイル画像を保存しますか？|
|`$deleteTempFile`|`bool`|true|取り込み後に一時ファイルを削除しますか？|
|`$hydrateFileMetadataOnStoreOriginalFalse`|`bool`|false|`$storeOriginal = false`の時にファイルメタデータを保湿しますか？|

上記の例では、`$storeOriginal`に`false`を渡し、デフォルトの`$storeThumbnails = true`に依存してダウンロードされた一時ファイルを破棄し、サムネイル画像のみを作成することを選択しています。

## カスタム取り込みおよびレンダリングの追加

組み込みの取り込みおよびレンダリングが不十分な場合、Omeka Sはカスタムのものを比較的簡単に追加することができます。例として、簡単な「ツイート」メディアを追加するモジュールを作成する方法を示します。

まずモジュールのINIファイルを`/modules/MyModule/config/module.ini`に作成します：
ini
[info]
version = "1.0.0-alpha"
omeka_version_constraint = "^1.0.0 || ^2.0.0"
name = "マイ・モジュール"
description = "簡単なツイートメディアを追加する"

次に、モジュールファイルを`/moudules/MyModule/Module.php`に作成し、設定内でメディアを登録します：
php
<?php
namespace MyModule;

use Omeka\Module\AbstractModule;

class Module extends AbstractModule
{
    public function getConfig()
    {
        return [
            'media_ingesters' => [
                'factories' => [
                    'mymodule_tweet' => Service\Media\Ingester\TweetFactory::class,
                ],
            ],
            'media_renderers' => [
                'invokables' => [
                    'mymodule_tweet' => Media\Renderer\Tweet::class,
                ],
            ],
        ];
    }
}

取り込みクラスとレンダリングクラスをOmeka Sに認識させるためには、モジュール設定に登録する必要があります。

次に、取り込みクラスが`Omeka\HttpClient`サービスを必要としているので、`/modules/MyModule/src/Service/Ingester/TweetFactory.php`に「ツイート」取り込みクラスのファクトリを作成しましょう：
php
<?php
namespace MyModule\Service\Media\Ingester;

use MyModule\Media\Ingester\Tweet;
use Zend\ServiceManager\Factory\FactoryInterface;
use Interop\Container\ContainerInterface;

class TweetFactory implements FactoryInterface
{
    public function __invoke(ContainerInterface $services, $requestedName, array $options = null)
    {
        return new Tweet($services->get('Omeka\HttpClient'));
    }
}

次に、`/modules/MyModule/src/Media/Ingester/Tweet.php`に「ツイート」取り込みクラスを作成しましょう：
php
<?php
namespace MyModule\Media\Ingester;

use Omeka\Api\Request;
use Omeka\Entity\Media;
use Omeka\Media\Ingester\IngesterInterface;
use Omeka\Stdlib\ErrorStore;
use Zend\Form\Element\Text;
use Zend\Http\Client;
use Zend\View\Renderer\PhpRenderer;

class Tweet implements IngesterInterface
{
    protected $client;
    public function __construct(Client $client)
    {
        $this->client = $client;
    }
    public function getLabel()
    {
        return 'ツイート'; // @translate
    }
    public function getRenderer()
    {
        return 'mymodule_tweet';
    }
    public function form(PhpRenderer $view, array $options = [])
    {
        $input = new Text('o:media[__index__][o:source]');
        $input->setOptions([
            'label' => 'ツイートURL', // @translate
            'info' => '埋め込むツイートのURL。', // @translate
        ]);
        $input->setAttributes([
            'required' => true,
        ]);
        return $view->formRow($input);
    }
    public function ingest(Media $media, Request $request, ErrorStore $errorStore)
    {
        // リクエストデータを確認する。
        $data = $request->getContent();
        if (!isset($data['o:source'])) {
            $errorStore->addError('o:source', 'ツイートURLが指定されていません');
            return;
        }
        // URLを確認する。
        $isMatch = preg_match('/^https:\/\/twitter\.com\/[\w]+\/status\/[\d]+$/', $data['o:source']);
        if (!$isMatch) {
            $errorStore->addError('o:source', sprintf(
                '無効なツイートURL: %s',
                $data['o:source']
            ));
            return;
        }
        // oEmbed JSONを取得する。
        $url = sprintf('https://publish.twitter.com/oembed?url=%s', urlencode($data['o:source']));
        $response = $this->client->setUri($url)->send();
        if (!$response->isOk()) {
            $errorStore->addError('o:source', sprintf(
                'ツイートの読み込みエラー: %s (%s)',
                $response->getReasonPhrase(),
                $response->getStatusCode()
            ));
            return false;
        }
        // メディアのソースとデータを設定する。
        $media->setSource($data['o:source']);
        $media->setData(json_decode($response->getBody(), true));
    }
}

最後に、`/modules/MyModule/src/Media/Renderer/Tweet.php`に「ツイート」レンダリングクラスを作成しましょう：
php
<?php
namespace MyModule\Media\Renderer;

use Omeka\Api\Representation\MediaRepresentation;
use Omeka\Media\Renderer\RendererInterface;
use Zend\View\Renderer\PhpRenderer;

class Tweet implements RendererInterface
{
    public function render(PhpRenderer $view, MediaRepresentation $media, array $options = [])
    {
        // oEmbed JSONの「html」値を返す。
        return $media->mediaData()['html'];
    }
}

モジュールをインストールした後、アイテムに「ツイート」メディアを追加し、単一のツイートへのURLを入力することで、埋め込みツイートをアイテムに追加することができます。（注意として、この機能は一般的に組み込みの「oembed」取り込みクラスでカバーされているため、新しい取り込みクラスは実際には必要ありません。それでも役立つ例です。）