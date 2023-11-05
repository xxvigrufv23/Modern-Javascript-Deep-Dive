## 46 제너레이터와 async/await

#### 46.1 제너레이터란?

- ES6에서 도입된 제너레이터는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수다. 제터레이터와 일반 함수의 차이는 다음과 같다.

  1.**제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다.**
- 일반 함수를 호출하면 제어권이 함수에게 넘어가고 함수 코드를 실행한다. 즉, 함수 호출자는 함수를 호출한 이후 함수 실행을 제어할 수 없다. 제너레이터 함수는 함수 호출자가 함수 실행을 일시 중지시키거나 재개시킬 수 있도록 제어권을 가진다.

2. **제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다.**

- 일반 함수를 호출하면 매개변수를 통해 값을 받고 함수 코드를 실행하여 결과값을 반환한다. 즉, 함수가 실행되고
  있는 동안에는 함수 외부에서 함수 내부로 값을 전달하여 함수의 상태를 변경할 수 없다. 제너레이터 함수는 함수
  호출자와 양방향으로 상태를 서로 주고 받을 수 있다.

3. **제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.**

- 일반 함수를 호출하면 함수 코드를 실행하고 값을 반환한다. 제너레이터 함수를 호출하면 함수 코드를 실행하는 것이 아니라 이터러블이면서 동시에 이터레이터인 제너레이터 객체를 반환한다.

#### 46.2 제너레이터 함수의 정의

제너레이터 함수는 function* 키워드로 선언한다. 그리고 하나 이상의 yield 표현식을 포함한다.

이 외에는 일반 함수를 정의하는 방법과 같다.

```javascript
// 제너레이터 함수 선언문
function* genDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genExpFunc = function* () {
  yield 1;
};

// 제너레이터 메서드
const obj = {
  * genObjMethod() {
    yield 1;
  }
};

// 제너레이터 클래스 메서드
class MyClass {
  * genClsMethod() {
    yield 1;
  }
}

// 화살표 함수로는 정의 할 수 없다.
const genArrowFunc = * () => {
  yield 1;
}; // SyntaxError: Unexpected token '*'
```

에스터리스크(*)의 위치는 function 키워드와 함수 이름 사이라면 어디든지 상관 없다.

제너레이터 함수는 화살표 함수로 정의할 수 없다.

```javascript
const getArrowFunc = * () => {
    yield 1;
} //SyntaxError: Unexpected token '*'
```

제너레이터 함수는 new 연산자와 함께 생성자 함수로 호출할 수 없다.

```javascript
function* genFunc() {
  yield 1;
}

new genFunc(); // TypeError: genFunc is not a constructor
```

#### 46.3 제너레이터 객체

제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환한다. 제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.

제너레이터 객체는 Symbol.iterator 메서드를 상속받는 이터러블이면서 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 next 메서드를 소유하는 이터레이터다. 제너레이터 객체는 next 메서드를 가지는 이터레이터이므로 Symbol.iterator 메서드를 호출해 이터레이터를 생성할 필요가 없다.

```javascript

// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
const generator = genFunc();

// 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.
// 이터러블은 Symbol.iterator 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 객체다.
console.log(Symbol.iterator in generator); // true
// 이터레이터는 next 메서드를 갖는다.
console.log('next' in generator); // true
```

- `*` 기호는 JavaScript에서 제너레이터(generator) 함수를 정의하는 데 사용된다.
- 제너레이터 함수는 일반 함수와 다르게 동작하며, 중간에 실행을 중단하고 재개할 수 있는 특별한 종류의 함수다.

`*` 기호를 함수 선언 앞에 놓으면 해당 함수가 제너레이터 함수로 정의된다.
제너레이터 함수는 `yield` 키워드를 사용하여 값을 반환하고 실행을 일시 중단한다.

예를 들어:

```javascript
function* myGenerator() {
  yield 1;
  yield 2;
  yield 3;
}
```

- 위의 코드에서 `*`는 `myGenerator` 함수를 제너레이터 함수로 만든다.
- 그 후에 `yield` 키워드를 사용하여 1, 2, 3을 반환하고 함수의 실행을 중단한다.
- `yield` 키워드와 제너레이터 함수를 사용하면 복잡한 반복 논리나 비동기 작업을 처리할 때 코드를 더 효과적으로 작성할 수 있다.

`yield` 키워드의 상세한 예제를 통해 이해를 도울 수 있다.
아래의 예제는 제너레이터 함수를 사용하여 간단한 숫자의 제곱 값을 계산하고 반환하는 방법이다.

```javascript
function* squareGenerator() {
  let i = 0;
  while (true) {
    i++;
    yield i * i; // 제곱 값을 반환하고 함수 일시 중단
  }
}

const squares = squareGenerator();

console.log(squares.next().value); // 1
console.log(squares.next().value); // 4
console.log(squares.next().value); // 9
console.log(squares.next().value); // 16
console.log(squares.next().value); // 25
console.log(squares.next().value); // 36
console.log(squares.next().value); // 49
console.log(squares.next().value); // 64
// 계속해서 호출하면 무한히 제곱 값 반환
```

이 예제에서 `squareGenerator` 함수는 제너레이터 함수로 정의되었고, 무한한 수의 제곱 값을 생성한다.
`yield`를 사용하여 제곱 값을 반환하고, 함수는 `while` 루프를 통해 계속 반복된다.
`squares.next().value`를 호출할 때마다 제곱 값을 반환하고, 다음 호출 시점까지 함수가 일시 중단된다.

#### async / await

ES8(ECMAScript 2017)에서는 비동기 처리를 동기 처럼 동작하도록 구현할 수 있는 async/await가 도입됐다

 async/await는 프로미스를 기반으로 동작한다.  async/await를 사용하면 프로미스의 then/catch/finally

후속 처리 메서드에 콜백 함수를 전달해서 비동기 처리 결과를 후속 처리할 필요 없이
**마치 동기 처럼 프로미스를 사용할 수 있다.**

quiz

```javascript
function* numberSequenceGenerator() {
  let number = 1;
  while (true) {

    yield number;
    number++;
  }
}

const sequenceGenerator = numberSequenceGenerator();


for (let i = 0; i < 5; i++) {
  const nextNumber = sequenceGenerator.next().value;
  console.log(nextNumber);
}
Quiz) console.log 에 찍히는 순서는?
ⓐ 1
   2
   3
   4
   5
   
ⓑ 0
   1
   2
   3
   4

ⓒ undefined
   undefined
   undefined
   undefined
   undefined

ⓓ 0
```
