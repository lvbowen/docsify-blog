# 手撕 Promise
Promise 有三个状态：pending（等待中）、resolved（已完成）、rejected（已失败）。执行 resolve() 或 reject() 函数会触发状态更改，当执行 then 函数时，如果此时状态还是 pending 中，则 then 的回调函数参数会在 resolve() 或 reject() 中执行，如果是其他状态，则直接在 then 函数中执行。

手写简易版 Promise
```js
const PENDING = 'pending'
const RESOLVED = 'resolved'
const REJECTED = 'rejected'

function MyPromise(fn) {
  const that = this
  that.state = PENDING
  that.value = null
  that.resolvedCallbacks = []
  that.rejectedCallbacks = []

  function resolve(value) {
    if (that.state === PENDING) {
        that.state = RESOLVED
        that.value = value
        that.resolvedCallbacks.map(cb => cb(that.value))
    }
  }

  function reject(value) {
    if (that.state === PENDING) {
        that.state = REJECTED
        that.value = value
        that.rejectedCallbacks.map(cb => cb(that.value))
    }
  }
  // 执行 fn 函数
  try {
    fn(resolve, reject)
  } catch (e) {
    reject(e)
  }
}

// then 函数
MyPromise.prototype.then = function(onFulfilled, onRejected) {
  const that = this
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
  onRejected =
    typeof onRejected === 'function'
      ? onRejected
      : r => {
          throw r
        }
  if (that.state === PENDING) {
    that.resolvedCallbacks.push(onFulfilled)
    that.rejectedCallbacks.push(onRejected)
  }
  if (that.state === RESOLVED) {
    onFulfilled(that.value)
  }
  if (that.state === REJECTED) {
    onRejected(that.value)
  }
}
```