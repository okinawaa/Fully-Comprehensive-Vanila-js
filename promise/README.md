# Promise !

---

### Promise가 뭔가요?
`A promise is an object that may produce a single value some time in the future`

프로미스는 자바스크립트 비동기 처리에 사용되는 객체입니다. 
여기서 자바스크립트의 비동기 처리란 ‘특정 코드의 실행이 완료될 때까지 기다리지 않고 다음 코드를 먼저 수행하는 자바스크립트의 특성’을 의미합니다. 

### Promise가 왜 필요한가요?

프로미스는 주로 서버에서 받아온 데이터를 화면에 표시할 때 사용합니다. 일반적으로 웹 애플리케이션을 구현할 때 서버에서 데이터를 요청하고 받아오기 위해 아래와 같은 API를 사용합니다.

```javascript
$.get('url 주소/products/1', function(response) {
  // ...
});
```

위 API가 실행되면 서버에다가 `‘데이터 하나 보내주세요’` 라는 요청을 보내죠. 
그런데 여기서 데이터를 받아오기도 전에 마치 데이터를 다 받아온 것 마냥 화면에 데이터를 표시하려고 하면 오류가 발생하거나 빈 화면이 뜹니다.
이와 같은 문제점을 해결하기 위한 방법 중 하나가 프로미스입니다.
리액트에서도 이런 경우가 많았습니다.. 왜 불러왔는데 콘솔에 안찍히지? 라고 고민했던적이 있었습니다😂


### 프로미스 코드 - 기초

그럼 프로미스가 어떻게 동작하는지 이해하기 위해 예제 코드를 살펴보겠습니다. 먼저 아래 코드는 간단한 ajax 통신 코드입니다.

```javascript
function getData(callbackFunc) {
  $.get('url 주소/products/1', function(response) {
    callbackFunc(response); // 서버에서 받은 데이터 response를 callbackFunc() 함수에 넘겨줌
  });
}

getData(function(tableData) {
  console.log(tableData); // $.get()의 response 값이 tableData에 전달됨
});
```

위 코드는 제이쿼리의 ajax 통신 API를 이용하여 지정된 url에서 1번 상품 데이터를 받아오는 코드입니다.
비동기 처리를 위해 프로미스 대신에 콜백 함수를 사용했습니다.

위 코드에 프로미스를 적용하면 아래와 같은 코드가 됩니다.

```javascript
function getData(callback) {
  // new Promise() 추가
  return new Promise(function(resolve, reject) {
    $.get('url 주소/products/1', function(response) {
      // 데이터를 받으면 resolve() 호출
      resolve(response);
    });
  });
}

// getData()의 실행이 끝나면 호출되는 then() // 프로미스를 반환할거임!
getData().then(function(tableData) {
  // resolve()의 결과 값이 여기로 전달됨
  console.log(tableData); // $.get()의 reponse 값이 tableData에 전달됨
});
```
콜백 함수로 처리하던 구조에서 new Promise(), resolve(), then()와 같은 프로미스 API를 사용한 구조로 바뀌었습니다. 
여기서 new Promise()는 좀 이해가 가겠는데 resolve(), then()은 뭐 하는 애들일까요? 
뭔가 getData() 의 실행이 끝나면~~~~~ 그다음 then ?~
아래에서 함께 알아보겠습니다.

### 프로미스의 3가지 상태(states)
프로미스를 사용할 때 알아야 하는 가장 기본적인 개념이 바로 프로미스의 상태(states)입니다. 
여기서 말하는 상태란 프로미스의 처리 과정을 의미합니다.
new Promise()로 프로미스를 생성하고 종료될 때까지 3가지 상태를 갖습니다.

- Pending(대기) : 비동기 처리 로직이 아직 완료되지 않은 상태
- Fulfilled(이행) : 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태 (성공! 주어진 임무를 정확히 이행했다😊)
- Rejected(실패) : 비동기 처리가 실패하거나 오류가 발생한 상태

#### Pending(대기)
먼저 아래와 같이 new Promise() 메서드를 호출하면 대기(Pending) 상태가 됩니다.

```javascript
new Promise();
```

new Promise() 메서드를 호출할 때 콜백 함수를 선언할 수 있고, 콜백 함수의 인자는 resolve, reject입니다.

```javascript
new Promise(function(resolve, reject) {
  // ...
});
```


#### Fulfilled(이행)

여기서 콜백 함수의 인자 resolve를 아래와 같이 실행하면 이행(Fulfilled) 상태가 됩니다.

```javascript
new Promise(function(resolve, reject) {
  resolve(); // 성공했습니다~~~~ 지금은 파라미터를 안넘겨주지만 실제 전달하고자 하는 값을 넘겨줌
});
```
그리고 이행 상태가 되면 아래와 같이 then()을 이용하여 처리 결과 값을 받을 수 있습니다.

```javascript
function getData() {
  return new Promise(function(resolve, reject) {
    var data = 100;
    resolve(data);
  });
}

// resolve()의 결과 값 data를 resolvedData로 받음
getData().then(function(resolvedData) {
  console.log(resolvedData); // 100
});
```

#### Rejected(실패)

new Promise()로 프로미스 객체를 생성하면 콜백 함수 인자로 resolve와 reject를 사용할 수 있다고 했습니다. 여기서 reject를 아래와 같이 호출하면 실패(Rejected) 상태가 됩니다.
위에서 resolve 는 성공(이행) 을 나타냈었습니다😘

