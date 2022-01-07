# Symbol 💛

JavaScript는 총 6개의 원시 타입(Number, String, Boolean, null, undefined, Symbol)과 1개의 객체 타입(Object)을 가지고 있다.
심볼(Symbol) 타입도 6개의 원시 타입 중 하나로, ES6 버전의 JavaScript에서 새롭게 추가되었다.
결론부터 얘기하자면, 일반적으로 심볼 타입은 객체의 프로퍼티 키를 고유하게 설정함으로써 프로퍼티 키의 충돌을 방지하기 위해 사용된다. 
다른 타입에 비해 흔하게 사용되는 타입은 아니지만,
JavaScript에는 심볼 타입에 대한 이해가 전제되어야 이해하고 사용할 수 있는 몇몇 문법(EX. iterable 객체)들도 있기 때문에 공부해두는 것이 좋다.

### 심볼의 생성 및 활용

심볼은 Symbol 함수를 호출함으로써 생성할 수 있다. 이때 생성되는 심볼은 변경이 불가능한 원시 값이다. 
Symbol 함수를 호출할 때 인자로 전달하는 문자열 값은 생성될 심볼에 대한 일종의 설명문(Description)으로, 오직 디버깅의 용도로만 사용된다. 
즉, 뒤에서 설명할 심볼의 키와는 완전히 다른 것으로, console.log() 등을 이용한 디버깅 시에 각 심볼을 구분하기 위한 용도로 사용이 된다.

```javascript
// Create symbols
const sym1 = Symbol();
const sym2 = Symbol('foo');
const sym3 = Symbol('bar');

// Print symbols (use the description that was specified when calling Symbol function)
console.log(sym1);  // Symbol()
console.log(sym2);  // Symbol(foo)
console.log(sym3);  // Symbol(bar)

// Check type of symbol
console.log(typeof sym1);  // symbol
console.log(typeof sym2);  // symbol
console.log(typeof sym3);  // symbol
```

한편, `Symbol 함수`를 호출하면 매번 새로운(고유한) 심볼이 생성된다. 일치 연산자(===)를 통해 이를 확인해 보자.

```javascript
const sym1 = Symbol();
const sym2 = Symbol();
const sym3 = Symbol('foo');
const sym4 = Symbol('foo');

console.log(sym1 === sym1);  // true 당연히 자기자신과는 같다

console.log(sym1 === sym2);  // false 하나하나가 고유이므로 다르다
console.log(sym3 === sym4);  // false
```

그런데 심볼 타입에는 특이한 점이 하나 있다. 
그것은 바로 Number, String, Boolean 타입과 달리 new 연산자를 이용한 래퍼 객체의 생성이 불가능하다는 점이다.
new 연산자를 이용하여 래퍼런스 객체를 생성하려고 하면 TypeError가 발생한다.
new 연산자를 이용할 수 없다는 것은 곧 Symbol 함수를 생성자로 사용할 수 없음을 의미한다.


그러면 이렇게 생성된 심볼은 어디에 쓰일까? 결론부터 얘기하자면, 일반적으로 심볼은 객체의 `프로퍼티 키`로 사용된다. 
`프로퍼티 키`란 곧 해당 프로퍼티의 값에 접근하고자 할 때 사용하는 이름이다. 
객체를 이용할 때 주로 키를 통해 value 에 접근할 때 사용하는 것 과 같다.
JavaScript에서 객체의 프로퍼티 키는 대개 문자열 값이다. 
숫자로 쓰는 것도 사실은 문자열이다(내부적으로 문자열로 변환됨).

```javascript
const obj = {};

obj.propertyKey1 = 'propertyValue1';
obj['propertyKey2'] = 'propertyValue2';

obj[3] = 'propertyValue3';  // obj['3'] = 'propertyValue3'으로 변환

console.log(obj);  // {propertyKey1: 'propertyValue1', propertyKey2: 'propertyValue2', 3: 'propertyValue3'} 
```

그런데 문자열 값 대신 심볼도 프로퍼티 키로 사용될 수 있다는 것이다. 
이 경우, 기본적으로 심볼은 고유하기 때문에 심볼을 키로 갖는 프로퍼티는 다른 어떤 프로퍼티와도 충돌하지 않을 것이다.
심볼은 고유한 심볼이 생성되기 때문에 겹칠 일이 없기 때문이다.

```javascript
const obj = {};

const sym1 = Symbol();
const sym2 = Symbol('foo');
const sym3 = Symbol('foo');

obj[sym1] = 'propertyValue1';
obj[sym2] = 'propertyValue2';
obj[sym3] = 'propertyValue3';  // no conflict with sym2

console.log(obj);  // {Symbol(): 'propertyValue1', Symbol(foo): 'propertyValue2', Symbol(foo): 'propertyValue3'}

console.log(obj[sym1]);  // propertyValue1
console.log(obj[sym2]);  // propertyValue2
console.log(obj[sym3]);  // propertyValue3
```

