# loader list

## basic

* [`json`](https://github.com/webpack/json-loader) (default at `.json`): Loads file as JSON
* [`raw`](https://github.com/webpack/raw-loader): Loads raw content of a file (as utf-8)
* [`bundle`](https://github.com/webpack/bundle-loader): Wraps request in a `require.ensure` block
* [`val`](https://github.com/webpack/val-loader): Excutes code as module and consider exports as javascript code
* [`script`](https://github.com/webpack/script-loader): Executes a javascript file once in global context (like in script tag), requires are not parsed. Use this to include a library. ex. `require("script!./jquery.min.js")`. This is synchron, so the `$` variable is available after require.
* [`imports`](https://github.com/webpack/imports-loader): imports stuff to the module
* [`exports`](https://github.com/webpack/exports-loader): exports stuff from the module


## packaging

* [`file`](https://github.com/webpack/file-loader): Emits the file into the output folder and returns the (relative) url (`file/{ext}` for some extensions)
* [`url`](https://github.com/webpack/url-loader): The url loader works like the file loader, but can return a Data Url if the file is smaller than a limit.
* [`worker`](https://github.com/webpack/worker-loader): The worker loader creates a WebWorker for the provided file. The bundling of dependencies of the Worker is transparent.


## dialects

* [`coffee`](https://github.com/webpack/coffee-loader) (default at `.coffee`): Loads coffee-script like javascript
* [`json5`](https://github.com/webpack/json5-loader): Like json, but not so strict.


## templating

* [`jade`](https://github.com/webpack/jade-loader) (default at `.jade`): Loads jade template and returns a function


## styling

* [`css`](https://github.com/webpack/css-loader): Loads css file with resolved imports and returns css code
* [`less`](https://github.com/webpack/less-loader): Loads and compiles a less file and returns css code
* [`style`](https://github.com/webpack/style-loader): Adds result of javascript execution to DOM


## support

* [`mocha`](https://github.com/webpack/mocha-loader): do tests with mocha in browser or node.js
* [`coverjs`](https://github.com/webpack/coverjs-loader): PostLoader to code coverage with CoverJs
* [`jshint`](https://github.com/webpack/jshint-loader): PreLoader for linting code.