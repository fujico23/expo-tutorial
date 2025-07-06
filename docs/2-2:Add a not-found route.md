## Add a not-found route
When a route doesn't exist, we can use a +not-found route to display a fallback screen. This is useful when we want to display a custom screen when navigating to an invalid route on mobile instead of crashing the app or display a 404 error on web. Expo Router uses a special +not-found.tsx file to handle this case.

- Create a new file named +not-found.tsx inside the app directory to add the NotFoundScreen component.
