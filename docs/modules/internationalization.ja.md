# 国際化

Omeka S コアは、モジュール内の文字列を国際化するためのツールを含んでおり、Transifex またはその他の翻訳サービスに追加するための `template.pot` ファイルを作成したり、翻訳された `.po` ファイルを処理したりすることができます。

このページでは、モジュール内の国際化に特有のトピックを取り上げています。Omeka S 翻訳システム全般に関する情報は、[メインの国際化ページ](../miscellaneous/internationalization.md)を参照してください。

## モジュールの準備

モジュールを翻訳準備するためには、コード全体にわたる文字列自体を準備する必要があります。また、翻訳ファイルを格納するディレクトリを作成し、設定する必要があります。

文字列を処理するためには、[メインのドキュメントにある文字列を翻訳可能にするためのガイドライン](../miscellaneous/internationalization.md#making-strings-translatable)がモジュールにも適用されます。

ディレクトリについては、モジュールのルートに `/language` ディレクトリを作成し、モジュールの config/module.config.php ファイルで参照します：

```php
return [
// ...
    'translator' => [
        'translation_file_patterns' => [
            [
                'type' => 'gettext',
                'base_dir' => dirname(__DIR__) . '/language',
                'pattern' => '%s.mo',
                'text_domain' => null,
            ],
        ],
    ],
// ...
];
```

## 文字列の作成とコンパイル

`gulp i18n:module:template` タスクは、`/language`ディレクトリ内に `template.pot` ファイルを生成します。このタスクはモジュール内で実行されると、自動的に適切なモジュールを検出しますが、`--module MyModule` パラメータを指定することもできます。ここでの値はモジュールのディレクトリ名に相当します。

`template.pot` ファイルに基づいた翻訳が作成されたら、その `*.po` ファイルを `/language` ディレクトリに追加します。すべてのファイルは、正しいローカリゼーションコード（例：`en-GB.po`）に従って命名されるべきです。

次に、`gulp i18n:module:compile` タスクを使用して、`*.po` ファイルを対応する `*.mo` バイナリにコンパイルします。

これらのステップの間に、翻訳の対象となる適切な文字列を全て正しくマークしたかどうかを確認する必要があります。`translate-toolkit` Linux パッケージは、テスト翻訳を作成するための[`podebug`](http://docs.translatehouse.org/projects/translate-toolkit/en/latest/commands/podebug.html)コマンドを提供しています。`/language` ディレクトリから `podebug -i template.pot -o debug.po --rewrite=unicode` を実行すると、仮の翻訳が入った`debug.po`ファイルが作成されます（他のオプションも利用可能です）。

`debug.po` ファイルを作成し、その `debug.mo` カウンターパートをコンパイルした後、Omeka S の `/config/local.config.php` ファイルで翻訳者のロケール値を `debug` に設定します：

```php
    'translator' => [
        'locale' => 'debug',
    ],
```

その後、モジュールの全てのページをブラウズして未翻訳の文字列を更新し、プロセスを繰り返してください。

## 静的翻訳

Omeka S バージョン 1.2 以降、最終テンプレートに含まれる静的翻訳ファイルのテンプレートを含めることができるようになりました。
これは、`xgettext`によって簡単に発見できない、または`@translate`コメントでマークできない文字列が翻訳される必要がある場合に便利です。

`gulp i18n:module:template` コマンドは `language/template.static.pot` で静的テンプレートを探します。
