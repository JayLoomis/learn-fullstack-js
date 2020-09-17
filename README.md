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

## Coding

There's lots to talk about in regards to the code. Let's take it piece by piece.

### Importing modules
Imports are cached, so when you import the same file in different places, each
susequent invoke just uses the cached version instead of trying to load the
source again.

Each module has its own scope. So creating a variable in a module and importing
that module doesn't automatically give you scope into the imported module. You
must export things to make them visible when you import the module.

The standard way to import modules in node is to assign them to a local variable
of the same name as the module:

```javascript
import config from './config';
```

The basic `import` command shown above assigns the local variable to the default
object exported by the module. Here's an example of a module with a simple deault
object exported:

```javascript
const env = process.env;

export default {
  // The OR makes 8080 the default, if there isn't a defined port in the environment.
  port: env.PORT || 8080
};
```

You can create other exports in addition to (or instead of) the default object:

```javascript
const env = process.env;

export const nodeEnv = env.NODE_ENV || 'development';

export default {
  port: env.PORT || 8080
};
```

But if you do, you'll need to import them explicitly:

```javascript
// The const name is in brackets because we have to "destructure" exports
//  that aren't in the default. Any others are added, comma-delimited, to
//  the list in the brackets.
import config, { nodeEnv } from './config';
```

A stray function export works just the same way. It gets exported:

```javascript
// This gets inserted in the pervious example before the defualt export.
export const logStars = function(message) {
  console.info('**********');
  console.info(message);
  console.info('**********');
};
```

Then you can import it and use it as a normal function:

```javascript
import config, {nodeEnv, logStars} from './config';

logStars(config);
logStars(nodeEnv);
```

### Node HTTP and Express

Express is a wrapper around the core HTTP modules in node. Node's HTTP modules
can be used for both clients and servers.

Here's some code that will request a get of Lynda.com and convert the response to 
string to output to the console.
```javascript
imprt https from 'https';

https.get('https://www.lynda.com', res => {
  console.log('Response status code: ', res.statusCode);
  
  res.on('data', chunk => {
    console.log(chunk.toString());
  });
});
```

Here's some basic server code:
```javascript
import http from 'http';

// createServer starts a server for us.
const server = http.createServer();

// Begin listening to the incoming port.
server.listen(8080);

// Configure the request response for the server.
server.on('request', (req, res) => {
  // The response object is a writable stream.
  res.write('Hello HTTP!\n');
  
  // Send another message 3 seconds after the first.
  setTimeout(() => {
    res.write('I can stream!\n');
    // Be sure to end the response when done.
    res.end();
  }, 3000);
});
```

The core HTTP module is very flexible and easy to use, so why use Express?
When the anticipated requests and responses get complicated, your code using
the core modules will quickly get clogged with boilerplate infrastructure code.
Express manages that complexity for you.


