# api documentation for  [rc (v1.2.0)](https://github.com/dominictarr/rc#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-rc.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-rc) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-rc.svg)](https://travis-ci.org/npmdoc/node-npmdoc-rc)
#### hardwired configuration loader

[![NPM](https://nodei.co/npm/rc.png?downloads=true)](https://www.npmjs.com/package/rc)

[![apidoc](https://npmdoc.github.io/node-npmdoc-rc/build/screen-capture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-rc_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-rc/build..beta..travis-ci.org/apidoc.html)

![package-listing](https://npmdoc.github.io/node-npmdoc-rc/build/screen-capture.npmPackageListing.svg)



# package.json

```json

{
    "author": {
        "name": "Dominic Tarr",
        "email": "dominic.tarr@gmail.com",
        "url": "dominictarr.com"
    },
    "bin": {
        "rc": "./index.js"
    },
    "browserify": "browser.js",
    "bugs": {
        "url": "https://github.com/dominictarr/rc/issues"
    },
    "dependencies": {
        "deep-extend": "~0.4.0",
        "ini": "~1.3.0",
        "minimist": "^1.2.0",
        "strip-json-comments": "~2.0.1"
    },
    "description": "hardwired configuration loader",
    "devDependencies": {},
    "directories": {},
    "dist": {
        "shasum": "c7de973b7b46297c041366b2fd3d2363b1697c66",
        "tarball": "https://registry.npmjs.org/rc/-/rc-1.2.0.tgz"
    },
    "gitHead": "71d7d1524f7421c5357daac289961b49f039d19d",
    "homepage": "https://github.com/dominictarr/rc#readme",
    "keywords": [
        "config",
        "rc",
        "unix",
        "defaults"
    ],
    "license": "(BSD-2-Clause OR MIT OR Apache-2.0)",
    "main": "index.js",
    "maintainers": [
        {
            "name": "dominictarr",
            "email": "dominic.tarr@gmail.com"
        }
    ],
    "name": "rc",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/dominictarr/rc.git"
    },
    "scripts": {
        "test": "set -e; node test/test.js; node test/ini.js; node test/nested-env-vars.js"
    },
    "version": "1.2.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module rc](#apidoc.module.rc)
1.  object <span class="apidocSignatureSpan">rc.</span>utils

#### [module rc.utils](#apidoc.module.rc.utils)
1.  [function <span class="apidocSignatureSpan">rc.utils.</span>env (prefix, env)](#apidoc.element.rc.utils.env)
1.  [function <span class="apidocSignatureSpan">rc.utils.</span>file ()](#apidoc.element.rc.utils.file)
1.  [function <span class="apidocSignatureSpan">rc.utils.</span>find ()](#apidoc.element.rc.utils.find)
1.  [function <span class="apidocSignatureSpan">rc.utils.</span>json ()](#apidoc.element.rc.utils.json)
1.  [function <span class="apidocSignatureSpan">rc.utils.</span>parse (content)](#apidoc.element.rc.utils.parse)



# <a name="apidoc.module.rc"></a>[module rc](#apidoc.module.rc)



# <a name="apidoc.module.rc.utils"></a>[module rc.utils](#apidoc.module.rc.utils)

#### <a name="apidoc.element.rc.utils.env"></a>[function <span class="apidocSignatureSpan">rc.utils.</span>env (prefix, env)](#apidoc.element.rc.utils.env)
- description and source-code
```javascript
env = function (prefix, env) {
  env = env || process.env
  var obj = {}
  var l = prefix.length
  for(var k in env) {
    if(k.toLowerCase().indexOf(prefix) === 0) {

      var keypath = k.substring(l).split('__')

      // Trim empty strings from keypath array
      var _emptyStringIndex
      while ((_emptyStringIndex=keypath.indexOf('')) > -1) {
        keypath.splice(_emptyStringIndex, 1)
      }

      var cursor = obj
      keypath.forEach(function _buildSubObj(_subkey,i){

        // (check for _subkey first so we ignore empty strings)
        // (check for cursor to avoid assignment to primitive objects)
        if (!_subkey || typeof cursor !== 'object')
          return

        // If this is the last key, just stuff the value in there
        // Assigns actual value from env variable to final key
        // (unless it's just an empty string- in that case use the last valid key)
        if (i === keypath.length-1)
          cursor[_subkey] = env[k]


        // Build sub-object if nothing already exists at the keypath
        if (cursor[_subkey] === undefined)
          cursor[_subkey] = {}

        // Increment cursor used to track the object at the current depth
        cursor = cursor[_subkey]

      })

    }

  }

  return obj
}
```
- example usage
```shell
...
defaults = (
    'string' === typeof defaults
  ? cc.json(defaults) : defaults
  ) || {}

parse = parse || cc.parse

var env = cc.env(name + '_')

var configs = [defaults]
var configFiles = []
function addConfigFile (file) {
  if (configFiles.indexOf(file) >= 0) return
  var fileConfig = cc.file(file)
  if (fileConfig) {
...
```

#### <a name="apidoc.element.rc.utils.file"></a>[function <span class="apidocSignatureSpan">rc.utils.</span>file ()](#apidoc.element.rc.utils.file)
- description and source-code
```javascript
file = function () {
  var args = [].slice.call(arguments).filter(function (arg) { return arg != null })

  //path.join breaks if it's a not a string, so just skip this.
  for(var i in args)
    if('string' !== typeof args[i])
      return

  var file = path.join.apply(null, args)
  var content
  try {
    return fs.readFileSync(file,'utf-8')
  } catch (err) {
    return
  }
}
```
- example usage
```shell
...

var env = cc.env(name + '_')

var configs = [defaults]
var configFiles = []
function addConfigFile (file) {
  if (configFiles.indexOf(file) >= 0) return
  var fileConfig = cc.file(file)
  if (fileConfig) {
    configs.push(parse(fileConfig))
    configFiles.push(file)
  }
}

// which files do we look at?
...
```

#### <a name="apidoc.element.rc.utils.find"></a>[function <span class="apidocSignatureSpan">rc.utils.</span>find ()](#apidoc.element.rc.utils.find)
- description and source-code
```javascript
find = function () {
  var rel = path.join.apply(null, [].slice.call(arguments))

  function find(start, rel) {
    var file = path.join(start, rel)
    try {
      fs.statSync(file)
      return file
    } catch (err) {
      if(path.dirname(start) !== start) // root
        return find(path.dirname(start), rel)
    }
  }
  return find(process.cwd(), rel)
}
```
- example usage
```shell
...
 [join(etc, name, 'config'),
  join(etc, name + 'rc')].forEach(addConfigFile)
if (home)
 [join(home, '.config', name, 'config'),
  join(home, '.config', name),
  join(home, '.' + name, 'config'),
  join(home, '.' + name + 'rc')].forEach(addConfigFile)
addConfigFile(cc.find('.'+name+'rc'))
if (env.config) addConfigFile(env.config)
if (argv.config) addConfigFile(argv.config)

return deepExtend.apply(null, configs.concat([
  env,
  argv,
  configFiles.length ? {configs: configFiles, config: configFiles[configFiles.length - 1]} : undefined,
...
```

#### <a name="apidoc.element.rc.utils.json"></a>[function <span class="apidocSignatureSpan">rc.utils.</span>json ()](#apidoc.element.rc.utils.json)
- description and source-code
```javascript
json = function () {
  var content = file.apply(null, arguments)
  return content ? parse(content) : null
}
```
- example usage
```shell
...
module.exports = function (name, defaults, argv, parse) {
if('string' !== typeof name)
  throw new Error('rc(name): name *must* be string')
if(!argv)
  argv = require('minimist')(process.argv.slice(2))
defaults = (
    'string' === typeof defaults
  ? cc.json(defaults) : defaults
  ) || {}

parse = parse || cc.parse

var env = cc.env(name + '_')

var configs = [defaults]
...
```

#### <a name="apidoc.element.rc.utils.parse"></a>[function <span class="apidocSignatureSpan">rc.utils.</span>parse (content)](#apidoc.element.rc.utils.parse)
- description and source-code
```javascript
parse = function (content) {

  //if it ends in .json or starts with { then it must be json.
  //must be done this way, because ini accepts everything.
  //can't just try and parse it and let it throw if it's not ini.
  //everything is ini. even json with a syntax error.

  if(/^\s*{/.test(content))
    return JSON.parse(stripJsonComments(content))
  return ini.parse(content)

}
```
- example usage
```shell
...

//if it ends in .json or starts with { then it must be json.
//must be done this way, because ini accepts everything.
//can't just try and parse it and let it throw if it's not ini.
//everything is ini. even json with a syntax error.

if(/^\s*{/.test(content))
  return JSON.parse(stripJsonComments(content))
return ini.parse(content)

}

var file = exports.file = function () {
var args = [].slice.call(arguments).filter(function (arg) { return arg != null })
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
