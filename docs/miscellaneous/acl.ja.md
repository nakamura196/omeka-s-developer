# アクセスコントロールリスト (ACL)

Omeka Sは、権限管理のためにアクセスコントロールリストを使用します。

## ユーザーロール

以下の六つの明確なユーザーロールがあり、基本的には前のものよりも大きなアクセス権を持っています：

* 研究者: `researcher`
* 作成者: `author`
* レビュアー: `reviewer`
* 編集者: `editor`
* サイト管理者: `site_admin`
* グローバル管理者: `global_admin`

## パーミッションの確認

パーミッション確認を行う方法は三つあります。

### ACLサービス
ACLサービスが利用可能な場所では、`userIsAllowed()`, `isAdmin()`, そして `isAllowed()` の三つのメソッドがあります。詳細は[サービスとファクトリー](../configuration/services_and_factories.md)をご覧ください。

`userIsAllowed($resource, $privilege)`は、現在のユーザーがリソースと権限にアクセスできるかどうかをチェックします。

`isAllowed($user, $resource, $privilege)`は、任意のユーザーの同じアクセスをチェックするのに使えます。

`isAdminRole($role)`は、ユーザーロールが管理権限を持つもの（すなわち、`site_admin` または `global_admin`）の中にあるかどうかをチェックします。
php
// ACLサービスを取得:
$acl = $this->getServiceLocator()->get('Omeka\Acl');
if ($acl->userIsAllowed($resource, $privilege)) {
    // 現在のユーザーには許可されています
}

if ($acl->isAllowed($user, $resource, $privilege)) {
    // 指定されたユーザーには許可されています
}

$role = $user->getRole();
if ($acl->isAdminRole($role) {
    // 管理者アクセスを許可する
}

### リソース表現の内部から

リソース表現を扱っている場合、`userIsAllowed()` を使ってそれに対する権限をチェックします。
php
// APIマネージャーを通じてリソース表現を取得:
$api = $this->getServiceLocator()->get('Omeka\ApiManager');
$item = $api->read('items', 1)->getContent();
if ($item->userIsAllowed($privilege)) {
    // 現在のユーザーには許可されています
}

### ビューまたはコントローラーの内部から

ビューまたはコントローラーの内部からは、`userIsAllowed()` ヘルパーが利用可能です：
php
// ビュースクリプトの中で:
if ($this->userIsAllowed($resource, $privilege)) {
    // 現在のユーザーには許可されています
}