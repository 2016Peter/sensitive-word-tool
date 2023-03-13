# sensitive-word-tool

![npm](https://img.shields.io/npm/v/sensitive-word-tool)
![npm](https://img.shields.io/npm/dm/sensitive-word-tool)
![npm bundle size](https://img.shields.io/bundlephobia/minzip/sensitive-word-tool)
![NPM](https://img.shields.io/npm/l/sensitive-word-tool)

基于 DFA 算法实现，支持过滤掉干扰词的基础上处理敏感词，非常轻巧完备的 JavaScript 敏感词处理库🚀🚀🚀

## 说明

本库是一个处理敏感词的工具库，所以不会提供默认的敏感词。如果需要的话可以[参考这里](https://github.com/fwwdn/sensitive-stop-words)。

## 性能

以下测试均在本地进行，在生产环境将会更快一些。

测试字符串为随机生成的汉字、字母、数字。 在 20000 个随机敏感词构建的树下进行测试，每组测试 5 次取平均值。

| 字符串长度    | 实例化时间  |  `verify`   | `match`     |  `filter`  |
| ----------- |----------- | ----------- | ----------- | -----------|
| 1000        |   < 65ms   |  < 0.7ms    |  < 1.25ms   |  < 1.35ms  |
| 5000        |   < 65ms   |  < 0.75ms   |  < 10ms     |  < 9.5ms   |
| 10000       |   < 65ms   |  < 1.5ms    |  < 13.5ms   |  < 15ms    |
| 20000       |   < 65ms   |  < 1.5ms    |  < 22ms     |  < 24ms    |

## 安装

- 使用 npm

```sh
npm install sensitive-word-tool
```

- 使用 yarn

```sh
yarn add sensitive-word-tool
```

- 使用 pnpm

```sh
pnpm add sensitive-word-tool
```

## 使用

### 导入包

- CommonJS 导入

```js
const { SensitiveWordTool } = require('sensitive-word-tool')
```

- ESModule 导入

```js
import SensitiveWordTool from 'sensitive-word-tool'
```

### 进行敏感词检测

- 基础用法

```ts
import SensitiveWordTool from 'sensitive-word-tool'

const sensitiveWordTool = new SensitiveWordTool()
sensitiveWordTool.addWords(['王八蛋', '王八羔子', '测试', '江南皮革厂'])

// 《》()属于干扰词，会处理掉后再对敏感词进行处理
sensitiveWordTool.match('浙江温州，江南《皮革厂》老板王(八)蛋，带着小姨子跑了') // ['江南皮革厂', '王八蛋']
sensitiveWordTool.verify('浙江温州，江南《皮革厂》老板王(八)蛋，带着小姨子跑了') // true
sensitiveWordTool.filter('浙江温州，江南《皮革厂》老板王(八)蛋，带着小姨子跑了') // 浙江温州，**(***)老板*(*)*，带着小姨子跑了

sensitiveWordTool.match('皮革厂老板带着小姨子跑了') // []
sensitiveWordTool.verify('皮革厂老板带着小姨子跑了') // false
sensitiveWordTool.filter('皮革厂老板带着小姨子跑了') // 皮革厂老板带着小姨子跑了
```

- 进阶用法

```ts
// 初始化时设置敏感词
const sensitiveWordTool = new SensitiveWordTool({ wordList: ['王八蛋', '王八羔子', '测试', '江南皮革厂'] })

// 支持继续增加敏感词
sensitiveWordTool.addWords(['小姨子'])
sensitiveWordTool.match('江南皮革厂老板带着小姨子跑了')  // ['江南皮革厂', '小姨子']


// 支持清空当前的敏感词
sensitiveWordTool.clearWords()
sensitiveWordTool.addWords(['江南皮革厂'])
sensitiveWordTool.match('江南皮革厂老板带着小姨子跑了')  // ['江南皮革厂']


// 支持主动设置干扰词（不设置将使用默认干扰词），敏感词检测时会将文本中的干扰词删除再匹配
sensitiveWordTool.setNoiseWords(' $')
sensitiveWordTool.match('浙江温州，江南 皮革$厂老板王$八&蛋，带着小姨子跑了')  // ['江南皮革厂']
```

## API

### `构造函数`

##### 示例

```ts
const sensitiveWordTool = new SensitiveWordTool({
  wordList: ['王八蛋', '王八羔子', '测试', '江南皮革厂'],
  noiseWords: ' $'
})
```

##### 参数

- `wordList`: 可选。用于设置初始的敏感词。默认值：`[]`
- `noiseWords`: 可选。用于设置干扰词，敏感词检测时会将待检测文本中的干扰词删除后再匹配。默认值：

```
 \t\r\n~!@#$%^&*()_+-=【】、{}|;\':"，。、《》？αβγδεζηθικλμνξοπρστυφχψωΑΒΓΔΕΖΗΘΙΚΛΜΝΞΟΠΡΣΤΥΦΧΨΩ。，、；：？！…—·ˉ¨‘’“”々～‖∶＂＇｀｜〃〔〕〈〉《》「」『』．〖〗【】（）［］｛｝ⅠⅡⅢⅣⅤⅥⅦⅧⅨⅩⅪⅫ⒈⒉⒊⒋⒌⒍⒎⒏⒐⒑⒒⒓⒔⒕⒖⒗⒘⒙⒚⒛㈠㈡㈢㈣㈤㈥㈦㈧㈨㈩①②③④⑤⑥⑦⑧⑨⑩⑴⑵⑶⑷⑸⑹⑺⑻⑼⑽⑾⑿⒀⒁⒂⒃⒄⒅⒆⒇≈≡≠＝≤≥＜＞≮≯∷±＋－×÷／∫∮∝∞∧∨∑∏∪∩∈∵∴⊥∥∠⌒⊙≌∽√§№☆★○●◎◇◆□℃‰€■△▲※→←↑↓〓¤°＃＆＠＼︿＿￣―♂♀┌┍┎┐┑┒┓─┄┈├┝┞┟┠┡┢┣│┆┊┬┭┮┯┰┱┲┳┼┽┾┿╀╁╂╃└┕┖┗┘┙┚┛━┅┉┤┥┦┧┨┩┪┫┃┇┋┴┵┶┷┸┹┺┻╋╊╉╈╇╆╅╄
```

### `.setNoiseWords`

设置干扰词。敏感词检测时会将待检测文本中的干扰词过滤掉再匹配。

##### 示例

```ts
const sensitiveWordTool = new SensitiveWordTool()
sensitiveWordTool.setNoiseWords(' $')
```

### `.clearWords`

清空当前设置的所有敏感词。

##### 示例

```ts
const sensitiveWordTool = new SensitiveWordTool()
sensitiveWordTool.clearWords()
```

### `.addWords`

继续增加敏感词。

##### 示例

```ts
const sensitiveWordTool = new SensitiveWordTool()
sensitiveWordTool.addWords(['王八蛋', '王八羔子', '测试', '江南皮革厂'])
```

### `.match`

从文本中匹配出所有出现过的敏感词。返回匹配到的敏感词数组，如未匹配则返回空数组。

##### 示例

```ts
const sensitiveWordTool = new SensitiveWordTool()
sensitiveWordTool.addWords(['王八蛋', '王八羔子', '测试', '江南皮革厂'])
sensitiveWordTool.match('浙江温州，江南《皮革厂》老板王(八)蛋，带着小姨子跑了') // ['江南皮革厂', '王八蛋']
```

### `.verify`

检测文本中是否出现了敏感词。返回 `true` or `false`

##### 示例

```ts
const sensitiveWordTool = new SensitiveWordTool()
sensitiveWordTool.addWords(['王八蛋', '王八羔子', '测试', '江南皮革厂'])
sensitiveWordTool.verify('浙江温州，江南《皮革厂》老板王(八)蛋，带着小姨子跑了') // true
```

### `.filter`

替换掉文本中出现的敏感词。

##### 示例

```ts
const sensitiveWordTool = new SensitiveWordTool()
sensitiveWordTool.addWords(['王八蛋', '王八羔子', '测试', '江南皮革厂'])
sensitiveWordTool.filter('浙江温州，江南《皮革厂》老板王(八)蛋，带着小姨子跑了', '*') // 浙江温州，**《***》老板*(*)*，带着小姨子跑了
```

##### 参数

```ts
sensitiveWordTool.filter(content)
sensitiveWordTool.filter(content, filterChar)
```

- `content`: 待匹配文本内容
- `filterChar`: 敏感词替代符，默认为`*`

## TODOs

- ✅ 增加单元测试
- ✅ 打包代码压缩
- [ ] 支持 CI
- ✅ 完善 readme 文档
- [ ] 性能测试
- ✅ 支持配置干扰词: `setNoiseWords`
- ✅ 支持对敏感词进行过滤替代： `filter`
- ✅ 支持校验文本中是否有敏感词： `verify`
