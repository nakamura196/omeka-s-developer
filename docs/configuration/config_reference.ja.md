# 設定リファレンス

一般的な設定の概要と`/config/local.config.php`やモジュールの`config/module.config.php`を使用してインストールの設定をカスタマイズする方法については、[Omeka S を設定する](index.md)を参照してください。

以下の設定項目が使用可能です（モジュールによって追加されることがあります）：

## API アダプター

- `api_adapters`: Omeka S リソース用の API アダプターのリスト（[Omeka S API ドキュメント](../api/index.md)参照）

## アセット

アセットファイル（JavaScript、CSS、フォントなど）の設定。

- `assets`
  - `use_externals`: 外部アセットを使用しますか？（`true`（デフォルト）または`false`）
  - `externals`: ローカルアセットパス別にキー化された外部アセット URL のリスト。

## ブロックレイアウト

- `block_layouts`: Omeka S サイト用のブロックレイアウトのリスト

## CLI

PHP-CLI の実行に関する設定。

- `cli`
  - `execute_strategy`: サーバーに許可されている実行戦略（"exec"（デフォルト）または"proc_open"）
  - `phpcli_path`: Omeka S を実行するために使用される PHP バージョンのサーバーパス（デフォルトで自動検出；例："/usr/bin/php55"）

## コントローラー

