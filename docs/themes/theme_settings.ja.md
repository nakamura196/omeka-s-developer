# テーマ設定

テーマ設定とは、ユーザーがプロジェクトに合わせてテーマをカスタマイズできるようにする、テーマの設定可能部分を提供することです。テーマ設定の例には、設定可能な要素の色、画像アセットのアップロード、フッターコンテンツなどが含まれます。

テーマ設定のフォームフィールドは、`config/theme.ini` で作成します。`[info]` メタデータの下にある `[config]` タイトルで始めます。`[config]` タイトルはテーマ設定のリストの開始を意味するため、個々の設定ごとに含める必要はありません。

[info]
name = "テーマ名"

#...

[config]

各テーマ設定はフォーム要素であるため、設定のラベルを付け、ユニークな小文字の名前を提供し、要素リストに追加することから始めます。

[config]
elements.my_theme_setting.name = "my_theme_setting"
elements.my_theme_setting.options.label = "マイテーマ設定"

次に、テーマ設定のフォーム要素タイプを定義する必要があります。

Zend Framework の利用可能なフォーム要素タイプは、[そのドキュメント](https://docs.laminas.dev/laminas-form/v3/element/intro/)で見つけることができます。設定ファイル内で、パス `Zend\Form\Element\[Type]` を使って要素タイプを参照します。次の例では、テーマ設定をチェックボックスとして定義しています。

elements.my_theme_setting.type = "Zend\Form\Element\Checkbox"

Omeka 用に作成された利用可能なフォーム要素は `application\src\Form\Element` にあります。設定ファイル内で、パス `Omeka\Form\Element\[Type]` を使って要素タイプを参照します。次の例では、ユーザーがアクセスできるリソーステンプレートを含んでいるセレクト要素としてテーマ設定を定義しています。

elements.my_theme_setting.type = "Omeka\Form\Element\ResourceTemplateSelect"

この例は、この場合ラジオ入力のための要素の値オプションを定義する方法を示しています。

elements.my_theme_setting.type = "Zend\Form\Radio"
elements.my_theme_setting.options.value_options.first = "最初のオプション"
elements.my_theme_setting.options.value_options.second = "二番目のオプション"
elements.my_theme_setting.options.value_options.third = "三番目のオプション"

テーマ設定のフォーム要素に HTML 属性を定義することもできます。この例では、要素の id を定義しています。

elements.my_theme_setting.attributes.id = "my_theme_settings"

ラジオ入力を使用した完全なテーマ設定の例です。

elements.my_theme_setting.name = "my_theme_setting"
elements.my_theme_setting.attributes.id = "my_theme_setting"
elements.my_theme_setting.options.label = "マイテーマ設定"
elements.my_theme_setting.type = "Zend\Form\Radio"
elements.my_theme_setting.options.value_options.first = "最初のオプション"
elements.my_theme_setting.options.value_options.second = "二番目のオプション"
elements.my_theme_setting.options.value_options.third = "三番目のオプション"

## 要素グループ

Omeka S 4.0.0 から、テーマは設定要素をラベル付きセクションにグループ化することができます。

`[config]` セクション内で、まず `element_groups` キーを使ってグループ自体を定義する必要があります。例えば、"Global Settings" と "Item-specific Settings" の2つのグループを持つテーマは、次のような設定になります：

element_groups.global = "グローバル設定"
element_groups.item = "アイテム特有の設定"

各 `element_groups.` の後の部分は、グループを参照してそれに物を置くために使用する内部 ID で、等号の後のテキストはユーザーに見せるための見出しです。

次に、各要素に対して、その要素が属するグループを示す追加の行が必要です。上記の `my_theme_setting` 例要素を `global` グループに入れるためには、その要素の他の行に次の行を加えます：

elements.my_theme_setting.options.element_group = "global"

ここで使用される値は `global` です。これは、グループを定義した `element_groups` ラインの「内部 ID」です。

グループを使用するときは、「グループ未分類」の要素を残さないことが最善ですので、定義したグループのいずれかにすべての要素を配置する必要があります。