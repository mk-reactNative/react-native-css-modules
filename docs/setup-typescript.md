_Have a look at the example app to see how you can use CSS modules and Typescript for both React Native and web browsers using the same code._

:point_right: [react-native-css-modules-with-typescript-example](https://github.com/kristerkari/react-native-css-modules-with-typescript-example)

## Setup CSS modules for React Native + Typescript

### Step 1: Setup React Native CSS modules

- [Setup React Native CSS modules with CSS support](setup-css.md)
- [Setup React Native CSS modules with PostCSS support](setup-postcss.md)
- [Setup React Native CSS modules with Sass support](setup-sass.md)
- [Setup React Native CSS modules with Less support](setup-less.md)
- [Setup React Native CSS modules with Stylus support](setup-stylus.md)

### Step 2: Install Typescript dependencies

_This step is not needed if you are using React Native v0.57 or newer._

#### For React Native v0.56 or older (Typescript is supported out-of-the-box by React Native 0.57+)

```
npm install react-native-typescript-transformer typescript --save-dev
```

or

```
yarn add react-native-typescript-transformer typescript --dev
```

### Step 3: Install custom `@types/react-native` package with `className` support

- https://github.com/kristerkari/react-native-types-for-css-modules#installation

### Step 4: Check project's `tsconfig.json`

Make sure that `jsx` property is set to `react-native` in Typescript's `compilerOptions`. This is important so that `className` property can be mapped to `style` property (won't work if `jsx` option is set to `react`).

`tsconfig.json`:

```json
"compilerOptions": {
  ...
  "jsx": "react-native"
}
```

### Step 5: Setup transformer to support CSS modules and Typescript

_This step is not needed if you are using React Native v0.57 or newer._

#### For React Native v0.56 or older (Typescript is supported by React Native 0.57+)

_This example only shows how to setup CSS support. Have a look at the setup documentation if you need PostCSS, Sass, Less or Stylus support._

In your project's root folder:

`rn-cli.config.js`

```js
module.exports = {
  getTransformModulePath() {
    return require.resolve("./transformer.js");
  },
  getSourceExts() {
    return ["ts", "tsx", "css"];
  }
};
```

`transformer.js`

```js
// For React Native version 0.56 or later
var upstreamTransformer = require("metro/src/reactNativeTransformer");

// For React Native version 0.52-0.55
// var upstreamTransformer = require("metro/src/transformer");

// For React Native version 0.47-0.51
// var upstreamTransformer = require("metro-bundler/src/transformer");

// For React Native version 0.46
// var upstreamTransformer = require("metro-bundler/build/transformer");

var cssTransformer = require("react-native-css-transformer");
var typescriptTransformer = require("react-native-typescript-transformer");

module.exports.transform = function({ src, filename, options }) {
  if (filename.endsWith(".css")) {
    return cssTransformer.transform({ src, filename, options });
  } else if (filename.endsWith(".ts") || filename.endsWith(".tsx")) {
    return typescriptTransformer.transform({ src, filename, options });
  }
  return upstreamTransformer.transform({ src, filename, options });
};
```
