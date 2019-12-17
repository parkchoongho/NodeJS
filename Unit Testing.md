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

### Testing Strings

String Test예시는 너무 specific해서는 안되고 또 너무 general 해서도 안됩니다.

lib.js

```javascript
module.exports.greet = function(name) {
  return "Welcome " + name;
};
```

lib.test.js

```javascript
const lib = require("../lib");

describe("greet", () => {
  it("should return the greeting message", () => {
    const result = lib.greet("Mosh");
    expect(result).toBe('Welcome Mosh');
  });
});
```

이렇게 test를 작성하고 test를 실행하면 아래와 같은 결과가 나타납니다.

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 PASS  tests/lib.test.js
  absolute
    √ should return a positive number if input is positive (7ms)
    √ should return a positive number if input is negative (1ms)
    √ should return 0 if input is 0
  greet
    √ should return the greeting message

Test Suites: 1 passed, 1 total
Tests:       4 passed, 4 total
Snapshots:   0 total
Time:        7.119s
Ran all test suites.
```

제대로 동작된 것처럼 보이지만 lib.js를 수정해서 다시 동작시켜 보겠습니다.

lib.js

```javascript
module.exports.greet = function(name) {
  return "Welcome " + name + '!';
};
```

위와 같이 코드를 수정하고 실행하면 아래와 같은 결과가 나타납니다.

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 FAIL  tests/lib.test.js
  absolute
    √ should return a positive number if input is positive (6ms)
    √ should return a positive number if input is negative (1ms)
    √ should return 0 if input is 0 (1ms)
  greet
    × should return the greeting message (10ms)

  ● greet › should return the greeting message

    expect(received).toBe(expected) // Object.is equality

    Expected: "Welcome Mosh"
    Received: "Welcome Mosh!"

      21 |   it("should return the greeting message", () => {
      22 |     const result = lib.greet("Mosh");
    > 23 |     expect(result).toBe("Welcome Mosh");
         |                    ^
      24 |   });
      25 | });
      26 | 

      at Object.it (tests/lib.test.js:23:20)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 3 passed, 4 total
Snapshots:   0 total
Time:        6.958s
Ran all test suites.
npm ERR! Test failed.  See above for more details.
```

위와 같은 에러가 발생합니다. 여기서 String을 test하는 코드가 너무 specific하기 때문에 조금 general하게 수정하도록 하겠습니다.

lib.test.js 수정

```javascript
const lib = require("../lib");

describe("greet", () => {
  it("should return the greeting message", () => {
    const result = lib.greet("Mosh");
    expect(result).toMatch(/Mosh/);
    // expect(result).toContain("Mosh"); 정규 표현식을 사용하고 싶지 않으면 이와 같이 작성해도 같은 결과를 도출합니다.
  });
});
```

아래와 같이 수정을 하고 코드를 실행하면

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 PASS  tests/lib.test.js
  absolute
    √ should return a positive number if input is positive (7ms)
    √ should return a positive number if input is negative
    √ should return 0 if input is 0
  greet
    √ should return the greeting message (1ms)

Test Suites: 1 passed, 1 total
Tests:       4 passed, 4 total
Snapshots:   0 total
Time:        6.786s
Ran all test suites.
```

위와 같은 결과가 나타납니다.

### Testing Arrays

lib.js

```javascript
module.exports.getCurrencies = function() {
  return ["USD", "AUD", "EUR"];
};
```

lib.test.js

```javascript
const lib = require("../lib");

describe("getCurrencies", () => {
  it("should return supported currencies", () => {
    const result = lib.getCurrencies();

    // Too General
    expect(result).toBeDefined();
    expect(result).not.toBeNull();

    // Too Specific
    expect(result[0]).toBe("USD");
    expect(result[1]).toBe("AUD");
    expect(result[2]).toBe("EUR");
    expect(result.length).toBe(3);

    // Proper Way
    expect(result).toContain("USD");
    expect(result).toContain("AUD");
    expect(result).toContain("EUR");

    // Ideal Way
    expect(result).toEqual(expect.arrayContaining(["EUR", "USD", "AUD"]));
  });
});
```

### Testing Objects

lib.js

```javascript
module.exports.getProduct = function(productId) {
  return { id: productId, price: 10 };
};
```

lib.test.js

```javascript
const lib = require("../lib");

describe("getProduct", () => {
  it("should return the product with the given id", () => {
    const result = lib.getProduct(1);
    expect(result).toBe({ id: 1, price: 10 });
  });
});
```

Test 실행 결과

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 FAIL  tests/lib.test.js
  absolute
    √ should return a positive number if input is positive (8ms)
    √ should return a positive number if input is negative (2ms)
    √ should return 0 if input is 0 (1ms)
  greet
    √ should return the greeting message (2ms)
  getCurrencies
    √ should return supported currencies (7ms)
  getProduct
    × should return the product with the given id (19ms)

  ● getProduct › should return the product with the given id

    expect(received).toBe(expected) // Object.is equality

    If it should pass with deep equality, replace "toBe" with "toStrictEqual"

    Expected: {"id": 1, "price": 10}
    Received: serializes to the same string

      53 |   it("should return the product with the given id", () => {
      54 |     const result = lib.getProduct(1);
    > 55 |     expect(result).toBe({ id: 1, price: 10 });
         |                    ^
      56 |   });
      57 | });
      58 | 

      at Object.it (tests/lib.test.js:55:20)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 5 passed, 6 total
Snapshots:   0 total
Time:        7.206s, estimated 9s
Ran all test suites.
npm ERR! Test failed.  See above for more details.
```

