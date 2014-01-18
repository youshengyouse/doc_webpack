webpack is feeded with a configuration object. Depending on your usage of webpack there are two ways to pass this configuration object:

### CLI

If you use the [[CLI]] it will read a file `webpack.config.js` (or the file passed by the `--config` option). This file should export the configuration object:

``` javascript
module.exports = {
	// configuration
};
```

### node.js API

If you use the [[node.js API]] you need to pass the configuration object as parameter:

``` javascript
webpack({
	// configuration
}, callback);
```





# configuration object content

Very simple configuration object example:

``` javascript
{
	context: __dirname + "/app",
	entry: "./entry",
	output: {
		path: __dirname + "/dist",
		filename: "bundle.js"
	}
}
```



## `context`

The base directory for resolving the `entry` option. If `output.pathinfo` is set, the included pathinfo is shortened to this directory.

> Default: `process.cwd()`



## `entry`

The entry point for the bundle.

If you pass a string: The string is resolve to a module which is loaded upon startup.

If you pass an array: All modules are loaded upon startup. The last one is exported.

``` javascript
entry: ["./entry1", "./entry2"]
```

If you pass an object: Multiple entry bundles are created. The key is the chunk name. The value can be a string or an array.

``` javascript
{
	entry: {
		page1: "./page1",
		page2: ["./entry1", "./entry2"]
	},
	output: {
		// Make sure to use [name] or [id] in output.filename
		//  when using multiple entry points
		filename: "[name].bundle.js",
		chunkFilename: "[id].bundle.js"
	}
}
```



## `output`

Options affecting the output.

If you use any hashing (`[hash]` or `[chunkhash]`) make sure to have a consistent ordering of modules. Use the `OccurenceOrderPlugin` or `recordsPath`.

### `output.path`

The output directory as absolute path.

`[hash]` is replaced by the hash of the compilation.

### `output.filename`

The filename of the entry chunk as relative path inside the `output.path` directory.

`[name]` is replaced by the name of the chunk.

`[hash]` is replaced by the hash of the compilation.

### `output.chunkFilename`

The filename of non-entry chunks as relative path inside the `output.path` directory.

`[id]` is replaced by the id of the chunk.

`[hash]` is replaced by the hash of the compilation.

`[chunkhash]` is replaced by the hash of the chunk.

### `output.namedChunkFilename`

The filename of named chunks as relative path inside the `output.path` directory.

`[name]` is replaced by the name of the chunk.

`[hash]` is replaced by the hash of the compilation.

### `output.sourceMapFilename`

The filename of the SourceMaps for the Javascript files. They are inside the `output.path` directory.

`[file]` is replaced by the filename of the Javascript file.

`[id]` is replaced by the id of the chunk.

`[hash]` is replaced by the hash of the compilation.

> Default: `"[file].map"`

### `output.hotUpdateChunkFilename`

The filename of the Hot Update Chunks. They are inside the `output.path` directory.

`[id]` is replaced by the id of the chunk.

`[hash]` is replaced by the hash of the compilation. (The last hash stored in the records)

> Default: `"[id].[hash].hot-update.js"`

### `output.hotUpdateChunkFilename`

The filename of the Hot Update Main File. It is inside the `output.path` directory.

`[hash]` is replaced by the hash of the compilation. (The last hash stored in the records)

> Default: `"[hash].hot-update.json"`

### `output.publicPath`

The `output.path` from the view of the javascript.

``` javascript
// Example
output: {
  path: "/home/proj/public/assets",
  publicPath: "/assets"
}
// Example CDN
output: {
  path: "/home/proj/cdn/assets/[hash]",
  publicPath: "http://cdn.example.com/assets/[hash]/"
}
```

### `output.jsonpFunction`

The JSONP function used by webpack for asnyc loading of chunks.

A shorter function may reduce the filesize a bit. Use different identifier, when having multiple webpack instances on a single page.

> Default: `"webpackJsonp"`

### `output.hotUpdateFunction`

The JSONP function used by webpack for asnyc loading of hot update chunks.

> Default: `"webpackHotUpdate"`

### `output.pathInfo`

Include comments with information about the modules.

`require(/* ./test */23)`

