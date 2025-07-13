React Nativeは、アプリの他の部分の上にコンテンツを表示する<Modal>コンポーネントを提供します。
一般的に、モーダルはユーザーの注意を重要な情報に向けたり、アクションを取るように誘導したりするために使用されます。
たとえば、第3章ではボタンを押した後、alert() を使ってプレースホルダ・テキストを表示しました。
これが、モーダルコンポーネントがオーバーレイを表示する方法です。
この章では、絵文字ピッカー リストを表示するモーダルを作成します。

# Declare a state variable to show buttons
モーダルを実装する前に、3 つの新しいボタンを追加します。
これらのボタンは、ユーザーがメディア ライブラリから画像を選択するか、プレースホルダ画像を使用した後に表示されます。
これらのボタンのいずれかが、絵文字ピッカー モーダルをトリガーします。
app/(tabs)/index.tsx で、
1. boolean ステート変数 showAppOptions を宣言して、モーダルを開くボタンの表示/非表示を設定します。アプリ画面がロードされるとき、画像を選択する前にオプションが表示されないように、false に設定します。ユーザーが画像をピックするか、プレースホルダ画像を使用すると、これを true に設定します。 
2. pickImageAsync() 関数を更新して、ユーザーが画像をピックした後に showAppOptions の値を true に設定します。
3. 次の値を持つonPress propを追加することで、テーマのないボタンを更新します。
```tsx
export default function index() {
  const [showAppOptions, setShowAppOptions] = useState<boolean>(false);

  const pickImageAsync = async () => {
    // existing code...
    if (!result.canceled) {
      setSelectedImage(result.assets[0].uri);
      setShowAppOptions(true); // 画像を選択した後にオプションを表示
    }
  }

  return (
    // ... existing code ...
      {showAppOptions ? (
        <View />
      ) : (
        <View style={styles.footerContainer}>
          <Button
            label="Choose a photo"
            theme="primary"
            onPress={pickImageAsync} // Buttonを押した時にカメラを起動する
          />
          <Button
            label="Use this photo"
            onPress={() => {
              setShowAppOptions(true);
            }}
          />
        </View>
      )}
```
上記のスニペットでは、showAppOptionsの値に基づいてButtonコンポーネントをレンダリングし、三項演算子ブロック内のボタンを移動しています。
showAppOptionsの値がtrueの場合、空の<View>コンポーネントをレンダリングします。この状態については、次のステップで説明します。
ここで、Buttonコンポーネントのアラートを削除し、components/Button.tsxで2番目のボタンをレンダリングするときにonPressプロップを更新します：
```tsx
<Pressable style={styles.button}  onPress={onPress} >
```

# Add buttons
この章で実装するオプションボタンのレイアウトを分解してみましょう。デザインはこのようになります：
親となる <View> には 3 つのボタンが並んでいます。
真ん中のプラス アイコン (+) のボタンはモーダルを開くもので、他の 2 つのボタンとは異なるスタイルになっています。
componentsディレクトリ内に、次のコードで新しいCircleButton.tsxファイルを作成します：
```tsx
import MaterialIcons from "@expo/vector-icons/MaterialIcons";
import { Pressable, StyleSheet, View } from "react-native";

type Props = {
  onPress: () => void;
};

export default function CircleButton({ onPress }: Props) {
  return (
    <View style={styles.circleButtonContainer}>
      <Pressable style={styles.circleButton} onPress={onPress}>
        <MaterialIcons name="add" size={38} color="#25292e" />
      </Pressable>
    </View>
  );
}
```

