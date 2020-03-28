# 自定义命令行工具 cli
一个创建项目功能的 cli 大致步骤：
- package.json 添加 bin 属性，指定命令的入口文件
- 创建 bin 和 lib 目录（一般至少需要这两个），bin 放命令定义的文件包括入口文件，lib 放工具和命令指定逻辑
- 入口文件第一行添加 `#!/usr/bin/env node`， 定义当前脚本的执行环境是用 Node 执行
- 命令的代码逻辑主要需要用到 `commander`、`inquirer`、`chalk` 这三个插件
  - commander：定义命令、解析命令行参数等
  - inquirer：开发者和命令行的交互功能
  - chalk：美化命令行
- 如果项目模板在 git 仓库，则可以使用 `download-git-repo` 插件去拉取远程模板到本地
- 工具完成之后，通过 npm publish 发布
