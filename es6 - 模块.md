### 解决的问题
在 es6 之前，js 中没有模块体系，不能把一个大程序拆分为一个个小文件。（写一个复杂的项目，单个js文件和其他文件没有显式的联系）
社区使用 CommonJS 和 AMD 作为模块的加载方案（require），但是这种只能在运行时确定依赖关系，不能单独加载模块中的单个对象，只能加载整个模块
```javascript
// CommonJS模块
let { stat, exists, readFile } = require('fs');

// 等同于
let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```
es6 中的 export 和 import 是在编译时加载，可以对模块进行静态分析，可以选择性加载需要的函数
```javascript
// ES6模块
import { stat, exists, readFile } from 'fs';
```
### import 和 export
模块由 import 和 export 两个关键字构成，一个模块就是一个独立的文件，模块 export 的可以是 class，变量，函数等。模块内部采取严格模式。
可以用 export default 导出整个模块，一个文件只能使用一次，不需要关注模块内函数的名字

```javascript
// 第一组
export default function crc32() { // 输出
  // ...
}
import crc32 from 'crc32'; // 输入

// 第二组
export function crc32() { // 输出
  // ...
};
import {crc32} from 'crc32'; // 输入
```
### 支持度
现在一些浏览器不支持这个特性，可以使用 babel 转义。
1. 安装 node
2. 安装 npm
3. npm 安装 babel
4. 安装 gulp / webpack 通过插件编译打包文件

参考：https://github.com/kaplanmaxe/imports-exports-es6
