---
title: TODO BLOG
category: experience
author: Thomas
tag: 
- Javascript
---

## Class

- static

  - 静态方法中`this`指向类本身而非实例；
  - 静态方法可与非静态方法重名；
  - 父类静态方法可被子类继承；

- getter & setter

  - 用于对某个属性设置存值函数和取值函数，拦截该属性的存取行为；

  - 'get' & 'set' all in Descriptor;

  - 

    ```javascript
    set a(a) {
      this.a = a; // that will cause call maximum stack size error
      // should use this._a = a;
    }
    ```

  - getter 不能单独出现， getter与setter必须同级出现

- `__proto__`

  - 不建议使用，因为对当前实例`__proto__`属性操作会改变类本身，进而影响所有实例。

- constructor

  - `constructor()` 类默认方法，new实例时自动调用该方法，如果没有显示调用，会被默认添加。
  - 子类可以调用父类构造方法，`super()` 可将子类参数提供给父类构造函数作为参数。

- 类内部定义所有方法是不可枚举的，但可通过`Object.getOwnPropertyNames(Class.prototype)` 或 `Object.getOwnPropertyDescriptors(Class.prototype)`

- super

  - super 关键字在constructor中使用，需在this之前使用，只能调用一次
  - 可用来调用父类函数 `super.staticFnInParentClass()`
  - 在普通方法中，super指向父类的原型对象，在静态方法中super指向父类

- extends

  - 子类必须在constructor中调用super方法，负责实例时会报错，因为子类的this对象，需要先通过父类构造函数构造完成，得到与父类同样的实例属性和方法，在此基础上在加上子类方法与实例属性，如果不调用super方法，子类得不到this对象，会报ReferenceError

  - ES5的继承，实质是先创建子类的实例对象this，再将父类方法添加到this上（`Parent.apply(this)`）

    - `Child.call(this, prop)`

    - ```js
      var prototype = object(child.prototype);
      prototype.constructor = Child;
      Child.prototype = prototype;
      ```

- Class 不存在变量提升hoisting

## call / apply / bind

- `call` / `apply` 都是为了改变函数运行时的上下文而存在的，即为了改变函数内部this指向；
- js 存在 ’定义是上下文‘， ‘运行时上下文’，‘上下文可以改变’ 概念；
- call 与 apply 作用完全一样，只是接收参数方式不一样，call 需要按顺序一个个传进去，apply则需要把参数最为数组传入；
- `bind` 会创建一个新函数，不会立即执行； `apply`/`call` 会立即执行函数；
- `bind` 可分多次传入参数，`apply` & `call` 必须一次性传入；
- 应用场景：数组追加（apply）；获取最大最小；