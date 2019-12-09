# Node Modules

### Modules

Node안 각각의 js 파일들은 각각 module 형태로 존재합니다. 이 각각의 file들은 모두 module이며 해당 파일에 선언된 함수와 변수들은 해당 파일까지만 scope가 한정됩니다. 따라서 global하게 동작하지 않기 때문에 다른 module에 있는 변수나 함수를 불러오기 위해서는 명시적으로 그 module을 불러와야 사용할 수 있습니다.

```javascript
console.log(module);
```

위와 같이 코드를 작성하고 실행하면 아래와 같은 결과가 나타납니다.

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node app.js
Module {
  id: '.',
  exports: {},
  parent: null,
  filename: 'C:\\Users\\user\\Desktop\\Project\\NodeJS\\app.js',
  loaded: false,
  children: [],
  paths:
   [ 'C:\\Users\\user\\Desktop\\Project\\NodeJS\\node_modules',
     'C:\\Users\\user\\Desktop\\Project\\node_modules',
     'C:\\Users\\user\\Desktop\\node_modules',
     'C:\\Users\\user\\node_modules',
     'C:\\Users\\node_modules',
     'C:\\node_modules' ] }
```

module.exports는 기본적으로 빈객체가 할당됩니다. module.exports를 어떻게 설정하느냐에 따라 해당 module을 사용하는 방법이 달라지게 됩니다.

```javascript
let url = "http://mylogger.io/log";

function log(message) {
  console.log(message);
}

module.exports.log = log;
console.log(module);
```

해당 file에서는 module.exports에 log key값을 주고 log 함수를 할당했습니다. 따라서 외부에서 해당 module을 가져온다는 것은 log key에 해당하는 log함수를 value로 가지는 객체를 가져오게 되는 것입니다.

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\logger.js
Module {
  id: '.',
  exports: { log: [Function: log] },
  parent: null,
  filename: 'C:\\Users\\user\\Desktop\\Project\\NodeJS\\logger.js',
  loaded: false,
  children: [],
  paths:
   [ 'C:\\Users\\user\\Desktop\\Project\\NodeJS\\node_modules',
     'C:\\Users\\user\\Desktop\\Project\\node_modules',
     'C:\\Users\\user\\Desktop\\node_modules',
     'C:\\Users\\user\\node_modules',
     'C:\\Users\\node_modules',
     'C:\\node_modules' ] }
```

exports를 보면 객체가 할당되어 있는 것을 확인할 수 있습니다. 이를 app.js에서 불러오도록 하겠습니다.

```javascript
const log = require("./logger");

log("hi");

console.log(log);
```

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\app.js
C:\Users\user\Desktop\Project\NodeJS\app.js:3
log("hi");
^

TypeError: log is not a function
    at Object.<anonymous> (C:\Users\user\Desktop\Project\NodeJS\app.js:3:1)
    at Module._compile (internal/modules/cjs/loader.js:701:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:712:10)
    at Module.load (internal/modules/cjs/loader.js:600:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:539:12)
    at Function.Module._load (internal/modules/cjs/loader.js:531:3)
    at Function.Module.runMain (internal/modules/cjs/loader.js:754:12)
    at startup (internal/bootstrap/node.js:283:19)
    at bootstrapNodeJSCore (internal/bootstrap/node.js:622:3)
```

에러가 발생합니다. 이유는 app.js에서 할당받은 log는 `{log: function log()....}` 로 구성된 객체이기 때문입니다. 따라서 아래와 같이 코드를 수정해야 동작합니다.

```javascript
const log = require("./logger");

log.log("hi");

console.log(log);
```

log 객체안에 있는 log 함수를 불러왔기 때문에 해당 코드는 올바르게 동작합니다.

### Module Wrapper Function

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\app.js
C:\Users\user\Desktop\Project\NodeJS\app.js:1
(function (exports, require, module, __filename, __dirname) { let x =;
                                                                     ^
```

일부러 에러를 발생시키는 코드를 입력하고 해당파일을 실행하면 console에 이러한 결과가 나타납니다. 여기서 보이는 `(function (exports, require, module, __filename, __dirname){}...`의 의미를 알아보겠습니다.

