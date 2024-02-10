# サーバーイベントリファレンス

## API アダプターイベント

`Omeka\Api\Adapter\AbstractAdapter`を拡張する全てのクラスはこれらのイベントをトリガーします。イベントの`getTarget()`メソッドを使用してアダプターオブジェクトを取得し、`getParam()`メソッドを使用してパラメーターの値を取得します（もしある場合）。

### api.execute.pre

* **request**: APIリクエスト。

任意のAPI操作を実行する前にトリガーされます。

### api.execute.post

* **request**: APIリクエスト。
* **response**: APIレスポンス。

任意のAPI操作を実行した後にトリガーされます。実行中にバリデーション以外の例外が投げられた場合はトリガーされません。

### api.*.pre

* **request**: APIリクエスト。

特定のAPI操作を実行する前にトリガーされます。アスタリスクを次の操作のいずれかに置き換えてください：`search`, `create`, `read`, `update`, `delete`, `batch_create`, `batch_update`, `batch_delete`。

### api.*.post

* **request**: APIリクエスト。
* **response**: APIレスポンス。

特定のAPI操作を実行した後にトリガーされます。アスタリスクを次の操作のいずれかに置き換えてください：`search`, `create`, `read`, `update`, `delete`, `batch_create`, `batch_update`, `batch_delete`。

## API エンティティアダプターイベント

`Omeka\Api\Adapter\AbstractEntityAdapter`を拡張する全てのクラスはこれらのイベントをトリガーします。イベントの`getTarget()`を使用してアダプターオブジェクトを取得します。

### api.search.query

* **queryBuilder**: Doctrineクエリービルダー。
* **request**: APIリクエスト。

API `search`操作中に初期クエリが構築された後にトリガーされます。

### api.find.query

* **queryBuilder**: Doctrineクエリービルダー。
* **request**: APIリクエスト。

アダプターがエンティティを見つける際にいつでもトリガーされます。

### api.find.post

* **entity**: 見つかったエンティティ。
* **request**: APIリクエスト。

API `read`, `delete`, および `batch_delete` 操作中にエンティティが見つかった後にトリガーされます。

### api.hydrate.pre

* **entity**: 非ハイドレーションエンティティ。
* **request**: APIリクエスト。
* **errorStore**: エラーストア。

API `create`, `update`, `batch_create`, `batch_update` 操作中に、リクエストの検証とエンティティのハイドレーションの前にトリガーされます。

### api.hydrate.post

* **entity**: ハイドレーションされたエンティティ。
* **request**: APIリクエスト。
* **errorStore**: エラーストア。

API `create`, `update`, `batch_create`, `batch_update` 操作中に、ハイドレーションされたエンティティとエンティティの検証の前にトリガーされます。

## Doctrine ライフサイクルイベント

