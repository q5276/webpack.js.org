[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![test][test]][test-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]

<div align="center">
  <a href="https://github.com/webpack/webpack">
    <img width="200" height="200"
      src="https://webpack.js.org/assets/icon-square-big.svg">
  </a>
  <h1>Copy Webpack Plugin</h1>
  <p>将单个文件或整个目录复制到构建目录.</p>
</div>

<h2 align="center">安装</h2>

```bash
npm i -D copy-webpack-plugin
```

<h2 align="center">用法</h2>

**webpack.config.js**
```js
const CopyWebpackPlugin = require('copy-webpack-plugin')

const config = {
  plugins: [
    new CopyWebpackPlugin([ ...patterns ], options)
  ]
}
```

> ℹ️ 如果你希望`webpack-dev-server`在开发过程中将文件写入输出目录，你可以关注 [`write-file-webpack-plugin`](https://github.com/gajus/write-file-webpack-plugin) 。

### `模式`

一个简单的模式是这样的

```js
{ from: 'source', to: 'dest' }
```

或者, in case of just a `from` with the default destination, you can also use a `{String}` as shorthand instead of an `{Object}`

或者，也可以简化不使用对象，只写一个字符串的作为 `from` 的默认值。

```js
'source'
```

|选项名|类型|默认值|描述|
|:--:|:--:|:-----:|:----------|
|[`from`](#from)|`{String\|Object}`|`undefined`|Globs接受 [minimatch选项](https://github.com/isaacs/minimatch)|
|[`fromArgs`](#fromArgs)|`{Object}`|`{ cwd: context }`| 除了下面的还可以参照 [`node-glob` 选项](https://github.com/isaacs/node-glob#options) |
|[`to`](#to)|`{String\|Object}`|`undefined`|如果 `from` 是文件或者路径则返回路径，如果 `from` 是glob选项则解析glob路径|
|[`toType`](#toType)|`{String}`|``|[toType 选项](#totype)|
|[`test`](#test)|`{RegExp}`|``|用于提取要在`to`模板中使用的元素的模式|
|[`force`](#force)|`{Boolean}`|`false`|覆盖已经在`compilation.assets`中的文件（通常由其他插件/加载器添加）|
|[`ignore`](#ignore)|`{Array}`|`[]`|要忽略这种模式的Globs|
|`flatten`|`{Boolean}`|`false`|删除所有目录引用，仅复制文件名。 ⚠️ 如果文件具有相同的名称，则结果是不确定的|
|[`transform`](#transform)|`{Function\|Promise}`|`(content, path) => content`|复制之前使用 Function 或 Promise 修改文件。|
|[`transformPath`](#transformPath)|`{Function\|Promise}`|`(targetPath, sourcePath) => path`|Function 或者 Promise 修改文件写入路径。|
|[`cache`](#cache)|`{Boolean\|Object}`|`false`|启用`transform`缓存。 您可以使用`{cache：{key：'my-cache-key'}}`来使缓存无效。|
|[`context`](#context)|`{String}`|`options.context \|\| compiler.options.context`|确定如何解释`from`的路径|

### `from`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    'relative/path/to/file.ext',
    '/absolute/path/to/file.ext',
    'relative/path/to/dir',
    '/absolute/path/to/dir',
    '**/*',
    { glob: '\*\*/\*', dot: true }
  ], options)
]
```

### `to`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    { from: '**/*', to: 'relative/path/to/dest/' },
    { from: '**/*', to: '/absolute/path/to/dest/' }
  ], options)
]
```

### `toType`

|名称|类型|默认值|描述|
|:--:|:--:|:-----:|:----------|
|**`'dir'`**|`{String}`|`undefined`|如果`from`是目录，`to`没有扩展名或以/'结尾|
|**`'file'`**|`{String}`|`undefined`|如果`to`有扩展名或`from`是文件|
|**`'template'`**|`{String}`|`undefined`|如果`to`包含 [模板模式](https://github.com/webpack-contrib/file-loader#placeholders)|

#### `'dir'`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: 'path/to/file.txt',
      to: 'directory/with/extension.ext',
      toType: 'dir'
    }
  ], options)
]
```

#### `'file'`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: 'path/to/file.txt',
      to: 'file/without/extension',
      toType: 'file'
    },
  ], options)
]
```

#### `'template'`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: 'src/',
      to: 'dest/[name].[hash].[ext]',
      toType: 'template'
    }
  ], options)
]
```

### `test`

定义`{RegExp}`以匹配文件路径的某些部分。 可以使用`[N]`占位符在name属性中重用这些捕获组。 请注意，`[0]`将替换为文件的整个路径，而`[1]`将包含`{RegExp}`的第一个捕获括号，依此类推......

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: '*/*',
      to: '[1]-[2].[hash].[ext]',
      test: /([^/]+)\/(.+)\.png$/
    }
  ], options)
]
```

### `force`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    { from: 'src/**/*', to: 'dest/', force: true }
  ], options)
]
```

### `ignore`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    { from: 'src/**/*', to: 'dest/', ignore: [ '*.js' ] }
  ], options)
]
```

