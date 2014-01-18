For big web apps it's not efficient to put all code into a single file.
Especially if some blocks of code are only required under some circumstances.
webpack has a feature to split you codebase into so called chunks which are loaded on demand.
This feature is also called "code splitting".
Names for similar features in other bundlers: layers, rollups, fragments.

It's any opt-in feature. You can define split point in your code base. webpack cares for the dependencies, output files and runtime stuff.



## Defining a split point

AMD and CommonJs specify different methods to load code on demand. Both are supported and act as split points:

### `require.ensure` (CommonJs)

``` javascript
require.ensure(dependencies, callback)
```

The `require.ensure` method ensures that every dependency in `dependencies` can be synchronously required when calling the `callback`. `callback` is called with the `require` function as parameter.

Example:

``` javascript
require.ensure(["module-a", "module-b"], function(require) {
	var a = require("module-a");
	// ...
});
```

Note: `require.ensure` only loads the modules, it doesn't evaluate them.

### `require` (AMD)

The AMD spec defines a asynchronous `require` method with this definition:

``` javascript
require(dependencies, callback)
```

When called all `dependencies` are loaded and the `callback` is called with the exports of the loaded `dependencies`.

Example:

``` javascript
require(["module-a", "module-b"], function(a, b) {
	// ...
});
```

Note: AMD `require` loads and evaluate the modules. In webpack modules are evaluated left to right.

Note: It's allowed to omit the callback.



## Chunk content

All dependencies at a split point go into a new chunk. Dependencies are also recursivly added.

If you pass a function expression (or bound function expression) as callback to the split point, webpack automatically puts all dependencies required in this function expression into the chunk too.



## Chunk optimization

If two chunks contain the same modules, they are merged into one. This can cause chunks to have multiple parents.

If a module is available in all parents of a chunk, it's removed from that chunk.

If a chunk contains all modules of another chunk, this is stored. It fulfill multiple chunks.



## Chunk loading

Depending on the configuration option `target` a runtime logic for chunk loading is added to the bundle. I. e. for the `web` target chunks are loaded via jsonp. A chunk is only loaded once and parallel requests are merged into one. The runtime checks for loaded chunks whether they fulfill multiple chunks.



## Chunk types

### Entry chunk

A entry chunks contains the runtime plus a bunch of modules. If the chunk contains the module `0` the runtime executes it. If not, it waits for chunks that contains the module `0` and executes it (every time when there is a chunk with a module `0`).

### Normal chunk

A normal chunk contains no runtime. It only contains a bunch of modules. The structure depends on the chunk loading algorithm. I. e. for jsonp the modules are wrapped in a jsonp callback function. The chunk also contains a list of chunk id that it fulfills.

### Initial chunk (non-entry)

A initial chunk is a normal chunk. The only difference is that optimization threads it as more important because it counts toward the initial loading time (like entry chunks). That chunk type can occur in combination with the `CommonsChunkPlugin`.



## Optimization 

There are optimizing plugins that can merge chunks depending on specific criteria. See [[list of plugins]].



## Named chunks

The `require.ensure` function accepts an additional 3rd parameter. This must be a string. If two split point pass the same string they use the same chunk.



## `require.include`

``` javascript
require.include(request)
```

`require.include` is a webpack specific function that adds a module to the current chunk, but doesn't evaluate it (The statement is removed from the bundle).

Example:

``` javascript
require.ensure(["./file"], function(require) {
  require("./file2");
});

// is equals to

require.ensure([], function(require) {
  require.include("./file");
  require("./file2");
});
```

`require.include` can be useful if a module is in multiple child chunks. A `require.include` in the parent would include the module and the instances of the modules in the chunk chunks would disappear.



## Examples

For a running demo see the [example-app](http://webpack.github.io/example-app/). Check Network in DevTools.

* [Simple](https://github.com/webpack/webpack/tree/master/examples/code-splitting)
* [with bundle-loader](https://github.com/webpack/webpack/tree/master/examples/code-splitting-bundle-loader)
* [with context](https://github.com/webpack/webpack/tree/master/examples/code-splitted-require.context)
* [with amd and context](https://github.com/webpack/webpack/tree/master/examples/code-splitted-require.context-amd)
* [with deduplication](https://github.com/webpack/webpack/tree/master/examples/code-splitted-dedupe)
* [named-chunks](https://github.com/webpack/webpack/tree/master/examples/named-chucks)
* [multiple entry chunks](https://github.com/webpack/webpack/tree/master/examples/multiple-entry-points)
* [multiple commons chunks](https://github.com/webpack/webpack/tree/master/examples/multiple-commons-chunks)