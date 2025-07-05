We'll add a bottom tab navigator to our app and reuse the existing Home and About screens to create a tab layout (a common navigation pattern in many social media apps like X or BlueSky). We'll also use the stack navigator in the Root layout so the +not-found route displays over any other nested navigators.

- Inside the app directory, add a (tabs) subdirectory. This special directory is used to group routes together and display them in a bottom tab bar.
- Create a (tabs)/_layout.tsx file inside the directory. It will be used to define the tab layout, which is separate from Root layout.
- Move the existing index.tsx and about.tsx files inside the (tabs) directory. The structure of app directory will look like this:
