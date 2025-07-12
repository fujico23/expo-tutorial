React Nativeは、<View>、<Text>、<Pressable>などの組み込みコンポーネントを標準のビルディングブロックとして提供しています。
私たちは、デバイスのメディアギャラリーから画像を選択する機能を構築しています。
これはコアコンポーネントでは不可能なので、アプリにこの機能を追加するにはライブラリが必要になります。

Expo SDKのライブラリexpo-image-pickerを使用します。
expo-image-pickerは、システムのUIにアクセスし、携帯電話のライブラリから画像やビデオを選択します。

1. Install expo-image-picker
```bash
npx expo install expo-image-picker
```
ヒント プロジェクトに新しいライブラリをインストールするときはいつでも、ターミナルでCtrl + cキーを押して開発サーバーを停止し、インストール・コマンドを実行してください。
インストールが完了したら、同じターミナル・ウィンドウからnpx expo startを実行することで、開発サーバーを再び起動できる。

2. Pick an image from the device's media library
expo-image-pickerはlaunchImageLibraryAsync()メソッドを提供し、デバイスのメディアライブラリから画像や動画を選択してシステムUIを表示します。
この機能を実装するために、デバイスのメディアライブラリから画像を選択し、デバイスの画像ライブラリを起動する関数を作成します。
app/(tabs)/index.tsxで、expo-image-pickerライブラリをインポートし、Indexコンポーネント内にpickImageAsync()関数を作成する：

3. Update the button component
プライマリ・ボタンを押すと、ButtonコンポーネントのpickImageAsync()関数が呼び出されます。
components/Button.tsxのButtonコンポーネントのonPress propを更新します：
```tsx
type Props = {
  label: string;
  theme?: 'primary';
  onPress?: () => void;
};

export default function Button ({label, theme, onPress}: Props) {
  // ... existing code
  <Pressable style={[styles.button, { backgroundColor: "#fff" }]} onPress={onPress}>
    <FontAwesome
      name="picture-o"
      size={18}
      color="#25292e"
      style={styles.buttonIcon}
    />
    <Text style={[styles.buttonLabel, { color: "#25292e" }]}>
      {label}
    </Text>
  </Pressable>
}
```
app/(tabs)/index.tsxで、最初の<Button>のonPress propにpickImageAsync()関数を追加します。
```tsx
// ... existing code
      <View style={styles.footerContainer}>
        <Button theme="primary" label="Choose a photo" onPress={pickImageAsync} />
        <Button label="Use this photo" />
      </View>
```
pickImageAsync()関数は、ImagePicker.launchImageLibraryAsync()を呼び出し、その結果を処理します。
launchImageLibraryAsync() メソッドは、選択された画像に関する情報を含むオブジェクトを返します。

4. Use the selected image
resultオブジェクトは、選択された画像のuriを含むassets配列を提供する。
イメージピッカーからこの値を取り出し、アプリで選択された画像を表示するために使ってみましょう。
app/(tabs)/index.tsxファイルを修正する：
- ReactのuseStateフックを使って、selectedImageというステート変数を宣言する。このステート変数を使って、選択された画像のURIを保持する。
- pickImageAsync()関数を更新して、画像のURIをselectedImage状態変数に保存する。
- selectedImageをImageViewerコンポーネントにpropとして渡す。
```tsx
import {useState} from "react";

export default function index() {
  const [selectedImage, setSelectedImage] = useState<string | undefined>(undefined);
  const pickImageAsync = async () => {
    // ... existing code
    if (!result.canceled) {
      setSelectedImage(result.assets[0].uri);
    } else {
      alert("You did not select any image.");
    }
  }
  // ... existing code

  return (
    // ... existing code
    <ImageViewer imageSource={PlaceholderImage} selectedImage={selectedImage} />
  )
}
```
selectedImageプロップをImageViewerコンポーネントに渡すと、プレースホルダ画像の代わりに選択された画像が表示されます。
- components/ImageViewer.tsxファイルを修正して、selectedImageプロップを受け入れるようにします。
- 画像のソースが長くなっているので、imageSourceという別の変数に移動させます。
- Image コンポーネントの source prop の値として imageSource を渡します。
```tsx
type Props = {
  imgSource: ImageSourcePropType;
  selectedImage?: string;
};

export default function ImageViewer({ imgSource, selectedImage }: Props) {
  const imageSource = selectedImage ? { uri: selectedImage } : imgSource;
  return <Image source={imageSource} style={styles.image} />;
}
```
上のスニペットでは、Imageコンポーネントは条件演算子を使って画像のソースを読み込んでいます。ピックされた画像はuri文字列であり、プレースホルダ画像のようなローカルアセットではありません。
https://unsplash.com/
