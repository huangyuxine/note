在 JavaScript 中，`Proxy` 是一种用于定义自定义行为的机制，它允许你创建一个对象的代理，从而拦截和定制对目标对象的操作。`Proxy` 使得你可以在对象的操作（如读取、写入、删除属性等）上添加自定义逻辑。

例子

```javascript
  const validator = {
      set: function(target, property, value) {
          if (property === 'age') {
              if (!Number.isInteger(value) || value <= 0) {
                  throw new Error('设置错误');
              }
          }
          target[property] = value;
          return true;
      }
  };

  const person = new Proxy({}, validator);
  person.age = 30; // 正确
  console.log(person.age); // 30
  try {
      person.age = -5;
  } catch (error) {
      console.log(error)
  }
```

例子

```javascript
  var message = {
      code: 200,
      msg: "success"
  }

  const p1 = new Proxy(message, {
      set: function (message, property, value) {
          console.log('set调用')
          return Reflect.set(...arguments);
      },
      get: function(message, property, receiver) {
          console.log(`get调用`);
          return Reflect.get(...arguments);
      },
  })
  // 设置
  p1.code = 400
  // 获取
  console.log(p1.code)
  console.log(p1.msg)
```