`Omeka\Entity\EntityInterface`を実装する全てのクラスはこれらのイベントをトリガーします。イベントの`getTarget()`を使用してエンティティオブジェクトを取得します。これらは選択された[Doctrineライフサイクルイベント](https://www.doctrine-project.org/projects/doctrine-orm/en/latest/reference/events.html#reference-events-lifecycle-events)をOmekaイベントに委任します。

### entity.remove.pre

`preRemove`ライフサイクルイベントを参照してください。

### entity.remove.post

`postRemove`ライフサイクルイベントを参照してください。

### entity.persist.pre

`prePersist`ライフサイクルイベントを参照してください。

### entity.persist.post

`postPersist`ライフサイクルイベントを参照してください。

### entity.update.pre

`preUpdate`ライフサイクルイベントを参照してください。

### entity.update.post

`postUpdate`ライフサイクルイベントを参照してください。

## API レプレゼンテーションイベント

イベントの`getTarget()`を使用してレプレゼンテーションオブジェクトを取得します。

### rep.resource.json

* **jsonLd**: JSON-LD配列。

`Omeka\Api\Representation\AbstractRepresentation`を拡張する全てのクラスはこのイベントをトリガーします。

レプレゼンテーションのJSON-LDのシリアライズの後にトリガーされます。JSON-LDをフィルタリングするには、リスナーは`jsonLd`パラメーターを変更し、イベントに戻すことができます。

### rep.value.html

* **html**: 値テキスト。

`Omeka\Api\Representation\ValueRepresentation`クラスはこのイベントをトリガーします。値に関するすべての情報がイベントのターゲットオブジェクトにあります。これにはターゲットURL、ターゲットID（リソース値の場合）、ラベルが含まれます。

ウェブページで表示用のValueレプレゼンテーションのテキストを取得する後にトリガーされます。テキストをフィルタリングするには、リスナーは`html`パラメーターを変更し、イベントに戻すことができます。

### rep.resource.display_values

* **values**: リソースの値の配列

`Omeka\Api\Representation\AbstractResourceEntityRepresentation`を拡張する全てのクラスはこのイベントをトリガーします。

メソッド`displayValues`内で値が部分的に変更されるためにトリガーされます。

### rep.resource.values

* **values**: リソースの値の配列

`Omeka\Api\Representation\AbstractResourceEntityRepresentation`を拡張する全てのクラスはこのイベントをトリガーします。

メソッド`values`内で返される値の配列を変更するためにトリガーされます。このイベントで行われた変更は`displayValues`メソッドにも影響を及ぼします。

バージョン2.1.0で追加されました。

### rep.resource.title

* **title**: リソースのタイトル文字列

`Omeka\Api\Representation\AbstractResourceEntityRepresentation`を拡張する全てのクラスはこのイベントをトリガーします。

メソッド`title`で返されるタイトルを変更するためにトリガーされます。このイベントを使用して行われた変更は`displayTitle`メソッドにも影響を及ぼします。

バージョン2.1.0で追加されました。

## ビューイベント

`trigger`ビューヘルパーはビューテンプレートの戦略的な場所でこれらのイベントをトリガーします。イベント識別子としてコントローラーの呼び出し可能なサービス名を使用します。これらは`application/config/module.config.php`の下、`controllers`キーの下に探してください。その名前は、`invokable`または`factory`キーのいずれかでリストされているかもしれません。たとえば、名前が`Omeka\Controller\Admin\ItemController`ではなく`Omeka\Controller\Admin\Item`の場合があります。イベントの`getTarget()`を使用してビューレンダラーを取得します。リスナーでエコーされたマークアップはページに表示されます。

### view.layout

ビューレイアウト内でトリガーされます。このイベントを使用して、インストール内のすべてのページのHTMLヘッドに追加します。

### view.show.before

表示ページのマークアップの前にトリガーされます。

### view.show.after

表示ページのマークアップの後にトリガーされます。

### view.browse.before

閲覧ページのマークアップの前にトリガーされます。

### view.browse.after

閲覧ページのマークアップの後にトリガーされます。

### view.add.before

追加ページのマークアップの前にトリガーされます。

### view.add.after

追加ページのマークアップの後にトリガーされます。

### view.edit.before

編集ページのマークアップの前にトリガーされます。

### view.edit.after

編集ページのマークアップの後にトリガーされます。

### view.add.form.before

* **form**: 現在のフォームのフォームオブジェクト。

追加ページのフォームマークアップの前に、フォームの中でトリガーされます。

### view.add.form.after

* **form**: 現在のフォームのフォームオブジェクト。

追加ページのフォームマークアップの後に、フォームの中でトリガーされます。

### view.add.form.advanced

* **form**: 現在のフォームのフォームオブジェクト。

(バージョン3.0.0で追加)

リソースフォームの「Advanced」タブの最後にトリガーされます。

### view.edit.form.before

* **form**: 現在のフォームのフォームオブジェクト。

編集ページのフォームマークアップの前に、フォームの中でトリガーされます。

### view.edit.form.after

* **form**: 現在のフォームのフォームオブジェクト。

編集ページのフォームマークアップの後に、フォームの中でトリガーされます。

## view.edit.form.advanced

* **form**: 現在のフォームのフォームオブジェクト。

(バージョン3.0.0で追加)

リソースフォームの「Advanced」タブの最後にトリガーされます。

### view.show.section_nav

* **section_nav**: セクションナビゲーションラベルの配列。セクションIDによってキー付けされます。

表示ページのセクションナビゲーションを作成する際にトリガーされます。セクションナビゲーションアイテムを追加するには、**section_nav**パラメーターを変更し、`setParam('section_nav', $sectionNav)`を使用してイベントに戻します。

### view.add.section_nav

* **section_nav**: セクションナビゲーションラベルの配列。セクションIDによってキー付けされます。

追加ページのセクションナビゲーションを作成する際にトリガーされます。セクションナビゲーションアイテムを追加するには、**section_nav**パラメーターを変更し、`setParam('section_nav', $sectionNav)`を使用してイベントに戻します。

### view.edit.section_nav

* **section_nav**: セクションナビゲーションラベルの配列。セクションIDによってキー付けされます。

編集ページのセクションナビゲーションを作成する際にトリガーされます。セクションナビゲーションアイテムを追加するには、**section_nav**パラメーターを変更し、`setParam('section_nav', $sectionNav)`を使用してイベントに戻します。

### view.advanced_search

* **query**: 現在のクエリ配列（ある場合）
* **resourceType**: クエリされているリソースの種類（`item`, `itemSet`, `media`）
* **partials**: フォームをレンダリングする部分的な配列

このイベントは`partials`パラメーターをフィルタリングするために使用されることを意図されています：リスナーは配列にあるパーシャルを追加、削除、または再順序することができ、フォームに表示される内容を変更します。追加されたパーシャルはレンダリングされるときに1つのパラメーター`query`を渡されます。

### view.manage_resources.before

管理ダッシュボードの「リソースを管理する」パネルの前にトリガーされます。

### view.manage_resources.after

管理ダッシュボードの「リソースを管理する」パネルの後にトリガーされます。

### view.manage_sites.before

管理ダッシュボードの「サイトを管理する」パネルの前にトリガーされます。

### view.manage_sites.after

管理ダッシュボードの「サイトを管理する」パネルの後にトリガーされます。

### view.details

* **entity**: 詳細が表示されているエンティティ

管理側で詳細が表示される内側でトリガーされます。

### view.sort-selector

* **sortBy**: ソートオプションの配列。各オプションはサブ配列であり、「label」と「value」のキーを持ちます（フィルター可能）。
* **sortByQuery**: 現在のアクティブなソートフィールドのクエリ文字列パラメーター。
* **sortOrderQuery**: 現在のアクティブなソート方向のクエリ文字列パラメーター。

閲覧ページでソート`select`エレメントのオプションをフィルタリングするためのイベント。sortSelectorビューヘルパーでトリガーされます。Omeka S 1.3.0で追加されました。

### view.search.filters

* **filters**: フィルターされた値の配列。フィルターラベルによってキー付けされます。
* **query**: 検索クエリ。

検索結果のために人間が読める検索パラメーターを作成した後にトリガーされます。

## ビューヘルパー