プラスアイコンを表示するために、このボタンは@expo/vector-iconsライブラリの<MaterialIcons>アイコンセットを使用しています。
他の2つのボタンも<MaterialIcons>を使用して、縦に整列したテキスト・ラベルとアイコンを表示する。
componentsディレクトリ内にIconButton.tsxという名前のファイルを作成します。このコンポーネントは、3つのpropsを受け入れます：
- icon : MaterialIconsライブラリのアイコンに対応する名前。
- label : ボタンに表示されるテキスト・ラベル。
- onPress : ユーザーがボタンを押したときに呼び出される関数。
```tsx
import { Pressable, StyleSheet, Text } from 'react-native';
import MaterialIcons from '@expo/vector-icons/MaterialIcons';

type Props = {
  icon: keyof typeof MaterialIcons.glyphMap;
  label: string;
  onPress: () => void;
};

export default function IconButton({ icon, label, onPress }: Props) {
  return (
    <Pressable style={styles.iconButton} onPress={onPress}>
      <MaterialIcons name={icon} size={24} color="#fff" />
      <Text style={styles.iconButtonLabel}>{label}</Text>
    </Pressable>
  );
}
```

app/(tabs)/index.tsx: 
1. CircleButtonとIconButtonコンポーネントをインポートして表示する。
2. これらのボタンのために3つのプレースホルダ関数を追加します。onReset()関数は、ユーザーがリセット・ボタンを押すと呼び出され、イメージ・ピッカー・ボタンが再び表示されます。他の2つの関数は後で追加します。
```tsx
import CircleButton from "@/components/CircleButton";
import IconButton from "@/components/IconButton";

export default function index() {
  // ... existing code ...
  const onReset = () => {
  }
  const onAddSticker = () => {
    console.log("Add sticker button pressed");
  }
  const onSaveImageAsync = async () => {
    console.log("Save image button pressed");
  }

  return (
    // ... existing code ...
    {showAppOptions ? (
      <View>
        <View style={styles.optionRow}>
          <IconButton icon="refresh" label="Reset" onPress={onReset} />
          <CircleButton onPress={onAddSticker} />
          <IconButton icon="save" label="Save" onPress={onSaveImageAsync} />
        </View>
      </View>
    )}
  )
}
```

# Create an emoji picker modal
モーダルでは、利用可能な絵文字のリストからユーザーが絵文字を選択できます。components ディレクトリ内に EmojiPicker.tsx ファイルを作成します。このコンポーネントは、3つのpropsを受け入れます：
- isVisible : モーダルの表示状態を決定するブール値。
- onClose : モーダルを閉じる関数です。
- children : 後で絵文字のリストを表示するために使用します。
```tsx
import { Modal, Pressable, StyleSheet, Text, View } from "react-native";
import { PropsWithChildren } from "react";
import MaterialIcons from "@expo/vector-icons/MaterialIcons";

type Props = PropsWithChildren<{
  isVisible: boolean;
  onClose: () => void;
}>;

export default function EmojiPicker({isVisible, onClose, children}: Props) {
  return (
    <View>
      <Modal visible={isVisible} animationType="slide" transparent={true} >
        <View>
          <View>
            <Text>Choose a sticker</Text>
            <Pressable onPress={onClose} >
              <MaterialIcons name="close" size={24} color="#000" />
            </Pressable>
          </View>
        </View>
      </Modal>
    </View>
  )
}
```


上のコードが何をするのか学んでみよう： 
- <Modal> コンポーネントは、タイトルと閉じるボタンを表示します。
- visible プロパは isVisible の値を取り、モーダルが開いているか閉じているかを制御します。
- transparent プロパは boolean 値で、モーダルがビュー全体を満たすかどうかを決定します。
- animationType プロパは、モーダルがどのように画面に出入りするかを決定します。この場合、画面下部からスライドします。
- 最後に、<EmojiPicker> は、ユーザーが閉じる <Pressable> を押すと、onClose プロップを呼び出します。

1. <EmojiPicker>コンポーネントをインポートする。
2. useState フックで isModalVisible 状態変数を作成します。デフォルト値は false で、ユーザーがボタンを押して開くまでモーダルは非表示になります。
3. onAddSticker() 関数のコメントを置き換えて、ユーザーがボタンを押したときに isModalVisible 変数を true に更新します。これにより、絵文字ピッカーが開きます。
4. isModalVisible状態変数を更新するonModalClose()関数を作成します。
5. Indexコンポーネントの下に<EmojiPicker>コンポーネントを配置します。

