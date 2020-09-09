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
npm install -D babel-loader @bable/node @babel/cli @babel/core @babel/preset-env @babel/preset-react @babel/plugin-proposal-class-properties
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