사실 Node는 해당 파일에 작성된 코드를 바로 실행하지 않습니다. Node는 해당 코드를 위와 같은 함수형태로 감싼뒤에 코드를 실행합니다. 이를 적용하면 실질적으로는 아래와 같은 코드를 실행하는 것입니다.

```javascript
(function(exports, require, module, __filename, __dirname) {
  const log = require("./logger");

  log.log("hi");

  console.log(log);
});
```

위 함수를 **Node Wrapper Function**이라고 칭합니다.

```javascript
console.log(__dirname);
console.log(__filename);
const log = require("./logger");

log.log("hi");

console.log(log);
```

### Path Module

```javascript
const path = require("path");

const pathObj = path.parse(__filename);

console.log(pathObj);
```

위 파일을 실행하면 아래와 같은 결과가 도출됩니다.

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\app.js
{ root: 'C:\\',
  dir: 'C:\\Users\\user\\Desktop\\Project\\NodeJS',
  base: 'app.js',
  ext: '.js',
  name: 'app' }
```

### OS Module

```javascript
const os = require("os");

let totalMem = os.totalmem();
let freeMem = os.freemem();

console.log(`Total Memory: ${totalMem}`);
console.log(`Free Memory: ${freeMem}`);
```

위 파일을 실행하면 아래와 같은 결과가 도출됩니다.

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\app.js
Total Memory: 4174069760
Free Memory: 1060306944
```

### File System Module

```javascript
const fs = require("fs");

let files = fs.readdirSync("./");

console.log(files);

fs.readdir("./", function(err, files) {
  if (err) console.log(`Error: ${err}`);
  else console.log(`Files: ${files}`);
});
```

Node는 Single Thread기반의 Asynchronous rutime이므로  Asynchronous한 method를 사용하는 것이 좋습니다. 모든  Asynchronous한 mehod들은 마지막 argument로 call back 이라는 함수를 받습니다. Node는  Asynchronous 동작이 완료된 후 call back 함수를 호출합니다.

위 코드를 실행한 결과는 이렇습니다.

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\app.js
[ '.git',
  'app.js',
  'Node Modules.md',
  'NodeJS Fundamentals.md',
  'README.md' ]
Files: .git,app.js,Node Modules.md,NodeJS Fundamentals.md,README.md
```

### Event Module

**Event**는 Node의 핵심 Concept 중 하나입니다.

**Event** => A signal that something has happened  

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();

// Register an Event
emitter.emit("messageLogged");
```

emit method는 event를 발생시킬 때 사용합니다. 위 코드를 실행하면 아무런 결과가 나타나지 않습니다. 왜냐하면 해당 event를 listen할 listener를 등록하지 않았기 때문입니다. Listener란, 해당 event가 발생했을 때 실행되는 함수입니다.

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();

// Register a Listener
emitter.on("messageLogged", function() {
  console.log("Listener Called!!");
});

// Register an Event
emitter.emit("messageLogged");
```

on과 addListener method는 둘다 Listener를 등록하는 같은 역할을 하는 method입니다. 이렇게 코드를 작성하고 실행하면 console창에 결과가 나타납니다.

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\app.js
Listener Called!!
```

emit method와 on의 순서도 중요합니다. emit method가 실행되면 Node는 등록된 모든 listener를 동기적으로 iterate합니다. 따라서 순서가 바뀐다면 원하는 결과가 나타나지 않게 됩니다.

### Event Arguments

많은 경우에 우리는 event 발생 시, 해당 이벤트에 대한 데이터를 보내고 싶어합니다.

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();

// Register a Listener
emitter.on("messageLogged", function(args) {
  // e, eventArg
  console.log("Listener Called!!: ", args);
});

// Register an Event
emitter.emit("messageLogged", { id: 1, url: "http://" });
```

위 처럼 data를 보내면 해당 event의 listener는 이 event argument를 받아서 처리할 수 있습니다.

arrow function을 활용하여 아래와 같이 코드를 변경할 수 있습니다.

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();

// Register a Listener
emitter.on("messageLogged", args => {
  // e, eventArg
  console.log("Listener Called!!: ", args);
});

// Register an Event
emitter.emit("messageLogged", { id: 1, url: "http://" });
```

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();

