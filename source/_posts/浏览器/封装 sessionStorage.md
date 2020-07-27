---
title: 封装 sessionStorage
date: 2020-07-27 18:21:37
categories:
- 浏览器
comments: true
---

## 前言

简单封装 sessionStorage

<!-- more -->



## 封装代码

### `sessionStorage.ts`

```typescript
const QUOTA_EXCEEDED_ERR_CODE: number = 22;

export default {
  setItem(key, value) {
    try {
      sessionStorage.setItem(key, JSON.stringify(value));
    } catch (e) {
      // if (e.code === QUOTA_EXCEEDED_ERR_CODE) {
      sessionStorage.clear();
      sessionStorage.setItem(key, JSON.stringify(value));
      // }
    }
  },
  getItem(key) {
    const value = sessionStorage.getItem(key);
    return JSON.parse(value);
  },
};
```



### `sessionStorage.test.js`


```js
import testSessionStorage from '../src/sessionStorage.ts';

describe('getSessionStorage', () => {
  afterAll(() => {
    window.sessionStorage.clear();
  });
  test('无入参', () => {
    const value = testSessionStorage.getItem();
    expect(value).toBeFalsy();
  });
  test('有入参，入参为字符串，取的到值', () => {
    window.sessionStorage.setItem('foo', JSON.stringify('bar'));
    const value = testSessionStorage.getItem('foo');
    expect(value).toBe('bar');
  });
  test('有入参，入参为对象，取的到值', () => {
    window.sessionStorage.setItem('foo', JSON.stringify({ a: 'b' }));
    const value = testSessionStorage.getItem('foo');
    expect(value).toEqual({
      a: 'b',
    });
  });
});

describe('setSessionStorage', () => {
  afterAll(() => {
    window.sessionStorage.clear();
    jest.resetModules();
    jest.restoreAllMocks();
  });
  test('无value，取不到值', () => {
    const value = testSessionStorage.setItem('foo', '');
    expect(value).toBeUndefined();
  });
  test('有value，value为字符串，取得到值', () => {
    testSessionStorage.setItem('foo', 'bar');
    expect(JSON.parse(window.sessionStorage.getItem('foo'))).toBe('bar');
  });
  test('有value，value为对象，取得到值', () => {
    testSessionStorage.setItem('foo', {
      a: 'a',
    });
    expect(JSON.parse(window.sessionStorage.getItem('foo'))).toEqual({
      a: 'a',
    });
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
    const testSessionStorage = require('../src/sessionStorage.ts').default;
    testSessionStorage.setItem('foo333', {
      a: 'a',
    });
    expect(clear).toHaveBeenCalled();
  });
});
```