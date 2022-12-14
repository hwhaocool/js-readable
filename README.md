# js-readable
把js代码转换为更加可读的模式


## 适合场景

> 转换出来的代码，可读性是没问题的；就是会过于直白，转换出来的代码会显得呆呆的

适合逆向阅读别人压缩后的代码的时候，先用其它解压工具解压缩，再用这个工具简化语法

## 功能
### 1. if  语句自动加大括号
```js
if(x)  c=2;
```
👇
```js
if(x) {
    c=2;
}
```
 
### 2. 多逗号表达式展开

```js
a=1,b=3,c=2;
```
👇
```js
a=1;
b=3;
c=2;
```

### 3. if 判断条件consequent展开

```js
if(a=1,b=2,c==3) {x=1}; 
```
👇
```js
a=1;
b=2; 
if(c==3) {
    x=1;
}
```

### 4. 复杂三元表达式展开为if


```js
1 - 1 ? aa = b + 4 : c + 5 ? cc = a + 1 : b + 2 ? ee = c + 3 : ff = 4;
```
👇
```js
if (1 - 1) {
  aa = b + 4;
} else {
  if (c + 5) {
    cc = a + 1;
  } else {
    if (b + 2) {
      ee = c + 3;
    } else {
      ff = 4;
    }
  }
}
```

### 5. 逻辑表达式 嵌套 三元表达式，展开为if


```js
a==1 && (b?c=2:d=4)
```
👇
```js
if (a == 1) {
  if (b) {
    c = 2;
  } else {
    d = 4;
  }
}
```

### 6. for 控制语句展开
```js
for (console.log("hei"), aa=1, r = 0; r < bb.length; r++) {
  
}
```
👇
```js
console.log("hei");
aa = 1;
for (r = 0; r < bb.length; r++) {

}
```



### 7. o O l L l1 oO0I1 变量替换


## 参考
https://evilrecluse.top/Babel-traverse-api-doc/#/
