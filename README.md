# What is Node js

## Node.js is JavaScript

Node.js is a runtime for built on top of Chrome's V8. It allows you to develop apps in JavaScript outside of the browser. It's single threaded non blocking and asynchronous. This is acheived by the use of an event loop at the core of Node.js. If you know JS then you already know how to develop with Node.js, kinda....
## Use cases
Because Node.js can run outside of the browser, it can be used for pretty much anything!

- API's and servers
- Databases (yes some DB's are built in Node)
- CLI's
- Build Tools
- Automations
- Basic Scripting
- GPU shopping bots 👀

## Installation
You can use NVW to install node js, because with NVM you can choose your own node js version and you can also switch to any version at any time.
```
nvm install --lts
```

## Executing Node
 
### Node REPL
Before we get into creating programs and apps with Node.js, let's get a feet wet with the Node.js REPL. Just type node in your terminal. This will create a Node.js environment where we can write and execute JavaScript. You'll see that all that JS knowledge you have learned carries over to Node.js! 💯. Although, some things, specifically browser based API's probably won't work. Go ahead, try an `alert('hello world')` and see what happens. You'll get an error. Although the Node.js runtime uses JS as it's languge, none of the Browser based globals that you are familiar with actually exist in Node.js or they are pollyfilled to prevent runtime errors.


### File Execution
The Node REPL it nice, but you can't build an application with that. We need to be able write our code to a file and tell Node.js to execute that.

```
Create a file called:index.js
```

In that file, write some JS. Try out `console.log('hello there')`. To execute this .js file in the Node.js runtime, we can use the `node` command with the file name as an argument.
```
node index.js
```

Like the Browser, Node.js comes with some practical globals for us to use in our applications.

### Common
- `global` Think of this as like `window` but for Node.js. DON'T ABUSE IT!
- `__dirname` This global is a `String` value that points the the directory name of the file it's used in.
  - `__filename` Like `__dirname`, it too is relative to the file it's written in. A `String` value that points the the file name.
- `process` A swiss army knife global. An `Object` that contains all the context you need about the current program being executed. Things from env vars, to what machine you're on.
- `exports` `module` `require` These globals are used for creating and exposing modules throughout your app. We'll get to modules in a second 🌈

# Modules
There is no GUI in Node.js, no HTML or CSS. This also means there aren't any scipt tags to include JS files into our application. Node.js uses modules to to share your JavaScript with other JavaScript in your apps. No window or globals needed. If you've ever done `window.App = window.App || {`} then you'll like this!

## What is a module
A module is a reusable chunk of code that has its own context. That way modules can't interfere with or polute the global scope.
You can think of them like lego blocks that you can create, import, and share.

## Two module types
By default, Node.js uses common js modules. With newer versions of Node.js we can now take advantage of ES6 modules. To opt into using this syntax, you can use the .mjs extension instead of `.js`. We'll be using the ES6 module syntax going forward as they are the standard now with browsers adding support now.

## Module syntax
Now, let's create our first module. The only thing we have to do is expose some code in one for our JavaScript files. We can do that with the `export` keyword.

```
// utils.js
export const action = () => {

}

export const run = () => {

}
```

Few things happening here. Let's look at the `utils.js` file. Here we're using the `export` keyword before the variable declartion. This creates a named export. With the name being whatever the variable name is. In this case, a function called `action`. Now in `app.js`, we `import` the action module from the `utils` file. The path to the file is relative to the file you're importing from. You also have to prefix your path with a `'./'`. If you don't, Node will think you're trying to import a built in module or npm module. Because this wa a named export, we have to import with brackets `{ action, run }` with the exact name of the modules exported. We don't have to import every module that is exported.

Usually if you only have to expose one bit of code, you should use the `default` keyword. This allows you to import the module with whatever name you want.
```
// utils.js
default export function () {
  console.log('did action')
}
```

```
// app.js
import whateverIWant from './utils'
```
You can read more about the module syntax on the <a href="https://nodejs.org/api/packages.html" target="_blank">Node.js docs</a>.

## Internal Modules
Node.js comes with some great internal modules. You can think of them as like the phenonminal global APIs in the browser. Here are some of the most useful ones:

- `fs` - useful for interacting with the file system.
- `path` - lib to assit with manipulating file paths and all their nuiances.
- `child_process` - spawn subprocesses in the background.
- `http` - interact with OS level networking. Useful for creating servers.


# File System

Until Node.js, there wasn't a great way to access the file system on a machine with JavaScript, this is due to secutrity restrictions in most browsers. With Node.js, one can create, edit, remote, read, stream, & more with files. If you've ever used a build tool like webpack or a parser like babel, then you realize just how powerful Node.js can be when manipulating the file system.

## Reading a file
Node.js ships with a powerful module, fs short for file system. There are many methods on the fs module. To read a file, we'll use the readFile method.

Create a simple html file template.html.

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>
  <h1>{title}</h1>
  <p>{body}</p>
</body>
</html>
```

This html file will be used as template and has placeholders that we will interpolate later when writing a file.

To read the file:

```
import { readFile } from 'fs/promises'

let template = await readFile(new URL('./template.html', import.meta.url), 'utf-8')
```

The fs module has import for promise based methods. We'll opt to use those as they have a cleaner API and using async + non-blocking methods are preferred. More on that later. Because we're using .mjs files, we don't have access to __dirname or __filename which is what is normally used in combination with the path module to form an appropiate file path for fs. So we have to use the URL global that takes a relative path and a base path and will create a URL object that is accepted by readFile. If you were to log template, you'd see that its just a string.

## Write a file
Writing a file is similar to reading a file, except you need some content to place in the file. Let's interpolate the variables inside our template string and write the final html string back to disk.


```
import { readFile, writeFile } from 'fs/promises'

let template = await readFile(new URL('./test.html', import.meta.url), 'utf-8')

const data = {
  title: 'My new file',
  body: 'I wrote this file to disk using node'
}

for (const [key, val] of Object.entries(data)) {
  template = template.replace(`{${key}}`, val)
}

await writeFile(new URL('./index.html', import.meta.url), template)
```
