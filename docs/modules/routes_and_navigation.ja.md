# ルートとナビゲーション

## ルート

ルートはURLパスとそれらを処理するコントローラー及びアクション間の接続を定義します。したがって、モジュールが例えば `/yourmodule/controller/action` でページを表示するためには、そのルートを `module.config.php` で定義する必要があります：
php

    'router' => array(
        'routes' => array(
                'your-module' => array(
                    'type' => 'Segment',
                    'options' => array(
                        'route' => '/yourmodule/controller/action',
                        'defaults' => array(
                            '__NAMESPACE__' => 'Yourmodule\Controller',
                            'controller' => 'Controller',
                            'action' => 'action',
                        ),
                    ),
                ),
            ),
        ),

ルートを定義するための多くのオプションがあります。詳細については [Zend Framework のドキュメント](https://docs.zendframework.com/zend-router/routing/) を参照してください。

## ナビゲーション

モジュールのナビゲーションを追加するには、まずルートを定義し、次にそのルートをナビゲーションシステムに追加します。管理者ページの左サイドバーにリンクを追加する例としては、以下はOmeka2Importerモジュールから取ったものです。まず、ルートはメインの管理者ルーターの子として追加されます。モジュールには`omeka2importer`ルートの下で自身の2つの子ルート、`past-imports`と`map-elements`を定義することによって、2つの有効なルートがあります。
php
    'router' => array(
        'routes' => array(
            'admin' => array(
                'child_routes' => array(
                    'omeka2importer' => array(
                        'type' => 'Literal',
                        'options' => array(
                            'route' => '/omeka2importer',
                            'defaults' => array(
                                '__NAMESPACE__' => 'Omeka2Importer\Controller',
                                'controller' => 'Index',
                                'action' => 'index',
                            ),
                        ),
                        'may_terminate' => true,
                        'child_routes' => array(
                            'past-imports' => array(
                                'type' => 'Literal',
                                'options' => array(
                                    'route' => '/past-imports',
                                    'defaults' => array(
                                        '__NAMESPACE__' => 'Omeka2Importer\Controller',
                                        'controller' => 'Index',
                                        'action' => 'past-imports',
                                    ),
                                ),
                            ),
                            'map-elements' => array(
                                'type' => 'Literal',
                                'options' => array(
                                    'route' => '/map-elements',
                                    'defaults' => array(
                                        '__NAMESPACE__' => 'Omeka2Importer\Controller',
                                        'controller' => 'Index',
                                        'action' => 'map-elements',
                                    ),
                                ),
                            ),
                        ),
                    ),
                ),
            ),
        ),
    ),

その後、ナビゲーションキーが`AdminModule`によって定義された管理者用ナビゲーションに追加されます。`module.config.php`内の全ての設定について、Omekaは各キーの配列値をマージします。
php
    'navigation' => array(
        'AdminModule' => array(
            array(
                'label' => 'Omeka 2 Importer',
                'route' => 'admin/omeka2importer',
                'resource' => 'Omeka2Importer\Controller\Index',
                'pages' => array(
                    array(
                        'label' => 'Import',
                        'route' => 'admin/omeka2importer',
                        'resource' => 'Omeka2Importer\Controller\Index',
                    ),
                    array(
                        'label' => 'Import',
                        'route' => 'admin/omeka2importer/map-elements',
                        'resource' => 'Omeka2Importer\Controller\Index',
                        'visible' => false,
                    ),
                    array(
                        'label' => 'Past Imports',
                        'route' => 'admin/omeka2importer/past-imports',
                        'controller' => 'Index',
                        'action' => 'past-imports',
                        'resource' => 'Omeka2Importer\Controller\Index',
                    ),
                ),
            ),
        ),
    ),


ページへのリンクはルーターで定義されたルートに対応します。

サイトのナビゲーションに追加するパターンは似ていますが、`admin/slug`ルートと`site`ナビゲーションに追加され、MetadataBrowseモジュールから取られたこの例で示されています：
php

    'navigation' => [
        'site' => [
            [
                'label' => 'Metadata Browse', // @translate
                'route' => 'admin/site/slug/metadata-browse/default',
                'action' => 'index',
                'useRouteMatch' => true,
                'pages' => [
                    [
                        'route' => 'admin/site/slug/metadata-browse/default',
                        'visible' => false,
                    ],
                ],
            ],
        ],
    ],
    'router' => [
        'routes' => [
            'admin' => [
                'child_routes' => [
                    'site' => [
                        'child_routes' => [
                            'slug' => [
                                'child_routes' => [
                                    'metadata-browse' => [
                                        'type' => 'Literal',
                                        'options' => [
                                            'route' => '/metadata-browse',
                                            'defaults' => [
                                                '__NAMESPACE__' => 'MetadataBrowse\Controller\Admin',
                                                'controller' => 'index',
                                                'action' => 'index',
                                            ],
                                        ],
                                        'may_terminate' => true,
                                        'child_routes' => [
                                            'default' => [
                                                'type' => 'Segment',
                                                'options' => [
                                                    'route' => '/:action',
                                                    'constraints' => [
                                                        'action' => '[a-zA-Z][a-zA-Z0-9_-]*',
                                                    ],
                                                ],
                                            ],
                                        ],
                                    ],
                                ],
                            ],
                        ],
                    ],
                ],
            ],
        ],
    ],


## 関連項目

[コントローラー](index.md)
[設定](../configuration/index.md)