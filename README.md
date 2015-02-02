# ECMAScript 6 <sup>[http://git.io/zAjDkQ](http://git.io/zAjDkQ)</sup>

## 소개
ECMAScript 6(이하 ES6)는 ECMAScript 표준의 다음 버전이다.
2015년 6월 승인을 목표로 하고 있으며 2009년 표준화된 ES5 이후 처음으로 중요한 업데이트이다.
주요 자바스크립트 엔진들은 현재 이 기능들의 구현이 [진행중](http://kangax.github.io/es5-compat-table/es6/)이다.

ES6의 전체 표준의 [제안](https://people.mozilla.org/~jorendorff/es6-draft.html)을 확인해볼 수 있다.

ES6는 다음과 같은 새로운 기능들을 포함한다.


## ES6 기능

- [arrows](#arrows)
- [classes](#classes)
- [enhanced object literals](#enhanced-object-literals)
- [template strings](#template-strings)
- [destructuring](#destructuring)
- [default + rest + spread](#default-rest-spread)
- [let + const](#let-const)
- [iterators + for..of](#iterators-forof)
- [generators](#generators)
- [unicode](#unicode)
- [modules](#modules)
- [module loaders](#module-loaders)
- [map + set + weakmap + weakset](#map-set-weakmap-weakset)
- [proxies](#proxies)
- [symbols](#symbols)
- [subclassable built-ins](#subclassable-built-ins)
- [promises](#promises)
- [math + number + string + object APIs](#math-number-string-object-apis)
- [binary and octal literals](#binary-and-octal-literals)
- [reflect api](#reflect-api)
- [tail calls](#tail-calls)

### Arrows

`=>` 문법은 함수의 단축표기이다. C#, Java 8, CoffeeScript와 문법적으로 비슷하다.
표현식과 괄호식(statement body) 모두 지원한다. 함수와는 다르게 `this`를 바깥에서 가져온다.

```JavaScript
// 표현식
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);

// 괄호식
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// Lexical this
var bob = {
  _name: "Bob",
  _friends: [],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

### Classes

ES6 클래스는 프로토타입 기반의 객체지향 패턴보다 쓰기 쉽다.
한줄짜리 코드로 클래스를 더 사용하기 쉽고 상호유용성(interoperability)이 좋아진다.
클래스는 프로토타입 기반의 상속, 부모 호출, 상속, 그리고 스태틱 메소드, 생성자를 지원한다.

```JavaScript
class SkinnedMesh extends THREE.Mesh {
  constructor(geometry, materials) {
    super(geometry, materials);

    this.idMatrix = SkinnedMesh.defaultMatrix();
    this.bones = [];
    this.boneMatrices = [];
    //...
  }
  update(camera) {
    //...
    super.update();
  }
  static defaultMatrix() {
    return new THREE.Matrix4();
  }
}
```

### Enhanced Object Literals

객체 표현에 생성 시 사용할 프로토타입 설정, `foo: foo`의 단축표기, 메소드 정의 및 부모 호출 등의 지원이 추가되었다.
게다가 객체 표현을 클래스 선언에 가깝게 사용할 수 있으며 객체지향 설계의 장점도 가져올 수 있다.

```JavaScript
var obj = {
   // 프로토타입 설정
   __proto__: theProtoObj,
   // `handler: handler`의 단축 표기
   handler,
   // 메소드
   toString() {
     // 부모 호출
     return "d " + super.toString();
   }
};
```

### Template Strings

템플릿 스트링은 문자열 생성을 위한 문법적인 편의이다. 펄, 파이썬 등의 스트링 인터폴레이션 기능과 비슷하다.
인젝션 공격을 피하거나 문자열 내용을 통해 고차원의 자료구조를 생성하는데 사용할 수 있다.

```JavaScript
// 기본적인 문자열 생성
`In JavaScript '\n' is a line-feed.`

// 여러줄의 문자열
`In JavaScript this is
 not legal.`

// DOM 쿼리 생성
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

// HTTP 요청을 위해 필요한 정보 치환
GET`http://foo.org/bar?a=${a}&b=${b}
    Content-Type: application/json
    X-Credentials: ${credentials}
    { "foo": ${foo},
      "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

### Destructuring

배열이나 객체에서 패턴매칭을 통한 바인드를 지원한다.
실패 완화(fail-soft)는 객체에서 속성을 찾는 방법 `foo["bar"]`와 비슷해서, 못찾으면 `undefined`가 된다.

```JavaScript
  // 배열의 매칭
  var [a, , b] = [1,2,3];

  // 객체의 매칭
  var { op: a, lhs: { op: b }, rhs: c }
         = getASTNode()

  // 객체 매칭의 단축표기
  // 스코프에서 `op`, `lhs`, 그리고 `rhs`를 바인드
  var {op, lhs, rhs} = getASTNode()

  // 파라미터 위치에서도 쓸 수 있다
  function g({name: x}) {
    console.log(x);
  }
  g({name: 5})

  // 실패 완화의 분해
  var [a] = [];
  a === undefined;

  // 기본값을 사용한 실패 완화의 분해
  var [a = 1] = [];
  a === 1;
```

### Default + Rest + Spread

함수가 정의될 때 기본값을 설정할 수 있다. 뒤따라오는 인자들을 하나의 배열로 만들어준다. 배열을 직접 `arguments`처럼 사용할 수 있다.

```JavaScript
function f(x, y=12) {
  // 넘긴 값이 없을 때(혹은 undefined를 넘길 때) y는 12
  return x + y;
}
f(3) == 15
```
```JavaScript
function f(x, ...y) {
  // y는 배열
  return x * y.length;
}
f(3, "hello", true) == 6
```
```JavaScript
function f(x, y, z) {
  return x + y + z;
}
// 배열의 인자 각각을 인자로 넘긴다.
f(...[1,2,3]) == 6
```

## Let + Const

괄호({}) 안의 스코프 안에서 유효한 `let`은 새로운 `var`이다.
`const`는 선언 뒤에 값이 변경되는걸 막아준다.

```JavaScript
function f() {
  {
    let x;
    {
      // okay, block scoped name
      const x = "sneaky";
      // error, const
      x = "foo";
    }
    // error, already declared in block
    let x = "inner";
  }
}
```

### Iterators + For..Of

iterator는 공통 언어 런타임(CLR; Common Language Runtime)의 IEnumerable이나 Java의 Iterable과 비슷한 커스텀 이터레이터를 만들어준다.
`for-in`처럼 커스텀된 이터레이터는 `for-of`를 사용한다. 실제로 배열을 만들 필요 없이, LINQ처럼 나중에 만들 수 있는(lazy) 디자인 패턴이다.

```JavaScript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // 1000번째에서 자르기
  if (n > 1000)
    break;
  print(n);
}
```
이터레이터의 인터페이스를 TypeScript의 문법으로 표현하자면
```JavaScript
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```
### Generators

간단하게 말해서 `function*`와 `yield`로 이터레이터를 짜는 것이다. function*로 함수를 선언하면 제너레이터 인스턴스를 리턴한다.
제너레이터는 `next`와 `throw`가 추가된 이터레이터의 서브타입이다.
제너레이터를 통해 값을 던지는데, `yield`는 값을 리턴하는(혹은 던지는) 표현식이다.

노트: 'await'같은 비동기 프로그래밍도 가능하며, ES7에서는 `await`가 제안되었다.

```JavaScript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // 1000번째에서 자르기
  if (n > 1000)
    break;
  print(n);
}
```

제너레이터의 인터페이스를 TypeScript의 문법으로 표현하자면

    interface Generator extends Iterator {
        next(value?: any): IteratorResult;
        throw(exception: any);
    }

## Unicode

문자열에서의 새로운 유니코드 표현식과 정규식에서 `u` 모드를 포함해 전체 유니코드를 지원한다.
게다가 21bit도 문자열로 처리할 수 있는 새로운 API도 제공한다.
이런 추가들은 자바스크립트로 글로벌 앱을 만드는데 도움이 된다.

```JavaScript
// same as ES5.1
"𠮷".length == 2

// new RegExp behaviour, opt-in ‘u’
"𠮷".match(/./u)[0].length == 2

// new form
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// new String ops
"𠮷".codePointAt(0) == 0x20BB7

// for-of iterates code points
for(var c of "𠮷") {
  console.log(c);
}
```

### Modules

언어 차원에서 컴포넌트로 선언된 모듈을 지원한다.
암묵적으로 비동기 모델로 돌아가며, 요청받은 모듈을 실행할 때까지 코드가 실행되지 않는다.

```JavaScript
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```
```JavaScript
// app.js
import * as math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
```
```JavaScript
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
```
`export default`와 `export *`같은 기능들도 있다.

```JavaScript
// lib/mathplusplus.js
export * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.exp(x);
}
```
```JavaScript
// app.js
import exp, {pi, e} from "lib/mathplusplus";
alert("2π = " + exp(pi, e));
```

### Module Loaders

모듈 로더는 다음 기능들을 지원한다.

- 동적 로딩
- 상태 고립
- 글로벌 네임스페이스 고립
- 컴파일 후킹
- 중첩된 가상화

기본 모듈 로더는 설정이 가능하고, 새로운 로더로 실행하는 것도 가능하고 고립되어 로딩되거나 컨텍스트를 제한해서 로딩할 수도 있다.

```JavaScript
// 다이나믹 로딩 - `System`이 기본 로더
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// 새 로더 생성 - 샌드박스 실행
var loader = new Loader({
  global: fixup(window) // replace ‘console.log’
});
loader.eval("console.log('hello world!');");

// 직접 모듈 캐시를 관리
System.get('jquery');
System.set('jquery', Module({$: $})); // 경고: 아직 완료되지 않음
```

### Map + Set + WeakMap + WeakSet

흔히 쓰는 알고리즘을 위한 효율적인 자료 구조들. WeakMap은 누수없이 객체를 키로 갖는 테이블을 만들 수 있다.

```JavaScript
// Sets
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Maps
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Maps
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined

// Weak Sets
var ws = new WeakSet();
ws.add({ data: 42 });
```

### Proxies

객체가 할 수 있는 모든 행동을 다루게 해준다. 주입, 객체 가상화, 로그/프로파일링 등에서 사용될 수 있다.

```JavaScript
// 보통 객체에 대한 프록시
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```
```JavaScript
// 함수 객체에 대한 프록시
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```
런타임 단계에서 사용할 수 있는 핸들러들이다

```JavaScript
var handler = {
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

### Symbols

심볼은 새로운 프리미티브 타입니다. (ES5에서처럼) `string`이나 `symbol`을 키로 속성을 다룰 수 있다. 심볼은 유니크하지만 `Object.getOwnPropertySymbols` 같은 기능을 통해 노출되기 때문에 private는 아니다.

```JavaScript
(function() {

  // module scoped symbol
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

})();

var c = new MyClass("hello")
c["key"] === undefined
```

### Subclassable Built-ins

ES6에서는 `Array`, `Date`, DOM `Element`같은 내장 객체의 서브클래스를 만들 수 있다.

`Ctor`라는 이름의 함수로 객체를 생성하는데 (가상으로 실행되는) 2단계가 있다.

- `Ctor[@@create]`를 불러 객체를 할당하고 특정 행동들을 주입한다
- 초기화할 새 인스턴스의 생성자를 실행한다.

`@@create`라고 불리는 명령어는 `Symbol.create`를 통해서 이루어진다.
내장된 속성들은 `@@create`를 통해 명시적으로 드러난다.

```JavaScript
// Array의 의사 코드
class Array {
    constructor(...args) { /* ... */ }
    static [Symbol.create]() {
        // [[DefineOwnProperty]]를 주입
        // 'length'에 대한 갖가지 업데이트
    }
}

// Array의 서브클래스에 대한 유저 구현
class MyArray extends Array {
    constructor(...args) { super(...args); }
}

// 두 단계의 'new' :
// 1) @@create를 호출해서 객체를 할당한다.
// 2) 새로운 인스턴스에 생성자를 실행시킨다.
var arr = new MyArray();
arr[1] = 12;
arr.length == 2
```

### Math + Number + String + Object APIs

객체 복사를 위한 `Object.assign`, 각종 Array 유틸, 코어 Math 라이브러리 등 새로운 라이브러리들이 추가되었다.

```JavaScript
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".contains("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // 진짜 Array로 리턴
Array.of(1, 2, 3) // [1, 2, 3]
[0, 0, 0].fill(7, 1) // [0,7,7]
[1,2,3].findIndex(x => x == 2) // 1
["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // iterator 0, 1, 2
["a", "b", "c"].values() // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
```

### Binary and Octal Literals

2진표기`b`와 8진표기`o`

```JavaScript
0b111110111 === 503 // true
0o767 === 503 // true
```

## Promises

프로미스는 비동기 프로그래밍을 위한 라이브러리다. 향후 사용할지 모르는 값을 나타내는데 쓰는 일급 표현이다.
많은 자바스크립트 라이브러리들이 이미 쓰이고 있다.

```JavaScript
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
```

### Reflect API

런타임 단계에서 객체의 행동들을 드러내는 API이다. 프록시 API의 반대이며 프록시를 구현하는데 특히 유용하다.

```JavaScript
// 아직 샘플 없음
```

### Tail Calls

함수의 끝에서의 호출(꼬리 재귀)는 스택을 더 만들지 않도록 해준다. 길이를 모르는 입력에 대해 안전하게 재귀호출을 만드는 알고리즘이다.

```JavaScript
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// 요즘 대부분 구현체들에서는 스택 오버플로우
// 하지만 ES6에서는 어떤 입력에서도 안전하다.
factorial(100000)
```
