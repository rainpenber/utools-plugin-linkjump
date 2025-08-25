# 工程文件夹下的文件
在工程文件夹下，将会存放许多文件，有些文件是必不可少的，请参考官方推荐的文件目录结构。

我们应该先把 logo 文件以及页面对应的 html 入口文件放入工程文件夹下，然后添加必不可少的plugin.json 文件。


## plugin.json
```js
{
  "logo": "logo.png",
  "main": "index.html",
  "features": [
    {
      "code": "test",
      "cmds": [
        "第一个插件"
      ],
      "explain": "第一个插件"
    }
  ]
}
```
## 开始编写插件应用
要让你的插件应用展示任何内容，必须借助刚刚提前创建好的 “index.html” 文件，因为 uTools 插件应用本身借助了 Web 网页的界面来实现了界面的绘制，这对有 Web 开发经验的开发者来说，是相对简单易上手的方式。

现在，为你的插件应用输出最基础的内容，一行 hello world 。


### index.html
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>我的第一个插件应用</title>
</head>
<body>
  <h1>Hello World</h1>
</body>
</html>
```
>TIP
>在深入编写插件应用的过程中，你可能会慢慢运用到 uTools 提供的各种 api，比如：
>交互事件
>数据存储
>系统相关
>或者你需要自己定制更强大的系统交互能力，那么可以考虑为你的项目加入 preload.js，并尝试在其中使用 nodejs api。

# 插件应用目录结构
此部分会帮助你了解，通常情况下，一个插件应用的文件目录结构。

插件应用至少要有一个 plugin.json 作为入口，并配置 logo 字段以及 main 或者 preload 字段。

一个相对完整可打包成插件应用的目录可能是这样的：

```shell
/{plugin}
|-- plugin.json
|-- preload.js
|-- index.html
|-- index.js
|-- index.css
|-- logo.png
```

# 调试插件代码
在开发模式下，入口文件是支持 URL 协议的，可配合 Vite、Webpack 等工具，在开发阶段进行热更新。

## Vite
Vite 默认为各种框架提供了热更新的集成，所以只需要默认启动项目既可使用。


```shell
npm run dev
```
plugin.json增加development配置, 端口需要与 webpack-dev-server 开启的端口一致
```json
{
  "development": {
    "main": "http://127.0.0.1:5173/index.html"
  }
}
```
进入 uTools 开发工具, 点击接入开发后观察效果
## Webpack
添加 Webpack HMR 模块热替换插件
```shell
npm install webpack-dev-server --save-dev
```
入口index.js文件增加监听代码
```js
if (module.hot) {
    module.hot.accept();
}
```
启动 webpack-dev-server
```json
//package.json script
"scripts": {
    "serve": "webpack serve",
  }
