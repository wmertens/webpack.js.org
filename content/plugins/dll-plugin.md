---
title: DllPlugin
contributors:
  - wmertens
---

Bundles the `entry`s into Dynamic Link Libraries (DLL), which can be referenced from other builds, instead of compiling.
These DLLs then need to be loaded separately before the builds using them, under the name set in `output.libary`.

### building DLLs
``` javascript
new webpack.DllPlugin({path, name, context})
```
* `path`: absolute path to the manifest json file (output)
* `name`: name of the built DLL file (keep consistent with `output.library`)
* `context` (optional): context of requests in the manifest file, defaults to the webpack `context`

### using DLLs
``` javascript
new webpack.DllReferencePlugin({manifest, scope, context, name, sourceType, content})
```
* 

No entry module is added, loading the DLL has no effect other than providing the code to the referring bundles.
The bundled modules are only executed when they are first imported.

To enable the referencing, `DllPlugin` also generates a manifest file in JSON format, that lists the bundled modules.
`DllReferencePlugin` then reads that file to know which modules do not have to be built.

T> Note that if you use these bundles with hot reloading, you need to also build the DLL with the `HotModuleReplacementPlugin` enabled. Otherwise, modules you include won't support hot reload, which means you can't bundle the hot entry points.



### to import still

Can be used in two different modes:

Scoped mode

The content of the dll is accessible under a module prefix. i. e. with scope = "xyz" a file abc in the dll can be access via require("xyz/abc").

Mapped mode

The content of the dll is mapped to the current directory. If a required file matches a file in the dll (after resolving), then the file from the dll is used instead. Note: because this happens after resolving every file in the dll must be also available for the dll user at the same path. i. e. if the dll contains jquery and the file abc, require("jquery") and require("./abc") will be used from the dll.

new DllReferencePlugin({
  context: __dirname,
  scope: "xyz",
  manifest: require("./manifest.json"),
  name: "./my-dll.js",
  sourceType: "commonsjs2",
  content: { ... }
})
context: (absolute path) context of requests in the manifest (or content property)
scope (optional): prefix which is used for accessing the content of the dll
manifest (object): an object containing content and name
name (optional): the name where the dll is exposed (defaults to manifest.name) (see also externals)
sourceType (optional): the type how the dll is exposed (defaults to "var") (see also externals)
content (optional): the mappings from request to module id (defaults to manifest.content)
Usage example

Using dlls via <script> tags

Dll bundle: output.library = "[name]_[hash]" output.libraryTarget = "var" DllPlugin.name = "[name]_[hash]"

Dll consumer: DllReferencePlugin.sourceType = "var"

Using dlls via node.js

Dll bundle: output.libraryTarget = "commonjs2"

Dll consumer: DllReferencePlugin.sourceType = "commonjs2" DllReferencePlugin.name = "./path/to/dll.js"

