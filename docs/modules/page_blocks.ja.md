# ページブロック

Omeka Sのページはブロックで構成されています。埋め込まれたアイテムやコンテンツを埋め込むメディアから、単純なプレーンテキスト、より複雑で動的なものに至るまで、ページ上の全てのコンテンツはブロックとして配置されます。

各ブロックはレイアウトを持っており、これはページを作成または編集するユーザーがユーザーインターフェースとして見るものと、パブリックページを閲覧する訪問者が見るものの両方を制御します。

Omeka Sはいくつかのブロックレイアウトを標準で提供しており、モジュールも自身のブロックを追加することができ、そうすることでユーザーはページ上に含めるコンテンツの種類をより多く選択できます。

## ブロックに保存されているデータ

ブロック、そしてそのレイアウトは、ページを構築する際にユーザーが設定できる二つの基本的なデータタイプを取り扱います。

**アタッチメント**を使用すると、ブロックはOmeka Sインストールから既存のアイテムやメディアを使用できます。異なるレイアウト間で統一されて使用されるインターフェースがあり、ユーザーはアイテムプールを検索してアイテムを選択し（そして場合によってはそのアイテムの特定のメディアを選択することが可能です）。

アタッチメントを超えて、ブロックは**任意のデータ**も保存することができます。アタッチメントでない全てのもの（ユーザー提供のHTML、ブロックの設定やオプション、保存が必要なその他のあらゆる種類のデータ）は単一の「data」プロパティに保存されます。内部的には、このデータプロパティはJSONとして保存されるため、レイアウトはどのようなキーまたはその他の構造を使用してもデータを保存することができます。通常、レイアウトはこのデータを単純な文字列キー付き配列として保存します。

## 新しいレイアウトの追加

ブロックレイアウトは単一のクラスの形を取り、`BlockLayoutInterface`を実装しなければなりません。物事を単純化するために、ほとんどのブロックは実際に`AbstractBlockLayout`クラスを拡張します。これにより、「オプショナル」なメソッドをいくつか実装する必要がないため、簡単にスキップできます。

### 必須メソッド

#### `getLabel`

`getLabel()`は単にブロックの人間が読める名前を返します。管理インターフェースのページでユーザーに表示されるものです。ここで返される文字列はOmeka Sによって自動的に翻訳されるため、`// @translate`コメントでマークしておくべきです。

#### `form`

`form()`はブロックの管理側フォームのHTMLマークアップを返します。

Omeka Sは添付ファイルに対する共通のフォームタスクのためにいくつかのヘルパーを提供しているので、新しいレイアウトごとに一から再実装する必要はありません：

- `blockAttachmentsForm`は、ユーザーがメディアまたはアイテムを選択し、ブロックに添付するために使用されます。メディアコンテンツを表示するレイアウトは、ほぼ常にこのヘルパーを`form()`メソッドで使用します。このヘルパーは、ブロックが標準のアタッチメントUIを使用する方法です。

- `blockShowTitleSelect`は単純な`<select>`コントロールを提供しており、ユーザーはアタッチメントを表示する際にどのテキストを見出しとして表示するかを選択できます：アイテムのタイトル、メディアのタイトル、または何も表示しない。ここで選択された値は「data」プロパティに`'show_title_option'`キーの下に保存されます。

- `blockThumbnailTypeSelect`は`<select>`コントロールを提供し、ユーザーが添付されたメディアの画像を埋め込む際に使用するサムネイルの種類を選択できます。ここで選択された値は「data」プロパティに`'thumbnail_type'`キーの下に保存されます。

添付ファイルを超えて、ブロックはこのメソッドで任意の他の入力を提供できます。Omeka Sは`name`属性が特定のパターンに従っている限り、入力されたデータを「data」プロパティに自動的に保存します：`o:block[__blockIndex__][o:data][KEY]`、ここで`KEY`は保存されるデータの特定のキーで置き換えられます。

この方法で保存されたデータには、取得するキーの名前を渡して`dataValue`メソッドを使用してアクセスできます。

たとえば、組み込みのHTMLブロックレイアウトの非常にシンプルなフォームは、ユーザーが入力したマークアップを`html`キーの下にだけデータとして保存します：
php
public function form(PhpRenderer $view, SiteRepresentation $site,
    SitePageRepresentation $page = null, SitePageBlockRepresentation $block = null
) {
    $textarea = new Textarea("o:block[__blockIndex__][o:data][html]");
    $textarea->setAttribute('class', 'block-html full wysiwyg');
    if ($block) {
        $textarea->setAttribute('value', $block->dataValue('html'));
    }
    return $view->formRow($textarea);
}

`<textarea>`入力は`o:block[__blockIndex__][o:data][html]`を名前として使用し、ブロックを使用してフォームにデータを入力するために`$block->dataValue('html')`を読み込んでいます。このパターンに従えば、任意のデータ値を保存および取得するために追加のコードは必要ありません。

#### `render`

