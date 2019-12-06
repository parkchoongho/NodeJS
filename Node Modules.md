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

