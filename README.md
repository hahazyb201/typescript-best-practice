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

### redux store
当我们在typescript项目中使用redux store的时候，我们需要考虑如何组织redux的reducer，state，action等文件。一种我认为比较好的结构是对action进行分类。比如我们要写一个淘宝页面，它会有不同类型的action，比如验证API，用户信息API，搜索API等等，并且他们返回的结果会被存在redux state中。
```plain
/path/to/project
├── src
    ├── store
         ├── authentication
              ├── actions.ts
              ├── reducer.ts
              ├── state.ts
              ├── constants.ts
         ├── userInfo
         ├── searchProduct
         ├── store.ts

```
正如上面的文件结构，redux action按照其功能分类，并有各自的reducer和state，然后有一个总的store.ts，汇总所有reducer的值，供project下的所有文件使用。
对于诸如API call这样的异步action，包含有promise，我们需要使用在redux store中使用promiseMiddleware，这样就可以统一同步action和异步action。

### Opaque Types
在项目的很多函数之中，我们会用到string类型的变量，但typescript却无法区别这些变量的类型，因为他们都是string。可能导致的错误就是我们放错了参数，但由于都是string，typescript却不能报错。这里我们可以定义opaque type来区别这些string。例如
```
type Opaque<K, T> = T & { __TYPE__: K };
type serviceName = Opaque<"service", string>;
type inputARN = Opaque<"ARN", string>;

const validateARN = (service: serviceName, arn: inputARN) => { /*some validation logic*/ }
const service = getServiceName() as serviceName;
const arn = getARNfromInputBox() as inputARN;

validateARN(arn, service);
// Your IDE now throw an error
```

