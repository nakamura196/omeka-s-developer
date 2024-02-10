# PHP API

Omeka の PHP 環境内からは、3 つの方法で API リクエストを行うことができます：直接、
API マネージャーサービスを使用して、そして間接的に、API コントローラープラグインまたは
API ビューヘルパーを使用して。

## API マネージャー

Omeka の API マネージャーを使用して、直接 API 操作を行うことができます。API マネージャーは、
ユーザーの認証、API リクエストの初期化、リソース上での操作の実行、および
API レスポンスの最終化を担当します。（すべてのリソースがすべての API 操作を実装しているわけではありません。）

サービスロケーターを通じて API マネージャーに直接アクセスできます（もちろん、
サービスロケーターにアクセスできる場合に限ります）。マネージャーを手に入れたら、
以下の API 操作を行うことができます：

- `search($resource, $data, $options)`: クエリによるリソースの検索
- `read($resource, $id, $data, $options)`: ID によるリソースの読み込み
- `create($resource, $data, $fileData, $options)`: リソースの作成
- `batchCreate($resource, $data, $fileData, $options)`: リソースの一括作成
- `update($resource, $id, $data, $fileData, $options)`: ID によるリソースの更新
- `batchUpdate($resource, $ids, $data, $fileData, $options)`: ID によるリソースの一括更新
- `delete($resource, $id, $data, $options)`: ID によるリソースの削除
- `batchDelete($resource, $ids, $data, $options)`: ID によるリソースの一括削除

`$resource`引数はリソース名です。read、update、delete 操作の場合、
`$id`引数はリソース ID です。`$data`引数は API リクエストパラメーターの任意の配列です。
create および update 操作において、`$fileData`引数はファイルデータの任意の配列です。
`$options`引数は API リクエストオプションの任意の配列です。

各操作は、要求されたデータおよびその他の関連情報を含む API[レスポンスオブジェクト](#responses)を
返します。結果の表現を得るためには、レスポンスに対して`getContent()`を呼び出します。
例えば、ID が 123 のアイテムを取得するには：

```php
// $servicesはOmekaのサービスロケーターオブジェクトです。
$api = $services->get('Omeka\ApiManager');
$response = $api->read('items', 123);
$itemRepresentation = $response->getContent();
```

## コントローラープラグイン

コントローラー内で API にアクセスするには、API コントローラープラグインを使用します。
コントローラー内で`$this->api()`を呼び出すことでアクセス可能です。このプラグインは、
API マネージャーが持つすべての操作に加え、便利のための追加のものを持っています：

- `searchOne($resource, $data, $options)`: クエリによる 1 つのリソースの検索

例えば、典型的な show アクションにおいてルートパラメータを介してアイテム表現を取得するには：

```php
public function showAction()
{
    $itemRepresentation = $this->api()->read('items', $this->params('id'))->getContent();
    $view = new ViewModel;
    $view->setVariable('item', $item);
    return $view;
}
```

## ビューヘルパー

ビュー内で API マネージャーにアクセスするには、API ビューヘルパーを使用します。
ビュー内で`$this->api()`を呼び出すことでアクセスできます。ヘルパーは
`search()`、`searchOne()`、および`read()`操作のみを持っています。これにより、
状態を変更する操作がビューレイヤーで実行されるのを防ぎます。

例えば、ビューテンプレート内でアイテムを取得するには：

```php
<?php $itemRepresentation = $this->api()->read('items', 123); ?>
```

## レスポンス

すべての API 操作は`Omeka\Api\Response`クラスのレスポンスオブジェクトを返します。
主な使用方法は`getContent()`で、これは要求された実際のコンテンツを返します。
`read`の場合は単一の値であり、`search`の場合は配列のコンテンツになります。

デフォルトでは、`getContent()`によって返される値は、
[表現](representations.md)オブジェクトであり、ビューやコントローラーでの使用に適しています。

`search`に特に便利なもう一つのメソッドは`getTotalResults()`で、これはリクエストで指定された
クエリに一致するリソースの総数を返します。この「合計」値はしばしば
ユーザーに提示されるか、ページ数などの計算に使用されます。

## エラー

API メソッドはエラーケースに対して例外を投げます。これらの例外はすべて
`Omeka\Api\Exception\ExceptionInterface`インターフェースを実装しています。
一般的な例外には以下が含まれます：

- `Omeka\Api\Exception\NotFoundException`: `read`や`update`のような操作で
  ID が見つからないと指定されたリクエストの場合
- `Omeka\Api\Exception\PermissionDeniedException`: 現在のユーザーがリクエストを
  実行するための必要なレベルの許可を持っていない場合
- `Omeka\Api\Exception\ValidationException`: `update`または`create`
  リクエストが無効なデータを指定した場合。具体的な検証エラーメッセージは
  例外の`getErrorStore()`メソッドから利用可能です。

コントローラーヘルパーの場合、特に Form オブジェクトを渡せます：
`$this->api($form)`、そして、検証エラーが発生した場合、ヘルパーは自動的に
返されたエラーメッセージをそれに対応するフォーム要素と照合しようとし、
ユーザーがコンテキスト内でエラーを確認できるようにするとともに、
画面上部の「フラッシュ」メッセージとしてそれらを表示します。
