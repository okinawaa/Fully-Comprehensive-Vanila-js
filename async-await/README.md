# JS async & await

### What is a async & await 🤔
async와 await는 자바스크립트의 비동기 처리 패턴 중 가장 최근에 나온 문법입니다.
기존의 비동기 처리 방식인 콜백 함수와 프로미스의 단점을 보완하고 개발자가 읽기 좋은 코드를 작성할 수 있게 도와주죠.

### async & await 맛보기

```javascript
function logName() {
  var user = fetchUser('domain.com/users/1');
  if (user.id === 1) {
    console.log(user.name);
  }
}
```

위 코드가 async & await 으로 변환하면 아래와 같이 바뀝니다. 문법 자체는 엄청 쉽습니다.

```javascript
async function logName() {
  var user = await fetchUser('domain.com/users/1');
  if (user.id === 1) {
    console.log(user.name);
  }
}
```

### async & await 적용된 코드와 그렇지 않은 코드 비교

조금 전에 본 코드가 대체 어떤 의미인지 한번 알아보겠습니다. 먼저 아까 살펴봤던 logName() 함수 코드를 다시 보겠습니다.

```javascript
function logName() {
  var user = fetchUser('domain.com/users/1');
  if (user.id === 1) {
    console.log(user.name);
  }
}
```

여기서 fetchUser()라고 하는 코드는 서버에서 데이터를 받아오는 HTTP 통신 코드라고 가정했습니다. 
일반적으로 자바스크립트의 비동기 처리 코드는 아래와 같이 콜백을 사용해야지 코드의 실행 순서를 보장받을 수 있죠.

```javascript
function logName() {
  // 아래의 user 변수는 위의 코드와 비교하기 위해 일부러 남겨놓았습니다.
  var user = fetchUser('domain.com/users/1', function(user) {
    if (user.id === 1) {
      console.log(user.name);
    }
  });
}
```

서버에서 사용자 데이터를 불러와서 변수에 담고, 사용자 아이디가 1이면 사용자 이름을 출력한다.

이렇게 하려면 async await만 붙이시면 됩니다 😊

```javascript
// async & await 적용 후
async function logName() {
  var user = await fetchUser('domain.com/users/1');
  if (user.id === 1) {
    console.log(user.name);
  }
}
```

### async & await 기본 문법

앞에서 언급한 것과 같이 문법은 엄청 간단합니다😎

```javascript
async function 함수명() {
  await 비동기_처리_메서드_명();
}
```
먼저 함수의 앞에 async 라는 예약어를 붙입니다. 
그러고 나서 함수의 내부 로직 중 HTTP 통신을 하는 비동기 처리 코드 앞에 await를 붙입니다. 
여기서 주의하셔야 할 점은 비동기 처리 메서드가 꼭 프로미스 객체를 반환해야 await가 의도한 대로 동작합니다.

일반적으로 await의 대상이 되는 비동기 처리 코드는 Axios 등 프로미스를 반환하는 API 호출 함수입니다.

`React` 에서 useEffect 를 async 로 만드는 것은 불가능하므로 일반적으로 useEffect 에서 새로운 함수를 선언하고 그 함수에 async 를 부여합니다👀

### async & await 간단한 예제

```javascript
function fetchItems() {
  return new Promise(function(resolve, reject) {
    var items = [1,2,3];
    resolve(items)
  });
}

async function logItems() {
  var resultItems = await fetchItems();
  console.log(resultItems); // [1,2,3]
}
```

먼저 fetchItems() 함수는 프로미스 객체를 반환하는 함수입니다. 프로미스는 “자바스크립트 비동기 처리를 위한 객체“라고 배웠었죠. fetchItems() 함수를 실행하면 프로미스가 이행(Resolved)되며 결과 값은 items 배열이 됩니다.

그리고 이제 logItems() 함수를 보겠습니다. logItems() 함수를 실행하면 fetchItems() 함수의 결과 값인 items 배열이 resultItems 변수에 담깁니다. 따라서, 콘솔에는 [1,2,3]이 출력되죠.

await를 사용하지 않았다면 데이터를 받아온 시점에 콘솔을 출력할 수 있게 콜백 함수나 .then()등을 사용해야 했을 겁니다. 하지만 async await 문법 덕택에 비동기에 대한 사고를 하지 않아도 되는 것이죠.


### async & await 실용 예제

async & await 문법이 가장 빛을 발하는 순간은 여러 개의 비동기 처리 코드를 다룰 때입니다. 아래와 같이 각각 사용자와 할 일 목록을 받아오는 HTTP 통신 코드가 있다고 하겠습니다.
원래는 이렇게 많은 비동기 처리 코드를 다룰 때는 콜백 지옥을 경험했습니다.

```javascript
function fetchUser() {
  var url = 'https://jsonplaceholder.typicode.com/users/1'
  return fetch(url).then(function(response) {
    return response.json();
  });
}

function fetchTodo() {
  var url = 'https://jsonplaceholder.typicode.com/todos/1';
  return fetch(url).then(function(response) {
    return response.json();
  });
}
```


위 함수들을 실행하면 각각 사용자 정보와 할 일 정보가 담긴 프로미스 객체가 반환됩니다.

자 이제 이 두 함수를 이용하여 할 일 제목을 출력해보겠습니다. 살펴볼 예제 코드의 로직은 아래와 같습니다.

fetchUser()를 이용하여 사용자 정보 호출
받아온 사용자 아이디가 1이면 할 일 정보 호출
받아온 할 일 정보의 제목을 콘솔에 출력
그럼 코드를 보겠습니다.



```javascript
async function logTodoTitle() {
  var user = await fetchUser();     //  사용자 정보 호출
  if (user.id === 1) {        
    var todo = await fetchTodo(); // 할일 목록 호출
    console.log(todo.title); // delectus aut autem
  }
}

```

logTodoTitle()를 실행하면 콘솔에 할일 목록이 출력될 것입니다.
위 비동기 처리 코드를 만약 콜백이나 프로미스로 했다면 훨씬 더 코드가 길어졌을 것이고 인덴팅 뿐만 아니라 가독성도 좋지 않았을 겁니다.
(가독성은 프로그래밍에서 엄청 중요합니다😎)

### async & await 예외 처리
점점 프로그래밍을 할수록 예외 처리에 대한 중요성을 깨닫게 됩니다.

async & await에서 예외를 처리하는 방법은 바로 try catch입니다. 프로미스에서 에러 처리를 위해 .catch()를 사용했던 것처럼 async에서는 catch {} 를 사용하시면 됩니다.

조금 전 코드에 바로 try catch 문법을 적용해보겠습니다.

```javascript
async function logTodoTitle() {
  try {         // 이것을 실행해라
    var user = await fetchUser();
    if (user.id === 1) {
      var todo = await fetchTodo();
      console.log(todo.title); // delectus aut autem
    }
  } catch (error) {     // 만약 위 실행중 에러가 떳다면 여기로 들어오게 된다.
    console.log(error);
  }
}
```

위의 코드를 실행하다가 발생한 네트워크 통신 오류뿐만 아니라 간단한 타입 오류 등의 일반적인 오류까지도 catch로 잡아낼 수 있습니다. 발견된 에러는 error 객체에 담기기 때문에 에러의 유형에 맞게 에러 코드를 처리해주시면 됩니다.


### 참고자료


- [캡틴판교님](https://joshua1988.github.io/web-development/javascript/js-async-await/)















