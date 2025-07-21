# Add gestures
このチュートリアルでは、React Native Gesture Handler および Reanimated ライブラリからジェスチャを実装する方法を学習します。

ジェスチャーは、アプリで直感的なユーザーエクスペリエンスを提供する優れた方法です。React Native Gesture Handlerライブラリは、ジェスチャーを処理できるネイティブコンポーネントを組み込みで提供します。プラットフォームのネイティブタッチ処理システムを使用して、パン、タップ、回転などのジェスチャーを認識します。この章では、このライブラリを使用して2つの異なるジェスチャーを追加します。
- ダブルタップすると絵文字ステッカーのサイズが拡大され、もう一度ダブルタップするとサイズが縮小されます。
- パンして絵文字ステッカーを画面上で移動し、ユーザーが画像上の任意の場所にステッカーを配置できるようにします。
また、 Reanimatedライブラリを使用して、ジェスチャの状態間をアニメーション化します。

## Add GestureHandlerRootView
アプリでジェスチャーインタラクションを動作させるには、<index>コンポーネントのトップにreact-native-gesture-handlerの<GestureHandlerRootView>をレンダリングします。app /(tabs)/index.tsxのルートレベルの<View>コンポーネントを<GestureHandlerRootView>に置き換えます。
```tsx
import { GestureHandlerRootView } from 'react-native-gesture-handler';

export default function index() {
  <GestureHandlerRootView style={styles.container}>
  # 既存のコード
  </GestureHandlerRootView>
}
```

## Use animated components
Animated コンポーネントは、コンポーネントの style prop を見て、どの値をアニメーション化するかを決定し、更新を適用してアニメーションを作成します。Reanimatedは、<Animated.View>、<Animated.Text>、<Animated.ScrollView>などのアニメーション コンポーネントをエクスポートします。ここでは、<Animated.Image>コンポーネントにアニメーションを適用して、ダブルタップのジェスチャーを動作させます。
- componentsディレクトリのEmojiSticker.tsxファイルを開きます。その中で、アニメーションコンポーネントを使用するために、react-native-reanimatedライブラリからAnimatedをインポートします。
- Imageコンポーネントを<Animated.Image>に置き換えます。
```tsx
import Animated from "react-native-reanimated";

export default function EmojiSticker({imageSize, stickerSource}: Props) {
  return (
    <View>
      <Animated.Image
        source={stickerSource}
        resizeMode="contain"
        style={{ width: imageSize, height: imageSize }}>
    </View>
  )
}

```

## Add a tap gesture
React Native Gesture Handler を使用すると、ダブルタップ イベントなどのタッチ入力を検出したときの動作を追加できます。
EmojiSticker.tsxファイル内:
- react-native-gesture-handlerから<Gesture>と<GestureDetector>をインポートします。
- ステッカーのタップを認識するために、react-native-reanimatedからuseAnimatedStyle、useSharedValue、withSpringをインポートして、<Animated.Image>のスタイルをアニメーション化します。
- EmojiStickerコンポーネントの内部で、useSharedValue()フックを使ってscaleImageという参照を作成します。これは初期値としてimageSizeの値を取ります。
```tsx
import { Gesture, GestureDetector } from "react-native-gesture-handler";
import Animated, { useAnimatedStyle, useShareValue, withSpring } from "react-native-reanimated";

export default function EmojiSticker({ imageSize, stickerSource }: Props) {
  const scaleImage = useSharedValue(imageSize);
  return ();
}
```
useSharedValue()フックを使用して共有値を作成すると、多くの利点があります。データを変更したり、現在の値に基づいてアニメーションを実行したりするのに役立ちます。.valueプロパティを使って共有値にアクセスし、変更することができます。doubleTapオブジェクトを作成して初期値をスケーリングし、Gesture.Tap()を使用してステッカー画像をスケーリングしながらトランジションをアニメーションさせます。必要なタップ数を決定するために、numberOfTaps() を追加します。
EmojiStickerコンポーネントに以下のオブジェクトを作成します：
```tsx
const doubleTap = Gesture.Tap()
  .numberOfTaps(2)
  .onStart(() => {
    if (scaleImage.value !== imageSize * 2) {
      scaleImage.value = scaleImage.value * 2;
    } else {
      scaleImage.value = Math.round(scaleImage.value / 2);
    }
  });
```
トランジションをアニメーション化するために、バネを使ったアニメーションを使おう。バネという現実世界の物理に基づいているため、生き生きとしたアニメーションになる。react-native-reanimatedが提供するwithSpring()関数を使用する。

ステッカー画像では、useAnimatedStyle()フックを使ってスタイルオブジェクトを作成します。これにより、アニメーションが発生したときに共有値を使用してスタイルを更新できるようになる。また、widthプロパティとheightプロパティを操作して、画像のサイズを拡大縮小します。これらのプロパティの初期値はimageSizeに設定されています。

imageStyle 変数を作成し、EmojiSticker コンポーネントに追加します：

# Add a pan gesture
ステッカー上のドラッグ・ジェスチャーを認識し、その動きを追跡するために、パン・ジェスチャーを使用します。components/EmojiSticker.tsxに、2つの新しい共有値を作成します：

translateXとtranslateYの2つの新しい共有値を作成します。
<View>を<Animated.View>コンポーネントで置き換えます。
```tsx
const translateX = useSharedValue(0);
const translateY = useSharedValue(0);

return (
  <Animated.Value style={{top: -350}}>
  </Animated.Value >
);
```
上のコードが何をするのか見てみよう：

- 定義された平行移動の値によって、ステッカーは画面上を移動する。ステッカーは両軸に沿って動くので、XとYの値を追跡する必要があります。
- useSharedValue()フックでは、両方のトランスレーション変数に初期位置0を設定しています。この値は、ジェスチャが始まるときのステッカーの初期位置を設定します。
前のステップでは、Gesture.Tap()メソッドにチェーンされたタップ・ジェスチャーのonStart()コールバックをトリガーしました。パンジェスチャの場合は、onChange() コールバックを指定します。
パン ジェスチャを処理するドラッグ オブジェクトを作成します。onChange()コールバックは、パラメータとしてeventを受け取ります。changeXとchangeYプロパティは、最後のイベントからの位置の変化を保持し、translateXとtranslateYに格納されている値を更新します。
useAnimatedStyle() フックを使用して containerStyle オブジェクトを定義します。これは、トランスフォームの配列を返します。<Animated.View>コンポーネントでは、transformプロパティにtranslateXとtranslateYの値を設定する必要があります。これにより、ジェスチャーがアクティブになったときのステッカーの位置が変わります。
