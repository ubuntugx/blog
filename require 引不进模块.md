https://github.com/webpack/webpack/issues/4921

``` javascript
var path = 'path/to/file.js' 
require(path) // Error: Cannot find module "."
require('path/to/file.js' ) // All good
```
``` require ``` 内部不能写变量
如果写的话可以是模版字符串形式的

it helped me:
``` javascript
require(`${path_to_file}`);
```
and if you need more variable in your dynamic path try split require:
``` javascript
function getIcon(name) {
  const [category, filename] = name.split(":");

  if (filename) {
    return require(`one_directory/icons/${category}/${filename}.svg`);
  } else {
    return require(`two_directory/icons/${name}.svg`);
  }
}
```
