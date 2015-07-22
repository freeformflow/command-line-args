[![view on npm](http://img.shields.io/npm/v/command-line-args.svg)](https://www.npmjs.org/package/command-line-args)
[![npm module downloads per month](http://img.shields.io/npm/dm/command-line-args.svg)](https://www.npmjs.org/package/command-line-args)
[![Build Status](https://travis-ci.org/75lb/command-line-args.svg?branch=rewrite)](https://travis-ci.org/75lb/command-line-args)
[![Dependency Status](https://david-dm.org/75lb/command-line-args.svg)](https://david-dm.org/75lb/command-line-args)

# command-line-args
A library to collect command-line options and generate a usage guide.

## Features
- Supports the most common option notation styles
    - long options (`--find lib.js` or `--find=lib.js`)
    - short options (`-f lib.js`)
    - getopt-style combinations (`-xvf  lib.js`)
    - Two ways to specify a list (`--file one two` or `--file one --file two`)
- Modular - define common option sets and reuse in multiple projects.
- Split options into groups, for apps with a large set of options.
- Control over the type and value received from each option

## Synopsis
You create and supply an array of option definitions. Typically, it looks something like:

```js
module.exports = [
    { name: "help", alias: "h", type: Boolean, description: "Display this usage guide." },
    { name: "files", alias: "f", type: String, multiple: true, defaultOption: true, description: "The input files to process" },
    { name: "timeout", alias: "t", type: Number, description: "Timeout value in ms" }
];
```

If your app was run with a command like this:
```sh
$ my-app -vt 1000 lib/*.js
```

.. parsing the command-line args would return an object like this:
```js
{ files:
   [ 'lib/command-line-args.js',
     'lib/definition.js',
     'lib/definitions.js',
     'lib/option.js' ],
  verbose: true,
  timeout: 1000  }
 ```

and the usage guide would look like:
```
  a typical app
  Generates something useful

  Usage
  $ cat input.json | my-app [<options>]
  $ my-app <files>

  Main options
  This group contains the most important options.

  -h, --help               Display this usage guide.
  -f, --files <string[]>   The input files to process
  -t, --timeout <number>   Timeout value in ms

  Project home: https://github.com/me/my-app
```

If you don't like the built-in template, you can fork [command-line-usage]() and edit as required. Or write your own.


## Walk-through via example

### Simplest case
Options are defined as an array of Definition objects. The only required Definition property is `name`, so the simplest example is
```js
[
  { name: "main" },
  { name: "dessert" },
  { name: "courses" }
]
```

Using the command-line-args test harness (install globally) you can see how the `.parse()` output looks:

```sh
$ cat example/one.js | command-line-args --main
{ main: true }

$ cat example/one.js | command-line-args --main --dessert
{ main: true, dessert: true }
```

In this case, `--main` and `--dessert` were interpreted as flags, and set to `true` as no values were passing. If you supply values, they will be set as a string.

```sh
$ cat example/one.js | command-line-args --main beef --dessert trifle
{ main: 'beef', dessert: 'trifle' }
```

## Examples

| #   | Command line | parse output |
| --- | ------------ | ------------ |
| 1   | `$ meal --main` | `{ main: true }` |
| 2   | `$ meal --main --dessert` | `{ main: true, dessert: true }` |
| 3   | `$ meal --main beef --dessert trifle` | `{ main: "beef", dessert: "trifle" }` |
| 4   | `$ meal --main beef --courses 1` | `{ main: "beef", courses: "1" }` |

## Take control of type

### Setter
```js
[
    { name: "main", type: String },
    { name: "dessert", type: String },
    { name: "courses", type: Number }
]
```

| #   | Command line | parse output |
| --- | ------------ | ------------ |
| 5   | `$ meal --main --courses 3` | `{ main: null, courses: 3 }` |

in 1, main was passed but is set to null (not true, as before) meaning "no value was specified".

### class

```js
function Dessert(name){
    if (!(this instanceof Dessert)) return new Dessert(name);
    this.name = name;
}

module.exports = [
    { name: "main", type: String },
    { name: "dessert", type: Dessert },
    { name: "courses", type: Number }
];
```

| #   | Command line | parse output |
| --- | ------------ | ------------ |
| 5   | `$ meal --main Beef --dessert "Spotted Dick"` | `{ main: 'Beef', dessert: { name: 'Spotted Dick' } }` |


## Tips
- To validate the collected options, use `test-value`.

## Install
```sh
$ npm install command-line-args --save
```


# API Reference
## Modules
<dl>
<dt><a href="#module_command-line-args">command-line-args</a></dt>
<dd></dd>
<dt><a href="#module_definition">definition</a></dt>
<dd></dd>
<dt><a href="#module_definitions">definitions</a></dt>
<dd></dd>
<dt><a href="#module_option">option</a></dt>
<dd></dd>
</dl>
<a name="module_command-line-args"></a>
## command-line-args
<a name="exp_module_command-line-args--CliArgs"></a>
### CliArgs(definitions) ⇒ <code>object</code> ⏏
**Kind**: Exported function  

| Param | Type |
| --- | --- |
| definitions | <code>module:command-line-args.argDefType</code> | 

<a name="module_definition"></a>
## definition

* [definition](#module_definition)
  * [Definition](#exp_module_definition--Definition) ⏏
    * [.name](#module_definition--Definition+name) : <code>string</code>
    * [.type](#module_definition--Definition+type) : <code>function</code>
    * [.description](#module_definition--Definition+description) : <code>string</code>
    * [.alias](#module_definition--Definition+alias) : <code>string</code>
    * [.multiple](#module_definition--Definition+multiple) : <code>boolean</code>
    * [.defaultOption](#module_definition--Definition+defaultOption) : <code>boolean</code>
    * [.group](#module_definition--Definition+group) : <code>string</code> &#124; <code>Array.&lt;string&gt;</code>
    * [.value](#module_definition--Definition+value) : <code>boolean</code>

<a name="exp_module_definition--Definition"></a>
### Definition ⏏
Option Definition

**Kind**: Exported class  
<a name="module_definition--Definition+name"></a>
#### definition.name : <code>string</code>
**Kind**: instance property of <code>[Definition](#exp_module_definition--Definition)</code>  
<a name="module_definition--Definition+type"></a>
#### definition.type : <code>function</code>
**Kind**: instance property of <code>[Definition](#exp_module_definition--Definition)</code>  
<a name="module_definition--Definition+description"></a>
#### definition.description : <code>string</code>
**Kind**: instance property of <code>[Definition](#exp_module_definition--Definition)</code>  
<a name="module_definition--Definition+alias"></a>
#### definition.alias : <code>string</code>
a single character

**Kind**: instance property of <code>[Definition](#exp_module_definition--Definition)</code>  
<a name="module_definition--Definition+multiple"></a>
#### definition.multiple : <code>boolean</code>
**Kind**: instance property of <code>[Definition](#exp_module_definition--Definition)</code>  
<a name="module_definition--Definition+defaultOption"></a>
#### definition.defaultOption : <code>boolean</code>
**Kind**: instance property of <code>[Definition](#exp_module_definition--Definition)</code>  
<a name="module_definition--Definition+group"></a>
#### definition.group : <code>string</code> &#124; <code>Array.&lt;string&gt;</code>
**Kind**: instance property of <code>[Definition](#exp_module_definition--Definition)</code>  
<a name="module_definition--Definition+value"></a>
#### definition.value : <code>boolean</code>
**Kind**: instance property of <code>[Definition](#exp_module_definition--Definition)</code>  
<a name="module_definitions"></a>
## definitions
<a name="module_option"></a>
## option
* * *

&copy; 2015 Lloyd Brookes \<75pound@gmail.com\>. Documented by [jsdoc-to-markdown](https://github.com/75lb/jsdoc-to-markdown).
