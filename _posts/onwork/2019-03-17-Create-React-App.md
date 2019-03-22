# Start up

开始前需要确保npm版本为5.2+。

```
npx create-react-app my-app
cd my-app
npm start
```

生成的目录结构为：

```
my-app/
README.md
  node_modules/
  package.json
  public/
    index.html
    favicon.ico
  src/
    App.css
    App.js
    App.test.js
    index.css
    index.js
    logo.svg
```

<mark>在生成的目录当中，public/index.html 和 src/index.js 是不能删除的，其中index.html是网站的入口，而index.js是javascript执行代码的入口。</mark>

<mark>src目录里面的文件是可以被Webpack处理的，因此所有的js文件和css文件都应该放在该目录下。src目录下可以新建目录来放这些文件。</mark>

# Available Scripts

create-react-app相关的命令有：

- npm start: 开启服务器，所有文件的更改都会使服务器重新加载
- npm test: 调试
- npm run build: 将项目中的原始代码（js, css）编译（优化成）成直接可用的代码，输出到build文件夹中
- npm run eject: （<mark>谨慎使用，不可逆</mark>），把所有代码开放出来以供编辑，包括build选项、配置等，一般不需要。

# 在VSCode中使用Chrome插件调试

添加下面的选项到launch.json中：

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Chrome",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:3000",
      "webRoot": "${workspaceFolder}/src",
      "sourceMapPathOverrides": {
        "webpack:///src/*": "${webRoot}/*"
      }
    }
  ]
}
```