Do not use this in production.

> Default: `false`

### `output.library`

If set, export the bundle as library. `output.library` is the name.

Use this, if you are writing a library and want to publish it as single file.

### `output.libraryTarget`

Kind of exporting as library.

`var` - Export by setting a variable: `var Library = xxx` (default)

`this` - Export by setting a property of `this`: `this["Library"] = xxx`

`commonjs` - Export by setting a property of `exports`: `exports["Library"] = xxx`

`commonjs2` - Export by setting `module.exports`: `module.exports = xxx`

`umd` - Export to AMD, CommonJS2 or as property in root

> Default: `var`

If `output.library` is not set, but `output.libraryTarget` is set to a value other that `var`, every property of the exported object is copied.



## `module`

Options affecting the normal modules (`NormalModuleFactory`)

### `module.loaders`

A array of automatically applied loaders.

Each item can have these properties:

* `test`: A condition that must be met
* `exclude`: A condition that must not be met
* `include`: A condition that must be met
* `loader`: A string of "!" separated loaders
* `loaders`: A array of loaders as string

A condition can be a RegExp, or a array of RegExps combined with "and".

See more: [[loaders]]

### `module.preLoaders`, `module.postLoaders`

Syntax like `module.loaders`.

A array of applied pre and post loaders.

### `module.noParse`

A RegExp or an array of RegExps. Don't parse files matching.

This can boost performance when ignoring big libraries.

The files are expected to have no call to `require`, `define` or similar. They are allowed to use `exports` and `module.exports`.


## `resolve`

Options affecting the resolving of modules.

### `resolve.alias`

Replace modules by other modules or paths.

Expected is a object with keys being module names. The value is the new path. It's similar to a replace but a bit more clever. If the the key ends with `$` only the exact match (without the `$`) will be replaced.

If the value is a relative path it will be relative to the file containing the require.

Examples: Calling a require from `/abc/entry.js` with different alias settings.

| `alias:` | `require("xyz")` | `require("xyz/file.js")` |
|---|---|---|
| `{}` | `/abc/node_modules/xyz/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "/absolute/path/to/file.js" }` | `/absolute/path/to/file.js` | error |
| `{ xyz$: "/absolute/path/to/file.js" }` | `/absolute/path/to/file.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "./dir/file.js" }` | `/abc/dir/file.js` | error |
| `{ xyz$: "./dir/file.js" }` | `/abc/dir/file.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "/some/dir" }` | `/some/dir/index.js` | `/some/dir/file.js` |
| `{ xyz$: "/some/dir" }` | `/some/dir/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "./dir" }` | `/abc/dir/index.js` | `/abc/dir/file.js` |
| `{ xyz: "modu" }` | `/abc/node_modules/modu/index.js` | `/abc/node_modules/modu/file.js` |
| `{ xyz$: "modu" }` | `/abc/node_modules/modu/index.js` | `/abc/node_modules/xyz/file.js` |
| `{ xyz: "modu/some/file.js" }` | `/abc/node_modules/modu/some/file.js` | error |
| `{ xyz: "modu/dir" }` | `/abc/node_modules/modu/dir/index.js` | `/abc/node_modules/dir/file.js` |
| `{ xyz: "xyz/dir" }` | `/abc/node_modules/xyz/dir/index.js` | `/abc/node_modules/xyz/dir/file.js` |
| `{ xyz$: "xyz/dir" }` | `/abc/node_modules/xyz/dir/index.js` | `/abc/node_modules/xyz/file.js` |

`index.js` may resolve to another file if defined in the `package.json`.

`/abc/node_modules` may resolve in `/node_modules` too.

### `resolve.root`

The directory that contains your modules. May also be an array of directories. This setting should be used to add individual directories to the search path.

### `resolve.modulesDirectories`

An array of directory names to be resolved to the current directory as well as its ancestors, and searched for modules. This functions similarly to how node finds "node_modules" directories. For example, if the value is `["mydir"]`, webpack will look in "./mydir", "../mydir", "../../mydir", etc.

> Default: `["web_modules", "node_modules"]`

### `resolve.fallback`

A directory (or array of directories), in which webpack should look for modules that weren't fount in `resolve.root` or `resolve.modulesDirectories`.

