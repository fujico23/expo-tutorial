The app/index.tsx file defines the text displayed on the app's screen. It is the entry point of our app and executes when the development server starts. It uses core React Native components such as <View> and <Text> to display background and text.

Styles applied to these components use JavaScript objects rather than CSS, which is used on web. However, a lot of the properties will look familiar if you've previously used CSS on web. Most React Native components accept a style prop that accepts a JavaScript object as its value. For more details, see Styling in React Native.

Let's modify app/index.tsx screen:

Import StyleSheet from react-native and create a styles object to define our custom styles.

React Native uses the same color format as the web. It supports hex triplets (this is what #fff is), rgba, hsl, and named colors, such as red, green, blue, peru, and papayawhip. For more information, see Colors in React Native.
