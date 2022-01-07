# event Loop🎉

이벤트 루프란 `Callback Queue` 에서 하나씩 꺼내서 동작시키는 Loop를 말합니다.
일단 이번 `event loop` 를 이해하기 위해서는 큰 그림을 그리고 단어를 혼동하면 안됩니다. 
단어를 혼동하지 않고 딱 3단어만 기억하면 쉽게 이해할 수 있을 것입니다. 지금 단계에서는 단어와 정의를 암기하는 느낌으로 하고 예제를 살펴보면 암기가 아닌 이해를 할 수 있을 것입니다.

### Keyword for eventloop

- Call Stack (호출 스택)
  -  실행될 코드의 한 줄 단위로 할당되는 스택 
- Web APIs (노드에서는 백그라운드로 설멷된다)
  - 비동기 처리를 담당한다 ➡ 비동기 함수들이(지금바로 실행안되는) 것들이 쌓인다
- Callback Queue
  -  비동기 처리가 끝난 후 실행되어야 할 콜백 함수

![image](https://user-images.githubusercontent.com/69495129/148482939-2b75541e-25b0-4485-aadb-0c38828740d9.png)
