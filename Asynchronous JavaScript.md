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

### Promises

Promise는 asynchronous한 동작의 eventual 결과를 가지고 있는 객체입니다. 비동기적인 operation이 완료된 후에는, 어떠한 값이나 에러 둘 중 하나를 반환하게 됩니다. Promise는 비동기적인 코드가 실행된 후 어떠한 결과가 반환됨을 약속합니다(Promise).

Promise 객체는 총 세개의 상태 중 한가지 상태에 있게 됩니다.

먼저, Promise 객체를 생성하면 이는 **pending** 상태에 있게 됩니다. 그 후 async operation이 진행된 후, 성공적으로 실행이 되었으면 **Fulfilled**, 실패했다면 **Rejected** 상태가 됩니다.

```javascript
const p = new Promise((resolve, reject) => {
  // Kick off some async work
  // ...

  setTimeout(() => {
    resolve(1); // pending => resolved, fulfilled
    reject(new Error("The Error!!!")); // pending => rejected
  }, 2000);
});

p.then(result => {
  console.log(result);
}).catch(error => {
  console.log(error.message);
});
```

resolve와 reject는 함수입니다. 만일 async operation을 성공적으로 행했다면 resolve함수를 호출하고  에러가 발생했다면 reject 함수를 호출합니다. 만든 promise 객체 p를 활용하여 async operation을 handling해 보았습니다.

### Changing Callback to Promise

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

위에 작성했던 이 콜백 코드를 Promise로 변환시키겠습니다.

```javascript
console.log("시작!");

getUser(1)
  .then(user => {
    console.log("User: ", user);
    return getRepos(user.github);
  })
  .then(repos => console.log("Repos: ", repos))
  .catch(error => console.log(error));

console.log("끝!");

function getUser(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("데이터 베이스에서 읽어오는 중입니다....");
      resolve({ id: id, github: "pchyo92" });
    }, 2000);
  });
}

function getRepos(userName) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("GitHub API에 요청 중....");
      resolve(["repo1", "repo2", "repo3"]);
    }, 2000);
  });
}
```

### Running Promises in Parallel

만일 여러 async operation이 완료된 후의 상태에서부터 코드가 진행되는 Logic을 짜고 싶으면 어떻게 해야 할까요? (예를 들어 Facebook API를 찌르고 Twitter API를 찌른 다음 각각의 결과를 모두 가진 상태를 만든다고 가정해봅시다.)

```javascript
const p1 = new Promise(resolve => {
  setTimeout(() => {
    console.log("Async Operation 1...");
    resolve(1);
  }, 2000);
});

const p2 = new Promise(resolve => {
  setTimeout(() => {
    console.log("Async Operation 2...");
    resolve(2);
  }, 2000);
});

Promise.all([p1, p2]).then(result => console.log(result));
```

위 코드를 실행하면

```powershell
PS C:\Users\User\Desktop\Project\express-demo> node .\promise-api.js
Async Operation 1...
Async Operation 2...
[ 1, 2 ]
```

이렇게 결과가 나타납니다. Promise 클래스의 all method는 둘다 모두 성공적으로 async operation을 진행해야 결과를 가져옵니다. 그리고 각각의 결과를 배열에 담아서 반환합니다. 둘 중 하나라도 에러가 발생한면 reject를 실행합니다.

```javascript
const p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log("Async Operation 1...");
    reject(new Error("Somthing Happen!!"));
  }, 2000);
});

const p2 = new Promise(resolve => {
  setTimeout(() => {
    console.log("Async Operation 2...");
    resolve(2);
  }, 2000);
});

Promise.all([p1, p2])
  .then(result => console.log(result))
  .catch(error => console.log("Error: ", error));
```

위 코드를 실행하면

```powershell
PS C:\Users\User\Desktop\Project\express-demo> node .\promise-api.js
Async Operation 1...
Error:  Error: Somthing Happen!!
    at Timeout._onTimeout (C:\Users\User\Desktop\Project\express-demo\promise-api.js:4:12)
    at listOnTimeout (internal/timers.js:531:17)
    at processTimers (internal/timers.js:475:7)
Async Operation 2...
```

