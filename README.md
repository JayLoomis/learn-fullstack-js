# learn-fullstack-js
Personal repo for Lynda course. Notes in this file.

## Creating a package.json file
The first step in setting up any node project is to create a package.json file.
The easiest way to do this is to run the following command from the root of your
Git repo:

```bash
npm init
```
Answer the prompts to fill out the package file. NPM tries to intuit the right answers,
but you'll need to figure some of them out yourself.

### Dependencies
There are two kinds of dependencies that you'll need: main dependencies that are used
for production code and dev dependencies that are only used in a local development
environment.

We're using a Node web server, so we need Express installed:

```bash
npm install express
```
When you run the command from your Git repo root, NPM will add the dependency to your
package file automatically.

For our database, we're using MongoDB. To connect to it from Node, we need a driver:

```bash
npm install mongodb
```

For this simple site, we don't need any other backend dependencies. But for the front end,
we're going to need react:

```bash
npm install react react-dom
```

We'll use PropTypes, which used to be part of React:

```bash
npm install prop-types
```

Those are the core main dependencies. There will be more later, but for now, we'll start 
installing our dev dependencies.

Webpack and its command-line tools:

```bash
npm install -D webpack webpack-cli
```

Notice that we use the `-D` flag, which designates this as a dev dependency.
Webpack is a set of tools that help us bundle all of our js modules into js
files for the browser.

We'll also need Babel:

```bash
npm install -D babel-loader @babel/cli @babel/core @babel/preset-env @babel/preset-react @babel/plugin-proposal-class-properties
```

Babel transforms our bundled modules so that react can deal with it. 

We'll use nodemon to auto-restart node when we make changes to processes:

```bash
npm install -D nodemon
```

We'll use ESLint:

```bash
npm install -D eslint babel-eslint eslint-plugin-react
```

### Project structure

With the dependencies installed, now it's time to add some structure for the project's files.

In the root of the project directory, add a `src` directory, which is where all the modular
front-end code will go.

Then add a `public` directory to host static assets, like HTML, JavaScript, and CSS that will
get loaded onto clients.

Finally, add a top-level directory for back end server, called `api`.

It's a good idea to create empty files in each directory so that Git will capture the directory
structure (it ignores empty directories). Under `src` and `api`, create files named `index.js`. 
Under `public`, create a file named `index.html`. Use `index` to designate the starting point in
any directory.

In PowerShell, the above setup looks like this:

```
mkdir src
mkdir public
mkdir api

fsutil file createnew .\src\index.js
fsutil file createnew .\public\index.html
fsutil file createnew .\api\index.js
```

With directories in place, it's time to define some scripts in the package file.
For now, there's no test script. That's a bad idea, generally.

```json
  "scripts": {
    "start": "nodemon --exec babel-node server.js --ignore public/",
    "dev": "webpack -wd"
  },
```

Three of our dependencies, webpack, ESLint, and Babel, need configuration files,
which we'll put in the root directory for the project. Here are the files:

For webpack, create `webpack.config.js` and put this code in the file:

```js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve('public'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
        },
      },
    ],
  },
};
```

For Babel, create `babel.config.js` and put this code in the file:

```js
module.exports = {
  presets: ['@babel/react', '@babel/env'],
  plugins: ['@babel/plugin-proposal-class-properties'],
};
```

For ESLint, create `.eslintrc.js` and put this code in the file:

```js
module.exports = {
  parser: 'babel-eslint',
  env: {
    browser: true,
    commonjs: true,
    es6: true,
    node: true,
    jest: true,
  },
  extends: ['eslint:recommended', 'plugin:react/recommended'],
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    sourceType: 'module',
  },
  plugins: ['react'],
  rules: {
    indent: ['error', 2, {SwitchCase: 1}],
    'linebreak-style': ['error', 'unix'],
    quotes: ['error', 'single'],
    semi: ['error', 'always'],
    'no-console': ['warn', {allow: ['clear', 'info', 'error', 'dir', 'trace']}],
  },
};
```





