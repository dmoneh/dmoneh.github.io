title: Node 单元测试 - Mocha
date: 2015-09-01 16:17:24
tags: node,unit-test
---
## 简介
mocha 是一个基于 nodejs 的单元测试框架。 
[mocha 官网](http://mochajs.org/)

命名约定：
* test case == 测试用例 
```
   it('description',function(){});
```

* test suite == 测试集 (eg: descript('xxx',function(){}))
```
  describe('description',function(){
    // test cases
  });
```

## 安装及入门
```
npm install mocha -g
```

下面是一个小例子：

hello.js
```
  var assert = require("assert");
  describe('Array', function() {
  describe('#indexOf()', function () {
    it('should return -1 when the value is not present', function () {
      assert.equal(-1, [1,2,3].indexOf(5));
    });
   });
 });
```

 ```
 $ mocha hello.js

  Array
    #indexOf()
      √ should return -1 when the value is not present


  1 passing (20ms)

 ```

可以看到测试通过了： 1 passing 。 

修改一下hello.js ，使它失败。

```
var assert = require("assert");
describe('Array', function() {
  describe('#indexOf()', function () {
    it('should return -1 when the value is not present', function () {
      assert.equal(-1, [1,2,3].indexOf(1));
    });
  });
});
```

再次测试

```
$ mocha hello.js

  Array
    #indexOf()
      1) should return -1 when the value is not present


  0 passing (19ms)
  1 failing

  1) Array #indexOf() should return -1 when the value is not present:

      AssertionError: -1 == 0
      + expected - actual

      --1
      +0

```
输出信息中还会包含很多 stack trace，这里忽略它。

ok，我们基本入门了。

## 组织结构
项目中，我们一般会在根目录下新建 test 目录，并且，将所有测试js放在这个目录。然后，在根目录下运行 
```
$ mocha
```
就会依次执行 test 目录中的测试。

## 用法

大部分用法都是从官网 copy 的代码，但是 copy 下来的代码其实只是一个示范，并不能直接运行。 为了方便观察效果， 我加入了一些代码，以便他们可以直接执行。

### 1 异步

基于 callback 的：

```
function User(name){
  this.name = name;
}
User.prototype.save = function(callback){
  callback();
}


var should = require('should');

describe('User', function() {
  describe('#save 1()', function() {
    it('should save without error', function(done) {
      var user = new User('Luna');
      user.save(function(err) {
        if (err) throw err;
        done();
      });
    });
  });
});

describe('User', function() {
  describe('#save 2()', function() {
    it('should save without error', function(done) {
      var user = new User('Luna');
      user.save(done);
    });
  });
});
```


基于 promise 的：

```
var chai = require("chai"),
  chaiAsPromised = require("chai-as-promised"),
  Q = require('q'),
  should = chai.should();

chai.use(chaiAsPromised);

function DB() {

}

DB.prototype.clear = function() {
  this.users = [];
  return Q.fcall(function() {
    //do nothing
  });
}

DB.prototype.save = function(users) {
  return Q.fcall(function() {
    this.users = users;
  });
}

DB.prototype.find = function() {
  return Q.fcall(function() {
    this.users;
  });
}

var db = new DB();



describe('#find()', function() {

  beforeEach(function() {
    return db.clear()
      .then(function() {
        return db.save(['tobi', 'loki', 'jane']);
      });
  });

  it('respond with matching records', function() {
    db.find().should.eventually.have.length(3);
  });
});

```

上面的测试是对官网例子的补充。 拙劣的模拟db，勿怪。
依赖的库 [chai](http://chaijs.com/guide/)，[chai-as-promised](https://www.npmjs.com/package/chai-as-promised)。 官网中提到后者，但是没有提及用法。

### 2. Hooks
Mocha 提供了 before(), after(), beforeEach() 和 afterEach(), 事实上， 很多测试框架都提供了这些函数，以用于在测试之前准备数据，在测试之后清理数据，等等。 比如 [Jasmine](http://dmoneh.github.io/jasmine-chinesedoc/)。 

```
describe('hooks', function() {

  before(function() {
    // 在全部测试执行之前执行
  });

  after(function() {
    // 在全部测试执行之后执行
  });

  beforeEach(function() {
    // 在每一个测试执行之前执行
  });

  afterEach(function() {
    // 在每一个测试执行之后执行
  });

  // test cases
});

```

### 3. Pending Tests

有些时候，我们会先创建一个不包含测试函数的 test case, 最后再由自己或者其他人补充测试函数。这种 test case 就叫做 pending test。
```
describe('Array', function() {
  describe('#indexOf()', function() {
    // pending test below
    it('should return -1 when the value is not present');
  });
});

```
运行测试，输出的信息是：
```
  Array
    #indexOf()
      - should return -1 when the value is not present


  0 passing (13ms)
  1 pending
```

### 4. 灵活的选取test suite 和 test case

mocha支持 only 和 skip 方法， 前者用于指定一个唯一执行的测试， 后者用于跳过某一个测试。这里所说的测试，包括 suite 和 case。

   * Exclusive Tests（单独测试）
   
     有时候，我们需要单独测试某一个测试集(test suite)或者测试用例(test case)。
     
     ***请确保只在一个地方使用 only***
     
     ```
     describe('Array', function() {
       describe.only('#indexOf()', function() {
        // ...
       });
      });
    
     describe('Array', function() {
        describe('#indexOf()', function() {
         it.only('should return -1 unless present', function() {
            // ...
        });

        it('should return the index when present', function() {
          // ...
        });
       });
     });
     
     ```
     `
   * 跳过测试

     有时候，我们需要暂时的跳过某些测试。 可以使用 skip 方法跳过某些测试，并且，这些测试会在测试报告中显示为 pending 状态。
     
      ```
      describe('Array', function() {
       describe.skip('#indexOf()', function() {
         // ...
       });
      });

      describe('Array', function() {
        describe('#indexOf()', function() {
          it.skip('should return -1 unless present', function() {
            // ...
          });

          it('should return the index when present', function() {
           // ...
          });
        });
       });

     ```

## 总结

mocha 是一个灵活的 js 测试框架， 它本身不支持断言，promise等语法，但是可以通过第三方库，如[should.js](https://github.com/shouldjs/should.js),[chai](http://chaijs.com/guide/)，[chai-as-promised](https://www.npmjs.com/package/chai-as-promised)，[q](http://documentup.com/kriskowal/q/),来扩展自己的功能。由此看见，设计一个伟大的框架，不是一定要面面俱到，但是一定要保留开放的接口，以供和其他库交互。 这样， 一方面，框架本身不会太过庞大，其次，可以通过开发的接口，借助开源的力量，逐渐丰富自己的生态系统。最后，终端用户，也可以根据自己的需求，搭建适合的测试系统。

好像跑题了。。。

## 参考
* [mocha](http://mochajs.org/)
* [should.js](https://github.com/shouldjs/should.js)
* [chai](http://chaijs.com/guide/)
* [chai-as-promised](https://www.npmjs.com/package/chai-as-promised)
* [q](http://documentup.com/kriskowal/q/)