### `flatten`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    { from: 'src/**/*', to: 'dest/', flatten: true }
  ], options)
]
```

### `transform`

#### `{Function}`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: 'src/*.png',
      to: 'dest/',
      transform (content, path) {
        return optimize(content)
      }
    }
  ], options)
]
```

#### `{Promise}`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: 'src/*.png',
      to: 'dest/',
      transform (content, path) {
        return Promise.resolve(optimize(content))
      }
  }
  ], options)
]
```

### `transformPath`

#### `{Function}`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: 'src/*.png',
      to: 'dest/',
      transformPath (targetPath, absolutePath) {
        return 'newPath';
      }
    }
  ], options)
]
```

#### `{Promise}`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: 'src/*.png',
      to: 'dest/',
      transformPath (targePath, absolutePath) {
        return Promise.resolve('newPath')
      }
  }
  ], options)
]
```


### `cache`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    {
      from: 'src/*.png',
      to: 'dest/',
      transform (content, path) {
        return optimize(content)
      },
      cache: true
    }
  ], options)
]
```

### `context`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin([
    { from: 'src/*.txt', to: 'dest/', context: 'app/' }
  ], options)
]
```

<h2 align="center">选项</h2>

|选项|类型|默认值|描述|
|:--:|:--:|:-----:|:----------|
|[`debug`](#debug)|`{String}`|**`'warning'`**|[Debug 选项](#debug)|
|[`ignore`](#ignore)|`{Array}`|`[]`|要忽略的数组 (应用到 `from`)|
|[`context`](#context)|`{String}`|`compiler.options.context`|确定如何解释`from`的路径, 所有模式都有效|
|[`copyUnmodified`](#copyUnmodified)|`{Boolean}`|`false`|使用watch或`webpack-dev-server`时，无论修改如何，都会复制文件。 无论此选项如何，所有文件都在第一次构建时复制。|

### `debug`

|名称|类型|默认值|描述|
|:--:|:--:|:-----:|:----------|
|**`'info'`**|`{String\|Boolean}`|`false`|文件位置和读取信息|
|**`'debug'`**|`{String}`|`false`|非常详细的调试信息|
|**`'warning'`**|`{String}`|`true`|只是警告信息|

#### `'info'`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin(
    [ ...patterns ],
    { debug: 'info' }
  )
]
```

#### `'debug'`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin(
    [ ...patterns ],
    { debug: 'debug' }
  )
]
```

#### `'warning' (default)`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin(
    [ ...patterns ],
    { debug: true }
  )
]
```

### `ignore`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin(
    [ ...patterns ],
    { ignore: [ '*.js', '*.css' ] }
  )
]
```

### `context`

**webpack.config.js**
```js
[
  new CopyWebpackPlugin(
    [ ...patterns ],
    { context: '/app' }
  )
]
```

### `copyUnmodified`

> ℹ️ 在使用`webpack --watch` 或者`webpack-dev-server` 构建的时候，默认只是复制**修改的** 文件。设置为 `true`的时候就复制所有的文件。

**webpack.config.js**
```js
[
  new CopyWebpackPlugin(
    [ ...patterns ],
    { copyUnmodified: true }
  )
]
```

<h2 align="center">维护者</h2>

<table>
  <tbody>
    <tr>
      <td align="center">
        <a href="https://github.com/bebraw">
          <img width="150" height="150" src="https://github.com/bebraw.png?v=3&s=150">
          </br>
          Juho Vepsäläinen
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/d3viant0ne">
          <img width="150" height="150" src="https://github.com/d3viant0ne.png?v=3&s=150">
          </br>
          Joshua Wiens
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/michael-ciniawsky">
          <img width="150" height="150" src="https://github.com/michael-ciniawsky.png?v=3&s=150">
          </br>
          Michael Ciniawsky
        </a>
      </td>
      <td align="center">
        <a href="https://github.com/evilebottnawi">
          <img width="150" height="150" src="https://github.com/evilebottnawi.png?v=3&s=150">
          </br>
          Alexander Krasnoyarov
        </a>
      </td>
    </tr>
  <tbody>
</table>


[npm]: https://img.shields.io/npm/v/copy-webpack-plugin.svg
[npm-url]: https://npmjs.com/package/copy-webpack-plugin

[node]: https://img.shields.io/node/v/copy-webpack-plugin.svg
[node-url]: https://nodejs.org

[deps]: https://david-dm.org/webpack-contrib/copy-webpack-plugin.svg
[deps-url]: https://david-dm.org/webpack-contrib/copy-webpack-plugin

[test]: https://secure.travis-ci.org/webpack-contrib/copy-webpack-plugin.svg
[test-url]: http://travis-ci.org/webpack-contrib/copy-webpack-plugin

[cover]: https://codecov.io/gh/webpack-contrib/copy-webpack-plugin/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/copy-webpack-plugin

[chat]: https://img.shields.io/badge/gitter-webpack%2Fwebpack-brightgreen.svg
[chat-url]: https://gitter.im/webpack/webpack
