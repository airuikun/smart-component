## [English](./README.md) | 简体中文

# React加载vue的微前端组件
##### &emsp;&emsp;基于single-spa的react微前端组件
预计single-spa原理，实现react项目加载vue组件

# demo 体验
```
git clone git@github.com
cd mircro-tech
npm install
npm run start
```


# 使用方式
```js
npm install --save micro-tech
```
```js
  /** 加载远程组件 **/
  import React from 'react';
  import VueFrame from 'micro-tech';
  const Test = () => (
    <div>
      <VueIFrame jsurl="http://originPath/vueComponent.umd.js"/>
    </div>
  )
```
```js
  /** 加载本地组件 **/
  import React from 'react';
  import VueFrame from 'micro-tech';
  import VueComponent from './vueComponent.vue';
  const Test = () => (
    <div>
      <VueFrame componet={VueComponent} />
    </div>
  )
```
```js
  /** 也可以加载一个远程的react组件 **/
  import React from 'react';
  import { ReactFrame } from 'micro-tech';
  const Test = () => (
    <div>
      <ReactFrame jsurl="http://reactComponentAddress.umd.js" />
    </div>
  )
```
&emsp;&emsp;注意: 组件的开发请务必使用**umd规范**。
&emsp;&emsp;建议使用vue-cli提供的<a href="https://cli.vuejs.org/zh/guide/build-targets.html#%E5%BA%94%E7%94%A8" target="_blank">构建库</a>的功能</br>
&emsp;&emsp;或者可以使用<a href="https://github.com/y805939188/elf-cli" target="_blank">elf-cli</a>简单地创建一个符合umd规范的react组件
</br>

# 参数
只有jsurl或component属性是必须的, 其他参数均可选
| 参数名 | 类型 | 是否必须 |功能 |
|:-|:-|:-|:-|
| jsurl | string | jsurl和component必须二选一 | 远程组件的地址 |
| component | VueComponent | component和jsurl必须二选一 | 本地vue组件 |
| extraProps | Object | 非必须 | 传递给组件的属性 |
| visible | boolean | 非必须, 默认是true | 是否显示组件 |
| cssurl | string | 非必须 | 远程css的地址, 如果自己确定这个地址有css文件可以使用该属性 |
| name | string | 非必须 | 远程vue组件的名字 |
| loadType | 'xhr' 或 'script' | 非必须, 默认是script | 加载远程组件的方式, 使用xhr有跨域风险, 当存在跨域风险时候会强制使用script方式加载 |

# Feature
- [x] 加载远程vue或react组件
- [x] 加载本地vue组件
- [x] 跨域加载
- [x] 静态资源加载
- [x] css样式隔离

# TODO
- [ ] 调研是否可基于 qiankun2.x 重构
- [ ] 加载整个 vue/react 应用
- [ ] frame 组件外渲染 (由于当前设计类似于原生 iframe 标签，所以无法做到组件范围外的渲染)
- [ ] frame 组件与外部通信的pipeline (由于当前设计类似原生 iframe 标签，只能接受外部参数，但是没有专门用来和外部其他组件通信的 pipeline，这对于组件外部需要状态管理等场景很有必要)

# 可能存在的问题
1. 样式隔离使用的是shadow dom的方式, 所以暂时不支持ie
2. 静态资源在不做任何处理的情况下只支持通过src属性加载的资源, 比如image啥的, 对于类似tty之类的js无法处理的静态资源, 可以通过提前约定publicPath的方式加载。比如:
```js
/** vue-cli的vue.config.js */
const ASSET_PATH = '__WILL_BE_REPLACED_PUBLIC_PATH__';

function PublicPathWebpackPlugin () {}

PublicPathWebpackPlugin.prototype.apply = function (compiler) {
  compiler.hooks.beforeRun.tap('PublicPathWebpackPlugin', (compiler) => {
    compiler.options.output.publicPath = ASSET_PATH;
  })
}

module.exports = {
  publicPath: ASSET_PATH,
  configureWebpack (config) {
    config.plugins.unshift(new PublicPathWebpackPlugin());
  },
}
/**
 * react-vue-micro-frame 组件内部会替换掉提前约定好 ASSET_PATH 以此来加载那些非src属性加载的静态资源
 */

```
3. vue-cli提供的构件库的方式默认是会将css文件单独提取出来, 可以通过cssurl属性加载远程的css, 也可以通过inline的方式把css放进js文件里:
```js
// vue-cli的vue.config.js
module.exports = {
  css: {
    extract: false,
  },
}
```
4. 请大佬们多提bug, 感激不尽(方便的话顺便点个星星吧, 谢谢大佬们!)
