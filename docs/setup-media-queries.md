## Setup CSS modules for React Native (with CSS Media Queries support)

The following modules are used to implement CSS modules with CSS media queries support for React Native:

* [react-native-css-transformer](https://github.com/kristerkari/react-native-css-transformer) - Transforms CSS to a React Native compatible style object and handles live reloading
* [babel-plugin-react-native-platform-specific-extensions](https://github.com/kristerkari/babel-plugin-react-native-platform-specific-extensions) - Transforms ES6 `import` statements to platform specific `require` statements if the platform specific files exist on disk.
* [babel-plugin-react-native-classname-to-dynamic-style](https://github.com/kristerkari/babel-plugin-react-native-classname-to-dynamic-style) - Transforms `className` property to `style` property and matches media queries at runtime with React Native.

### Step 1: Install depencies to run React Native

Make sure that you have `react-native-cli` installed and [XCode](https://developer.apple.com/xcode/)/[Android Studio](https://developer.android.com/studio/index.html) installed and working.

* Go to "Building Projects with Native Code" tab and follow the guide: https://facebook.github.io/react-native/docs/getting-started.html

### Step 2: Create a new React Native app and test that it works

e.g.

```sh
react-native init AwesomeProject
cd AwesomeProject
```

Start packager:

```sh
npm start
```

Run project on iOS simulator:

```sh
react-native run-ios
```

### Step 3: Install dependencies for React Native CSS modules

```sh
npm install babel-plugin-react-native-classname-to-dynamic-style babel-plugin-react-native-platform-specific-extensions react-native-css-transformer --save-dev
```

### Step 4: Setup your project's `.babelrc`

```json
{
  "presets": ["react-native"],
  "plugins": [
    "react-native-classname-to-dynamic-style",
    [
      "react-native-platform-specific-extensions",
      {
        "extensions": ["css"]
      }
    ]
  ]
}
```

### Step 5: Setup `rn-cli.config.js` in your project

Add this to your `rn-cli.config.js` (make one if you don't have one already):

```js
module.exports = {
  getTransformModulePath() {
    return require.resolve("react-native-css-transformer");
  },
  getSourceExts() {
    return ["css"];
  },
};
```

### Step 6: Add some CSS with media queries to your project and use it inside a React component

`styles.css`:

```css
.container {
  flex: 1;
  justify-content: center;
  align-items: center;
  background-color: #f5fcff;
}

.blue {
  color: blue;
  font-size: 30px;
}

@media (orientation: landscape) {
  .blue {
    font-size: 10px;
  }
}
```

Add style import and `BlueText` component to `App.js`:

```jsx
import React, { Component } from "react";
import { Text, View } from "react-native";
const styles = require("./styles.css");

const BlueText = () => {
  return <Text className={styles.blue}>Blue Text</Text>;
};

export default class App extends Component<{}> {
  render() {
    return (
      <View style={styles.container}>
        <BlueText />
      </View>
    );
  }
}
```

### Step 7: Restart packager and clear cache

Restart React Native packager and clear it's cache (important) to see the styles that you added.

```sh
npm start -- --reset-cache
```

If it throws an error for missing `node_modules/react-native/local-cli/cli.js`, just run `npm install` and then try again.