```
```shell
npm run serve
```
plugin.json增加development配置, 端口需要与 webpack-dev-server 开启的端口一致
```json
{
  "development": {
    "main": "http://127.0.0.1:8080/index.html"
  }
}
```
进入 uTools 开发工具, 点击接入开发后观察效果
>注意
>preload.js 代码变更后无法自动热更新，在应用开发点击设置开启 退出到后台立即结束运行
## 源码编译
uTools 仅识别 html + css + javascript, 通常我们在开发过程中可能会使用各种的工具来辅助开发，比如 vite、webpack 等等，也可能会引入各种前端框架，比如 vue、react、svelte 等等，而这些代码并不是直接可以被 uTools 识别的，当我们打包插件应用前应该先将框架代码编译成普通的 html 、css、js 文件。通常是将源码编译输出到 dist 文件夹，然后将 dist 文件夹打包成插件应用，切勿将整个项目的根目录打包成插件应用。

## 第三方依赖
当你使用第三方依赖时，根据项目情况进行区分：

当你使用前端依赖时，只需要在项目的根目录下安装即可，对前端项目进行正常的编译，输出到 dist 文件夹。

当你使用 nodejs 的第三方依赖时，应当保证你的模块存在于 preload.js 同级目录，并且不要对它们进行编译操作，保证提交插件应用时的目录结构不变，并且源码清晰可读。
# plugin.json 配置
plugin.json 文件是插件应用的配置文件，它是最重要的一个文件，用来定义这个插件应用将如何与 uTools 集成。 每当你创建一个插件应用时，都需要从创建一个 plugin.json 文件开始。

## 配置文件格式
plugin.json 文件是一个标准的 JSON 文件，它的结构如下：

```json
{
  "main": "index.html",
  "logo": "logo.png",
  "preload": "preload.js",
  "features": [
    {
      "code": "hello",
      "explain": "hello world",
      "cmds": ["hello", "你好"]
    }
  ]
}
```
## 基础字段说明
### main
- 类型：string
- 必填：main 与 preload 至少存在一个
- 必须是一个相对于 plugin.json的相对路径，且只能是一个 .html 文件。

### logo
- 类型：string
- 必填：是
- 插件应用 Logo，必须为 png 或 jpg 文件

### preload
- 类型：string
- 必填：main 与 preload 至少存在一个
- 预加载 js 文件，这是一个关键文件，你可以在此文件内调用 nodejs、 electron 提供的 api。查看更多关于 preload.js

## 开发模式字段说明
### development
- 类型：object
- 必填：否
- 开发模式下的配置，对象的同名字段会会覆盖基础配置字段。

### development.main
- 类型：string
- 必填：否
- 开发模式下，插件应用的入口文件，与基础配置字段 main 字段相同，但是此处可以配置为一个 http 协议的地址（不推荐）。

> [!WARNING]注意
> 支持 http 协议的地址，是为了方便开发者配合前端框架或者各种构建工具的使用，请勿将基础字段 main 字段配置为 http 协议的地址。

## 插件应用设置字段说明
### pluginSetting
- 类型：object
- 必填：否
- 插件应用设置，可以配置一些插件在基座中的默认行为或者样式。

### pluginSetting.single
- 类型：boolean
- 必填：否
- 默认值：true
- 是否单例，默认为 true，表示插件在基座中只能存在一个应用实例。

### pluginSetting.height
- 类型：number
- 必填：否
- 最小值：1
- 插件应用初始高度。可以通过 utools.setExpendHeight 动态修改。

## 插件应用功能字段说明
### features
- 类型：Array<object>
- 必填：是
- 最小长度：1

features 定义插件应用的指令集合，一个插件应用可定义多个功能，一个功能可配置多条指令。
features 的每个元素都是一个 feature 对象，对象中包含以下字段：

#### feature.code
- 类型：string
- 必填：是
- 功能编码，此字段的值必须唯一。进入插件应用会将该编码带入，根据不同编码实现功能区分执行

#### feature.explain
- 类型：string 
- 必填：否
- 功能描述

#### feature.icon
- 类型：string
- 必填：否
- 功能图标 支持 png、jpg、svg 格式。

#### feature.platform
- 类型：Array<string>|string
- 必填：否
- 指定功能可用平台，可设置的值是 ["win32","darwin","linux"] 分别对应 Windows、macOS、Linux 平台

#### feature.mainPush
- 类型：boolean
- 必填：否
- 是否向搜索框推送内容。

#### feature.mainHide
- 类型：boolean
- 必填：否
- 若配置为true，打开此功能不主动显示搜索框。

#### feature.cmds
- 类型：Array<string|object>
- 必填：是
- 最小长度：1
- 配置该功能的指令集合，指令分「功能指令」和「匹配指令」

### 功能指令
搜索框可直接搜索和打开的指令


#### plugin.json
````json
{
  "features": [
    {
      "code": "text",
      "cmds": ["测试", "你好"]
    }
  ]
}
````
> [!WARNING]
> 指令配置为中文时，无需再配置它的拼音和首字母作为指令，uTools 支持拼音和首字母搜索。
> 必须配置至少一个「功能指令」，不然用户将无法安装插件应用

### 匹配指令
搜索框输入任意文本或粘贴图片、文件(夹)匹配出可处理它的指令

#### regex
正则匹配特定文本


plugin.json
````json
{
  "features": [
    {
      "code": "regex",
      "cmds": [
        {
          // 类型标记（必须）
          "type": "regex",
          // 指令名称（必须）
          "label": "打开网址",
          // 正则表达式字符串
          // 注意: 正则表达式存如果在斜杠 "\" 需要多加一个，"\\"
          // 注意：“任意匹配的正则” 会被 uTools 忽视，例如：/.*/ 、/(.)+/、/[\s\S]*/ ...
          "match": "/^https?:\\/\\/[^\\s/$.?#]\\S+$|^[a-z0-9][-a-z0-9]{0,62}(\\.[a-z0-9][-a-z0-9]{0,62}){1,10}(:[0-9]{1,5})?$/i",
          // 最少字符数 (可选)
          "minLength": 1,
          // 最多字符数 (可选)
          "maxLength": 1000
        }
      ]
    }
  ]
}
````

#### over
匹配任意文本

plugin.json
````json
{
  "features": [
    {
      "code": "over",
      "cmds": [
        {
          // 类型标记（必须）
          "type": "over",
          // 指令名称（必须）
          "label": "百度一下",
          // 排除的正则表达式字符串 (任意文本中排除的部分) (可选)
          "exclude": "/\\n/",
          // 最少字符数 (可选)
          "minLength": 1,
          // 最多字符数 (默认最多为 10000) (可选)
          "maxLength": 500
        }
      ]
    }
  ]
}
````
#### img
匹配图像
plugin.json
````json
{
  "features": [
    {
      "code": "img",
      "cmds": [
        {
          // 类型标记（必须）
          "type": "img",
          // 指令名称（必须）
          "label": "图像保存为文件"
        }
      ]
    }
  ]
}
````

#### files
匹配文件(夹)
plugin.json
````json
{
  "features": [
    {
      "code": "files",
      "cmds": [
        {
          // 类型标记（必须）
          "type": "files",
          // 指令名称（必须）
          "label": "图片批量处理",
          // 文件类型 - "file"、"directory" (可选)
          "fileType": "file",
          // 文件扩展名 (可选)
          "extensions": ["png", "jpg", "jpeg", "svg", "webp", "tiff", "avif", "heic", "bmp", "gif"],
          // 匹配文件(夹)名称的正则表达式字符串，与 extensions 二选一 (可选)
          "match": "/\\.(?:jpg|jpeg|png|svg|webp|tiff|avif|heic|bmp)$/i",
          // 最少文件数 (可选)
          "minLength": 1,
          // 最多文件数 (可选)
          "maxLength": 100
        }
      ]
    }
  ]
}
````
#### window
匹配当前活动的系统窗口
plugin.json
````json
{
  "features": [
    {
      "code": "window",
      "cmds": [
        {
          // 类型标记（必须）
          "type": "window",
          // 指令名称（必须）
          "label": "窗口置顶",
          // 窗口匹配规则
          "match": {
            // 应用名称（必须）
            "app": ["xxx.app", "xxx.exe"],
            // 匹配窗口标题的正则表达式字符串 (可选)
            "title": "/xxx/",
            // 窗口类 (Windows 专有) (可选)
            "class": ["xxx"]
          }
        }
      ]
    }
  ]
}
````
>[!WARNING]
>正则表达式存如果在斜杠 "" 需要多加一个，"\"

### 配置示例
#### 正则匹配
打开uTools开发工具
新建项目
选择utools-match-text-example下的plugin.json
运行
在uTools主输入框输入网址、手机号、身份证号即可观察匹配到该插件
```json
{
  "name": "demo_match_text",
  "version": "1.0.0",
  "pluginName": "智能匹配文本示例",
  "description": "智能匹配文本示例插件",
  "main": "index.html",
  "logo": "logo.png",
  "preload": "preload.js",
  "features": [
    {
      "code": "demo_match_text",
      "explain": "智能匹配文本示例",
      "cmds": [
        "match_text",
        {
          "type": "regex",
          "label": "网址匹配",
          "match": "/^(?:(http|https|ftp):\/\/)?((?:[\\w-]+\\.)+[a-z0-9]+)((?:\/[^\/?#]*)+)?(\\?[^#]+)?(#.+)?$/i"
        },
        {
          "type": "regex",
          "label": "身份证号匹配",
          "match": "/^[1-9]\\d{5}(19|20)\\d{2}(0[1-9]|1[0-2])(0[1-9]|[12]\\d|3[01])\\d{3}(\\d|X)$/"
        },
        {
          "type": "regex",
          "label": "手机号正则匹配",
          "match": "/^1[3456789]\\d{9}$/"
        }
      ]
    }
  ]
}
```
#### 图像匹配
1. 打开[uTools] - [插件应用市场]
2. 安装并打开[uTools 开发者工具]
3. 新建项目
4. 选择utools-match-img-example下的plugin.json
5. 运行
6. 屏幕截图后呼出uTools观察可看到智能匹配到了该插件
```json
{
  "name": "demo_match_img",
  "version": "1.0.0",
  "pluginName": "智能匹配图片示例",
  "description": "智能匹配图片示例插件",
  "main": "index.html",
  "logo": "logo.png",
  "preload": "preload.js",
  "features": [
    {
      "code": "demo_match_img",
      "explain": "智能匹配图片示例",
      "cmds": [
        "match_img",
        {
          "type": "img",
          "label": "智能匹配图片"
        }
      ]
    }
  ]
}
```
#### 文件匹配
打开uTools开发工具
新建项目
选择utools-match-files-example下的plugin.json
运行
复制文件或文件夹后呼出uTools即可看到智能匹配到了该插件
```json
{
  "name": "demo_match_files",
  "version": "1.0.0",
  "pluginName": "智能匹配文件示例",
  "description": "智能匹配文件示例插件",
  "main": "index.html",
  "logo": "logo.png",
  "preload": "preload.js",
  "features": [
    {
      "code": "demo_match_files",
      "explain": "智能匹配文件示例",
      "cmds": [
        "match_files",
        {
          "type": "files",
          "label": "智能匹配文件",
          "minLength": 1,
          "maxLength": 10
        }
      ]
    }
  ]
}
```
#### 窗口匹配
1. 打开[uTools] - [插件应用市场]
2. 安装并打开[uTools 开发者工具]
3. 新建项目
4. 选择utools-match-windows-example下的plugin.json
5. 运行
6. windows下打开[文件夹, 微信, 飞书, vsCode, 网易云音乐, Edg浏览器]任一窗口再呼出uTools观察第二栏匹配上了该插件
```json
{
  "name": "demo_match_windows",
  "version": "1.0.0",
  "pluginName": "智能匹配活动窗口示例",
  "description": "智能匹配活动窗口示例插件",
  "main": "index.html",
  "logo": "logo.png",
  "preload": "preload.js",
  "features": [
    {
      "code": "demo_match_windows",
      "explain": "智能匹配活动窗口示例",
      "cmds": [
        "match_windows",
        {
          "type": "window",
          "label": "置顶窗口",
          "match": {
            "app": [
              "explorer.exe",
              "Feishu.exe",
              "WeChat.exe",
              "cloudmusic.exe",
              "msedge.exe",
              "Code.exe"
            ]
          }
        }
      ]
    }
  ]
}
```
# 认识 preload
当你在 plugin.json 文件配置了 preload 字段，指定的 js 文件将被预加载，该 js 文件可以调用 Node.js API 的本地原生能力和 Electron 渲染进程 API。

## 为什么需要 preload
在传统的 web 开发中，为了保持用户运行环境的安全，JavaScript 被做了很强的沙箱限制，比如不能访问本地文件，不能访问跨域网络资源，不能访问本地存储等。

uTools 基于 Electron 构建，通过 preload 机制，在渲染线程中，释放了沙箱限制，使得用户可以通过调用 Node.js 的 API 来访问本地文件、跨域网络资源、本地存储等。

## preload 的定义
preload 是完全独立于前端项目的一个特殊文件，它应当与 plugin.json 位于同一目录或其子目录下，保证可以在打包插件应用时可以被一起打包。

preload js 文件遵循 CommonJS 规范，因此你可以使用 require 来引入 Node.js 模块，此部分可以参考 Node.js 文档。

## 前端使用 preload
只需给 window 对象自定义一个属性，前端就可直接访问该属性。

### preload.js
```js 
const fs = require("fs");