- `controllers`: MVC コントローラーのリスト（[zend-mvc ドキュメント](https://docs.zendframework.com/zend-mvc/quick-start/#create-a-controller)参照）

## コントローラープラグイン

- `controller_plugins`: MVC コントローラープラグインのリスト（[zend-mvc ドキュメント](https://docs.zendframework.com/zend-mvc/plugins/)参照）

## データタイプ

- `data_types`: Omeka S リソーステンプレート用のデータタイプのリスト

## エンティティマネージャー

Doctrine エンティティマネージャーの設定。

- `entity_manager`:
  - `is_dev_mode`: 開発モードでエンティティマネージャを実行するか（[Doctrine ドキュメント](http://doctrine-orm.readthedocs.io/projects/doctrine-orm/en/latest/reference/configuration.html#obtaining-an-entitymanager)参照）
  - `mapping_classes_paths`: Doctrine エンティティクラスを含むパスのリスト
  - `resource_discriminator_map`: Omeka の`Resource`エンティティにマッピングされているエンティティのリスト（値を使用して記述できるファーストクラスのリソース用）
  - `filters`: SQL フィルターのリスト（[Doctrine ドキュメント](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/filters.html)参照）
  - `functions`: 数値、文字列、および日付時間 DQL 関数のユーザー定義リスト（[Doctrine ドキュメント](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/cookbook/dql-user-defined-functions.html)参照）
  - `proxy_paths`: Doctrine プロキシが含まれているディレクトリへのサーバーパスのリスト

## ファイルレンダラー

- `file_renderers`: Omeka S ファイルメディア用のレンダラーのリスト

## ファイルストア

ファイルの保存に関する設定。

- `file_store`:
  - `local`: ローカルストアの設定
    - `base_path`: ローカルファイルディレクトリへの基本パス
    - `base_uri`: ローカルファイルディレクトリへの基本 URI

`['service_manager']['aliases']['Omeka\File\Store']`
を以下のいずれかに設定して、ファイルストアを設定します：

- `Omeka\File\Store\Local`（デフォルト）：ファイルをローカルサーバーに保存
- （モジュールには追加のファイルストアアダプターと設定が含まれている可能性があります）

## フォームエレメント

- `form_elements`: フォーム要素のリスト（[zend-form ドキュメント](https://docs.zendframework.com/zend-form/advanced/#creating-custom-elements)参照）

## HTTP クライアント

HTTP クライアントの設定。

- `http_client`
  - `adapter`: 接続アダプター
  - `sslcapath`: SSL 証明書ディレクトリへのサーバーパス
  - `sslcafile`: SSL 証明書ファイルへのサーバーパス

Omeka S インスタンスが HTTP 外部トラフィックのためにプロキシサーバーを必要とする場合は、`'adapter' => \Laminas\Http\Client\Adapter\Proxy::class,`を設定し、これらの追加の設定パラメーターを使用してください：

- `http_client`:
  - `proxy_host`: プロキシサーバーのホスト名または FQDN
  - `proxy_port`: プロキシサーバーの TCP ポート
  - `proxy_user`: 必要な場合のプロキシサーバーのユーザー名
  - `proxy_pass`: 必要な場合のプロキシサーバーのパスワード

設定オプションについては[Laminas http ドキュメント](https://docs.laminas.dev/laminas-http/client/adapters/)を参照してください。

## インストーラー

Omeka S インストールの設定。

- `installer`:
  - `pre_tasks`: インストール前に実行するタスクのリスト。通常は環境のチェックを行います
  - `tasks`: インストール中に実行するタスクのリスト

## JavaScript 翻訳文字列

- `js_translate_strings`: 翻訳が必要な JavaScript によってレンダリングされたメッセージのリスト

## リスナー

- `listeners`: 実行時にロードする MVC リスナーのリスト（[zend-mvc ドキュメント](https://docs.zendframework.com/zend-mvc/mvc-event/)参照）

## ロガー

アプリケーションレベルのメッセージのロギング設定。

- `logger`
  - `log`: エラーを記録しますか？（`false`（デフォルト）または`true`）
  - `priority`: ロギングを開始する優先度レベル（デフォルトは`\Zend\Log\Logger::NOTICE`； [zend-log ドキュメント](https://docs.zendframework.com/zend-log/intro/#using-built-in-priorities)参照）
  - `path`: ログファイルへのサーバーパス

## メール

メールトランスポートとメッセージオプションに関する設定。

- `mail`
  - `transport`: メールトランスポート設定（[zend-mail ドキュメント](https://docs.zendframework.com/zend-mail/transport/intro/)参照）
  - `default_message_options`: メッセージオプション（[zend-mail ドキュメント](https://docs.zendframework.com/zend-mail/message/intro/#configuration-options)のオプションを参照）

デフォルトのトランスポートは Sendmail で、`application/config/module.config.php`でセットアップされています。
SMTP を使用する場合は、`local.config.php`の最後にこの例の設定を追加してください（[zend-mail ドキュメント](https://docs.zendframework.com/zend-mail/transport/smtp-options/)参照）：

```
'mail' => [
    'transport' => [
        'type' => 'smtp',
        'options' => [
            'name' => 'localhost',
            'host' => '127.0.0.1',
            'port' => 25, // 'ssl'の場合は465、および'tls'の場合は587
            'connection_class' => 'smtp', // 'plain', 'login', または 'crammd5'
            'connection_config' => [
                'username' => null,
                'password' => null,
                'ssl' => null, // 'ssl' または 'tls'
                'use_complete_quit' => true,
            ],
        ],
    ],
],
```

## メディアインジェスター

- `media_ingesters`: Omeka S メディア用のインジェスターのリスト

## メディアレンダラー

- `media_renderers`: Omeka S メディア用のレンダラーのリスト

## ナビゲーションリンク

- `navigation_links`: Omeka S サイトページ用のナビゲーションリンクのリスト

## oEmbed

- `oembed`: oEmbed メディアの取り込みに許可される URL パターンのホワイトリスト

## パスワード

- `password`: 設定可能なパスワード制限のリスト
  - `min_length`: 最小長
  - `min_lowercase`: 小文字の最小数
  - `min_uppercase`: 大文字の最小数
  - `min_number`: 数字の最小数
  - `min_symbol`: 記号の最小数
  - `symbol_list`: `min_symbol`によって許可される記号のリスト

## 権限

アクセスコントロールリスト（ACL）の設定。

- `permissions`:
  - `acl_resources`: アクセスコントロールリストに読み込むためのリソースのリスト。設定に登録されている API アダプター、Doctrine エンティティ、およびコントローラーは自動的に読み込まれます。

## サービスマネージャー

- `service_manager`: サービスのリスト（[zend-servicemanager ドキュメント](https://docs.zendframework.com/zend-servicemanager/configuring-the-service-manager/)参照）

## セッション

リクエスト間で状態を保存するための設定。

- `session`:
  - `config`: セッション構成オプション（[zend-session ドキュメント](https://docs.zendframework.com/zend-session/config/)参照）
  - `save_handler`: セッション保存ハンドラ（デフォルトのデータベースハンドラを使用するには`null`のままにする；[zend-session ドキュメント](https://docs.zendframework.com/zend-session/save-handler/)参照）)

## 一時ディレクトリ

- `temp_dir`: 一時ディレクトリへのパス

## サムネイル

ファイルデリバティブの作成に関する設定。

- `thumbnails`
  - `thumbnailer_options`:
    - `imagemagick_dir`: `ImageMagick`サムネイラー用の ImageMagick コマンドがあるディレクトリ
    - `page`: 複数ページのファイル用に、どのページのサムネイルを作成するか
  - `types`（large, medium, and square タイプ）:
    - `strategy`: "default"や"square"サムネイル
    - `constraint`: サムネイルの幅のピクセル制約
    - `options`: タイプに応じたオプション（例：`'gravity' => 'center'`）

`['service_manager']['aliases']['Omeka\File\Thumbnailer']`
を以下のいずれかに設定してサムネイラーを設定します：

- `Omeka\File\Thumbnailer\ImageMagick`（デフォルト）：ImageMagick を直接使用
- `Omeka\File\Thumbnailer\Imagick`：PHP の ImageMagick 拡張を使用
- `Omeka\File\Thumbnailer\Gd`：PHP の GD 拡張を使用

## 翻訳者

翻訳者の設定。

- `translator`
  - `locale`: ロケールの言語コード
  - `translation_file_patterns`: 翻訳の選択肢

## ビューヘルパー

- `view_helpers`: ビューヘルパーのリスト（[zend-view ドキュメント](https://docs.zendframework.com/zend-view/helpers/advanced-usage/)参照）

## ビューマネージャー

- `view_manager`: ビューマネージャーの設定（[zend-view ドキュメント](https://docs.zendframework.com/zend-view/quick-start/#configuration)参照）
