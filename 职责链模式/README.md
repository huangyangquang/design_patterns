# 职责链模式

## 什么是职责链模式呢？
责任链模式（Chain of Responsibility Pattern）为**请求**创建了一个接收者对象的链。

使多个对象都有机会处理请求，从而避免请求的**发送者**和**接收者**之间的耦合关系。

从发送者和接收者的角度，可以看作是发送者和接收者之间的一种**通信模式**，比如**对操作的处理**应该如何传递等等。  
从请求的角度, 每一个要处理的请求是平行关系，**任何请求都可能要求被响应**，且对每个请求的处理都是一个链条，存在处理、加工、再处理的逻辑关系。

在这种模式中，通常**每个接收者都包含对另一个接收者的引用**。如果一个对象不能处理该请求，那么它会把相同的请求传给下一个接收者，依此类推，直到有一个对象处理它为止。

**责任链模式中的角色：发送者、接受者、请求**

## 责任链模式的流程：
    1. 发送者知道链中的第一个接受者，它向这个接受者发出请求
    2. 每一个接受者都对请求进行分析，要么处理它，要么往下传递
    3. 每一个接受者知道的其他对象只有一个，即它的下家对象
    4. 如果没有任何接受者处理请求，那么请求将从链上离开，不同的实现对此有不同的反应


## 意图
**使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。**

**请求**指的是某个触发机制产生的请求，是一个通用概念。

“避免请求的发送者和接收者之间的耦合关系”，指的是如果我们只有一个对象有处理请求的机会，那接收者就与发送者之间耦合了，其他接收者必须通过这个接收者才能继续处理，这种模式不够灵活。

后半句描述的是如何设计，可以实现这个灵活的模式，即将对象连成一条链，沿着链条传递该请求，直到有一个对象处理它为止。还要理解到，任何一个对象都拥有阻断请求继续传递的能力。


## 职责链模式有什么好处？有什么坏处？
### 好处
职责链模式的最大优点：请求发送者只需要知道链中的第一个节点，从而**弱化了发送者和一组接收者之间的强联系**。  

解耦了请求发送者和N个接收者之间的复杂关系，由于不知道链中的哪个节点可以处理你发出的请求，所以你只需把请求传递给第一个节点即可.  

职责链中的节点数量和顺序是可以自由变化的，我们可以在运行时决定链中包含哪些节点。  

增强给对象指派职责的灵活性, 通过改变链内的成员或者调动它们的次序，允许动态地新增或者删除责任。  

降低了请求的发送者和接受者之间的耦合、分工明确、代码更加清晰。


### 弊端
职责链模式不保证每个接受者都有机会处理请求，因为接受者顺序的问题，后面接受者可能被前面的接受者阻断，因此当接受者之间存在不信任关系时，职责链模式并不能保证接受者调用的可靠性。

另外就是不要扩大设计模式的使用范围，可能在某一次请求过程中，大部分节点没有起到实质性作用，他们的作用只是让请求传递下去，从性能方面考虑，避免过长的职责链提高性能。  

其次是对一堆对象的连续调用就没必要使用职责链模式，因为职责链**适合处理对象数量不确定、是否处理请求由每个对象灵活决定的场景**，而确定了对象数量以及是否调用的场景，就没必要使用职责链模式了。  

可能不容易观察运行时的特征，有碍于除错



## 职责链模式中，我们前端常接触的场景有？
- JS 事件冒泡机制
- express、koa中间件洋葱模型


## 职责链模式是为了解决什么问题呢？
## 职责链模式是为了解决什么场景呢？
责任链模式比较适合比如一个任务需要多个对象才能完成处理的情况或者代码存在许多if-else判断的情况



## 职责链模式中如何将请求传递给下一个接收者呢？
## 场景代入（代码案例）
假设我们负责一个售卖手机的电商网站，经过分别交纳500元定金和200元定金的两轮预定后（订单已在此时生成），现在已经到了正式购买的阶段。

公司针对支付过定金的用户有一定的优惠政策。在正式购买后，已经支付过500元定金的用户会收到100元的商城优惠券，200元定金的用户可以收到50元的优惠券，而之前没有支付定金的用户只能进入普通购买模式，也就是没有优惠券，且在库存有限的情况下不一定保证能买到。

我们的订单页面是PHP吐出的模板，在页面加载之初，PHP会传递给页面几个字段。

