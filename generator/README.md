![image](https://user-images.githubusercontent.com/69495129/148337453-4cc28dc8-5dd7-4f8e-b60f-52ee49740b7d.png)

`function*` 은 `generator function` 을 정의합니다. `Generator` object 를 반환합니다. 

---

#### 예시

```javascript
function* generator(i) {
  yield i;
  yield i + 10;
  yield i + 20;
}

const gen = generator(10);

console.log(gen.next().value);
// expected output: 10
console.log(gen.next().value);
// expected output: 20
console.log(gen.next().value);
```

#### 설명

Generator 는 함수를 중간에 멈출 수 있다 라고 많이들 이야기를 하는데 저는 처음에 저말을 듣고 이해가 되지 않았습니다😂
밑의 설명을 천천히 읽으면 이 추상적인 말이 이해가 될 것입니다. 위 코드를 보시면서 설명을 읽어보세요!

Generator 함수는 호출될때 즉시 실행되지 않고, 대신 함수를 위한 `Iterator` 객체가 반환됩니다. Iterator의 next() 메서드를 호출하면 Generator 함수가 실행되어 yield 문을 만날 때까지 진행하고, 해당 표현식이 명시하는 Iterator로부터의 반환값을 반환합니다. yield* 표현식을 마주칠 경우, 다른 Generator 함수가 위임(delegate)되어 진행됩니다. 이후 next() 메서드가 호출되면 진행이 멈췄던 위치에서부터 재실행합니다. 처음에 한번 실행 후 다시 실행될 곳은 위 코드의 주석 expected output : 10 부분일 것 입니다. next() 가 반환하는 객체는 yield문이 반환할 값(yielded value)을 나타내는 value 속성과, Generator 함수 안의 모든 yield 문의 실행 여부를 표시하는 boolean 타입의 done 속성을 갖습니다. done 을 통해서 모든 yield 가 실행되었는지도 판단 할 수 있습니다. JAVA 의 JDBC 를 사용해보신분은 이해가 편하실 겁니다. next() 를 인자값과 함께 호출할 경우, 진행을 멈췄던 위치의 yield 문을  next() 메서드에서 받은 인자값으로 치환하고 그 위치에서 다시 실행하게 됩니다.



---
