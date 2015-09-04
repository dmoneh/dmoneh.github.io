title: Nodejs 内置的 unit test 模块 - assert
date: 2015-09-01 13:15:36
tags: node,unit-test
excerpt: Node assert 断言模块，可以独立使用， 也可以在其他测试框架中作为断言模块。
---

Nodejs 有一个叫做 [assert](https://nodejs.org/api/assert.html) 的模块，可以用来做简单的 断言。 

下面是一个小例子。

```
var assert = require('assert');
var ret = assert.ok(true); 
console.log('ret: ' + ret);
```
上面的例子中，assert.ok 用于检测测试值是否为 true。 很显然，测试应该通过。
运行，发现没有打印任何信息，而返回值 ret 也是 undefined 。
```
$ node 001.asssert.js
ret: undefined
```
如果检查一下程序的退出状态
```
$ echo $?
0
```
熟悉linux的朋友应该知道， 这代表程序正常结束了。 我把它叫做状态码。

如果我们运行一个不能通过的测试呢？
```
var assert = require('assert');
var ret = assert.ok(false); 
console.log('ret: ' + ret);
```
这一次，结果是：
```
$ node 001.asssert.js

assert.js:86
  throw new assert.AssertionError({
        ^
AssertionError: false == true
    at Object.<anonymous> (d:\workspace\node\learn-node\001.asssert.js:2:18)
    at Module._compile (module.js:460:26)
    at Object.Module._extensions..js (module.js:478:10)
    at Module.load (module.js:355:32)
    at Function.Module._load (module.js:310:12)
    at Function.Module.runMain (module.js:501:10)
    at startup (node.js:129:16)
    at node.js:814:3
```

再检查一下状态码。

```
$ echo $?
1
```
这一次，程序没有正常退出。 
看起来，我们可以通过测试程序的结束状态来得知全部测试是否通过。
至于每一个独立的测试是否通过，需要查看打印的信息。 但是，assert的打印信息并不是那么容易阅读的。 事实上，对于单元测试， 程序员只关心每个case是否通过，抛出下面这样的 stack 对与测试来说，并没有什么意义：


```
assert.js:86
  throw new assert.AssertionError({
        ^
AssertionError: false == true
    at Object.<anonymous> (d:\workspace\node\learn-node\001.asssert.js:2:18)
    at Module._compile (module.js:460:26)
    at Object.Module._extensions..js (module.js:478:10)
    at Module.load (module.js:355:32)
    at Function.Module._load (module.js:310:12)
    at Function.Module.runMain (module.js:501:10)
    at startup (node.js:129:16)
    at node.js:814:3
```

理想的单元测试，应该可以定义测试用例的名字， 应该在测试完成之后， 输出测试用例的名字和通过状态。 assert 并不能完成这样的工作， 但是它具备了基本的行为模式： 测试，输出测试状态。 所以， 它可以作为单元测试框架的断言库，事实上，[mocha](https://mochajs.org/)，一种比较流行的测试框架， 确实支持用 node assert 做断言库。




