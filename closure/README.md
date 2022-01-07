# Closure

클로저는 예시와 함께하는게 이해가 편했습니다.

### 예시

```javascript

function makeAdder(x){
  return function(y){
    return x + y ;
  }
}


const add3 = makeAdder(3);
console.log(add3(2)); // 5

const add10 = makeAdder(10);
console.log(add10(5)); // 15
consold.log(add3(1)); // 4
```

### 설명
add3(2) 가 실행될 때 그 함수 자체에는 x 변수가 없지만 이미 lexical 환경에서 외부변수 x를 갖고있기 때문에 makeAdder 의 x 에 접근이 가능합니다.
클로저는 함수와 그 함수의 렉시컬 환경의 조합입니다. 함수가 생성될 당시의 외부 변수를 기억합니다. 생성 이후에도 계속 접근이 가능합니다.

makeAdder(10) 이 호출되도 add3 과는 무관합니다. 즉 서로 다른 환경을 갖고 있습니다.

---
### 다른 예시

```javascript
function makeCounter() {
  let num = 0;
  
  return function () {
    return num++;
  }
}


let counter = makeCounter();

console.log(counter()) ; // 0
console.log(counter()) ; // 1
console.log(counter()) ; // 2
```


### 설명
위 예시처럼 counter 는 외부 변수 `num` 을  알아챌 수 있다. 즉 외부 변수 num 에 접근이 가능하다.
위 처럼 코드를 작성한다면, num 은 외부에서 제어가 불가능하므로 은닉성에 성공했다고 볼 수 있다.
---
### 다른예시 

```javascript
function makeAdder(x){
  let y = 1;
  return function(z){
    y = 100;
    return x + y + z;
  }
}

const add5 = makeAdder(5); // 인자에 5를 더하는 함수
const add10 = makeAdder(10);  // 인자에 10을 더하는 함수
// 클로저에 x와 y가 저장됨 x 는 makeAdder 의 인자로 5와10을 각각넘겨줬고 y는 선언 및 초기화

console.log(add5(2)); // 107 (x: 5, y: 100, z: 2)
console.log(add10(2)); // 112 (x: 10, y: 100, z: 2)
// 함수 실행시 클로저에 저장된 x,y 값에 접근하여 값을 계산 클로저가 선언될 때 그 때 lexical 환경 관찰

```

### 설명

`add5` 와 `add10` 을 비교해보겠습니다 `add5` 와 `add10` 둘 다 클로저입니다.
이들은 같은 함수 본문 정의를 공유하지만 서로 다른 lexical 환경을 저장합니다. 함수 실행시 add5 의 lexical 환경에서 클로저 내부의 x는 5이지만,
add10 의 lexical 환경에서 x는 10입니다.

이는 클로저가 리턴된 후에도 외부함수의 변수들에 접근이 가능하다는 것을 보여주는 포인트이며
클로저에 단순히 값 형태로 전달되는 것이 아니라는 것을 의미합니다.

---

### 클로저 활용 (Private 접근자 흉내내기)

```javascript
const counter = (function(){
    let privateCounter = 0;
    function changeBy(val) {
        privateCounter += val;
    }
    return {
        increment: function(){
            changeBy(1);
        },
        decrement: function(){
            changeBy(-1);
        },
        value: function(){
            return privateCounter;
        }
    };
})();

console.log(counter.value()); // 0
counter.increment();
counter.increment();
console.log(counter.value()); // 2
counter.decrement();
console.log(counter.value()); // 1
```

### 설명
counter 라는 네임스페이스가 생성이되고 내부의 privateCounter 변수와 changeBy 메소드는 외부로 return 되지 않기 때문에 외부에서 직접 접근이 불가능합니다 즉,
private한 메소드와 변수가 된 것을 알 수 있습니다.

또한 리턴된 세 퍼블릭 함수들은 같은 환경을 공유하는 클로저가 됨으로서
어휘적 유효 범위 덕분에 private한 변수와 메소드를 사용하고 접근할 수 있게 되는 겁니다. (여기서 private한 변수와 메소드란 그 당시의 lexical 환경에서의 변수 privateCounter 와 changeBy() 메소드를 나타냅니다)


---


### 정리
- 함수와 그 함수의 렉시컬 환경의 조합! 
- 함수 그 자체가 갖고 있는 변수가 아닐지라도 함수 생성당시의 외부변수가 있었다면 변수 접근가능!
- 클로저가 선언될 그 당시에 lexical 환경 관찰😊


### 참고 문서

- [김민수님 깃허브](https://github.com/ChanhyukPark-Tech/es_features/blob/master/etc/closure.md)