orderType：表示订单类型（定金用户或者普通购买用户），code的值为1的时候是500元定金用户，为2的时候是200元定金用户，为3的时候是普通购买用户。
pay：表示用户是否已经支付定金，值为true或者false，虽然用户已经下过500元定金的订单，但如果他一直没有支付定金，现在只能降级进入普通购买模式。
stock：表示当前用于普通购买的手机库存数量，已经支付过500元或者200元定金的用户不受此限制。

下面我们把这个流程写成代码：

```
var order = function( orderType, pay, stock ){
    if ( orderType === 1 ){ // 500元定金购买模式
        if ( pay === true ){ // 已支付定金
            console.log( '500元定金预购, 得到100优惠券' );
        }else{ // 未支付定金，降级到普通购买模式
            if ( stock > 0 ){ // 用于普通购买的手机还有库存
                console.log( '普通购买, 无优惠券' );
            }else{
                console.log( '手机库存不足' );
            }
        }
    }
    else if ( orderType === 2 ){ // 200元定金购买模式
        if ( pay === true ){
            console.log( '200元定金预购, 得到50优惠券' );
        }else{
            if ( stock > 0 ){
                console.log( '普通购买, 无优惠券' );
            }else{
                console.log( '手机库存不足' );
            }
        }
    }
    else if ( orderType === 3 ){
        if ( stock > 0 ){
            console.log( '普通购买, 无优惠券' );
        }else{
            console.log( '手机库存不足' );
        }
    }
};
order( 1 , true, 500); // 输出： 500元定金预购, 得到100优惠券
```
虽然我们得到了意料中的运行结果，但这远远算不上一段值得夸奖的代码。order函数不仅巨大到难以阅读，而且需要经常进行修改。虽然目前项目能正常运行，但**接下来的维护工作无疑是个梦魇**。

### 用职责链模式重构代码
现在我们采用职责链模式重构这段代码，先把500元订单、200元订单以及普通购买分成3个函数。

接下来把orderType、pay、stock这3个字段当作参数传递给500元订单函数，如果该函数不符合处理条件，则把这个请求传递给后面的200元订单函数，如果200元订单函数依然不能处理该请求，则继续传递请求给普通购买函数，代码如下：
```
// 500元订单
var order500 = function( orderType, pay, stock ){
    if ( orderType === 1 && pay === true ){
        console.log( '500元定金预购, 得到100优惠券' );
    }else{
        order200( orderType, pay, stock ); // 将请求传递给200元订单
    }
};
// 200元订单
var order200 = function( orderType, pay, stock ){
    if ( orderType === 2 && pay === true ){
        console.log( '200元定金预购, 得到50优惠券' );
    }else{
        orderNormal( orderType, pay, stock ); // 将请求传递给普通订单
    }
};
// 普通购买订单
var orderNormal = function( orderType, pay, stock ){
    if ( stock > 0 ){
        console.log( '普通购买, 无优惠券' );
    }else{
        console.log( '手机库存不足' );
    }
};
// 测试结果：
order500( 1 , true, 500); // 输出：500元定金预购, 得到100优惠券
order500( 1, false, 500 ); // 输出：普通购买, 无优惠券
order500( 2, true, 500 ); // 输出：200元定金预购, 得到50优惠券
order500( 3, false, 500 ); // 输出：普通购买, 无优惠券
order500( 3, false, 0 ); // 输出：手机库存不足
```
可以看到，执行结果和前面那个巨大的order函数完全一样，但是代码的结构已经清晰了很多，我们把一个大函数拆分了3个小函数，去掉了许多嵌套的条件分支语句。

目前已经有了不小的进步，但我们不会满足于此，虽然已经把大函数拆分成了互不影响的3个小函数，但可以看到，**请求在链条传递中的顺序非常僵硬，传递请求的代码被耦合在了业务函数之中**：
```
var order500 = function( orderType, pay, stock ){
    if ( orderType === 1 && pay === true ){
        console.log( '500元定金预购, 得到100优惠券' );
    }else{
        order200( orderType, pay, stock );
        // order200和order500耦合在一起
    }
};
```
这依然是违反开放-封闭原则的，如果有天我们要增加300元预订或者去掉200元预订，意味着就必须改动这些业务函数内部。就像一根环环相扣打了死结的链条，如果要增加、拆除或者移动一个节点，就必须得先 砸烂这根链条。

