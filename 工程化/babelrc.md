# babelrc文件简单分析

在.babelrc配置文件中，主要是对预设（presets）和插件（plugins）进行配置，因此不同的转译器作用不同的配置项，大致可分为以下三项：

  - 语法转义器：主要对javascript最新的语法糖进行编译，并不负责转译javascript新增的api和全局对象。例如let/const就可以被编译，而includes/Object.assign等并不能被编译。常用到的转译器包有，babel-preset-env、babel-preset-es2015、babel-preset-es2016、babel-preset-es2017、babel-preset-latest等。在实际开发中可以只选用babel-preset-env来代替余下的，但是还需要配上javascirpt的制作规范babel-preset-stage-x一起使用，同时也是官方推荐。
  - 补丁转义器：主要负责转译javascript新增的api和全局对象，例如babel-plugin-transform-runtime这个插件能够编译Object.assign,同时也可以引入babel-polyfill进一步对includes这类用法保证在浏览器的兼容性。
  - jsx和flow插件：这类转译器用来转译JSX语法和移除类型声明的，使用Rect的时候你将用到它，转译器名称为babel-preset-react。

执行顺序：先执行plugins再执行presets，plugins里的插件是从前往后执行，presets预设是从后往前执行。
>预设（presets）是多个插件（plugins）的组合

下面是一份实际工作中的配置，解释见注释：
```js
{
  "presets": [
    /**
    *  babel-preset-env: 对js最新的语法糖进行编译，并不负责转译js新增的api和全局对象。
    */
    ["env", {
      "targets": {
        "browsers": ["last 10 versions", "ie >= 9"]  // 指定需要兼容的浏览器类型和版本，通过指定更高的浏览器版可减少插件和 polyfill 的代码量
      },
      "useBuiltIns": "usage"    // 只对项目中用到的新js语法进行编译，而不是所有语法
    }],
    /**
    * babel-preset-react: 转义react语法
    */
    "react",
    /**
    *   babel-preset-stage-0: 处于标准草案提案阶段的功能
    */
    "stage-0"
  ],
  "plugins": [
    // babel-plugin-import: 编译过程中将 import 的写法自动转换为按需引入的方式
    ["import", {
      "libraryName": "doraemon",    // 设置组价库名称
      "style": true     // 按需引入样式
    }],
    // babel-plugin-transform-decorators-legacy: 转译装饰器语法
    "transform-decorators-legacy",
    // babel-plugin-transform-runtime: 转译js新增的api和全局对象，与babel-polyfill功能类似，但是不会污染全局环境
    "transform-runtime",
  ],
  "env": {
    //  development 是提前设置的环境变量，如果没有设置BABEL_ENV则使用NODE_ENV，如果都没有设置默认就是development
    "development": {
      "plugins": [
        "dva-hmr"
      ]
    }
  }
}

```