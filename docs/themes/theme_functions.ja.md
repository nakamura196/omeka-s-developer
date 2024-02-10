# テーマ機能

テーマに特有の機能があり、別々のモジュールとして存在するべきではない場合があります。そのような場合には、ヘルパー関数を記述することができます。ヘルパーを設定するには、`config/theme.ini`でそれらを定義する必要があります。ヘルパーはパスカルケースを使用して名付けられ、`helpers`配列に追加する必要があります。

[info]
name = "テーマ名"

...

helpers[] = "FirstHelper"
helpers[] = "SecondHelper"

各ヘルパー関数はテーマ内の独自の`.php`ファイルで定義されます。

- asset/
- config/
- helper/
    - FirstHelper.php
    - SecondHelper.php
- view/
- README.md
- theme.jpg

これらのファイルの中で、以下のテンプレートを使用して機能を定義することができます。

<?php 
namespace OmekaTheme\Helper;

use Laminas\View\Helper\AbstractHelper;

class FirstHelper extends AbstractHelper
{
    public function __invoke() 
    {
        // ここにコードを記述

    }
}