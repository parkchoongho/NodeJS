# Asynchronous JavaScript

### Synchronous vs Asynchronous

JavaScript는 Asynchronous language입니다. 우선 Asynchronous가 어떤 말인지부터 알아보겠습니다.

프로그램 코드는 위에서 부터 아래로 진행됩니다. 따라서 위에 있는 코드가 다 진행되지 않았다면 아래에 있는 코드는 실행될 수 없습니다.

```javascript
console.log("시작!");

console.log("중간");

console.log("끝!");
```

위와 같은 코드를 진행하면 콘솔에는 아래와 같이 나타날 것입니다.

```powershell
PS C:\Users\User\Desktop\Project\express-demo> node index.js
시작!
중간
끝!
```

시작을 나타내는 console.log가 실행되고 종료된 이후 그 다음, 중간을 나타내는 console.log가 실행되고 종료된 담에야 끝을 나타내는 console.log가 실행됩니다.

```javascript
console.log("시작!");

setTimeout(() => {
  console.log("중간!");
}, 1000);

console.log("끝!");
```

이처럼 코드를 작성하고 실행하면 아래와 같이 결과가 나타납니다.

```powershell
PS C:\Users\User\Desktop\Project\express-demo> node index.js
시작!
끝!
중간!
```

setTimeout 함수는 argument1 으로 받는 함수를 argument2로 받는 시간 뒤에 실행하는 함수입니다. 그런데 자바스크립트는 2초라는 시간후에 setTimeout이 실행되고 종료 (blocking)되고 난 후에 그 다음 코드를 진행하는 것이 아닌 setTimeout()을 실행하고 난 후 바로 그 다음 코드를 진행하는 특징을 가지고 있습니다. 따라서 중간! 이라는 텍스트가 가장 나중에 찍히는 것이죠. 이것이 바로 Asynchronous, non-blocking 이라 불리는 자바스크립트의 특징입니다. 

### Patterns for Dealing with Asynchronous Code

자바스크립트에는 이러한 Aynchronous를 Hanling 할 수 있는 Coding Pattern이 존재합니다. 크게 3가지가 있는데 

- Callbacks
- Promises
- Async/Await

이 바로 그것들 입니다. 다음에서는 먼저 Callback에 대해서 알아보겠습니다.

