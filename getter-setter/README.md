# Getter & Setter

JS 에도 getter & setter 를 구현할 수 있다는 것을 알았습니다..🙄

### 프로퍼티(Property) 의 종류
프로퍼티란 2가지가 있으며, 저도 지금까지는 `데이터 프로퍼티`만 알고 있었고 get과 set 은 `접근자 프로퍼티` 라고 합니다.

- 데이터 프로퍼티
  -   값을 저장하기 위한 프로퍼티, 일반적으로 사용하는 프로퍼티이다.
- 접근자 프로퍼티
  -   값이 없고, 프로퍼티를 읽거나 쓸 때 호출하는 함수를 값 대신에 지정하는 프로퍼티
  -   접근자 프로퍼티의 본질은 함수인데, 값을 획득(get) 하거나 설정(set) 하는 역할을 담당합니다.
  -   그런데 외부 코드에서는 함수가 아닌 일반적인 프로퍼티처럼 보입니다.

### 왜 접근자 프로퍼티를 사용?
자바를 많이 개발하신 분들은 익숙하시겠지만, 자바에서 `getter` `setter` 를 사용한 이유와 같습니다.

```
접근자란 객체 지향 프로그래밍에서 객체가 가진 프로퍼티 값을 객체 바깥에서 읽거나 쓸 수 있도록 제공하는 메서드를 말합니다. 
객체의 프로퍼티를 객체 바깥에서 직접 조작하는 행위는 데이터의 유지 보수성을 해치는 주요한 원인입니다. 
- 아소 히로시, 모던 자바스크립트 입문 9.3
```


### getter 와 setter
접근자 프로퍼티는 'getter(획득자)'와 ‘setter(설정자)’ 메서드로 표현됩니다. 객체 리터럴 안에서 getter와 setter 메서드는 get과 set으로 나타낼 수 있습니다.

```javascript
let obj = {
 get propName() {
   // getter, obj.propName을 실행할 때 실행되는 코드
 },

 set propName(value) {
   // setter, obj.propNAme = value를 실행할 때 실행되는 코드
 }
};
```

사용할 때 사용방법은 주석에 명시되어 있습니다.

- getter 메서드는 obj.propName을 사용해 프로퍼티를 읽으려고 할 때 실행됩니다.
- setter 메서드는 obj.propName = value으로 프로퍼티에 값을 할당하려 할 때 실행됩니다.


### `getter` method

```javascript
let user = {
  name: "chanhyuk",
  surname: "park",

  get fullName() {
    return `${this.name} ${this.surname}`;
  }
};

alert(user.fullName); // chanhyuk park
```

바깥 코드에선 접근자 프로퍼티를 일반 프로퍼티처럼 사용할 수 있습니다. 접근자 프로퍼티는 이런 아이디어에서 출발했습니다. 접근자 프로퍼티를 사용하면 함수처럼 호출 하지 않고, 일반 프로퍼티에서 값에 접근하는 것처럼 평범하게 user.fullName을 사용해 프로퍼티 값을 얻을 수 있습니다. 나머지 작업은 getter 메서드가 뒷단에서 처리해줍니다.
함수를 실행하고 싶을때 user.fullName() 으로 호출하는것이 아닌, user.fullName 만 작성해주면 이미 getter method 이기때문에 알아서 뒷단에서 처리해줍니다.


한편, 위 예시의 fullName은 getter 메서드만 가지고 있기 때문에 user.fullName=을 사용해 값을 할당하려고 하면 에러가 발생합니다.

```javascript
let user = {
  get fullName() {
    return `...`;
  }
};

user.fullName = "Test"; // Error (프로퍼티에 getter 메서드만 있어서 에러가 발생합니다.)
```


### `setter` method

```javascript
let user = {
  name: "John",
  surname: "Smith",

  get fullName() {
    return `${this.name} ${this.surname}`;
  }
  
  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  }
};

// 주어진 값을 사용해 set fullName이 실행됩니다.
user.fullName = "Alice Special"

alert(user.fullName); // Alice Special
alert(user.name); // Alice
alert(user.surname); // Special
```
이렇게 getter와 setter 메서드를 구현하면 객체엔 fullName이라는 '가상’의 프로퍼티가 생깁니다. 가상의 프로퍼티는 읽고 쓸 순 있지만 실제로는 존재하지 않습니다.

### 접근자 프로퍼티 설명자

데이터 프로퍼티의 설명자와 접근자 프로퍼티의 설명자는 다릅니다.

접근자 프로퍼티엔 설명자 value와 writable가 없는 대신에 get과 set이라는 함수가 있습니다.

따라서 접근자 프로퍼티는 다음과 같은 설명자를 갖습니다.

아래와 같이 defineProperty에 설명자 get과 set을 전달하면 fullName을 위한 접근자를 만들 수 있습니다.

```javascript
let user = {
  name: "John",
  surname: "Smith"
};

// 프로퍼티 정의
Object.defineProperty(user, 'fullName', {
  get() {
    return `${this.name} ${this.surname}`;
  },

  set(value) {
    [this.name, this.surname] = value.split(" ");
  }
});

alert(user.fullName); // John Smith

for(let key in user) alert(key); // name, surname
```

```
프로퍼티는 접근자 프로퍼티(get/set 메서드를 가짐)나 데이터 프로퍼티(value를 가짐)
중 한 종류에만 속하고 둘 다에 속할 수 없다는 점을 항상 유의하시기 바랍니다.
```

### 참조 자료

- [김민수님 깃허브](https://github.com/Minsoo-web/es_features/blob/master/es6/02.functions/06.getter_setter.md)
- [Velog: bigbroShin.log](https://velog.io/@bigbrothershin/JavaScript-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-getter-setter)


