---
title: Npm-dependency
date: 2017-11-20 17:37:00
tags:
- npm
---

当我们为某个library开发一个插件的时候，需要将确定的公共依赖放到`peerDependency`中。  
官方对`peerDependency`的解释是

>In some cases, you want to express the compatibility of your package with a host tool or library, while not necessarily doing a require of this host. This is usually referred to as a plugin. Notably, your module may be exposing a specific interface, expected and specified by the host documentation.

---
我们亲自尝试下，首先我们新建一个空repo

```bash
make dir npm-peerDependency && cd npm-peerDependency && npm init -y
```

生成一个空的`package.json`
```json
{
  "name": "npm-dependency",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "Mr.bone <sgbfire@gmail.com>",
  "license": "ISC"
}
```
接着我们模拟下载了一个插件。
```bash
mkdir fake-plugin && cd fake-plugin && npm init -y
```
在`fake-plugin`目录下也生成了一个空的`package.json`:

```json
{
  "name": "fake-plugin",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "Mr.bone <sgbfire@gmail.com>",
  "license": "ISC"
}
```
然后我们假设它是一个react的plugin，我们为其添加`peerDependencies`

```json
{
  "name": "fake-plugin",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "Mr.bone <sgbfire@gmail.com>",
  "license": "ISC",
  "peerDependencies": {
    "react-dom": ">16"
  }
}
```
这时我们去到npm-dependency使用local dependency方式安装这个`fake-plugin`。

```bash
npm i ./fake-plugin
```
这时候npm会提示warn

```bash
npm WARN react-dom@16.1.1 requires a peer of react@^16.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN fake-plugin@1.0.0 requires a peer of react-dom@>16 but none is installed. You must install peer dependencies yourself.
npm WARN npm-dependency@1.0.0 No description
npm WARN npm-dependency@1.0.0 No repository field.

added 18 packages in 1.635s
```
这时会发现插件所需要的peerDependency `react`和`react-dom` 都安装成功了，如果host dependency安装的依赖和插件声明的peerDependency不兼容时，npm会throw一个error，然安装失败，这时候需要我们去更换依赖或者想法兼容。  
所以我们如果在开发插件的时候，使用的第三方依赖尽量都用peerDependency声明，防止打包过程中重复打包多份依赖。
