## 47장 에러 처리

#### 47.1 에러처리의 필요성

에러가 발생하지 않은 코드를 작성하는 것을 불가능하다.

발생한 에러에 대해 대처하지 않고 방치하면 프로그램은 강제 종료된다.

```javascript 

console.log('[Start]');

foo(); // ReferenceError: foo is not defined
// 발생한 에러를 방치하면 프로그램은 강제 종료된다.

// 에러에 의해 프로그램이 강제 종료되어 아래 코드는 실행되지 않는다.
console.log('[End]');
```


직접적으로 에러를 발생하지 않는 예외적인 상황이 발생할 수도 있다. 예외적인 상황에 적절하게 대응하지 않으면 에러로 이어질 가능성이 크다.

```javascript
// DOM에 button 요소가 존재하지 않으면 querySelector 메서드는 에러를 발생시키지 않고 null을 반환한다.
const $button = document.querySelector('button'); // null

$button.classList.add('disabled');
// TypeError: Cannot read property 'classList' of null

```

- 언제나 에러나 예외적인 상황이 발생할수 있다는 것을 전제하고 이에 대응하는 코드를 작성하는 것이 중요


#### 47.2 try...catch...finally문

기본적으로 에러 처리를 구현하는 방법은 크게 두 가지가 있다.

1. if 문이나 단축평가 또는 옵셔널 체이닝 연산자를 통해 확인해서 처리하는 방법
2. 에러처리코드를 미리 등록해 두고 에러가 발생하면 에러 처리 코드로 점프하는 방법

- try..catch...finally 문은 두번째 방법이다. 
- 일반적으로 이 방법을 에러처리라고 한다.

- 해당 방법은 3개의 코드 블록으로 구성된다.

```javascript

try{
  // 실행할 코드(에러가 발생할 가능성이 있는 코드)
} catch (err) {
  // try 코드 블록에서 에러가 발생하면 이 코드 블록의 코드가 실행된다.
  // err에는 try 코드 블록에서 발생한 Error 객체가 전달된다.
} finally {
  // 에러 발생과 상관없이 반드시 한 번 실행된다.  
}
```

#### 47.3 Error 객체
- Error 생성자 함수는 에러 객체를 생성한다. Error 생성자 함수에는 에러를 상세히 설명하는 에러 메세지를 인수로 전달할 수 있다.

```javascript
const error = new Error('invalid');
```

자바스크립트는 Error 생성자 함수를 포함해 7가지의 에러 객체를 생성할 수 있는 Error 생성자 함수를 제공한다. SyntaxError, ReferenceError, TypeError, RangeError, URIError, EvalError 생성자 함수가 생성한 에러 객체의 프로토타입은 모두 Error.prototype을 상속받는다.

생성자 함수 | 인스턴스
Error | 일반적 에러 객체
SyntaxError | 자바스크립트 문법에 맞지 않는 문을 해석할 때 발생하는 에러 객체
ReferenceError | 참조할 수 없는 식별자를 참조했을 때 발생하는 에러 객체
TypeError | 피연산자 또는 인수의 데이터 타입이 유효하지 않을 때 발생하는 에러 객체
RangeError | 숫자값의 허용 범위를 벗어났을 때 발생하는 에러 객체
URIError | encodeURI 또는 decodeURI 함수에 부적절한 인수를 전달했을 때 발생하는 에러 객체
EvalError | eval 함수에서 발생하는 에러 객체

#### 47.4  throw 문
Error 생성자 함수로 에러 객체를 생성한다고 에러가 발생하는 것은 아니다. 즉, 에러 객체 생성과 에러 발생은 의미가 다르다.

```javascript
try {
  // 에러 객체를 생성한다고 에러가 발생하는 것은 아니다.
  new Error('something wrong');
} catch (error) {
  console.log(error);
}
```
에러를 발생시키려면 try 코드 블록에서 throw 문으로 에러 객체를던저야 한다.

throw 문의 표현식은 어떤 값이라도 상관없지만 일반적으로 에러 객체를 지정한다.
에러를 던지면 catch 문의 에러 변수가 생성되고 던져진 에러 객체가 할당된다. 그리고 catch 코드 블록이 실행되기 시작한다.

```javascript
try {
  // 에러 객체를 던지면 catch 코드 블록이 실행되기 시작한다.
  throw new Error('something wrong');
} catch (error) {
  console.log(error); // Error: something wrong
}
```


#### 에러의 전파 

```javascript
const foo = () => {
  throw Error('foo에서 발생한 에러'); // ④
};

const bar = () => {
  foo(); // ③
};

const baz = () => {
  bar(); // ②
};

try {
  baz(); // ①
} catch (err) {
  console.error(err);
}
```

①에서 baz 함수를 호출하면 ②에서 bar 함수가 호출되고 ③에서 foo함수가 호출되고 foo 함수는 ④에서 에러를 throw한다. 이때 foo 함수가 throw한 에러는 다음과 같이 호출자에게 전파되어 전역에서 캐치된다.
![1698663427096](image/cjy_47/1698663427096.png)

- 이처럼 throw된 에러를 캐치하지 않으면 호출자 방향으로 전파된다. 이때 throw된 에러를 캐치하여 적절히 대응하면 프로그램을 강제 종료시키지 않고 코드의 실행 흐름을 복구할 수 있다.

 

!!! 주의할 것은 비동기 함수인 setTimeout이나 프로미스 후속 처리 메서드의 콜백 함수는 호출자가 없다는 것이다. 
즉, 호출자 함수가 실행 컨텍스트가 삭제되고(컨텍스트 스택이 비어있다는 의미)

비동기 함수가 동작하기 때문에 전파할 호출자가 없어서 에러 처리가 힘들다.
