---
title: JavaScript call by reference 的有趣例子
categories:
- JavaScript 
---
同事分享了一個有趣的問題，請問下面顯示出來的值會是多少？
```javascript
const obj1 = {
    obj2: {}
};
console.log(obj1.obj2.a);
console.log(obj1.obj2);
obj1.obj2.a = 1;
```
這個圖片是執行後的結果：
![](https://i.imgur.com/QuVzgCq.png)
第一個結果不意外是 `undefined`，但第二個結果居然能顯示出 `a` 的值，原因是 `obj1.obj2` 是 call by reference