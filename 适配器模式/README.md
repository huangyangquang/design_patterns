# 适配器模式

适配器模式，主要的用处就是为了抹平接口差异，统一接口。😊

举个例子：  
在不同端上，有同一个功能，都是去同一件事情。

但是，不同端上的接口调用方式不一样的：
1. 参数不一样
2. 返回值不一样
3. 其他...

那么，这个时候，需要做的就是将接口进行统一，使得其他开发者在调用这些接口时，可以不用去考虑接口差异的问题，直接调用，传入统一的参数既可以

这就是适配器的任务所在，抹平接口差异，统一接口。

实际上，我们在日常开发的时候，最常听到的就是：`我需要适配下这个场景`, `我需要适配下这种情况`等，这些实际上都是会使用到设配器。

举个例子：  
模块化导入导出方式有很多种，比如有ES6 Modules, CommonJs，CMD, AMD等。

每一个项目的定位不一样，可能使用的模块化方式也不同，所以，一些第三方工具库（npm包），为了抹平使用不同模块化的导入导出的差异，就需要做好兼容性工作，将接口进行统一。

使得开发的第三方工具可以使用各种方式进行导入导出。

有一个第三方库：`classnames`
在导出时，为了兼容不同模块化，就做了适配。  
我们一起来看下源码：
```
if (typeof module !== 'undefined' && module.exports) { // commonJs导入
    classNames.default = classNames;
    module.exports = classNames;

} else if (typeof define === 'function' && typeof define.amd === 'object' && define.amd) { // amd导入
    // register as 'classnames', consistent with npm package name
    define('classnames', [], function () {
        return classNames;
    });

} else {
    window.classNames = classNames; // 浏览器导入
}
```

实际上，做法也是比较简单的：
就是去判断不同环境下，是否存在该环境下的一些特有的关键字：

比如：  
node环境，是使用commonJs实现的，使用可以通过`module.exports`来进行判断.  
commonJs的导出方式就是`module.exports`

浏览器下存在`window`这个全局变量，所以在浏览器可以使用`window`来进行判断.
浏览器下的导出方式，源码中式将接口直接挂在`window`这个全局变量上

在日常开发中，适配器模式式比较常用的。  
因为前端开发一般情况下，一套代码可能会去兼容多个端的，在pc浏览器上，pc小程序，ipad小程序，移动端H5, app等。

所以在这些需要兼容的场景下，使用适配器模式就是比较常见的做法了。




































