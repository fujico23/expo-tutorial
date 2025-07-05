# Expo Router basics
Expo Router is a file-based routing framework for React Native and web apps. It manages navigation between screens and uses the same components across multiple platforms. To get started, we need to know about the following conventions:

- app directory: A special directory containing only routes and their layouts. Any files added to this directory become a screen inside our native app and a page on the web.
- Root layout: The app/_layout.tsx file. It defines shared UI elements such as headers and tab bars so they are consistent between different routes.
- File name conventions: Index file names, such as index.tsx, match their parent directory and do not add a path segment. For example, the index.tsx file in the app directory matches / route.
- A route file exports a React component as its default value. It can use either .js, .jsx, .ts, or .tsx extension.
- Android, iOS, and web share a unified navigation structure.

The above list is enough for us to get started. For a complete list of features, see Introduction to Expo Router.

## Add a new screen to the stack
Let's create a new file named about.tsx inside the app directory. It displays the screen name when the user navigates to the /about route.

Inside _layout.tsx:
Add a <Stack.Screen /> component and an options prop to update the title of the /about route.
Update the /index route's title to Home by adding options prop.


What is a Stack?
A stack navigator is the foundation for navigating between different screens in an app. On Android, a stacked route animates on top of the current screen. On iOS, a stacked route animates from the right. Expo Router provides a Stack component to create a navigation stack to add new routes.


## Navigate between screens
We'll use Expo Router's Link component to navigate from the /index route to the /about route. It is a React component that renders a <Text> with a given href prop.

Import the Link component from expo-router inside index.tsx.
Add a Link component after <Text> component and pass href prop with the /about route.
Add a style of fontSize, textDecorationLine, and color to Link component. It takes the same props as the <Text> component.
