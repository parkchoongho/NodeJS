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

### Callbacks

Callback 함수는 다른 함수의 argument로 들어가고 이벤트에 기반해 작동하는 함수입니다. 콜백함수는 즉시 실행되는 것이 아닌 특정 시점에서 실행되는 함수입니다.

```javascript
console.log("시작!");

getUser(1, user => {
  console.log("User: ", user);
  getRepos(user.github, repos => {
    console.log("Repos: ", repos);
  });
});

console.log("끝!");

function getUser(id, callback) {
  setTimeout(() => {
    console.log("데이터 베이스에서 읽어오는 중입니다....");
    callback({ id: id, github: "pchyo92" });
  }, 2000);
}
function getRepos(userName, callback) {
  setTimeout(() => {
    console.log("GitHub API에 요청 중....");
    callback(["repo1", "repo2", "repo3"]);
  }, 2000);
}
```

위의 예제를 봅시다. 우선 결과는 이렇게 나타납니다.

```javascript
PS C:\Users\User\Desktop\Project\express-demo> node .\index.js
시작!
끝!
데이터 베이스에서 읽어오는 중입니다....
User:  { id: 1, github: 'pchyo92' }
데이터 베이스에서 읽어오는 중입니다....
Repos:  [ 'repo1', 'repo2', 'repo3' ]
```

우선 Call stack에는 getUser 함수가 쌓입니다. getUser 함수안에 있던 setTimeout 함수가 실행되고 해당 코드가 Web APIs에 쌓이게 됩니다. setTimeOut 함수는 종료되고 호출된 WebAPI는 요청 시간동안 대기하게 됩니다. 그 다음 `'끝!'` 을 console에 찍는 함수가 호출되고 종료됩니다. 그렇게 콜 스택에는 실행할 자바스크립트 코드가 없는 채로 비워집니다.

그 다음 Web API에서 timeout이 만료되고 Web API는 이벤트 루프에 해당 코드를 Event Loop에 보냅니다.Event Loop는 콜 스택에 실행할 코드가 있으면 콜 스택에 Push하지 않습니다. 현재는 콜 스택이 없으므로 Event Loop에 있던 코드들이 콜스택에 올라가며 실행됩니다.

그러면 `console.log("데이터 베이스에서 읽어오는 중입니다....")` 다 실행되고 해당 callback 함수를 실행합니다. 해당 Callback 함수에서의 `console.log("User: ", user)` 부분이 실행되고 그 다음 getRepos 함수가 실행됩니다.

getRepos함수 안에서 setTimeOut 함수가 실행되면서 해당 코드가 Web APIs에 들어갑니다. 요청 시간이 끝난 코드는 Event Loop안에 들어갑니다. 마지막에 호출됐던 getRepos 함수까지 종료되면 EventLoop안에서 대기하고 있던 나머지 코드들이 실행되면서 프로그램은 마무리됩니다.