### `resolve.extensions`

An array of extensions that should be used to resolve modules. For example, in order to discover CoffeeScript files, your array should contain the string `"*.coffee"`.

> Default: `["", ".webpack.js", ".web.js", ".js"]`

**IMPORTANT**: Setting this option will override the default, meaning that webpack will no longer try to resolve modules using the default extensions. If you want modules that were required with their extension (e.g. `require('./somefile.ext')`) to be properly resolved, you **must** include an empty string in your array. Similarly, if you want modules that were required without extensions (e.g. `require('underscore')`) to be resolved to files with ".js" extensions, you **must** include `".js"` in your array.

### `resolve.packageMains`

Check these fields in the `package.json` for suitable files.

> Default: `["webpack", "browser", "web", "main"]`

### `resolve.unsafeCache`

Enable aggressive but unsafe caching for the resolving of a part of your files. Changes to cached paths may cause failure (in rar cases). An array of RegExps, only a RegExp or `true` (all files) is expected. If the resolved path matches, it'll be cached.

> Default: `[]`



## `resolveLoader`

Like `resolve` but for loaders.

``` javascript
// Default:
{
  modulesDirectories: ["web_loaders", "web_modules", "node_loaders", "node_modules"],
  extensions: ["", ".webpack-loader.js", ".web-loader.js", ".loader.js", ".js"],
  packageMains: ["webpackLoader", "webLoader", "loader", "main"]
}
```

### `resolveLoader.moduleTemplates`

That's a `resolveLoader` only property.

It describes alternatives for the module name that are tried.

> Default: `["*-webpack-loader", "*-web-loader", "*-loader", "*"]`



## `target`

* `"web"` Compile for usage in a browser-like environment (default)
* `"webworker"` Compile as WebWorker
* `"node"` Compile for usage in a node.js-like environment (use `require` to load chunks)
* `"async-node"` Compile for usage in a node.js-like environment (use `fs` and `vm` to load chunks asnyc)



## `bail`

Report the first error at a hard error instead of tolerating it.



## `profile`

Capture timing information for each module.

> Hint: Use the [analyse tool](http://webpack.github.io/analyse) to visualize it.



## `cache`

Cache generated modules and chunks to improve performance for multiple incremental builds.



## `watch`

Enter watch mode, which rebuilds on file change.

Only use it with the node.js javascript api `webpack(options, fn)`.



## `watchDelay`

Delay the rebuilt after the first change. Value is a time in ms.

> Default: 200



## `debug`

Switch loaders to debug mode.



## `devtool`

Choose a developer tool to enhance debugging.

`eval` - Each module is executed with `eval` and `//@ sourceURL`.

`source-map` - A SourceMap is emitted. See also `output.sourceMapFilename`.

`inline-source-map` - A SourceMap is added as DataUrl to the Javascript file.

Prefixing `@`, `#` or `#@` will enforce a pragma style.



## `node`

Include polyfills or mocks for various node stuff:

* `console`: `true` or `false`
* `global`: `true` or `false`
* `process`: `true`, `"mock"` or `false`
* `buffer`: `true`, `"mock"` or `false`
* `__filename`: `true` (real filename), `"mock"` (`"/index.js"`) or `false`
* `__dirname`: `true` (real dirname), `"mock"` (`"/"`) or `false`
* `<node buildin>`: `true`, `"mock"` or `false`

``` javascript
// Default:
{
	console: false,
	process: true,
	global: true,
	buffer: true,
	__filename: "mock",
	__dirname: "mock"
}
```


## `amd`

Set the value of `require.amd` and `define.amd`.

Example: `amd: { jQuery: true }` (for old 1.x AMD versions of jquery)



## `loader`

Custom values available in the loader context.



## `recordsPath`, `recordsInputPath`, `recordsOutputPath`

Store/Load compiler state from/to a json file. This will result in persistent ids of modules and chunks.

An absolute path is excepted. `recordsPath` is used for `recordsInputPath` and `recordsOutputPath` if they left undefined.

This is required, when using Hot Code Replacement between multiple calls to the compiler.



### `plugins`

Add additional plugins to the compiler.