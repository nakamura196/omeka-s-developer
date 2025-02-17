# Omeka S への貢献

## コードスタイル

Omeka の開発は [PSR-2 スタイルガイド](http://www.php-fig.org/psr/psr-2)に準拠することを目指しており、[git-flow](http://nvie.com/posts/a-successful-git-branching-model) ブランチモデルを使用しています。

貢献がその標準を目指すべきです。

Omeka S には `.php_cs` ファイルが含まれており、コアおよびモジュールのコード整理に [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer) を使用できます。モジュールと共に使用する場合は、`.php_cs.cache` を `.gitignore` ファイルに追加する必要があります。貢献を提出する際には、スタイルの問題をプルリクエスト内の単一の独立したコミットで修正することをお勧めします。

## プルリクエストのヒント

一般的に、単一の問題に対処する小さく集中的なプルリクエストを受け入れる可能性が高まります。コードは[自己文書化](https://en.wikipedia.org/wiki/Self-documenting_code) であるべきです。コードが自明でない場所にはコメントを含める必要があります。コミットメッセージは理解しやすいものであるべきです。プルリクエストには、何を達成しようとしているか、どのようなアプローチをとっているか、レビュアーである私たちに役立つと思われるその他のメッセージが詳細に記載されていることが望ましいです。変更を受け入れる場合、メンテナンスも必要となるので、プルリクエストが高品質で説得力のあるものであることが重要です。

我々は [Laminas コーディングスタイルガイド](https://docs.laminas.dev/laminas-coding-standard/v2/coding-style-guide/) に準拠することを目指していますが、常にその基準に達しているわけではありません。したがって、プルリクエストを提出する過程でスタイルの修正を行うことは誘惑になります。それらは評価しますが、機能変更とは別のものであるべきです。

機能や機能性の変更と同じコミット内でスタイルの変更をコミットする(理解できる)衝動に抗ってください。それは私たちが提出された変更の機能性の変更をスタイルの変更と区別しやすくなり、プルリクエストが迅速に評価され受け入れられる可能性を高めます。

テストスイートで `php-cs-fixer` ライブラリを使用しています。機能変更を含めた後、`gulp test` (または `gulp test:cs` を使用してコードスタイルのテストのみを実行する)でそのテストを実行し、プルリクエストのために別のコミットで提案された変更を適用することを推奨します。

## ドキュメントへの協力

ドキュメントは必然的に実際のコードのいくつかの場所で遅れが生じます。私たちは [ドキュメント](https://github.com/omeka/omeka-s-developer/issues) への質問、訂正、プルリクエストを歓迎します。