window.customApis = {
  readFile: (path) => {
    return fs.readFileSync(path, "utf8");
  },
};
```
### app.jsx
```jsx app.jsx
import { useEffect, useState } from "react";
export default function App() {
  const [file, setFile] = useState("");
  useEffect(() => {
    window.customApis.readFile("/path/to/README.md").then((data) => {
      setFile(data);
    }
  }, []);

  return (
    <div>
      <pre>{file}</pre>
    <div>
  )
}
```
## preload js 规范
由于 preload js 文件可使用本地原生能力，为了防止开发者滥用各种读写文件、网络等能力，uTools 规定：

preload js 文件代码不能进行打包/压缩/混淆等操作，要保证每一行代码清晰可读。
引入的第三方模块也必须清晰可读，在提交时将源码一同提交，同样不允许压缩/混淆。

# 使用 Node.js
preload js 文件遵循 CommonJS 规范，通过 require 引入 Node.js (16.x 版本) 模块

可以引入 Node.js 所有原生模块，开发者自己编写的 Node.js 模块以及第三方 Node.js 模块。

## 引入 Node.js 原生模块

preload.js
```js
const fs = require("node:fs");
const path = require("node:path");
const os = require("node:os");
const { execSync } = require("node:child_process");

window.services = {
  readFile: (filename) => {
    return fs.readFileSync(filename, { encoding: "utf-8" });
  },
  getFolder: (filepath) => {
    return path.dirname(filepath);
  },
  getOSInfo: () => {
    return { arch: os.arch(), cpus: os.cpus(), release: os.release() };
  },
  execCommand: (command) => {
    execSync(command);
  },
};
```
## 引入自己编写的模块

preload.js
```js
const writeText = require("./libs/writeText.js");

window.services = {
  writeText,
};
```
libs/writeText.js

```js
const fs = require("fs");
const path = require("path");

module.exports = function writeText(text, filePath) {
  const dir = path.dirname(filePath);
  if (!fs.existsSync(dir)) {
    fs.mkdirSync(dir, { recursive: true });
    fs.writeFileSync(filePath, text);
    return true;
  }
  return false;
};
```

## 引入第三方模块
通过 npm 安装
在 preload.js 同级目录下，保证存在一个独立的 package.json，并且设置 type 为 commons。

```json
{
  "type": "commonjs"
  "dependencies": {}
}
```
在 preload.js 同级目录下，执行 npm install 安装第三方模块，保证 node_modules 目录存在。

以下是通过 npm 引入 colord 的示例:

```bash
npm install colord
```
preload.js
```js
const { getFormat, colord } = require("colord");

window.services = {
  colord: {
    darken(text) {
      const fmt = getFormat(text);
      if (!fmt) {
        return [null, "请输入一个有效的颜色值，比如 #000 或 rgb(0,0,0)"];
      } else {
        const darkColor = colord(text).darken(0.1);
        return [darkColor, null];
      }
    },
  },
};
```
## 通过源码引入
在 preload.js 同级目录下，下载源码，并使用 require 引入。

比如从 github 下载 nodemailer：

```bash
git clone https://github.com/nodemailer/nodemailer.git
```
preload.js
```js
const nodemailer = require("./nodemailer");
const _setImmediate = setImmediate;
process.once("loaded", function () {
  global.setImmediate = _setImmediate;
});
const sendMail = () => {
  let transporter = require("./nodemailer").createTransport({
    host: "smtp.qq.com",
    port: 465,
    secure: true,
    auth: {
      user: "aaa@qq.com",
      pass: "xxx",
    },
  });
  let mailOptions = {
    from: "aaa@qq.com",
    to: "bbb@gmail.com",
    subject: "Sending Email using Node.js",
    text: "That was easy!",
  };

  transporter.sendMail(mailOptions, function (error, info) {
    if (error) {
      console.log(error);
    } else {
      console.log("Email sent: " + info.response);
    }
  });
};
window.services = {
  sendMail: () => {
    return sendMail();
  },
};
```
## 引入 Electron 渲染进程 API

preload.js 
```js
const { clipboard, nativeImage } = require("electron");

window.services = {
  copyImage: (imageFilePath) => {
    clipboard.writeImage(nativeImage.createFromPath(imageFilePath))
  },
};
```

# 使用 uTools API 提示
当你需要在项目中使用 TypeScript 时，一般会遇到无法正常使用 utools 的 API 的情况。

因此 uTools 官方推出了完整的类型定义文件，这份类型文件完整的列举了目前 utools 对象下所有的 API，并会根据版本的迭代同步更新。

## utools-api-types
utools-api-types 是官方开源的一个 TypeScript 类型定义代码库，你可以直接访问 `utools-api-types`文件夹里的内容 进行查看相关信息。

当然，若要使用到项目中，可以通过 npm 进行安装，并通过简单的配置启用。

### 安装
```shell
npm install utools-api-types --save-dev
```
### 配置 tsconfig
```json5
{
  "compilerOptions": {
    "types": ["utools-api-types"]
  },
  "includes": [
    // 如果使用ts或者框架，请添加需要类型提示的文件范围
    // 案例：
    // src/**/*.ts
    // preload.js
  ]
}
```