```javascript
new Promise(function(resolve, reject) {
  reject();
});
```

그리고, 실패 상태가 되면 실패한 이유(실패 처리의 결과 값)를 catch()로 받을 수 있습니다.
.catch 를 통해서 받을 수 있어요! 에러처리와 연관 되어있습니다.

```javascript
function getData() {
  return new Promise(function(resolve, reject) {
    reject(new Error("Request is failed"));   // 실패 했어 그 이유는 Error 객체를 만들어서 넘겨줄게!
  });
}

// reject()의 결과 값 Error를 err에 받음
getData().then().catch(function(err) {      // 만약에 실패했다면 그게 err 로 넘어올거야! 실패는 reject 안에 담겨있겠지?
  console.log(err); // Error: Request is failed
});
```
![image](https://user-images.githubusercontent.com/69495129/148477211-184a6230-7bd2-40d0-9534-80968fe7ba72.png)


### 프로미스 코드 - 쉬운 예제
그럼 위에서 배운 내용들을 종합하여 간단한 프로미스 코드를 만들어보겠습니다. 이해하기 쉽게 앞에서 살펴본 ajax 통신 예제 코드에 프로미스를 적용해보겠습니다.
```javascript
function getData() {
  return new Promise(function(resolve, reject) {
    $.get('url 주소/products/1', function(response) {
      if (response) {
        resolve(response);
      }
      reject(new Error("Request is failed"));
    });
  });
}

// 위 $.get() 호출 결과에 따라 'response' 또는 'Error' 출력
getData().then(function(data) {
  console.log(data); // response 값 출력
}).catch(function(err) {
  console.error(err); // Error 출력
});
```

위 코드는 서버에서 제대로 응답을 받아오면 resolve() 메서드를 호출하고, 응답이 없으면 reject() 메서드를 호출하는 예제입니다. 호출된 메서드에 따라 then()이나 catch()로 분기하여 응답 결과 또는 오류를 출력합니다.

### 프로미스의 에러 처리 방법
앞에서 살펴본 프로미스 예제는 코드가 항상 정상적으로 동작한다고 가정하고 구현한 예제입니다. 실제 서비스를 구현하다 보면 네트워크 연결, 서버 문제 등으로 인해 오류가 발생할 수 있습니다. 따라서, 프로미스의 에러 처리 방법에 대해서도 알고 있어야 합니다.

에러 처리 방법에는 다음과 같이 2가지 방법이 있습니다.

1.then()의 두 번째 인자로 에러를 처리하는 방법

```javascript
getData().then(
  handleSuccess,
  handleError
);
```

2. `catch()` 를 이용하는 방법

```javascript
getData().then().catch();
```

위 2가지 방법 모두 프로미스의 reject() 메서드가 호출되어 실패 상태가 된 경우에 실행됩니다. 간단하게 말해서 프로미스의 로직이 정상적으로 돌아가지 않는 경우 호출되는 거죠. 아래와 같이 말입니다.

```javascript
function getData() {
  return new Promise(function(resolve, reject) {
    reject('failed');
  });
}

// 1. then()의 두 번째 인자로 에러를 처리하는 코드
getData().then(function() {
  // ...
}, function(err) {
  console.log(err);
});

// 2. catch()로 에러를 처리하는 코드
getData().then().catch(function(err) {
  console.log(err);
});
```

위 두가지 방법이 있지만 주로 두번째 방법을 많이 사용합니다 가독성도 훨씬 좋죠?

### 프로미스 에러 처리는 가급적 catch()를 사용

앞에서 프로미스 에러 처리 방법 2가지를 살펴봤습니다. 개개인의 코딩 스타일에 따라서 then()의 두 번째 인자로 처리할 수도 있고 catch()로 처리할 수도 있겠지만 가급적 catch()로 에러를 처리하는 게 더 효율적입니다.

그 이유는 아래의 코드를 보시면 알 수 있습니다.

```javascript
// then()의 두 번째 인자로는 감지하지 못하는 오류
function getData() {
  return new Promise(function(resolve, reject) {
    resolve('hi');
  });
}

getData().then(function(result) {
  console.log(result);
  throw new Error("Error in then()"); // Uncaught (in promise) Error: Error in then()
}, function(err) {
  console.log('then error : ', err);
});
```

getData() 함수의 프로미스에서 resolve() 메서드를 호출하여 정상적으로 로직을 처리했지만,
then()의 첫 번째 콜백 함수 내부에서 오류가 나는 경우 오류를 제대로 잡아내지 못합니다. (추후 에러는 못잡는다는 것이죠)



### 프로미스 인스턴스 체크

```javascript
const number = Promise.resolve(Math.random());

number.then((result) => {
  console.log(result);
});

number.then((result) => {
  console.log(result);
});

number.then((result) => {
  console.log(result);
});

```

위 코드를 시키면 셋다 다른 수가찍힐것 같은데 세 개 모두 같은 값이 나오게 됩니다.
프로미스로 생성된 비동기 인스턴스는 `한 번`만 실행됩니다.
그래서 한 번 생성된 인스턴스에는 여러 번 then문을 실행해도 처음에 결정된 promise의 값을 보여줍니다.



---

### 참고자료

- [캡틴 판교님](https://joshua1988.github.io/web-development/javascript/promise-for-beginners/)
