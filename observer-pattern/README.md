# Observer Pattern에 대해 이해하기


### What is the javascript observer

> The Observer pattern offers a subscription model in which objects subscribe to an event and get notified when the event occurs. This pattern is the cornerstone of event driven programming, including JavaScript. The Observer pattern facilitates good object-oriented design and promotes loose coupling.


중앙 집중식 저장소를 간단하게 `Store` 라고 표현해보자. `Store를` 구현하기 위해 먼저 `저장소(Store)`와 `컴포넌트(Component)`의 관계를 잘 살펴봐야 한다.

- Store는 여러 개의 컴포넌트에서 사용될 수 있다..
- Store가 변경될 때, Store가 사용되고 있는 Component도 변경되어야 한다.


```js
// Store를 생성한다.
const store = new Store({
  a: 10,
  b: 20,
});

// 컴포넌트를 생성한다.
const component1 = new Component({ subscribe: [store] });
const component2 = new Component({ subscribe: [store] });

// 컴포넌트가 store를 구독한다.
component1.subscribe(store); // a + b = ${store.state.a + store.state.b}
component2.subscribe(store); // a * b = ${store.state.a * store.state.b}

// store의 state를 변경한다.
store.setState({
  a: 100,
  b: 200,
});

// store가 변경되었음을 알린다.
store.notify();
```

- 처음에 component1은 a + b = 30을 출력하고, component2는 a * b = 200 을 출력할 것이다.
- store의 값이 변경된 다음에는 각각 a + b = 300 a * b = 20000 을 출력할 것이다

이러한 형태로 코드를 작성하는 것을 `Observer Pattern` 이라고 한다.

- Observer pattern은 객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴이다.
- 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용된다.
- 발행/구독 모델로 알려져 있기도 한다.




### Publish
제일 먼저 `발행기관(Publish)`를 만들어보자.

```js
class 발행기관 {
  #state;
  #observers = new Set();

  constructor (state) {
    this.#state = state;
    Object.keys(state).forEach(key => Object.defineProperty(this, key, {
      get: () => this.#state[key]
    }));
  }

  내부에_변화가_생김 (newState) {
    this.#state = { ...this.#state, ...newState };
    this.구독자에게_알림();
  }

  구독자_등록 (subscriber) {
    this.#observers.add(subscriber);
  }

  구독자에게_알림 () {
    this.#observers.forEach(fn => fn());
  }
}
```

- 위에서 핵심적인 내용은 `내부에 변화가 생길 경우 구독자에게 알리는 것` 이다.

```js
내부에_변화가_생김 (newState) {
  this.#state = { ...this.#state, ...newState };
  this.구독자에게_알림();
}
```


### Subscriber

이제 `구독자(Subscriber)`를 만들어보자.

```js
class 구독자 {
  #fn;

  constructor (발행기관에_변화가_생길_때_하는_일) {
    this.#fn = 발행기관에_변화가_생길_때_하는_일;
  }

  구독 (publisher) {
    publisher.구독자_등록(this.#fn);
  }
}
```

- 구독자는 `발행기관에서 변화가 생겼을 때 하는 일을 정의`해야 한다.
- 그리고 `발행기관을 구독` 한다.


### Apply

```js
const 상태 = new 발행기관({
  a: 10,
  b: 20,
});

const 덧셈계산기 = new 구독자(() => console.log(`a + b = ${상태.a + 상태.b}`));
const 곱셈계산기 = new 구독자(() => console.log(`a * b = ${상태.a * 상태.b}`));

덧셈계산기.구독(상태);
곱셈계산기.구독(상태);

상태.구독자에게_알림();
// a + b = 30
// a * b = 200

상태.내부에_변화가_생김({ a: 100, b: 200 });
// a + b = 300
// a * b = 20000
```


### 참조 자료
- [개발자 황준일](https://junilhwang.github.io/TIL/Javascript/Design/Vanilla-JS-Store/#_1-%E1%84%8C%E1%85%AE%E1%86%BC%E1%84%8B%E1%85%A1%E1%86%BC-%E1%84%8C%E1%85%B5%E1%86%B8%E1%84%8C%E1%85%AE%E1%86%BC%E1%84%89%E1%85%B5%E1%86%A8-%E1%84%89%E1%85%A1%E1%86%BC%E1%84%90%E1%85%A2%E1%84%80%E1%85%AA%E1%86%AB%E1%84%85%E1%85%B5)
- [dofactory](https://www.dofactory.com/javascript/design-patterns/observer)
