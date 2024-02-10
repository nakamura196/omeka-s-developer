# フォーム

Omeka S はしばしば `Zend\Form\Form` を拡張して `Omeka\Form` 名前空間に配置しますが、場合によってはモジュールがフォームにさらにフィールドを追加できるようにイベントを含んでいます。

フィールドセットを追加することは、モジュールがフォローする一般的なパターンです。テキストエリアを追加するには、`Module.php` クラスに次のように追加します。

これらのフォームに適用します

* `Omeka\Form\SettingForm`
* `Omeka\Form\SiteSettingsForm`

それぞれ `Event::ADD_ELEMENTS` イベントと `Event::ADD_INPUT_FILTERS` イベントを使用します。

モジュールの広範な構造については、[モジュール](../modules/index.md) を参照してください。
php

    public function addElements(Event $event)
    {
        $form = $event->getParam('form');
        $fieldset = new Fieldset('example');
        $fieldset->setLabel('例のフィールドセット');

        $fieldset->add([
                'name' => 'example',
                'type' => 'text',
                'options' => [
                    'label' => '例のテキスト入力', // 翻訳
                ],
            ]);

        $form->add($fieldset);
    }


## 検証

検証は要素の入力フィルターを介して処理されます。したがって、フォームのサーバー側の検証を変更するには、`Event::SITE_SETTINGS_ADD_INPUT_FILTERS` イベントと `Event::GLOBAL_SETTINGS_ADD_INPUT_FILTERS` イベントを使用します。
php

    public function addFilters($event)
    {
        $inputFilter = $event->getParam('inputFilter');
        $inputFilter->get('example')->add([
                    'name' => 'example',
                    'required' => false,
                ]);
    }