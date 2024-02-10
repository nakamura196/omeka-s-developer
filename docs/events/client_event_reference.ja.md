# クライアントイベントリファレンス

管理インターフェースでは、以下の[イベント](client_events.md)ではjQueryのtrigger関数を使用しています。

## 一般的なイベント

*global.js*

### `o:expanded`

`.expand` リンクが展開されたときにトリガーされます

### `o:collapsed`

`.collapse` リンクが折りたたまれたときにトリガーされます

## セクション（タブ）イベント

*global.js*

これらのイベントは、複数のタブがあるページでタブ間の切り替え時に発生します。`.section`要素に対してトリガーされます。
`Omeka.switchActiveSection()`によってトリガーされ、ユーザーが発生させた場合とプログラムによるセクションの切り替えの両方で発火します。"Open" されたセクションには `.active` クラスがありますが、閉じたセクションにはありません。

### `o:section-opened`

セクションが開かれた後の`.section`でトリガーされます

### `o:section-closed`

セクションが閉じられた後の`.section`でトリガーされます

## サイバーエベント

*global.js*

サイドバーのすべてのイベントはサイドバー自体（つまり`div.sidebar`）にトリガーされます。

### `o:sidebar-opened`

サイドバーが開かれた後にトリガーされます（`Omeka.openSidebar()`によって）

### `o:sidebar-closed`

サイドバーが閉じられた後にトリガーされます（`Omeka.closeSidebar()`によって）

### `o:sidebar-content-loaded`

コンテンツが`Omeka.populateSidebarContent()`によってAJAXで正常にロードされた後にトリガーされます

## マルチバリューフォームフィールドイベント

*global.js*

このイベントは、マルチバリューフォームフィールド（上級検索ページや一括編集ページで見られる）に適用されます。

### `o:value-created`

新しいフィールドが作成され、挿入された後にトリガーされます。

## リソースフォームイベント

*resource-form.js*

これらのイベントはリソース（アイテム、アイテムセット、メディア）の追加/編集フォームに適用されます。

### `o:prepare-value`

* `type`: 値のデータタイプ
* `value`: `.value` コンテナの jQuery オブジェクト
* `valueObj`: 値データを含むオブジェクト（あれば）
* `namePrefix`: フォーム要素の名前にプレフィックスとして使用すべきインデックス付きプレフィックス

リソースフォーム.jsで値の入力が作成されたり置き換えられたりした後にトリガーされます。カスタムデータタイプの値ノードを設定するために使用します。

### `o.property-added`

（3.0.0で追加）

リソースフォーム.jsでフォームに新しいプロパティが追加された後にトリガーされます。プロパティの"field"コンテナがイベントの対象です。

## リソース選択サイドバーイベント

*resource-selector.js*

これらのイベントはリソースフォームとサイトページの編集で使用されるリソース選択サイドバーに適用されます。

### `o:resource-selected`

リソース選択がユーザーによって碁認されたときに`#select-item a`確認リンクにトリガーされます

また、リソースが結果のリストから最初に選択されたときに`.select-resource`リンクにトリガーされます

### `o:resources-selected`

ユーザーによる複数リソースの"クイック"選択が確認されたときに`.select-resources-button`リンクにトリガーされます

## サイトページ編集フォームイベント

*site-page-edit.js*

このイベントはサイト内のページ編集フォームでのみ適用されます。

### `o:block-added`

新しいブロックフォーム（`.block` コンテナ）がページに追加された後にトリガーされます。

## フォーム変更検出イベント

これらのイベントは変更検出コード（変更が保存されていないことに関する警告を処理するため）によって使用されます。
イベントは`method`が`POST`に設定されたフォームにのみ発火し、クラス`.disable-unsaved-warning`があるフォームでは発火しません（変更検出からのオプトアウト）。

### `o:form-loaded`

ページの読み込み時、存在するすべてのPOSTメソッド`form`にトリガーされます。これを手動でトリガーすることで、フォームの保存状態をリセットします
（つまり、トリガーの時点での現在の状態が「変更されていない」状態と見なされます）。

### `o:before-form-unload`

ページ上の各フォームに対して、ウィンドウの`beforeunload`に対するハンドラーでトリガーされます。フォームに`Omeka.markDirty()`を渡すと変更警告がトリガーされます。
これは、実際のオンページフォームを送信直前にのみ更新するフォームにとって有用です。