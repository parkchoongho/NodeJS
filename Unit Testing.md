# Unit Testing

### What is Automated Testing?

The practice of writing code to test our code, and then run those tests in an automated fashion.

#### Automated tests are repeatable!!

### Benefits of Automated Testing

- Test your code frequently, in less time
- Catch the bugs before deploying
- Deploy with confidence
- Refactor with confidence (Refactoring means changing the structure of the code without changing its behavior)
- Focus more on the quality

### Types of Tests

- Unit Test: Tests a unit of an application **without its external dependencies** 
  - Cheap to write
  - Execute fast
  - Don't give a lot of confidence
- Integration Test: Tests the application **with its external dependencies**
  - Take longer to execute
  - Give more confidence
- End-to-End Test: Drives an application through its UI
  - Give you the greatest confidence
  - Very slow
  - Very brittle

The actual ratio between unit, integration and end-to-end tests depends on your project.

- Favour unit tests to e2e tests
- Cover until test gaps with integration tests
- Use end-to-end tests sparingly (Only for the key functions of the application)

### Writing Your First Unit Test

#### Install Jest

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm i jest --save-dev
```

jest는 실제 application이 동작하는데 필요한 것이 아닌 개발 과정에서 필요한 module이므로 `--save-dev`라는 명령어를 뒤에 추가해줍니다.

lib.test.js 파일

```javascript
test("Our First Test", () => {
  throw new Error("Something Failed!");
});
```

test 파일의 파일명은 test하려는 js파일명.test.js 파일처럼 작성하는게 좋습니다.

위 test 파일을 실행하면 아래와 같은 결과가 나타납니다.

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 FAIL  tests/lib.test.js
  × Our First Test (5ms)

  ● Our First Test

    Something Failed!

      1 | test("Our First Test", () => {
    > 2 |   throw new Error("Something Failed!");
        |         ^
      3 | });
      4 | 

      at Object.<anonymous>.test (tests/lib.test.js:2:9)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        6.948s, estimated 7s
Ran all test suites.
npm ERR! Test failed.  See above for more details.
```

### Testing Numbers

lib.test.js

```javascript
const lib = require("../lib");

test("absolute - should return a positive number if input is positive", () => {
  const result = lib.absolute(1);
  expect(result).toBe(1);
});

test("absolute - should return a positive number if input is negative", () => {
  const result = lib.absolute(-1);
  expect(result).toBe(1);
});

test("absolute - should return 0 if input is 0", () => {
  const result = lib.absolute(0);
  expect(result).toBe(0);
});
```

위 코드를 실행하면 아래와 같은 결과가 나타납니다.

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 PASS  tests/lib.test.js (9.811s)
  √ absolute - should return a positive number if input is positive (60ms)
  √ absolute - should return a positive number if input is negative (2ms)
  √ absolute - should return 0 if input is 0 (1ms)

Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        15.957s
Ran all test suites.
```

### Grouping Tests

Tests are **first-class citizens** in your source code.

위에 작성한 lib.test.js를 좀 더 유지보수가 쉬운 코드로 작성해보겠습니다.

lib.test.js

```javascript
const lib = require("../lib");

describe("absolute", () => {
  it("should return a positive number if input is positive", () => {
    const result = lib.absolute(1);
    expect(result).toBe(1);
  });

  it("should return a positive number if input is negative", () => {
    const result = lib.absolute(-1);
    expect(result).toBe(1);
  });

  it("should return 0 if input is 0", () => {
    const result = lib.absolute(0);
    expect(result).toBe(0);
  });
});
```

위 파일을 실행하면 아래와 같은 결과가 도출됩니다.

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 PASS  tests/lib.test.js (5.428s)
  absolute
    √ should return a positive number if input is positive (7ms)
    √ should return a positive number if input is negative (1ms)
    √ should return 0 if input is 0 (2ms)

Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        8.623s, estimated 10s
Ran all test suites.
```

### Refactoring with Confidence

lib.js의 absolute 함수는 아래와 같이 작성되어 있었습니다.

```javascript
module.exports.absolute = function(number) {
  if (number > 0) return number;
  if (number < 0) return -number;
  return 0;
};
```

Refactoring후 lib.js

```javascript
module.exports.absolute = function(number) {
  if (number >= 0) return number;
  return -number;
};
```

Refactoring후 lib.js

```javascript
module.exports.absolute = function(number) {
  
  return number >= 0 ? number : -number;
};
```