`render()`は公開ページ上でブロックを表示します。このメソッドは、ヘルパーの使用とビューへのアクセスに`$view`を受け取り、`$block`としてブロックの表現を受け取り、ブロックに保存された添付ファイルやその他のデータにアクセスします。

ベストプラクティスは、HTMLの実際の表示コードを別のビューパーシャルに書き、`$view->partial()`ヘルパーを使用してそれをレンダリングすることです。これにより、テーマがレイアウトのマークアップをオーバーライドするのが容易になります。慣習により、レイアウトのこのパーシャルファイルは、`common/block-layouts/layout-name.phtml`のパスに保存されます。ここで`layout-name`はレイアウトの名前を小文字で、単語はハイフンで区切ったものです。

`$block`には`render()`メソッドで使用するのに役立つ2つの主要なメソッドがあります：

- `$block->attachments()`はブロックの添付ファイルの配列を返します
- `$block->dataValue($key, [$default])`は、指定されたキーを持つ任意のデータを返します。オプショナルなパラメータ`$default`が指定された場合、データキーが設定されていない場合のフォールバック値を提供するために渡すことができます。

  このようにして、全くのデータセットに一度にアクセスすることができるようになります。`$block->data()`を呼び出すことでこれが可能となります。

### オプショナルなメソッド

`AbstractBlockLayout`によって空白のメソッドとして事前に実装されているいくつかの使用頻度の低いメソッドがあります。レイアウトに独自の要件がある場合はこれらのメソッドを提供することができますが、抽象クラスから拡張している場合はそれらを省略することもできます。

#### `prepareForm`

このメソッドはページフォームをレンダリングする際に、各レイアウトごとに一度だけ実行されます。このメソッドの典型的な使い方は、特にレイアウトのフォームで使用されるJavascriptファイルなど、ページにアセットをロードすることです。

Omeka Sは、ページフォームに新しいブロックのフォームが追加されたときにコードを実行するのに役立つ[クライアントサイドイベント](../events/client_events.md)を提供しています、`o:block-added`。

このメソッドは`$view`のパラメータを受け取り、ビューヘルパーを簡単に呼び出すことができます。

#### `prepareRender`

`prepareForm()`の公開側への相当部分として、このメソッドはページのレンダリング時に各レイアウトごとに一度だけ実行されます。再び、その典型的な使い方はブロックの表示に必要なJavascriptなどをロードするためです。

`prepareForm()`のように、このメソッドは`$view`のパラメータを受け取り、ビューヘルパーを使用できます。

#### `onHydrate`

このメソッドはブロックの保存時に実行され、データがブロックオブジェクトに設定された直後に発火します。`onHydrate()`はデータを検証またはフィルタするために使用されます。たとえば、HTMLブロックはこのメソッドを使用して、ユーザー提供のマークアップに対してHTML Purifierフィルタリングライブラリを実行します。

`form()`メソッドについての上記のガイドラインに従って提供されたヘルパーを使用する場合、データおよび添付ファイルの保存処理はOmeka Sによって自動的に行われるので、特に検証またはフィルタリングに関する要件がない限り、ブロックがこのメソッドを使用する必要はありません。

これは`$block`、ブロックのエンティティを読み取り、ブロックのデータを設定するために、`$errorStore`でエラーを設定するために使用します。

#### `getFulltextText`

Omeka S 2.0.0は新しい「フルテキスト」検索インデックスを追加しました。
これにはページからのコンテンツが含まれます。すべてのページのコンテンツはブロックに保存されているため、
ブロックレイアウトはどのデータが、検索可能であるべきかを決定しなければなりません。

このメソッドからのテキストの返却は、レイアウトを使用するブロックが含まれているページのインデックスにそれを含めます。

ユーザーが提供するテキストは、このメソッドから返却されるべき通常のデータのタイプです。たとえば、HTMLブロックはユーザーが入力したテキストを返します：
php
public function getFulltextText(PhpRenderer $view, SitePageBlockRepresentation $block)
{
    return strip_tags($this->render($view, $block));
}

### 設定

ブロックレイアウトは、使用可能にするために[設定](../configuration/index.md)に登録する必要があります。設定は標準のサービスマネージャーの設定セクションで、キー`block_layouts`の下にあります。ほとんどのブロックは他のサービスに依存していないため、単純にインボーカブルとして登録できます：
php
<?php
namespace MyModule;

use Omeka\Module\AbstractModule;

class Module extends AbstractModule
{
    public function getConfig()
    {
        return [
            'block_layouts' => [
                'invokables' => [
                    'myModuleLayout' => Site\BlockLayout\MyModuleLayout::class,
                ],
            ],
        ];
    }
}

サービス名（この例では`myModuleLayout`）は、ブロックが使用するレイアウトと内部的に接続するために使用され、Omeka Sインストール全体およびすべてのモジュールで一意でなければなりません。ベストプラクティスは、システム全体での競合を避けるために、サービス名の前にモジュールの名前を含めることです。