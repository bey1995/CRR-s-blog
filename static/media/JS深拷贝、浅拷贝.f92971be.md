内容学习自1,[https://juejin.im/post/5b20c9f65188257d7d719c1c](https://juejin.im/post/5b20c9f65188257d7d719c1c)
2,https://segmentfault.com/a/1190000008637489
3,https://segmentfault.com/a/1190000016149265
4,https://juejin.im/post/59ac1c4ef265da248e75892b

## 为什么要有深浅拷贝？
 - obj1：原始数据
 - obj2：赋值操作得到
 - obj3：浅拷贝得到

**改变基本类型**时，赋值得到的对象**obj2**同时也会改变原始值 **obj1**，而改变浅拷贝得到的 对象**obj3** 则不会改变原始对象 **obj1**。

这说明赋值得到的对象 obj2 只是将指针改变，其引用的仍然是同一个对象，而浅拷贝得到的的 obj3 则是重新创建了新对象。

**改变引用类型**时，无论是修改赋值得到的对象 **obj2** 和浅拷贝得到的 **obj3** 都会改变原始数据**obj1**。

浅拷贝只复制一层对象的属性，并不包括对象里面的为引用类型的数据。所以就会出现改变浅拷贝得到的 obj3 中的引用类型时，会使原始数据得到改变。

深拷贝是对对象以及对象的所有子对象进行拷贝。

## 浅拷贝
拷贝原对象的实例，但是对其内部的引用类型值，拷贝的是其引用，常用的就是如jquey中的$.extend({}, obj);
jQuery.extend第一个参数可以是布尔值，用来设置是否深度拷贝的`jQuery.extend(true, { a : { a : "a" } }, { a : { b : "b" } } );`

JS原生不支持深拷贝，Object.assign 和 {…obj}属于浅拷贝。
例如：

```
let copy_obj = Object.assgin({},obj);
let copy_obj = {...obj};

```

**拷贝原对象的引用**。
Array.prototype.slice()和Array.prototype.concat()都会返回一个数组或者对象的浅拷贝。
```
// 浅拷贝实现，仅供参考
function shallowClone(source) {
   //如果原对象不存在
    if (!source || typeof source !== 'object') {
        throw new Error('error arguments');
    }
    //如果原对象的constructor是数组，那么数组[]，否则对象{}
    var targetObj = source.constructor === Array ? [] : {};
    for (var keys in source) {
        if (source.hasOwnProperty(keys)) {
            targetObj[keys] = source[keys];
        }
    }
    return targetObj;
}
```
## 深拷贝
当遇到，对象里面嵌套一个对象的时候，就需要用到深拷贝。

1，JSON.stringify和JSON.parse，原理是将对象转化为字符串，再通过JSON.parse重新建立一个对象。
局限是：

 - 不能复制**function、正则、Symbol**
 - 循环引用报错
 - 相同的引用会被重复复制

例如：

```
let obj = {         
    reg : /^asd$/,
    fun: function(){},
    syb:Symbol('foo'),
    asd:'asd'
}; 
let cp = JSON.parse(JSON.stringify(obj));
console.log(cp);
```
![result](https://img-blog.csdnimg.cn/20190407220707257.png)
该对象中的正则，函数，symbol都没被复制。
如果stringify传入一个循环引用的对象，会直接报错。

**针对以下代码：**
JSON实现深复制不能处理指向相同引用的情况，相同的引用会被重复复制。
```
    let obj   = {  id:'obj' };
    let obj2 = {  name:'obj2' };
    obj.ref1 = obj2;
    obj.ref2 = obj2;
    let cp = JSON.parse(JSON.stringify(obj));
    obj.ref1.name = 'change';
    cp.ref1.name  = 'change';
    console.log(obj,cp);
```
![](https://img-blog.csdnimg.cn/20190408141903107.png)
原对象中，ref1.name改变，ref2.name也会改变。

复制的对象cp中，ref1 和 ref2 分别指向了两个对象。复制对象没有保持和原对象一样的结构。因此，**JSON实现深复制不能处理指向相同引用的情况，相同的引用会被重复复制**。

## 手动实现深复制
对于简单类型，直接复制；对于引用类型，递归复制每个属性；

1.递归实现

```
function deepCopy(target){
       let res = obj instanceof Array ? [] : {};
       for(let key in obj) {
       //这一步之前可以加入一个判断，判断是否出现相同引用
           res[key] = obj[key];
           if(typeof obj[key] === Object) {
               deepCopy(obj[key])
           }
       }
       return res;
   }
```

ES7新语法：

```
function copyObject(orig) {
  return Object.create(
    Object.getPrototypeOf(orig),
    Object.getOwnPropertyDescriptors(orig)
  );
}
```
## jQuery.extend源码

```
jQuery.extend = jQuery.fn.extend = function() {
    var src, copyIsArray, copy, name, options, clone,
        target = arguments[0] || {},
        i = 1,
        length = arguments.length,
        deep = false;

    // Handle a deep copy situation
    if ( typeof target === "boolean" ) {
        deep = target;
        // skip the boolean and the target
        target = arguments[ i ] || {};
        i++;
    }
    // Handle case when target is a string or something (possible in deep copy)
    if ( typeof target !== "object" && !jQuery.isFunction(target) ) {
        target = {};
    }

    // extend jQuery itself if only one argument is passed
    if ( i === length ) {
        target = this;
        i--;
    }

    for ( ; i < length; i++ ) {
        // Only deal with non-null/undefined values
        if ( (options = arguments[ i ]) != null ) {
            // Extend the base object
            for ( name in options ) {
                src = target[ name ];
                copy = options[ name ];

                // Prevent never-ending loop
                if ( target === copy ) {
                    continue;
                }

                // Recurse if we're merging plain objects or arrays
                if ( deep && copy && ( jQuery.isPlainObject(copy) || (copyIsArray = jQuery.isArray(copy)) ) ) {
                    if ( copyIsArray ) {
                        copyIsArray = false;
                        clone = src && jQuery.isArray(src) ? src : [];

                    } else {
                        clone = src && jQuery.isPlainObject(src) ? src : {};
                    }

                    // Never move original objects, clone them
                    target[ name ] = jQuery.extend( deep, clone, copy );

                // Don't bring in undefined values
                } else if ( copy !== undefined ) {
                    target[ name ] = copy;
                }
            }
        }
    }

    // Return the modified object
    return target;
};
```
## 区别：![表格](https://img-blog.csdnimg.cn/20190408162301343.png)


