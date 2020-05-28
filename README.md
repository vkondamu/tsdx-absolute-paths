# Absolute Paths with TSDX

## What is this?

A way to make absolute paths work with the prefix `'@/'` in TSDX.

This is a sample codebase that uses the absolute paths configuration. It supports using absolute paths while running `yarn start`, `yarn build`, and `yarn test`.

These changes were implemented on **Rollup** on TSDX's React template. This repository is also a proof of concept to fix [TSDX Issue #91](https://github.com/jaredpalmer/tsdx/issues/91).

## What does it do?

It uses [Rollup's alias plugin](https://github.com/rollup/plugins/tree/master/packages/alias) to change any reference of `@/` to `src/` to use it as an absolute path.

The same is accomplished by using `moduleNameMapper` for tests using jest.

## How do I use Absolute Paths?

Just use `@/<relative_path_to_src>` in the import statement

For example, consider this file structure:
```
src
│   index.tsx
|   continent.tsx

└───shallowfolder
│   │
│   └───folder
│       │   
|       └─ deepfolder
|          |   country.tsx
```

Assume continent.tsx contains a function called `getContinent()`

Now, if you wanted to call `getContinent()` in country.tsx
Instead of:

```ts
  import { getContinent } from "../../continent"
```

you can use

```ts
  import { getContinent } from "@/continent"
```

## How to Setup Absolute Paths on your TSDX Configuration

### 1. Add Rollup Configuration

1. Install @rollup/plugin-alias

```bash
  yarn add @rollup/plugin-alias
```

2. Copy the configuration in [`tsdx.config.js`](./tsdx.config.js)

### 2. Add path to `tsconfig.json`

Modify the following configuration of paths

```json
  "paths": {
    "@/*": ["src/*"],
    "*": ["node_modules/*"]
},
```

Running `yarn build` or `yarn start` will now work with absolute paths

### 3. Add Jest Configuration

1. Copy the configuration in [`jest.config.js`](./jest.config.js)

2. Move the "test" directory under "src/"


Now, you can use absolute paths in jest, and running `yarn test` should work

-------------------------- 

## Usage with the React-with-Storybook template 


Following steps 1. and 2. above, you can run `yarn start` or `yarn build`

To run `yarn storybook`, add the following configuration to  .storybook/main.js

```js
const path = require('path');
module.exports = {
  // ...
  webpackFinal: async (config) => {
    config.module.rules.push({
      // ...
      resolve: {
        alias: {
          // Map to src/ from .storybook/
          "@/": path.dirname(__dirname) + "/src/",
        },
      },
    });
    // ...
    return config;
  }
}
```

Note that absolute paths in Storybook do not work with jest tests, as the jest tests depend on files outside of 'src/'

