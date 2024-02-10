# ビューモデル

ビューモデルは、表示用のデータをビューに送信するために使われます。通常は、コントローラのアクション内で作成され、そこから返されます：
php

namespace MyModule\Controller;

use Zend\Mvc\Controller\AbstractActionController;
use Zend\View\Model\ViewModel;

class IndexController extends AbstractActionController
{
    public function indexAction()
    {
        $view = new ViewModel();
        // ビュー用の変数をセット
        $view->setVariable('mything', $mything);
        return $view;
    }
}

## イベントから

### `<head>`コンテンツをビューに追加する

ビューは多くの`Event`を通じて利用可能になります。これにより、多くの方法でページの`<head>`要素に追加することができます。

任意の`view.*`イベントにおいて、ビューはターゲットです。したがって、ビューを取得するためには、モジュールは次のようなコードを含める必要があります：
php

    public function attachListeners(SharedEventManagerInterface $sharedEventManager)
    {
        $sharedEventManager->attach(
            $identifier,
            'view.show.after',
            array($this, 'addCSS')
        );
    }

    public function addCSS($event)
    {
        $view = $event->getTarget();
        $view->headLink()->appendStylesheet($view->assetUrl('css/mymodule.css', 'MyModule'));
    }

`$view->assetUrl()`の最初のパラメータは、モジュールの`/asset`ディレクトリ内のパスで、2番目のパラメータはモジュールの名前空間です。

Javascriptファイルは同様に追加されます。以下のように使用します：
php
    $view->headScript()->appendFile($view->assetUrl('js/mymodule.js', 'MyModule'));

## Module::getConfigForm()関数から

上記はすべて、モジュールの`getConfigForm`関数を使用する場合にも当てはまりますが、渡されるオブジェクトは`Zend\View\Renderer\PhpRenderer`オブジェクトです。同じメソッドを使用できます。

## 関連情報

* [サーバーイベント](../events/server_events.md)