// Register a Listener
emitter.on("messageLogged", args => {
  // e, eventArg
  console.log("Listener Called!!: ", args);
});

emitter.on("Logging", args => {
  console.log(args.message);
});

// Raise: Logging (data: message)
emitter.emit("Logging", { message: "Logging..." });

// Register an Event
emitter.emit("messageLogged", { id: 1, url: "http://" });
```

### Extending EventEmitter

실제 개발에서는 이 EventEmitter를 그대로 사용하기 보다는 EventEmitter을 확장해서 사용하는 경우가 더 많습니다.

```mysql
const EventEmitter = require("events");

const emitter = new EventEmitter();

// Register a Listener
emitter.on("messageLogged", args => {
  // e, eventArg
  console.log("Listener Called!!: ", args);
});

emitter.on("Logging", args => {
  console.log(args.message);
});

// Raise: Logging (data: message)
emitter.emit("Logging", { message: "Logging..." });

const log = require("./logger");
log("message");
```

app.js 코드

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();
const url = "http://mylogger.io/log";

function log(message) {
  // Send an HTTP request
  console.log(message);

  // Register an Event
  emitter.emit("messageLogged", { id: 1, url: "http://" });
}

module.exports = log;
```

logger.js 코드

이렇게 코드를 분리시킨 후 app.js 를 실행하면 아래와 같은 결과가 나타납니다.

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\app.js
Logging...
message
```

messageLogged event가 호출되지 않은 것을 확인할 수 있습니다. 이렇게 된 이유는 무엇일까요?

그 이유는 우리가 2개의 서로 다른 eventEmitter를 사용하고 있기 때문입니다. 이 문제를 해결해봅시다.

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();
const url = "http://mylogger.io/log";

class Logger extends EventEmitter {
  log(message) {
    // Send an HTTP request
    console.log(message);

    // Register an Event
    emitter.emit("messageLogged", { id: 1, url: "http://" });
  }
}

module.exports = Logger;
```

logger.js 코드

위와 같이 보드를 작성하면 Logger class는 EventEmitter의 특징을 모두 가지고 자기 고유의 특징도 가지게 됩니다. 위 Logger class를 app.js파일에서 불러와 보겠습니다.

```javascript
const EventEmitter = require("events");

const emitter = new EventEmitter();

emitter.on("Logging", args => {
  console.log(args.message);
});

// Raise: Logging (data: message)
emitter.emit("Logging", { message: "Logging..." });

const Logger = require("./logger");
const logger = new Logger();

// Register a Listener
logger.on("messageLogged", args => {
  // e, eventArg
  console.log("Listener Called!!: ", args);
});

logger.log("message");
```

app.js 코드

이제 emit method와 on method가 같은 EventEmitter에서 호출되기 때문에 appjs를 실행하면 원하는 결과가 나타나게 됩니다.

```powershell
PS C:\Users\user\Desktop\Project\NodeJS> node .\app.js
Logging...
message
Listener Called!!:  { id: 1, url: 'http://' }
```

### HTTP Module

http module을 활용하여 http request 요청에 응답하는 간단한 server를 작성해보겠습니다.

```javascript
const http = require("http");

const server = http.createServer();
```

여기서 server 변수는 사실 EventEmitter입니다. 이렇게 Node에서 EventEmitter는 중요한 역할을 하고 있습니다.

```javascript
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.url === "/") {
    res.write("Hello!!");
    res.end();
  }
  if (req.url === "/api/courses") {
    res.write(JSON.stringify([1, 2, 3]));
    res.end();
  }
});

server.listen(3000);

console.log("Listening on Port: 3000...");
```

이렇게 간단하게 NodeJS의 http module을 활용하여 server를 만들어 봤습니다. 하지만 실제 개발에서는 http module로 서버를 개발하지 않습니다. 왜냐하면 route가 복잡해질수록 작성해야할 코드가 굉장히 복잡해지기 때문입니다. 그 대신 Express라는 Framework를 활용하여 서버를 구성합니다. Express는 http module에 기반해 만들어진 Web Framework입니다.

