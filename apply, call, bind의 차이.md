# apply, call, bind의 차이

## 함수 호출

- 먼저 자바스크립트에서 함수를 호출 하는 방법에는 여러 방법이 있습니다
- 가장 흔하게 사용되는 함수 뒤에 ()를 붙여 호출하는 방법이 있고
- call, aplly를 사용해서 호출하는 방법이 있습니다
- 그렇다면 각 호출 방법에 매개변수들이 어떤 역할을 하는지 차이점은 무엇인지에 대해 자세히 알아보겠습니다.

## Function.prototype.call(), Function.prototype.apply()

- **apply와 call 메서드의 본질적인 기능은 함수를 호출하는 것입니다.**

- call은 aplly와 거의 동일하지만 **call은 인수 목록**을, **apply는 인수 배열 하나**를 받는 다는 점이 중요한 차이점입니다.

- ```javascript
  function getThisBinding() {
    return this;
  }
  
  // this로 사용할 객체
  const thisArg = { a: 1 };
  
  console.log(getThisBinding.apply(thisArg, [1, 2, 3])); // { a: 1}
  console.log(getThisBinding.call(thisArg, 1, 2, 3)); // { a: 1}
  ```

- apply와 call 메서드는 arguments 객체와 같은 유사 배열 객체에 배열 메서드를 사용하는 경우에 유용합니다.

## Function.prototype.bind()

- apply와 call 메서드와 달리 함수를 호출하지 않고 This로 사용할 객체만 전달합니다.

- 그렇기 때문에 bind 메서드를 사용할 땐 명시적으로 호출해줘야 합니다.

- bind 메서드는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 유용하게 사용됩니다.

- ```javascript
  const person = {
    name: 'Im',
    foo(callback) {
      // 1
      setTimeout(callback, 100);
    }
  };
  
  person.foo(function () {
    console.log(`My name is ${this.name}.`); // My name is .
    // 일반 함수로 호출된 경우의 this는 전역 객체로 바인딩 됩니다.
    // 브라우저에는 name이라는 빌트인 프로터이가 있기 때문에 기본 값이 '' 입니다.
    // Node 환경에서 this.name은 undefined 입니다.
  });
  ```

- 위의 예제에서 콜백 함수가 호출되기 이전인 1의 시점에서 this는 foo메서드를 호출한 객체, 즉 person 객체를 가리킵니다

- 그러나 person.foo의 콜백 함수가 일반 함수로서 호출된 2의 시점에서 this는 전역 객체 window를 가리킵니다 

- 따라서 person.foo의 콜백 함수 내부에서 this.name은 window.name과 같습니다

- 위와 같은 상황이 발생하지 않기 위해서는 콜백 함수 내부의 This와 외부 함수 내부의 this를 일치시켜 주어야 합니다.

- bind메서드를 이용해 This를 일치 시키면 다음과 같습니다.

- ```javascript
  const person = {
    name:"Im",
    foo(callback) {
      // bind메서드를 이용해 callback 함수 내부의 this 바인딩을 전달합니다.
      setTimeout(callback.bind(this), 100);
    }
  };
  
  person.foo(function() {
    console.log(`My name is ${this.name}.`); // My name is Im.
  })
  ```