위와 같은 결과가 나타납니다.

만일 해당 Promise 중 하나라도 성공적으로 작동할 경우에 then을 실행하고 싶으면 Promise 클래스의 race method를 사용합니다. (가장 먼저 완료되는 operation이 성공하면 해당 resolve가 return하는 값을 반환합니다.)

```javascript
const p1 = new Promise(resolve => {
  setTimeout(() => {
    console.log("Async Operation 1...");
    resolve(1);
  }, 2000);
});

const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log("Async Operation 2...");

    reject(new Error("Somthing Happen!!"));
  }, 2000);
});

Promise.race([p1, p2])
  .then(result => console.log(result))
  .catch(error => console.log("Error: ", error));
```

```powershell
PS C:\Users\User\Desktop\Project\express-demo> node .\promise-api.js
Async Operation 1...
1
Async Operation 2...
```

### Async and Await

Async / Await은 asynchronous한 코드를 synchronous처럼 보이게 작성할 수 있습니다.

```javascript
console.log("시작!");

getUser(1)
  .then(user => {
    console.log("User: ", user);
    return getRepos(user.github);
  })
  .then(repos => console.log("Repos: ", repos))
  .catch(error => console.log(error));

console.log("끝!");

function getUser(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("데이터 베이스에서 읽어오는 중입니다....");
      resolve({ id: id, github: "pchyo92" });
    }, 2000);
  });
}

function getRepos(userName) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("GitHub API에 요청 중....");
      resolve(["repo1", "repo2", "repo3"]);
    }, 2000);
  });
}
```

위 코드를 아래와 같이 변환합니다.

```javascript
console.log("시작!");

// getUser(1)
//   .then(user => {
//     console.log("User: ", user);
//     return getRepos(user.github);
//   })
//   .then(repos => console.log("Repos: ", repos))
//   .catch(error => console.log(error));

async function displayRepos() {
  const user = await getUser(1);
  const repos = await getRepos(user.github);
  console.log(repos);
}

displayRepos();

console.log("끝!");

function getUser(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("데이터 베이스에서 읽어오는 중입니다....");
      resolve({ id: id, github: "pchyo92" });
    }, 2000);
  });
}

function getRepos(userName) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("GitHub API에 요청 중....");
      resolve(["repo1", "repo2", "repo3"]);
    }, 2000);
  });
}
```

위에서 보면 알 수 있듯이 Async / Await의 기반은 Promise입니다. 문법적으로 더 편리하고 Synchronous처럼 보이도록 하는 코드 작성법일 뿐이지 그 근간은 Promise입니다. Async / Await에서는 then method를 사용할 수 없기에 try, catch문을 사용하여 코드가 정상적으로 진행된 상황과 그 반대의 상황을 Handling합니다.

```javascript
console.log("시작!");

// getUser(1)
//   .then(user => {
//     console.log("User: ", user);
//     return getRepos(user.github);
//   })
//   .then(repos => console.log("Repos: ", repos))
//   .catch(error => console.log(error));

async function displayRepos() {
  try {
    const user = await getUser(1);
    const repos = await getRepos(user.github);
    console.log(repos);
  } catch (error) {
    console.log(error);
  }
}

displayRepos();

console.log("끝!");

function getUser(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("데이터 베이스에서 읽어오는 중입니다....");
      resolve({ id: id, github: "pchyo92" });
    }, 2000);
  });
}

function getRepos(userName) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("GitHub API에 요청 중....");
      reject(new Error());
    }, 2000);
  });
}
```

```powershell
PS C:\Users\User\Desktop\Project\express-demo> node .\index.js
시작!
끝!
데이터 베이스에서 읽어오는 중입니다....
GitHub API에 요청 중....
Error
    at Timeout._onTimeout (C:\Users\User\Desktop\Project\express-demo\index.js:38:14)
    at listOnTimeout (internal/timers.js:531:17)
    at processTimers (internal/timers.js:475:7)
```

