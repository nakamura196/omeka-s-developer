# サーバーイベント

サーバーイベント（`Omeka\Event`）は`Zend\EventManager\Event`から拡張されています。これらは Omeka S の PHP コードの中で、様々なコントローラー、ビュー、API アクションからトリガーされ、Omeka S 自体内やモジュール内での追跡アクションを行うことを可能にします。完全なリストについては[参照ページ](server_event_reference.md)をご覧ください。

## イベントにリスナーをアタッチする

メインのモジュールクラス（Module.php）はイベントにリスナーを簡単にアタッチするためのメソッド`attachListeners`を提供しています。

イベントはイベント名とそれをトリガーするオブジェクトのクラスに基づいてトリガーされます。モジュール内でリスナーをアタッチする典型的な例は次のようになります：

```php
public function attachListeners(SharedEventManagerInterface $sharedEventManager)
{
    $sharedEventManager->attach(
        'Omeka\Controller\Admin\Item',
        'view.show.after',
        [$this, 'showSource']
    );
}
```

ここで使用される`attach`メソッドは 3 つの引数を取ります：

1. クラス識別子。この場合は`'Omeka\Controller\Admin\Item'`であり、管理者側アイテムコントローラーからのイベントのみを待ち受けることを意味しています。

2. イベント識別子。このリスナーは`view.show.after`イベントにアタッチしており、"show"ビューの内部で発火されます。

3. イベントのコールバック。どのような PHP コールバックもここで指定できますが、同じモジュールクラスの他のメソッドを呼び出すための一般的なパターンは`[$this, 'methodName']`が使われます。ここでは、モジュールは`showSource`メソッドを使用してビューにコンテンツを追加します。`Event`オブジェクトがコールバックに渡されるので、そのオブジェクトから必要なデータを取得するためにパラメータを`$event->getParams()`や`$event->getParam('paramName')`で調べることができます。

## 詳細情報

Zend Framework の EventManager コンポーネントを使用しているため、多くの質問は[ドキュメント](http://framework.zend.com/manual/current/en/modules/zend.event-manager.event-manager.html)を読むことで解決できます。
