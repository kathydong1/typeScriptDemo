* 在团队开发中为了更好的协作，我们应该在项目中制定各种规则，比如：代码风格、变量命名、文件命名、git commit 提交格式等等。 这篇文章就介绍如何搭建快速搭建一个具有自定义规则的 TypeScript 项目。
* 首先在 GitHub 上创建一个仓库，然后 clone 到本地。

>> $ git clone git@github.com:ZhiXiao-Lin/create-ts-project.git

* 进入项目目录，执行 NPM 初始化

>> $ yarn init

* 安装 typescript 作为开发阶段的依赖项

>> $ yarn add typescript -D

*在根目录新建 tsconfig.json，配置项具体的意义可以参考 ts 官方文档
>> {
    "version": "1.8.0",
    "compilerOptions": {
        "outDir": "build/compiled",
        "lib": [ "es6" ],
        "target": "es5",
        "module": "commonjs",
        "moduleResolution": "node",
        "emitDecoratorMetadata": true,
        "experimentalDecorators": true,
        "sourceMap": true,
        "noImplicitAny": true,
        "declaration": true
    },
    "exclude": [ "build", "node_modules" ]
}

* 安装 @types/node 让 node 的核心包具备类型提示
>> $ yarn add @types/node -D

* 在根目录新建 src 目录，用于存放所有的 TypeScript 源文件，然后在 src 下新建 index.ts 作为入口文件
>> src/index.ts<br>
console.log('Hello TypeScript!');
image.png

* 在开发阶段为了能直接执行并且监听 ts 文件的变化，安装 ts-node-dev

>> $ yarn add ts-node-dev -D

* 
在 package.json 中定义一个启动脚本

>> "scripts": {
    "start": "ts-node-dev --respawn --transpileOnly src/index.ts"
}

* 试着运行
>> $ yarn start<br>
yarn run v1.12.3
$ ts-node-dev --respawn --transpileOnly src/index.ts
Using ts-node version 8.0.1, typescript version 3.2.4
Hello TypeScript!

* 不管团队中的成员用的是什么编辑器，我们都应该保持一致的代码风格，这就需要使用 prettier
>> $ yarn add prettier -D

* 然后在根目录下新增一个 .prettierrc.js，用于定义代码格式化的规则
>> module.exports = {
    // 一行最多 140 字符
    printWidth: 140,
    // 使用 4 个空格缩进
    tabWidth: 4,
    // 不使用缩进符，而使用空格
    useTabs: false,
    // 行尾需要有分号
    semi: true,
    // 使用单引号
    singleQuote: true,
    // jsx 不使用单引号，而使用双引号
    jsxSingleQuote: false,
    // 末尾不需要逗号
    trailingComma: 'none',
    // 大括号内的首尾需要空格
    bracketSpacing: true,
    // jsx 标签的反尖括号需要换行
    jsxBracketSameLine: false,
    // 箭头函数，只有一个参数的时候，也需要括号
    arrowParens: 'always',
    // 每个文件格式化的范围是文件的全部内容
    rangeStart: 0,
    rangeEnd: Infinity,
    // 不需要写文件开头的 @prettier
    requirePragma: false,
    // 不需要自动在文件开头插入 @prettier
    insertPragma: false,
    // 使用默认的折行标准
    proseWrap: 'preserve',
    // 根据显示样式决定 html 要不要折行
    htmlWhitespaceSensitivity: 'css',
    // 换行符使用 lf
    endOfLine: 'lf'
};

* 在 package.json 中新增一条 script
>> "scripts": {
    "start": "ts-node-dev --respawn --transpileOnly src/index.ts",
    "style:prettier": "prettier --write \"src/**/*.ts\""
},

* 运行 yarn style:prettier 将会自动格式化 src 目录下的所有 .ts 文件
>> $ yarn style:prettier<br>
yarn run v1.12.3
$ prettier --write "src/**/*.ts"
src\index.ts 225ms
Done in 0.66s.

* 安装 tslint 用于检查项目文件的命名、变量的命名等等
>> $ yarn add tslint -D

* 安装 tslint-config-prettier ，这是根据 prettier 定义的 tslint 规则
>> $ yarn add tslint-config-prettier -D

* 在根目录下新建 tslint.json，根据自己的需要定义 rules
>> {
    "defaultSeverity": "error",
    "extend": [ "tslint-config-prettier" ], // 继承 tslint-config-prettier
    "linterOptions": {
        "exclude": [ "**/node_modules/**" ]  // 排除 node_modules
    },
    "jsRules": {
        "no-unused-expression": true
    },
    "rules": {
        "typedef": {
            "options": [ "property-declaration" ]
        },
        "class-name": true,
        "interface-name": true,
        "variable-name": {
            "options": [ "ban-keywords", "check-format", "allow-leading-underscore", "allow-pascal-case" ]
        },
        "no-var-keyword": true,
        "no-var-requires": true,
        "no-any": false,
        "no-string-literal": true,
        "await-promise": true,
        "promise-function-async": true
    },
    "rulesDirectory": []
}

* 在 package.json 中新增一条 script
>> "scripts": {
    "start": "ts-node-dev --respawn --transpileOnly src/index.ts",
    "style:prettier": "prettier --write \"src/**/*.ts\"",
    "style:lint": "tslint -p tsconfig.json -c tslint.json"
},

