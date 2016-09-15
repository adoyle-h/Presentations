# BDD in NodeJS

## BDD
BDD 是 TDD 的超集。即 BDD 包含了 TDD 所有的东西。

是一种敏捷软件开发的技术。它鼓励软件项目中的开发者、QA 和非技术人员或商业参与者之间的协作。

### 优点
- 拥有 TDD 的所有优点
- 简单直观。非技术人员也能够编写测试，任何人都能看懂测试案例。
- 关注用户行为或故事(story)，对功能点测试完备
- 由外而内构建测试，由文档到代码

## Mocha
测试框架

### 优点
- 支持 TDD/BDD
- 支持前端/后端
- 学习成本很低
- 简洁，灵活，自由度高

### Install
`npm install -g mocha`

### 构件
- describe
- it
- anonymous function

- before
- after
- beforeEach
- afterEach
- only
- skip

### 同步
```js
var assert = require('chai').assert;

describe('Array', function() {
  describe('#indexOf()', function() {
    it('should return -1 when the value is not present', function(){
      assert.equal(-1, [1, 2, 3].indexOf(5));
    });

    it('2', function() {
      assert.equal(-1, [1, 2, 3].indexOf(0));
    });

    it('3', function() {
      assert.equal(1, [1, 2, 3].indexOf(2));
    });
  });
});
```

### 异步
```js
describe('User', function(){
  describe('#save()', function(){
    it('should save without error', function(done){
      var user = new User('Luna');
      user.save(function(err){
        if (err) done(err);
        done();
      });
    })
  })
});
```

### 未定的测试
```js
describe('Array', function(){
  describe('#indexOf()', function(){
    it('should return -1 when the value is not present');
  });
});
```

### before
after 的使用与 before 同理。

```js
describe('Array', function(){
  before(function(){
    // ...
  });

  describe('#indexOf()', function(){
    it('should return -1 when not present', function(){
      [1,2,3].indexOf(4).should.equal(-1);
    });
  });
});
```


同一个 describe 下可以有多个 before，执行顺序与代码顺序相同。
同一个 describe 下的执行顺序为 before, beforeEach, afterEach, after。
当一个 it 有多个 before 的时候，执行顺序从最外围的 describe 的 before 开始，其余同理。


### beforeEach
beforeEach 只对当前 describe 下的所有子孙it生效。

```js
describe('Connection', function(){
  var db = new Connection
    , tobi = new User('tobi')
    , loki = new User('loki')
    , jane = new User('jane');

  beforeEach(function(done){
    db.clear(function(err){
      if (err) return done(err);
      db.save([tobi, loki, jane], done);
    });
  })

  describe('#find()', function(){
    it('respond with matching records', function(done){
      db.find({ type: 'User' }, function(err, res){
        if (err) return done(err);
        res.should.have.length(3);
        done();
      })
    })
  })
})
```


### only
```js
describe('Array', function(){
  describe.only('#indexOf()', function(){

  })
})
```
### skip
skip describe

```js
describe('Array', function(){
  describe.skip('#indexOf()', function(){
    ...
  })
})
```

或者，skip 子用例

```js
describe('Array', function(){
  describe('#indexOf()', function(){
    it.skip('should return -1 unless present', function(){

    })

    it('should return the index when present', function(){

    })
  })
})
```

## Chai
断言库

### 优点
- 支持 Assert、Expect、Should
- 有各种插件增强功能

### Install
后端
`npm install --save-dev chai`

前端

```html
<script src="chai.js" type="text/javascript"></script>
```

### Assert
```js
var assert = require('chai').assert
  , foo = 'bar'
  , beverages = { tea: [ 'chai', 'matcha', 'oolong' ] };

assert.typeOf(foo, 'string', 'foo is a string');
assert.equal(foo, 'bar', 'foo equal `bar`');
assert.lengthOf(foo, 3, 'foo`s value has a length of 3');
assert.lengthOf(beverages.tea, 3, 'beverages has 3 types of tea');
```

### Expect
```js
var expect = require('chai').expect
  , foo = 'bar'
  , beverages = { tea: [ 'chai', 'matcha', 'oolong' ] };

expect(foo).to.be.a('string');
expect(foo).to.equal('bar');
expect(foo).to.have.length(3);
expect(beverages).to.have.property('tea').with.length(3);
```

```js
var answer = 43;

// AssertionError: expected 43 to equal 42.
expect(answer).to.equal(42);

// AssertionError: topic [answer]: expected 43 to equal 42.
expect(answer, 'topic [answer]').to.equal(42);
```

### Should
```js
var should = require('chai').should() //actually call the the function
  , foo = 'bar'
  , beverages = { tea: [ 'chai', 'matcha', 'oolong' ] };

foo.should.be.a('string');
foo.should.equal('bar');
foo.should.have.length(3);
beverages.should.have.property('tea').with.length(3);
```

#### 其他用法

```js
var should = require('chai').should();
db.get(1234, function (err, doc) {
  should.not.exist(err);
  should.exist(doc);
  doc.should.be.an('object');
});
```

- should.exist
- should.not.exist
- should.equal
- should.not.equal
- should.Throw
- should.not.Throw


### [Plugin](http://chaijs.com/plugins)
- HTTP
- Promise
- SPY
- JQuery


## 前端测试

### Mocha in Browser
```html
<html>
<head>
  <meta charset="utf-8">
  <title>Mocha Tests</title>
  <link rel="stylesheet" href="mocha.css" />
</head>
<body>
  <div id="mocha"></div>
  <script src="jquery.js"></script>
  <script src="chai.js"></script>
  <script src="mocha.js"></script>
  <script>
    mocha.setup('bdd')
    mocha.reporter('spec');
    should = chai.should();
  </script>
  <script src="test.js"></script>
  <script>
    mocha.checkLeaks();
    mocha.globals(['jQuery']);
    mocha.run();
  </script>
</body>
</html>
```

### 工具
#### [gremlins.js](https://github.com/marmelab/gremlins.js)
- 模拟用户随机操作，可做 fuzzing 测试
- 模拟点击、拖动、滚屏
- 自定义测试流程
- 增强插件
- 监控、分析

#### [supertest](https://github.com/visionmedia/supertest)
- 模拟 HTTP 请求

#### [PhantomJS](https://github.com/ariya/phantomjs#)
- Webkit 内核
- JavascriptCore

#### [SlimerJS](https://github.com/laurentj/slimerjs/)
- Gecko 内核
- SpiderMonkey(Mozilla 的 JS 引擎)

#### [CasperJS](https://github.com/n1k0/casperjs)
- 用于测 PhantomJS、SlimerJS 的工具

#### [intern](https://github.com/theintern/intern)
- 看介绍很牛X

#### [PhantomCSS](https://github.com/Huddle/PhantomCSS)
- 基于 PhantomJS 和 Resemble.js 的，CSS 测试工具。