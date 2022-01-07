# event loop🎉

이벤트 루프란 `Callback Queue` 에서 하나씩 꺼내서 동작시키는 Loop를 말합니다.
일단 이번 `event loop` 를 이해하기 위해서는 큰 그림을 그리고 단어를 혼동하면 안됩니다. 
단어를 혼동하지 않고 딱 3단어만 기억하면 쉽게 이해할 수 있을 것입니다. 지금 단계에서는 단어와 정의를 암기하는 느낌으로 하고 예제를 살펴보면 암기가 아닌 이해를 할 수 있을 것입니다.

### Keyword for eventloop

- `Call Stack` (호출 스택)
  -  실행될 코드의 한 줄 단위로 할당되는 스택 
- `Web APIs` (노드에서는 백그라운드로 설멷된다)
  - 비동기 처리를 담당한다 ➡ 비동기 함수들이(지금바로 실행안되는) 쌓인다
- `Callback Queue`
  -  비동기 처리가 끝난 후 실행되어야 할 콜백 함수

![image](https://user-images.githubusercontent.com/69495129/148482939-2b75541e-25b0-4485-aadb-0c38828740d9.png)


---

### 비동기적인 코드 예제

```javascript
console.log("시작")

setTimeout(function() {
  console.log("중간");
},3000);

console.log("끝");
```

이때 작동이 어떻게 되는지는 위에서 말한 `Call Stack` `Web APIs` `Callback Queue` 를 떠올리며 그려보자

### 작동 순서
1. Call Stack에 anonymous 가 쌓임
2. console.log("시작") 이 Call Stack 에 쌓였다가 바로 실행 (비동기가아님)
3. setTimeOut 이 비동기이므로 Web APIs 에 callback 함수인 console.log("중간") 을 실행시키는 함수가 들어감
4. Web APIs 에는 현재 timeout(비동기함수) 가 있고 일처리를 끝내면 해당하는 callback 함수가 Callback Queue 로 들어감
5. console.log("끝") 이 Call Stack 에 쌓였다가 바로 실행 (비동기가아님)
6. 끝까지 찍히면 anonymous 만 남아있고 이마저도 코드가 끝나서 나가게됌
7. 이때 event loop 가 계속 돌고있었고 Call Stack 이 비었다는것을 감지함
8. 이때 Call Stack 은 비어있고 Callback Queue 는 익명함수(아까 callback 함수) 가 있음 
9. 이제 Call Stack 이 비었으니깐 Callback Queue 의 익명함수를 Call Stack 에 옮겨준다.
10. Call Stack 을 비워주면서 console.log("중간") 이 실행된다 
11. 끝


---

### 다른 예제 비동기 함수가 2개!

```javascript
console.log("시작");

setTimeout(function () {
  console.log("중간");
},0);

Promise.resolve()
  .then(function() {
     console.log("프로미스"); 
  }
}

console.log("끝");
```


### 작동순서

1. anonymous 가 Call Stack에 쌓임
2. console.log("시작") 이 Call Stack 에 들어왔다가 실행되서 출력된다.
3. setTimeout(callback) 함수가 Call Stack 에 들어간다
4. 방금 setTimeout 은 비동기함수이기 때문에 Web APIs 로 들어간다 
5. promise 가 Call Stack 에 들어간다 (주의 promise 는 동기이다!!! promise 는 비동기라고 얘기하면안된다) Promise 자체는 동기인데 then 을 만나면 비동기로 동작하게 된다.
6. 엔진이 promise 를 비동기로 인식해서 web APIs 로 옮겨준다
7. console.log("끝") 이 call stack 에 쌓였다가 실행된다.
8. 지금 Web APIs 에는 then 과 timeout 이 있고 timeout이 먼저끝났다고 가정하겠다
9. timeout의 callback 함수인 익명함수가 callback queue 로 넘어간다
10. then 의 callback 함수가 callback queue 로 넘어간다.
11. anonymous 가 call stack 에서 빠진다(코드가 더이상없음)
12. event loop 가 call stack 이 비었다는 것을 인지한다
13. 이제 callback queue 에서 두 익명함수중 then 꺼를 가져온다! (timeout보다 promise 의 비동기가 우선순위가 더 높다😎)
14. then 의 callback 함수를 실행시킨다
15. timeout의 callback 함수를 실행시킨다
16. 끝

---

### callback queue 의 우선순위

callback queue 와 동치로 쓰이고 있는 단어들이 많지만 상하관계는 다음과같다.

callback queue 의 내부에 종류들이 많다. callback queue 중에 `Task Queue`, `Microtask Queue` , `Animation Frames`  가 있다.
이때 우선순위는 다음과 같다.

`Microtask Queue > Animation Frames > Task Queue` 이다. 아까 Promise 같은경우는 `Microtask Queue` 에 속했고, setTimeout 같은 경우는 `Task Queue`
에 속한다. 그래서 setTImeout 이 먼저 callback queue 에 들어갔다 할지언정, callback queue 에서 call stack 에 들어갈때는 microtask queue 의 분류인 promise 가 먼저 실행된다!

---

### 정리
- 머리속으로 처음에말한 3가지 Call Stack , WEB APIs , Callback queue 이 세가지를 그려가면서 뭐가 들어가고 나가는지를 생각하며 코드를 진행하면 편하다.

1. 코드가 호출스택에 쌓인 후, 실행되면 자바스크립트 엔진은 비동기 작업을 Web API에게 위임한다.
2. Web API는 해당 비동기 작업을 수행하고, 콜백 함수를 이벤트 루프를 통해서 태스트 큐에 넘겨주게된다.
3. 이벤트 루프는 콜스택에 쌓여있는 함수가 없을 때, 태스크 큐에서 대기하고 있던 콜백함수를 콜스택으로 넘겨준다.
4. 콜스택에 쌓인 콜백함수가 실행되고, 콜스택에서 제거된다.



### 참조 자료

- [우리밋님 강의](https://www.youtube.com/watch?v=S1bVARd2OSE)
- [zereight님 블로그](https://zereight.tistory.com/855)

