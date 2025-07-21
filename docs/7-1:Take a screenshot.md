このチュートリアルでは、サードパーティ製ライブラリとExpoメディアライブラリを使用してスクリーンショットをキャプチャする方法を学びます。


この章では、サードパーティのライブラリを使用してスクリーンショットを撮影し、デバイスのメディアライブラリに保存する方法を学びます。スクリーンショットの撮影にはreact-native-view-shotを使用し、デバイスのメディアライブラリに画像を保存するにはexpo-media-libraryを使用します。

## ライブラリのインストール
react-native-view-shotとexpo-media-libraryをインストールするには、以下のコマンドを実行する：
```bash
npx expo install react-native-view-shot expo-media-library
```
## Prompt for permissions
デバイスのメディアライブラリへのアクセスなど、機密情報を必要とするアプリは、アクセスを許可または拒否する許可を求める必要がある。expo-media-libraryのusePermissions()フックを使えば、パーミッションステータスとrequestPermission()メソッドを使ってアクセスを求めることができます。

アプリが初めてロードされ、パーミッションステータスが許可でも拒否でもない場合、ステータスの値はnullになります。許可を求められた場合、ユーザーは許可を与えるか拒否するかを選択できます。nullかどうかをチェックする条件を追加し、nullであればrequestPermission()メソッドを起動します。アクセス権を取得すると、ステータスの値が granted に変わります。

app/(tabs)/index.tsx内に以下のコード・スニペットを追加する：

## 現在のビューを保存するためのrefを作成する
ユーザーがアプリ内でスクリーンショットを撮影できるようにするために、react-native-view-shotを使用します。このライブラリはcaptureRef()メソッドを使って<View>のスクリーンショットを画像としてキャプチャします。キャプチャしたスクリーンショットの画像ファイルのURIを返します。

- react-native-view-shotからcaptureRefを、ReactからuseRefをインポートする。
- キャプチャしたスクリーンショット画像の参照を格納するimageRef参照変数を作成します。
- <ImageViewer>と<EmojiSticker>コンポーネントを<View>で囲み、参照変数を渡します。

```tsx
import { useState, useRef } from 'react';
import { captureRef } from 'react-native-view-shot';

export default function Index() {
  const imageRef = useRef<View>(null);
  return (
    <GestureHandlerRootView style={styles.container}>
      <View style={styles.imageContainer}>
        <View ref={imageRef} collapsable={false}>
          <ImageViewer imgSource={PlaceholderImage} selectedImage={selectedImage} />
          {pickedEmoji && <EmojiSticker imageSize={40} stickerSource={pickedEmoji} />}
        </View>
      </View>
      {/* ...rest of the code remains same */}
    </GestureHandlerRootView>
  );
}
```
上のスニペットでは、collapsable propをfalseに設定しています。これにより、<View>コンポーネントは背景画像と絵文字ステッカーのみをスクリーンショットすることができます。

## スクリーンショットのキャプチャと保存
onSaveImageAsync()関数内でreact-native-view-shotのcaptureRef()メソッドを呼び出すことで、ビューのスクリーンショットをキャプチャすることができます。captureRef()メソッドにはオプションの引数が渡され、スクリーンショットをキャプチャする領域の幅と高さを指定できます。使用可能なオプションについては、ライブラリのドキュメントを参照してください。

captureRef()メソッドはまた、スクリーンショットのURIを満たすプロミスを返します。このURIをMediaLibrary.saveToLibraryAsync()のパラメータとして渡し、スクリーンショットをデバイスのメディアライブラリに保存します。

app/(tabs)/index.tsx内で、onSaveImageAsync()関数を以下のコードで更新します：