# Display a list of emoji
モーダルのコンテンツに絵文字の水平リストを追加してみましょう。これには、React Native の <FlatList> コンポーネントを使用します。components ディレクトリ内に EmojiList.tsx ファイルを作成し、以下のコードを追加します：
```tsx
import { useState } from "react";
import { ImageSourcePropType, Platform, FlatList, Pressable, StyleSheet, View } from "react-native";
import { Image } from "expo-image";

type Props = {
  onSelect: (image: ImageSourcePropType) => void;
  onCloseModal: () => void;
}

export default function EmojiList({onSelect, onCloseModal}: Props) {
  const [emoji] = useState<ImageSourcePropType[]>([
    require("../assets/images/emoji1.png"),
    // example images, replace with actual emoji images
  ])

  return (
    <FlatList
      horizontal
      showsHorizontalScrollIndicator={Platform.OS === "web"}
      data={emoji}
      contentContainerStyle={styles.emojiList}
      renderItem={({item, index}) => (
        <Pressable
          onPress={() => {
            onSelect(item);
            onCloseModal();
          }}>
          <Image source={item} key={index} style={styles.emojiImage} />
        </Pressable>
      )} 
    />
  );
}

```

- 上記のコンポーネントは、 <FlatList>でラップされた コンポーネントを使用してすべての絵文字画像をレンダリングします。後でこれを改良し、ユーザーが画面上の絵文字をタップすると、画像上にステッカーとして表示できるようになります。Image<Pressable>
- また、配列変数によって提供されるアイテムの配列をプロパティemojiの値として受け取りますdata。このrenderItemプロパティはリストからアイテムを取得しdata、リスト内のアイテムを返します。最後に、このアイテムを表示するためのコンポーネントImageと<Pressable>コンポーネントを追加しました。
- このhorizontalプロパティは、リストを垂直ではなく水平にレンダリングします。React showsHorizontalScrollIndicatorNativeのPlatformモジュールを使用して値を確認し、Web上で水平スクロールバーを表示します。

次に、app/(tabs)/index.tsxを更新してコンポーネントをインポートし<EmojiList>、コンポーネント内のコメントを<EmojiPicker>次のコード スニペットに置き換えます。
```tsx
import { ImageSourcePropType, View, StyleSheet } from "react-native";
import EmojiList from "@/components/EmojiList";

export default function index() {
  // ... existing code ...
  const [pickedEmoji, setPickedEmoji] = useState<ImageSourcePropType | undefined>(undefined);
  // ... existing code ...

  return (
    // ... existing code ...
    <EmojiList onSelect={setPickedEmoji} onCloseModal={onModalClose} >
  );
}
```
# Display the selected emoji
それでは、画像に絵文字ステッカーを貼り付けましょう。componentsディレクトリにEmojiSticker.tsxという新しいファイルを作成し、以下のコードを追加してください。
```tsx
import { Image } from "expo-image";
import { ImageSourcePropType, View } from "react-native";

type Props = {
  imageSize: number;
  stickerSource: ImageSourcePropType;
};

export default function EmojiSticker({ imageSize, stickerSource }: Props) {
  return (
    <View style={{ top: -350 }}>
      <Image
        source={stickerSource}
        style={{ width: imageSize, height: imageSize }}
      />
    </View>
  );
}
```
このコンポーネントは 2 つのプロパティを受け取ります。

imageSize: コンポーネント内で定義された値Index。次の章では、この値を使用して、タップ時に画像のサイズを調整します。
stickerSource: 選択した絵文字画像のソース。
このコンポーネントをapp/(tabs)/index.tsxファイルにインポートし、Index画像に絵文字ステッカーが表示されるようにコンポーネントを更新します。pickedEmoji状態が以下の状態ではないかどうかを確認しますundefined。
