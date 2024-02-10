# データタイプ

データタイプは、ユーザーがリソースを記述する方法を拡張するクラスです。リソース値の完全なラウンドトリップを定義します：値がどのように入力され、検証され、データベースに保存され、スクリーンにレンダリングされるかについてです。すべての値がデータタイプを持っており、リソーステンプレート内のプロパティにデータタイプを設定することは、*新しく*作成された値にのみ影響することを知っておくことが重要です。

## 組み込みデータタイプ

Omeka Sには、いくつかの一般的に使用されるデータタイプが含まれています：

- **Literal**: リテラル文字列を使ってリソースを記述する;
- **URI**: [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)を使ってリソースを記述する;
- **Resource**:
    - **All**: Omeka Sの任意のリソースを使って記述する;
    - **Item**: Omeka Sのアイテムを使って記述する;
    - **Item Set**: Omeka Sのアイテムセットを使って記述する;
    - **Media**: Omeka Sのメディアを使って記述する。

これらのデータタイプを使用して、本を以下のように記述できます：

|主題|述語|目的語|データタイプ|
|---|---|---|---|
|この本|`dcterms:title`|"I Know Why the Caged Bird Sings"|Literal|
|この本|`bibo:uri`|[https://www.wikidata.org/wiki/Q3163506](https://www.wikidata.org/wiki/Q3163506)|URI|
|この本|`dcterms:creator`|["Maya Angelou"]() (アイテムへのリンク)|Item|

または、上記の例のJSON-LD表現を表示すると：
json
{
    "@context": "http:\/\/example.com\/api-context",
    "@id": "http:\/\/example.com\/api\/items\/1",
    "@type": "o:Item",
    "dcterms:title": [
        {
            "type": "literal",
            "@value": "I Know Why the Caged Bird Sings",
            "@language": "en"
        }
    ],
    "bibo:uri": [
        {
            "type": "uri",
            "@id": "https:\/\/www.wikidata.org\/wiki\/Q3163506",
            "o:label": "Wikidata"
        }
    ],
    "dcterms:creator": [
        {
            "type": "resource",
            "value_resource_id": 1,
        }
    ]
}

キー `@value`、`@language`、`@id`、`o:label`、`value_resource_id` に注目してください。これらはあなたのデータタイプの値マークアップを書く際に役立ちます（以下を参照）。

## インターフェース

すべてのデータタイプクラスは、`DataTypeInterface`を実装しなければなりません。必要なメソッドは以下の通りです：

|メソッド|目的|コメント|
|---|---|---|
|`getName()`|データタイプの名前を返す。|全データタイプ間でユニークである必要がある。名前にはモジュールの名前を接頭辞として付ける方が良い。|
|`getLabel()`|データタイプの読みやすいラベルを返す。|全データタイプ間で一意であるべきである。|
|`getOptgroupLabel()`|ある場合は、データタイプの読みやすいoptgroupラベルを返す。|通常、モジュールが複数のデータタイプを持つ場合に使用される。|
|`prepareForm()`|データタイプを有効にするためのビューを準備する。|通常、フォームを処理するために必要なスタイルシートやスクリプトを追加するのに使用される。|
|`form()`|リソースフォーム内の値をレンダリングするために使用される値マークアップを返す。|（以下の"Value Markup"セクションを参照）|
|`isValid()`|値オブジェクトが有効かどうかを返す。||
|`hydrate()`|値オブジェクトを使用して値エンティティをハイドレートする。||
|`render()`|値をレンダリングするために使用されるマークアップを返す。|通常、保存された値を読みやすいマークアップに変換するために使用される。|
|`toString()`|値を単純な文字列として返す。||
|`getJsonLd()`|値をJSON-LD表記を使用して配列として返す。||
|`getFulltextText()`|値の全文を返す。|通常、保存された値を検索可能な文字列に変換するために使用される。|

渡された引数の使い方については、組み込みのデータタイプを見るとより良く理解できます。可能であれば組み込みのデータタイプまたは`AbstractDataType`のいずれかを継承する必要があります。你が意識していないよりも、それらとの機能を共有している可能性があります。

## 値マークアップ

`DataTypeInterface::form()`から返されるマークアップは、Omeka Sがリソースフォームの値を動的に構築することを可能にします。マークアップは、あなたのデータタイプにとって意味のある方法で、またフォームに収まるように任意の形式で整形できます。

`DataTypeInterface::prepareForm()`を使ってカスタムスタイル（CSS）や挙動（JS）を追加したり、`DataTypeInterface::form()`から返されるパーシャル内でそれらを追加することが可能ですが、Omeka Sは多くの必要な挙動を自動化します。ページ読み込みとリソーステンプレートの選択時に、入力された`name`属性を自動的に入力します。この動作は、あなたがマークアップ内で定義する特別な属性に基づいて行われます。

### 特別な属性

入力には`name`や`value`の属性が含まれていないため、ページ読み込み時とリソーステンプレート選択時に動的に入力されます。Omeka Sは、特別な属性を持つ入力を検出し、それに応じて動作します。特別な属性は以下の通りです：

|属性|値キーにマッピングされる|説明|
|---|---|---|
|`data-value-key="@value"`|`@value`|文字列、構造化されているかどうかにかかわらず|
|`data-value-key="@language"`|`@language`|言語タグ|
|`data-value-key="@id"`|`@id`|URI|
|`data-value-key="o:label"`|`o:label`|URIラベル|
|`data-value-key="value_resource_id"`|`value_resource_id`|リソースID|

### 必須値

リソーステンプレートのプロパティが必須としてマークされているため、Omeka Sは自動的に`to-require`クラスを持つ入力を検出し、クライアント側でのバリデーションを行います。フォームで送信されるあらゆる入力には、このクラスを含めるべきです。

## カスタムデータタイプの追加

組み込みのデータタイプが十分でない場合、Omeka Sではカスタムのものを比較的簡単に追加することができます。例として、簡単な"Date"データタイプを追加するモジュールをビルドする方法を示しましょう。

まず、モジュールのINIファイルを`/modules/MyModule/config/module.ini`に作成します：
ini
[info]
version = "1.0.0-alpha"
omeka_version_constraint = "^1.0.0 || ^2.0.0"
name = "My Module"
description = "Adds a simple Date data type"

次に、モジュールファイルを`/moudules/MyModule/Module.php`に作成し、configでデータタイプを登録します：
php
<?php
namespace MyModule;

use Omeka\Module\AbstractModule;

class Module extends AbstractModule
{
    public function getConfig()
    {
        return [
            'data_types' => [
                'invokables' => [
                    'mymodule:date' => DataType\Date::class,
                ],
            ],
        ];
    }
}

Omeka Sがそれを検出するためには、モジュールのconfigでデータタイプを登録する必要があることに注意してください。

次に、"Date"データタイプクラスを`/modules/MyModule/src/DataType/Date.php`に作成しましょう：
php
<?php
namespace MyModule\DataType;

use Omeka\Api\Adapter\AbstractEntityAdapter;
use Omeka\Api\Representation\ValueRepresentation;
use Omeka\DataType\Literal;
use Zend\View\Renderer\PhpRenderer;

class Date extends Literal
{
    public function getName()
    {
        return 'mymodule:date';
    }
    public function getLabel()
    {
        return 'Date'; // @translate
    }
    public function form(PhpRenderer $view)
    {
        return '<input type="date" class="to-require" data-value-key="@value">';
    }
    public function isValid(array $valueObject)
    {
        return (bool) preg_match('/^\d{4}-\d{2}-\d{2}$/', $valueObject['@value']);
    }
    public function render(PhpRenderer $view, ValueRepresentation $value)
    {
        return date('l, j F Y', strtotime($value->value()));
    }
    public function getFulltextText(PhpRenderer $view, ValueRepresentation $value)
    {
        return $this->render($view, $value);
    }
}

組み込み`Literal`データタイプを継承しているため、その機能のいくつかを共有していることに注目してください。

`Date::form()`から返されるマークアップをもう少し詳しく見てみましょう：
html
<input name="" type="date" class="to-require" data-value-key="@value">

`data-value-key="@value"`属性に注目してください。Omeka Sはこれを検出し、自動的に`@value`キーを含む`name`属性を入力します。たとえば、`data-value-key="@language"`属性を持つ追加の言語入力がテンプレートに含まれている場合、`@language`キーを含む`name`属性を入力します。

モジュールをインストールした後、新しい"Date"データタイプをリソーステンプレートのプロパティに設定します。次に、このテンプレートに設定されたアイテムを追加します。プロパティ値は日付入力に変わるはずです。日付を入力し、フォームを保存すると、あなたのリソースを説明するフォーマットされた日付が表示されるはずです。アイテムを編集すると、入力した日付が日付入力に含まれているはずです。