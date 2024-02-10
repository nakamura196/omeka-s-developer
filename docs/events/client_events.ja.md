# クライアントイベント

クライアントイベントは Omeka S の JavaScript コード内から発火されます。Omeka S は jQuery を広範囲にわたって使用しており、特に管理ページでの使用が目立ちます。多くのユーザーのインタラクションではページ上の変更に応じた追加のアクションが必要になります。これに対応するために、Omeka S は`trigger()`関数を使用して、javascript コードの他の部分が追従できるようにしています。

例えば、サイドバーを開いたときに、あるモジュールが何らかの方法で反応する必要がある場合、サイドバーの開く操作は`sidebar-opened`イベントを発火させます：

```js
    openSidebar : function(sidebar) {
        sidebar.addClass('active');
        this.reserveSidebarSpace();
        sidebar.trigger('o:sidebar-opened');
    },
```

Omeka S が提供するクライアントサイドイベントの一覧については、[クライアントイベント参照ページ](client_event_reference.md)をご覧ください。
