# vue3 新特性
新特性：
- proxy 实现响应式机制
- 使用 ts
- 放弃 class 采用 function-based API
- 重构 complier
- 重构 virtual DOM  

>Vue3.0 依赖的是 Proxy 和 Reflect 这一对出生新时代的 CP，且无法被转译成 ES5 ，或者通过 Polyfill 提供兼容。开发者技术前线获悉的信息，官方在发布最终版本之前会做到兼容 IE11。

## function-based API
```
const App = { 
setup() { 
// data 
const count = value(0) 
// computed 
const plusOne = computed(() => count.value + 1)
 // method 
const increment = () => { count.value++ } 
// watch
 watch(() => count.value * 2, v => console.log(v)) 
// lifecycle 
onMounted(() => console.log('mounted!')) 
// 暴露给模版或渲染函数 
return { count } 
 } 
}
```
对比 class api 的好处是：
- 更好的 ts 类型推导支持
- 更灵活的逻辑复用能力
- Tree-shaking 友好
- 代码更容易被压缩