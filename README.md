# OpenLayers Client Starter

Starter files for building a standalone OpenLayers client using otherwise vanilla JavaScript. Does not require webpack or any other bundling tool. Only uses native JavaScript modules to simplify development. Provides intellisense type completion in VSCode for the OpenLayers library.

OpenLayers (`V4.6.5`) is preinstalled and linked in debug format. You can source it over CDN or in minified formats if preferred [instructions below](#openlayers-alternate-sourcing).

# Usage

## Setup

> fork this repo and clone it to begin development

```sh
$ git clone <your fork URL> [path/to/project-name]
```

> the last part is where the repo will be cloned into (if left off then the directory will be called "openlayers-vanilla-client/" )

```sh
# an example to clone into "my-openlayers-project" in my ~/codes/projects/ directory
$ git clone https://github.com/the-vampiire/openlayers-vanilla-client.git ~/codes/projects/my-openlayers-project
```

After cloning you can open the project in VSCode using the `code` shortcut

> provide the path that the repo was cloned into

```sh
$ code <path/to/repo>

# from example above
$ code ~/codes/projects/my-openlayers-project
```

> if you get an error that `code` command is not found

You need to set up the VSCode CLI. You can find the [documentation here](https://code.visualstudio.com/docs/editor/command-line). This will only take a minute.

## Development

> directory structure

```
jsconfig.json <-- adds VSCode intellisense using OpenLayers Type definitions
ol-types.d.ts <-- the Type definitions of OpenLayers
src/ <-- all your work goes in here
  index.html <-- prelinked with OpenLayers and the index.js module
  index.js <-- entrypoint module where any initialization can take place
  utils.js <-- a sample module to separate utils from initialization
  ol.css <-- OpenLayers CSS library (in debug format)
  ol.js <-- OpenLayers JS library (in debug format)
```

Write your [JavaScript module files](#using-javascript-modules) using the [export](#exporting) and [import](#importing) syntax.

The `index.js` module is linked in the HTML and set up to be used as the entrypoint to the application. You can import from other modules and initialize the JavaScript in this file. There is no need to link any other modules in the HTML.

Once you have written your code you can view it in your browser by serving the `index.html` file.

## Serving Locally

To serve the client you can use a Python HTTP server or the VSCode LiveServer extension.

### Serve with LiveServer

> right click the `src/index.html` file and select `Open with LiveServer`

> your browser should open automatically, otherwise go to http://localhost:5500/src/index.html

### Serve with Python

> Python 3+

```sh
$ python -m http.server -d src/ 3000
```

> Python 2

```sh
$ cd src/ && python -m SimpleHTTPServer 3000
```

> after starting the HTTP server open your browser to http://localhost:3000

## Debugging with Firefox

In order to use the VSCode debugger with Firefox you have to install the `Debugger for Firefox` extension.

> go to extensions > search "Debugger for Firefox" > install

The project already has a debugger configuration in `.vscode/launch.json` set up to work with [the Python server](#serve-with-python).

> if [using LiveServer](#serve-with-liveserver) replace `.vscode/launch.json` with the contents below

`.vscode/launch.json`

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Launch localhost debugger",
      "type": "firefox",
      "request": "launch",
      "reAttach": true,
      "url": "http://localhost:5500/src/index.html",
      "webRoot": "${workspaceFolder}/src/"
    }
  ]
}
```

### Use the Debugger

> **make sure you are [serving the client](#serving-locally) before launching the debugger!**

Once the configuration is set up and you are serving the client you can launch the debugger.

> from the taskbar (bottom of window) click "Launch localhost debugger"

## Deployment

No other build tools or scripts are needed to deploy the application!

> copy the `src/` directory to your HTTP server web directory, S3 bucket or other hosting location

# Using JavaScript Modules

Normally all scripts loaded in the browser share a common global scope for any namespaces declared in the scripts. This causes complications of conflicting names, dense single script files or working around delicate `<script>` element linking order.

> a namespace is a general term used to describe any uniquely named variable, function or class that has been declared in a given scope (a block or global)

JavaScript modules do not share a global scope. Instead they have a module scope that is independent of any other module. You can explicitly state what should be shared between other modules files. Using modules can help break up giant script files into more readable, organized and portable pieces. In other words modules help make your code more...modular!

> modules revolve around control of private and shared scopes for namespaces

> one module exports the namespaces of its choice while another can decide which of those to import and use

JavaScript modules use the `<script type="module">` attribute to alert the browser that it should parse `import` and `export` statements. Modules behave in `strict` mode and have a number of other features and convenient behaviors. Practical usage of modules and their `import` and `export` statements are described in this guide. Use the resources below to dig deeper into how modules work:

- [MDN Modules Guide (high level)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)
- [JavaScript.info (in depth)](https://javascript.info/modules-intro)

## Exporting

In order to import a namespace in a module it must first be exported by another module.

> a module can export a single default export, multiple named exports or a combination of the two

> any valid namespace can be exported although some depend on the type of export

> namespaces are declared using any declaration keyword from the list below

```js
let
var
const
class
function
```

### Default Export

A default export represents a single encapsulation of the main feature of a module. You can only have **one default export per module**.

> the default export does not retain its namespace when it gets imported (this will make more sense in the import section)

There are two cases for default exports

> the default export must be defined before the export statement

```js
// any valid namespace, function used as an example
function myFunc() {}

export default myFunc;
```

> the `class` keyword is special and can be declared and exported inline

```js
export default class ClassName {}
```

### Named Exports

Named exports can be used for a module to expose specific named features. **You can have any number of named exports in a module.**

> named exports retain their namespace when imported in another module

There are several ways to use named exports:

> inline export and declaration

```js
export const myVariable = "a value or object reference";
```

> export after declaration

```js
const myObject = {};

export myObject;
```

> multiple named exports in a single statement

```js
const myObject = {};
const myVariable = "";

export { myObject, myVariable };
```

## Importing

Once a module has declared its exports they can be imported in another module.

> a module can import a default export, named export or a combination of the two

> a module can decide what to import as well as what namespace to use

Importing from another module uses the `import from ""` syntax. The `from` keyword is used to provide the relative path to the module being imported.

> Note: browser modules must include the `.js` extension unlike NodeJS

When importing a default export the **namespace it is given is defined by the importing module**

> importing a default export

`src/exporting-module.js`

```js
export default class MyClass {}
```

`src/importing-module.js`

```js
// notice the namespace

import AClass from "./exporting-module.js";
```

When importing named exports the namespaces are preserved

> importing named exports

`src/exporting-module.js`

```js
const myObject = {};
const myVariable = "";

export const anotherNamedExport = "";

export { myObject, myVariable };
```

`src/importing-module.js`

```js
// the namespaces are preserved
import {
  myObject,
  myVariable,
  anotherNamedExport,
} from "./exporting-module.js";
```

> the importing module can decide which named exports it wants to import

```js
// selecting only one named export
import { myObject } from "./exporting-module.js";
```

If there are naming conflicts an alias can be provided using the `as` keyword.

> importing named exports with an alias

> the alias will be used within the scope of the importing module

`src/exporting-module.js`

```js
const myObject = {};
const myVariable = "";

export const anotherNamedExport = "";

export { myObject, myVariable };
```

`src/importing-module.js`

```js
// the namespaces can be overwritten with a local alias
import { myObject as myCustomName, myVariable } from "./exporting-module.js";
```

Finally both a default export as well as named exports can be imported in a single statement

> importing default and named exports

`src/exporting-module.js`

```js
const myObject = {};
const myVariable = "";
function myFunction() {}

export const anotherNamedExport = "";

export { myObject, myVariable };

export default myFunction;
```

`src/importing-module.js`

```js
// the default export is always named by the importing module
import theDefaultExport, {
  // the namespaces for named exports are preserved
  myObject,
  myVariable as someAlias, // unlessed aliased
  anotherNamedExport,
} from "./exporting-module.js";
```

# OpenLayers Alternate Sourcing

## Using the minified format

> enter the following commands to download the minified formats

> ~75% smaller in size in exchange for debugging support

```sh
$ curl https://cdnjs.cloudflare.com/ajax/libs/openlayers/4.6.5/ol.css -o src/ol.css

$ curl https://cdnjs.cloudflare.com/ajax/libs/openlayers/4.6.5/ol.js -o src/ol.js
```

## Using the OpenLayers CDN

Replace the local links in `index.html` with the CDN links for the CSS and JavaScript.

> V4.6.5: debug (non-minified) - 411KB

> use this for local development and debugging support

```html
<link
  rel="stylesheet"
  type="text/css"
  href="https://cdnjs.cloudflare.com/ajax/libs/openlayers/4.6.5/ol-debug.css"
/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/openlayers/4.6.5/ol-debug.js"></script>
```

> V4.6.5: minified lib - 144KB

> use this for deployment or if you don't need the full lib for debugging

```html
<link
  rel="stylesheet"
  type="text/css"
  href="https://cdnjs.cloudflare.com/ajax/libs/openlayers/4.6.5/ol.css"
/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/openlayers/4.6.5/ol.js"></script>
```
