# Closure

클로저는 예시와 함께하는게 이해가 편했습니다.

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
add3(2) 가 실행될 때 그 함수 자체에는 x 변수가 없지만 이미 lexical 환경에서 외부변수 x를 갖고있기 때문에 makeAdder 의 x 에 접근이 가능합니다.
클로저는 함수와 그 함수의 렉시컬 환경의 조합입니다. 함수가 생성될 당시의 외부 변수를 기억합니다. 생성 이후에도 계속 접근이 가능합니다.

makeAdder(10) 이 호출되도 add3 과는 무관합니다. 즉 서로 다른 환경을 갖고 있습니다.



### 정리
- 함수와 그 함수의 렉시컬 환경의 조합! 
- 함수 그 자체가 갖고 있는 변수가 아닐지라도 함수 생성당시의 외부변수가 있었다면 변수 접근가능!

