# Typescript best practice

结合工程实践探讨Typescript best practice, 提升代码质量。[TypeScript 官网](https://www.typescriptlang.org/)

## Contribute
欢迎各路大神批评、指正、补充

## 适合人群
掌握一定typescript，写过typescript 代码，并且想要提升代码robustness， consistency， readability.

## 正文

### 使用strict模式
在typescript中，我们需要提供变量的类型定义，正因为有了类型定义，typescript才可以变得比javascript更加强大。通过参数的类型的具体描述，我们可以轻易地理解一个没见过的函数的使用方法。还可以在编译阶段进行类型检查，发现类型错误。因此，如果我们不定义变量的类型，那typescript和javascript也就几乎没有区别了。 :laughing:
在/tsconfig.json文件中的compilerOptions中添加(默认也是true)
```
"strict": true,
```
这样的话，如果缺乏变量的类型定义，编译器会报错。


### 尽量不使用any
如果使用any作为变量类型，typescript就不会检查赋给该变量的值的类型，体现不出typescript的优势。 但有些时候我们不可避免地要使用any，尤其是当我们把javascript逐渐迁移到typescript的过程中，any支持了原来的javascript代码，是我们可以逐渐添加类型的定义。再或者我们从第三方API收到结果，但我们并不知道其具体类型，any可以帮助我们取消对它的类型检查。

### 使用tslint, automatic linting with webpack
如果你使用webpack作为bundler的话，那我们可以在其中设置linting。首先在/tslint.json中添加你想要的linting规则，例如：
```
"no-unused-variable": true,
"jsx-space-before-trailing-slash": true,
```
更多规则详见：https://palantir.github.io/tslint/rules/

然后我们需要在webpack中的rules中加入
```
{
  test: /\.tsx?$/,
  enforce: 'pre',
  use: [
    {
      loader: 'tslint-loader',
      options: {
        configFile: 'tslint.json',
        emitErrors: true,
      }
    }
  ]
},
```
如此一来，在编译的时候编译器就可以发现不符合定义的规则的代码并报错，有助于增强代码的一致性和可读性。

### 类型推导（type inference）
对于primitive类型的变量，可以直接给它赋值，不需要定义其类型，typescript会推导出来。例如：
```
let strValue = 'welcome';
```
而无需
```
let strValue: string = 'welcome';
```

### 使用arrow function
arrow function可以自动推导出this

