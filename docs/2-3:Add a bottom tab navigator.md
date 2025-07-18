# Add a Bottom Tab Navigator
アプリにボトムタブナビゲータを追加し、既存の「ホーム」と「バージョン情報」画面を再利用してタブレイアウトを作成します（XやBlueSkyのような多くのソーシャルメディアアプリで一般的なナビゲーションパターンです）。 また、ルートレイアウトでスタックナビゲータを使用し、+not-foundルートが他のネストされたナビゲータよりも優先して表示されるようにします。 
1. appディレクトリの中に、(tabs)サブディレクトリを追加します。 この特別なディレクトリは、ルートをグループ化して、下のタブバーに表示するために使われます。
2. ディレクトリ内に(tabs)/_layout.tsxファイルを作成します。 このファイルはルートレイアウトとは別に、タブレイアウトを定義するのに使われます。 
3. 既存のindex.tsxとabout.tsxファイルを(tabs)ディレクトリ内に移動します。 appディレクトリの構造は以下のようになります：

# Update bottom tab navigator appearance
現在、下部タブのナビゲーターはすべてのプラットフォームで同じように見えますが、私たちのアプリのスタイルにマッチしていません。 たとえば、タブ バーやヘッダーにはカスタム アイコンが表示されず、一番下のタブの背景色はアプリの背景色と一致しません。

タブバーのアイコンを追加するために、(tabs)/_layout.tsxファイルを修正する：

- 人気のアイコンセットを含むライブラリ@expo/vector-iconsからIoniconsアイコンセットをインポート。
- indexルートとaboutルートの両方にtabBarIconを追加する。 この関数は、focus と color をパラメータとして受け取り、アイコンコンポーネントをレンダリングします。 アイコンセットから、カスタムアイコン名を指定できます。
- タブ・コンポーネントにscreenOptions.tabBarActiveTintColorを追加し、その値を#ffd33dに設定する。 これにより、アクティブ時のタブバーのアイコンとラベルの色が変更されます。

このコードでは 
- tabBarActiveTintColorは、アクティブなタブのアイコンとラベルの色を#ffd33dに設定します。tabBarIconで渡されるcolorやタブの下のラベル文字が適用対象。
- headerStyleで画面上部の背景色を設定
- headerShadowVisibleを使用して、ヘッダーの影を無効にしています。 
- headerTintColorは、ヘッダー内のテキストや戻るボタンなどの色を設定。 
- tabBarStyle.backgroundColorは、タブバーに#25292eを適用します 私たちのアプリは、カスタムボトムタブナビゲータを持つようになりました：