### 灵活可拆分的职责链节点
首先需要改写一下分别表示3种购买模式的节点函数，我们约定，如果某个节点不能处理请求，则返回一个特定的字符串'nextSuccessor'来表示该请求需要继续往后面传递：
```
var order500 = function( orderType, pay, stock ){
    if ( orderType === 1 && pay === true ){
        console.log( '500元定金预购，得到100优惠券' );
    }else{
        return 'nextSuccessor'; // 我不知道下一个节点是谁，反正把请求往后面传递
    }
};
var order200 = function( orderType, pay, stock ){
    if ( orderType === 2 && pay === true ){
        console.log( '200元定金预购，得到50优惠券' );
    }else{
        return 'nextSuccessor'; // 我不知道下一个节点是谁，反正把请求往后面传递
    }
};
var orderNormal = function( orderType, pay, stock ){
    if ( stock > 0 ){
        console.log( '普通购买，无优惠券' );
    }else{
        console.log( '手机库存不足' );
    }
};
```
接下来需要把函数包装进职责链节点，我们定义一个**构造函数Chain**，在new Chain的时候传递的参数即为需要被包装的函数， 同时它还拥有一个实例属性this.successor，表示在链中的下一个节点。

此外Chain的prototype中还有两个函数，它们的作用如下所示：
```
// Chain.prototype.setNextSuccessor 指定在链中的下一个节点
// Chain.prototype.passRequest 传递请求给某个节点
var Chain = function( fn ){
    this.fn = fn;
    this.successor = null;
};
Chain.prototype.setNextSuccessor = function( successor ){
    return this.successor = successor;
};
Chain.prototype.passRequest = function(){
    var ret = this.fn.apply( this, arguments );
    if ( ret === 'nextSuccessor' ){
        return this.successor && this.successor.passRequest.apply( this.successor, arguments );
    }
    return ret;
};
```

现在我们把3个订单函数分别包装成职责链的节点：
```
var chainOrder500 = new Chain( order500 );
var chainOrder200 = new Chain( order200 );
var chainOrderNormal = new Chain( orderNormal );
```
然后指定节点在职责链中的顺序：
```
chainOrder500.setNextSuccessor( chainOrder200 );
chainOrder200.setNextSuccessor( chainOrderNormal );
```
最后把请求传递给第一个节点：
```
chainOrder500.passRequest( 1, true, 500 ); // 输出：500元定金预购，得到100优惠券

chainOrder500.passRequest( 2, true, 500 ); // 输出：200元定金预购，得到50优惠券

chainOrder500.passRequest( 3, true, 500 ); // 输出：普通购买，无优惠券

chainOrder500.passRequest( 1, false, 0 ); // 输出：手机库存不足
```
通过改进，我们**可以自由灵活地增加、移除和修改链中的节点顺序**，假如某天网站运营人员又想出了支持300元定金购买，那我们就在该链中增加一个节点即可：
```
var order300 = function(){
// 具体实现略
};
chainOrder300= new Chain( order300 );
chainOrder500.setNextSuccessor( chainOrder300);
chainOrder300.setNextSuccessor( chainOrder200);
```
对于程序员来说，我们总是喜欢去改动那些相对容易改动的地方，就像改动框架的配置文件远比改动框架的源代码简单得多。在这里完全不用理会原来的订单函数代码，我们要做的只是增加一个节点，然后重新设 置链中相关节点的顺序。


### 异步的职责链
在上面的职责链模式案例中，我们让每个节点函数同步返回一个特定的值"nextSuccessor"，来表示是否把请求传递给下一个节点。  

而在现实开发中，我们经常会遇到一些异步的问题，比如我们要在节点函数中 发起一个ajax异步请求，异步请求返回的结果才能决定是否继续在职责链中passRequest。

这时候让节点函数同步返回"nextSuccessor"已经没有意义了，所以要给Chain类再增加一个原型方法Chain.prototype.next，表示手动传递请求给职责链中的下一个节点：
```
Chain.prototype.next= function(){
    return this.successor && this.successor.passRequest.apply( this.successor, arguments );
};
```

来看一个异步职责链的例子：
```
var fn1 = new Chain(function(){
    console.log( 1 );
    return 'nextSuccessor';
});
var fn2 = new Chain(function(){
    console.log( 2 );
    var self = this;
    setTimeout(function(){
        self.next();
    }, 1000 );
});
var fn3 = new Chain(function(){
    console.log( 3 );
});
fn1.setNextSuccessor( fn2 ).setNextSuccessor( fn3 );
fn1.passRequest();
```



学习资料：
- [JavaScript设计模式之职责链模式](https://zhuanlan.zhihu.com/p/95817886)