sym2 와 sym3 은 각각 고유의 키로 사용된다.

### 내장 심볼 (Buile-in Symbol)
💢추후 Iterator 에 대한 학습필요..


### 3. Symbol.for(), Symbol.keyFor() methods
Symbol 함수를 호출함으로써 생성하는 심볼들은 키를 가지고 있지 않으며, 전역 심볼 레지스트리에 저장되지도 않는다.
전역 심볼 레지스트리(Global Symbol Registry)란 심볼들이 저장되는 전역 공간을 의미하는 것이며,
여러 모듈들이 하나의 심볼을 공유하기 위한 용도로 존재한다. 여러 모듈들이 하나의 심볼을 공유하려면 그 심볼이 키를 가지고 있어야 한다.
그래야 키를 통해 이미 존재하는 심볼을 찾아 재활용할 수 있기 때문이다. 이와 관련된 메소드들에 대해 한 번 알아보자.

 

#### 3-1. Symbol.for() 메소드

인자로 전달받은 문자열 값을 키로 갖는 심볼을 전역 심볼 레지스트리에서 찾아 반환하고,
탐색에 실패한다면 그 문자열 값을 키로 갖는 심볼을 새로 생성하여 전역 심볼 레지스트리에 저장한 뒤 이를 반환한다.
단순히 Symbol 함수를 호출하여 심볼을 생성하는 것에 비해, 생성되는 심볼이 키를 갖고 있으며 전역 심볼 레지스트리에 저장이 된다는 차이점이 있다.


```javascript
const sym1 = Symbol.for('foo');  // Cretate symbol  없기 때문애 생성
const sym2 = Symbol.for('foo');  // Reuse symbol  있기 때문에 재사용

console.log(sym1 === sym2);  // true
```

#### 3-2. Symbol.keyFor() 메소드

인자로 전달받은 심볼을 전역 심볼 레지스트리에서 찾고,  (인자로 심볼의 키가 아닌 심볼 그 자체를 받음!💫)
그 심볼의 키를 반환하고, 탐색에 실패한다면 undefined를 반환합니다. 
이 함수를 이용하여 3-1에서 설명한 내용을 검증해보자. 
즉, Symbol 함수를 호출하여 심볼을 생성하는 것과 Symbol.for() 메소드를 호출하여 심볼을 생성하는 것의 차이점을 확인해보자.

```javascript
const unsharedSym = Symbol('foo');
const symKey1 = Symbol.keyFor(unsharedSym);
console.log(symKey1);  // undefined

const sharedSym = Symbol.for('foo');
const symKey2 = Symbol.keyFor(sharedSym);
console.log(symKey2);  // foo
```




### 4. for ... in 문법과 JSON.stringify() 메소드에서의 심볼
(주의해야한다💌)

앞서 말하기를 심볼은 객체의 프로퍼티 키로 사용된다고 하였다. 
그런데 주의해야 할 점이 하나 있다. 기본적으로 JavaScript가 제공하는 for ... in 문법에서 키가 심볼인 프로퍼티들은 열거되지 않는다.
또한 Object.getOwnPropertyNames() 메소드 또한 키가 심볼인 프로퍼티들은 반환하지 않는다. 
이 사실은 다음 예시 코드를 통해 확인할 수 있다.

```javascript
const obj = {};

obj[Symbol('a')] = 'a';
obj[Symbol.for('b')] = 'b';
obj['c'] = 'c';
obj.d = 'd';

for (const propertyKey in obj) {
   console.log(propertyKey);  // logs 'c' and 'd'
}
```

만약 키가 심볼인 프로퍼티들의 목록을 확인하고 싶다면 `Object.getOwnPropertySymbols()` 메소드를 사용하면 된다. 
이 메소드는 프로퍼티의 키로 사용되는 심볼들로 이뤄진 배열을 반환한다. 일반적인 객체는 심볼이 키인 프로퍼티가 없기에 빈 배열을 반환한다.

 

또한, 객체를 JSON으로 만들 때(JSON.stringify() 메소드 호출)도 키가 심볼인 프로퍼티들은 무시된다. 이에 주의하도록 하자.

```javascript
const sym = Symbol('foo');
const obj = {
    [sym]: 'propertyValue1',
    propertyKey2: 'propertyValue2'
};

JSON.stringify(obj);  // {"propertyKey2":"propertyValue2"}
```


### 참조자료

- [elodrado 블로그](https://it-eldorado.tistory.com/149)
