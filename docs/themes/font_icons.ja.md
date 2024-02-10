# フォントアイコン

Omeka Sは管理インターフェースのアイコングラフィにFont Awesome 5.15.4アイコンセットを使用しています。これはOmeka Sのビジュアル言語のリファレンスです。

<table id="iconfonts">
  <thead>
    <tr>
      <th>グリフ</th>
      <th>使用法</th>
      <th>Omeka Sクラス</th>
      <th>Font Awesomeクラス</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
    <tr>
      <td><span class="o-icon-menu" aria-role="icon" aria-labelledby="o-icon-menu"></span></td>
      <td id="o-icon-menu">メニュー</td>
      <td>o-icon-menu</td>
      <td>fa-bars</td>
    </tr>
    <tr>
      <td><span class="o-icon-search" aria-role="icon" aria-labelledby="o-icon-search"></span></td>
      <td id="o-icon-search">検索</td>
      <td>o-icon-search</td>
      <td>fa-search</td>
    </tr>
    <tr>
      <td><span class="o-icon-user" aria-role="icon" aria-labelledby="o-icon-user"></span></td>
      <td id="o-icon-user">ユーザー</td>
      <td>o-icon-user</td>
      <td>fa-user</td>
    </tr>
    他の表の行は省略 ...
</table>

## 他のFont Awesomeアイコンの使用

Omeka SはFont Awesomeの[無料のソリッドウェイト](https://fontawesome.com/v5.15/icons?d=gallery&p=2&s=solid&m=free)で提供されているすべてのアイコンをロードします。上の表にないアイコンを使用したい場合は、アイコンのセレクタの前に`.o-icon-`を付けて、Font Awesomeのウェブサイトでアイコンのユニコードを探してください。

例えば、`.fa-bars`のアイコン（<span class="o-icon-bars" role="icon" aria-label=".fa-barsアイコン"></span>）を使用したい場合は、[Font Awesomeのアイコンエントリ](https://fontawesome.com/v5.15/icons/bars?style=solid)を検索し、そのユニコード（`\f0c9`）をアイコンの`:before`擬似要素のコンテンツに使用します。

.o-icon-bars:before {
    content: "\f0c9";
}