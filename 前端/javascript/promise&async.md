# Promise

Promises 是 JavaScript 中处理异步操作的一种机制。它们提供了一种更清晰和结构化的方式来处理异步操作，而不是传统的回调函数。

## 基本概念

一个 Promise 代表一个尚未完成但将来会完成的操作。Promise 对象有三个状态：

1. **Pending (待定)**: 初始状态，操作尚未完成。
2. **Fulfilled (已完成)**: 操作成功完成。
3. **Rejected (已拒绝)**: 操作失败。

## 例子

单个Promise

```javascript
  var p1 = new Promise((resolve, reject) => {
      // resolve('任务1')
      reject('任务1')
  })
  p1.then(data => {
      console.log(data, '成功')
  }).catch(data => {
      console.log(data, '失败')
  })
```

多个Promise

```javascript
  var p1 = new Promise((resolve, reject) => {
      resolve('任务1') // 成功
      // reject('任务1')
  })
  p1.then(data => {
      console.log(data, '成功1')
      return new Promise((resolve, reject) => {
          resolve('任务2') // 成功
      })
  })
  .then(data => {
      console.log(data, '成功2')
  })
  .catch(data => {
      console.log(data, '失败')
  })
```

多个Promise处理错误

```javascript
  var p1 = new Promise((resolve, reject) => {
      // resolve('任务1')
      reject('任务1')
  })
  p1.then(data => {
      console.log(data, '成功1')
      return new Promise((resolve, reject) => {
          resolve('任务2');
      })
  }, err => {
      console.log(err, '失败1')
      // 任务1失败，任务2没有执行下去的必要，继续执行成功拿不到data，这里得抛出错误，这样任务2中可以捕获到
      throw new Error('任务1失败')
  })
  .then(data => {
      // 任务1失败，任务2成功拿不到data
      console.log(data, '成功2')
  },err => {
      console.log('任务2失败')
  })
```

# Async 

`async` 和 `await` 是处理异步操作的现代语法糖，使得异步代码看起来更像同步代码。`async` 函数总是返回一个 Promise，`await` 用于等待一个 Promise 完成。

```javascript
  function asyncTask() {
      return new Promise((resolve, reject) => {
          var isSuccess = false
          if (isSuccess) {
              resolve('成功')
          } else {
              reject('失败')
          }
      })
  }
  async function main() {
      console.log('执行:')
      try {
          var result = await asyncTask()
          // TODO
      } catch (error) {
          console.log(error)
      }
      console.log('结束')
  }
  main();
```