toBe method를 사용하면 해당 객체 메모리 주소와 비교합니다. 따라서 우리는 해당 객체의 key에 해당하는 value값이 같은지 각각 비교해야 합니다. 이를 위해 다른 method를 사용합니다.

lib.test.js

```javascript
const lib = require("../lib");

describe("getProduct", () => {
  it("should return the product with the given id", () => {
    const result = lib.getProduct(1);
    expect(result).toEqual({ id: 1, price: 10 });
  });
});
```

그리고 test를 진행해보겠습니다.

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 PASS  tests/lib.test.js (6.982s)
  absolute
    √ should return a positive number if input is positive (6ms)
    √ should return a positive number if input is negative (1ms)
    √ should return 0 if input is 0 (1ms)
  greet
    √ should return the greeting message (2ms)
  getCurrencies
    √ should return supported currencies (5ms)
  getProduct
    √ should return the product with the given id (2ms)

Test Suites: 1 passed, 1 total
Tests:       6 passed, 6 total
Snapshots:   0 total
Time:        15.201s
Ran all test suites.
```

만일 정확하게 해당 property 값을 가지고 있고 맞춰야 한다면 toEqual method를 사용하고 해당 property 값을 맞추기만 하고 그 의외의 property는 맞추지 않아도 된다면 toMatchObject method를 사용합니다.

lib.js

```javascript
module.exports.getProduct = function(productId) {
  return { id: productId, price: 10, category: "Food" };
};
```

lib.test.js

```javascript
const lib = require("../lib");

describe("getProduct", () => {
  it("should return the product with the given id", () => {
    const result = lib.getProduct(1);
    expect(result).toEqual({ id: 1, price: 10 });

    expect(result).toMatchObject({ id: 1, price: 10 });
  });
});
```

Test 실행결과

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 FAIL  tests/lib.test.js
  absolute
    √ should return a positive number if input is positive (9ms)
    √ should return a positive number if input is negative (2ms)
    √ should return 0 if input is 0 (1ms)
  greet
    √ should return the greeting message (17ms)
  getCurrencies
    √ should return supported currencies (80ms)
  getProduct
    × should return the product with the given id (28ms)

  ● getProduct › should return the product with the given id

    expect(received).toEqual(expected) // deep equality

    - Expected
    + Received

      Object {
    +   "category": "Food",
        "id": 1,
        "price": 10,
      }

      53 |   it("should return the product with the given id", () => {
      54 |     const result = lib.getProduct(1);
    > 55 |     expect(result).toEqual({ id: 1, price: 10 });
         |                    ^
      56 | 
      57 |     expect(result).toMatchObject({ id: 1, price: 10 });
      58 |   });

      at Object.it (tests/lib.test.js:55:20)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 5 passed, 6 total
Snapshots:   0 total
Time:        7.819s
Ran all test suites.
npm ERR! Test failed.  See above for more details.
```

### Testing Exceptions

Exceptions를 Test하는데 있어서는 다른 관점에서 접근하는 것이  좋습니다. 해당 result를 값으로 전달받는 것이 아니기 때문에 해당 function을 callback function으로 주는 것이 더 좋습니다.

lib.js

```javascript
module.exports.registerUser = function(username) {
  if (!username) throw new Error("Username is required.");

  return { id: new Date().getTime(), username: username };
};
```

lib.test.js

```javascript
describe("registerUser", () => {
  it("should throw if username is falsy", () => {
    // Null
    // undefined
    // NaN
    // ''
    // 0
    // false
    expect(() => {
      lib.registerUser(null);
    }).toThrow();
  });
});
```

lib.test.js

```javascript
const lib = require("../lib");

describe("registerUser", () => {
  it("should throw if username is falsy", () => {
    const args = [null, undefined, NaN, "", 0, false];
    args.forEach(a => {
      expect(() => {
        lib.registerUser(a);
      }).toThrow();
    });
  });
  it("should return a user object if valid username is passed", () => {
    const result = lib.registerUser("mosh");
    expect(result).toMatchObject({ username: "mosh" });
    expect(result.id).toBeGreaterThan(0);
  });
});
```

Test 실행결과

```powershell
PS C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo> npm test

> testing-demo@1.0.0 test C:\Users\user\Desktop\11.6- Writing Your First Test\11.6- Writing Your First Test\testing-demo
> jest

 PASS  tests/lib.test.js
  absolute
    √ should return a positive number if input is positive (6ms)
    √ should return a positive number if input is negative (1ms)
    √ should return 0 if input is 0
  greet
    √ should return the greeting message (1ms)
  getCurrencies
    √ should return supported currencies (5ms)
  getProduct
    √ should return the product with the given id (2ms)
  registerUser
    √ should throw if username is falsy (53ms)
    √ should return a user object if valid username is passed (2ms)

Test Suites: 1 passed, 1 total
Tests:       8 passed, 8 total
Snapshots:   0 total
Time:        6.901s
Ran all test suites.
```