* 运行 yarn style:lint，会执行 lint 检查
>> $ yarn style:lint<br>
yarn run v1.12.3
$ tslint -p tsconfig.json -c tslint.json
Done in 1.45s.
安装 commitizen，让我们的项目具有统一的 git commit 规范
$ npm install -g commitizen
$ commitizen init cz-conventional-changelog --save --save-exact
Attempting to initialize using the npm package cz-conventional-changelog
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN create-ts-project@1.0.0 No description
cz-conventional-changelog@2.1.0
added 7 packages from 16 contributors, removed 1 package, updated 131 packages and audited 219 packages in 10.509s
found 0 vulnerabilities

* 执行后 package.json 中会多出 commitizen 的配置项
>> "config": {
    "commitizen": {
        "path": "./node_modules/cz-conventional-changelog"
    }
}

* 将所有的 git commit 操作替换成 git cz
>> $ git cz<br>
cz-cli@3.0.5, cz-conventional-changelog@2.1.0
Line 1 will be cropped at 100 characters. All other lines will be wrapped after 100 characters.
? Select the type of change that you're committing: (Use arrow keys)
> feat:     A new feature
  fix:      A bug fix
  docs:     Documentation only changes
  style:    Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
  refactor: A code change that neither fixes a bug nor adds a feature
  perf:     A code change that improves performance
  test:     Adding missing tests or correcting existing tests
(Move up and down to reveal more choices)

* 我们还需要在 git 提交的时候进行 commit 规范的检查，首先安装 validate-commit-msg 并在根目录中新建一个 .vcmrc 文件用于定义 commit 的验证规则
>> $ yarn add validate-commit-msg -D<br>
{
    "types": [ "feat", "fix", "docs", "style", "refactor", "perf", "test", "build", "ci", "chore", "revert" ],
    "scope": {
        "required": false,
        "allowed": [ "*" ],
        "validate": false,
        "multiple": false
    },
    "warnOnFail": false,
    "maxSubjectLength": 100,
    "subjectPattern": ".+",
    "subjectPatternErrorMsg": "subject does not match subject pattern!",
    "helpMessage": "",
    "autoFix": true
}

* 然后安装 husky 让我们可以很方便的定义 git hooks
>>  yarn add husky -D

* 在 package.json 中定义 git hooks，使用 validate-commit-msg 来检查 commit 格式
>> "husky": {
        "hooks": {
            "commit-msg": "validate-commit-msg"
        }
}

* 现在如果提交的 commit 不符合 .vcmrc 定义的格式就会提交失败
>> $ git commit -m "2223"<br>
husky > commit-msg (node v10.14.0)
INVALID COMMIT MSG: does not match "<type>(<scope>): <subject>" !
2223
husky > commit-msg hook failed (add --no-verify to bypass)

* 现在我们还希望能在 commit 时自动执行代码格式化和语法检查，要使用一条命令执行多项操作可以安装 npm-run-all
>> $  yarn add npm-run-all -D

* npm-run-all 提供两个命令 run-s 串行执行多条命令 run-p 并行执行多条命令，在 package.json 中定义一个 script，用于串行执行所有以 style: 开头的 script
>> "scripts": {
    "start": "ts-node-dev --respawn --transpileOnly src/index.ts",
    "style": "run-s style:**",
    "style:prettier": "prettier --write \"src/**/*.ts\"",
    "style:lint": "tslint -p tsconfig.json -c tslint.json"
},

* 运行一下，看到确实执行了两条命令
>> $ yarn style <br>
yarn run v1.12.3
$ run-s style:**
$ prettier --write "src/**/*.ts"
src\index.ts 229ms
$ tslint -p tsconfig.json -c tslint.json
Done in 3.02s.

* 修改 package.json 定义的 git hooks
>> "husky": {
    "hooks": {
        "pre-commit": "yarn style",
        "commit-msg": "validate-commit-msg"
    }
},

* 试着进行一次提交
>> $ git add .<br>
   $ git cz
cz-cli@3.0.5, cz-conventional-changelog@2.1.0
Line 1 will be cropped at 100 characters. All other lines will be wrapped after 100 characters.
? Select the type of change that you're committing: feat:     A new feature
? What is the scope of this change (e.g. component or file name)? (press enter to skip)
? Write a short, imperative tense description of the change:
 infrastructure
? Provide a longer description of the change: (press enter to skip)
? Are there any breaking changes? No
? Does this change affect any open issues? No
husky > pre-commit (node v10.14.0)
yarn run v1.12.3
$ run-s style:**
$ prettier --write "src/**/*.ts"
src\index.ts 228ms
$ tslint -p tsconfig.json -c tslint.json
Done in 3.01s.
husky > commit-msg (node v10.14.0)
[master 5c75d75] feat: infrastructure
 6 files changed, 2666 insertions(+)
 create mode 100644 package-lock.json
 create mode 100644 package.json
 create mode 100644 src/index.ts
 create mode 100644 tsconfig.json
 create mode 100644 tslint.json
 create mode 100644 yarn.lock

* 确实在提交前格式化了所有代码文件，并且进行了 commit 检查，至此我们的团队协作项目就搭建好了，再也不用担心五花八门的代码格式和 commit 提交说明了。
