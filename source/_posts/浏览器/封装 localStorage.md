---
title: 封装 localStorage
date: 2020-07-23 16:09:37
categories:
- 浏览器
comments: true
---

## 前言

简单封装 localStorage，支持传过期时间、自动转换类型（对象、数组）

<!-- more -->



## 封装代码

### `localStorage.ts`

```typescript
localStorage.ts
// const QUOTA_EXCEEDED_ERR_CODE: number = 22;
interface itemObj {
  name: string;
  value: string;
  expires: string;
  startTime: number;
}

export default {
  // 设置缓存
  setItem(params: any) {
    const obj: itemObj = {
      name: '',
      value: '',
      expires: '',
      startTime: new Date().getTime(), // 记录何时将值存入缓存，毫秒级
    };
    const options: any = {};
    // 将obj和传进来的params合并
    Object.assign(options, obj, params);
    if (options.expires) {
      // 如果options.expires设置了的话
      // 以options.name为key，options为值放进去
      try {
        localStorage.setItem(options.name, JSON.stringify(options));
      } catch (e) {
        // if (e.code === QUOTA_EXCEEDED_ERR_CODE) {
        localStorage.clear();
        localStorage.setItem(options.name, JSON.stringify(options));
        // }
      }
    } else {
      // 如果options.expires没有设置，就判断一下value的类型
      const type: string = Object.prototype.toString.call(options.value);
      // 如果value是对象或者数组对象的类型，就先用JSON.stringify转一下，再存进去
      if (type === '[object Object]') {
        options.value = JSON.stringify(options.value);
      }
      if (type === '[object Array]') {
        options.value = JSON.stringify(options.value);
      }
      try {
        localStorage.setItem(options.name, options.value);
      } catch (e) {
        // if (e.code === QUOTA_EXCEEDED_ERR_CODE) {
        localStorage.clear();
        localStorage.setItem(options.name, options.value);
        // }
      }
    }
  },
  // 拿到缓存
  getItem(name: string) {
    let item: any = localStorage.getItem(name);
    // 先将拿到的试着进行json转为对象的形式
    try {
      item = JSON.parse(item);
    } catch (error) {
      // 如果不行就不是json的字符串，就直接返回
      item = item;
    }
    // 如果有startTime的值，说明设置了失效时间
    if (item) {
      if (item.startTime) {
        const date: number = new Date().getTime();
        // 何时将值取出减去刚存入的时间，与item.expires比较，如果大于就是过期了，如果小于或等于就还没过期
        if (date - item.startTime > item.expires) {
          // 缓存过期，清除缓存，返回false
          localStorage.removeItem(name);
          return false;
        }
        // 缓存未过期，返回值
        return item.value;
      }
      // 如果没有设置失效时间，直接返回值
      return item;
    }
    return false;
  },
  // 移出缓存
  removeItem(name: string) {
    localStorage.removeItem(name);
  },
  // 移出全部缓存
  clear() {
    localStorage.clear();
  },
};
```



### `localStorage.test.js`

```js
import testLocalStorage from '../src/localStorage.ts';

describe('getLocalStorage', () => {
  afterAll(() => {
    window.localStorage.clear();
  });
  test('无入参', () => {
    const value = testLocalStorage.getItem();
    expect(value).toBeFalsy();
  });
  test('有入参，取的到值', () => {
    window.localStorage.setItem('foo', 'bar');
    const value = testLocalStorage.getItem('foo');
    expect(value).toBe('bar');
  });
});

describe('setLocalStorage', () => {
  afterAll(() => {
    window.localStorage.clear();
    // 清除缓存、mock
    jest.resetModules();
    jest.restoreAllMocks();
  });
  test('无value，取不到值', () => {
    const value = testLocalStorage.setItem({
      name: 'foo',
      value: '',
    });
    expect(value).toBeUndefined();
  });
  test('有value，value为字符串，取得到值', () => {
    testLocalStorage.setItem({
      name: 'foo',
      value: 'bar',
    });
    expect(testLocalStorage.getItem('foo')).toBe('bar');
  });
  test('有value，value为对象，取得到值', () => {
    testLocalStorage.setItem({
      name: 'foo',
      value: {
        a: 'a',
        b: 'b',
      },
    });
    expect(testLocalStorage.getItem('foo')).toEqual({
      a: 'a',
      b: 'b',
    });
  });
  test('有value，value为数组，取得到值', () => {
    testLocalStorage.setItem({
      name: 'foo',
      value: ['a', 'b'],
    });
    expect(testLocalStorage.getItem('foo')).toEqual(['a', 'b']);
  });
  test('有value，value为字符串，有过期时间，时间过期前取得到值', (done) => {
    jest.useRealTimers();
    testLocalStorage.setItem({
      name: 'foo',
      value: 'bar',
      expires: 2000,
    });
    setTimeout(function () {
      expect(testLocalStorage.getItem('foo')).toBe('bar');
      done();
    }, 1000);
  });
  test('有value，value为字符串，有过期时间，时间过期后取不到值', (done) => {
    jest.useRealTimers();
    testLocalStorage.setItem({
      name: 'foo',
      value: 'bar',
      expires: 1000,
    });
    setTimeout(function () {
      expect(testLocalStorage.getItem('foo')).toBeFalsy();
      done();
    }, 2000);
  });
  test('set溢出 ，catch', () => {
    const setItem = jest.spyOn(Storage.prototype, 'setItem').mockImplementationOnce(() => {
      throw new Error({
        name: '溢出',
        code: 22,
        message: '溢出',
      });
    });
    const clear = jest.spyOn(Storage.prototype, 'clear').mockImplementationOnce(() => {});
    const testLocalStorage = require('../src/localStorage.ts').default;
    testLocalStorage.setItem('foo333', {
      a: 'a',
    });
    expect(clear).toHaveBeenCalled();
  });
  test('set溢出，有expires ，catch', () => {
    const setItem = jest.spyOn(Storage.prototype, 'setItem').mockImplementationOnce(() => {
      throw new Error({
        name: '溢出',
        code: 22,
        message: '溢出',
      });
    });
    const clear = jest.spyOn(Storage.prototype, 'clear').mockImplementationOnce(() => {});
    const testLocalStorage = require('../src/localStorage.ts').default;
    testLocalStorage.setItem({
      name: 'foo',
      value: 'bar',
      expires: 1000,
    });
    expect(clear).toHaveBeenCalled();
  });
});

describe('removeLocalStorage', () => {
  beforeEach(() => {
    window.localStorage.setItem('foo', 'bar');
  });
  afterEach(() => {
    window.localStorage.removeItem('foo');
  });
  afterAll(() => {
    window.localStorage.clear();
  });
  test('无入参', () => {
    testLocalStorage.removeItem();
    const value = window.localStorage.getItem('foo');
    expect(value).toBe('bar');
  });
  test('有入参，能删除', () => {
    window.localStorage.setItem('foo2', 'bar2');
    testLocalStorage.removeItem('foo2');
    expect(window.localStorage.getItem('foo2')).toBeNull();
  });
});

describe('clearLocalStorage', () => {
  test('有入参，能清空', () => {
    window.localStorage.setItem('foo2', 'bar2');
    testLocalStorage.clear();
    expect(window.localStorage.getItem('foo2')).toBeNull();
  });
});
```

