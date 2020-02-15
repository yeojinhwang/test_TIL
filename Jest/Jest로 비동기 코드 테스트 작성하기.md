## Jest로 비동기 코드 테스트 작성하기

#### 1. 콜백 함수 테스트

```javascript
function fechUser(id, cb) {
    setTimeout(() => {
        console.log('wait 0.1 sec.');
        const user = {
            id: id,
            name: 'User' + id,
            email: id + '@test.com'
        };
        cb(user);
    }, 100);
}
```

- 콜백 함수를 인자로 받는 자바스크립트 함수로, 인자로 넘어온 `id`를 가진 사용자 객체를 다시 인자로 넘어온 콜백 함수(`cb`)의 인자로 넘겨서 호출한다.

```javascript
test('fetch a user', () => {
    fetchUser(1, user => {
        expect(user).toEqual({
            id: 1,
            name: 'User1',
            email: '1@test.com'
        });
    });
});
```

```bash
> npm test

> my-jest@1.0.0 test /my-jest
> jest

 PASS  ./async.test.js
  ✓ fetch a user (1ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.756s, estimated 1s
Ran all test suites.
```

- 테스트 코드가 제대로 실패하는지 확인하기 위해 `id` `2`를 넘기는 테스트 코드를 작성한 경우

```bash
> npm test

> my-jest@1.0.0 test /my-jest
> jest

 PASS  ./async.test.js
  ✓ fetch a user (1ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.88s, estimated 1s
Ran all test suites.
```

- Jest Runner에게 명시적으로 이 테스트 함수는 비동기 코드를 테스트하니 콜백 함수가 호출되는지도 봐야한다고 알려줘야 한다.
  - 실행할 테스트 함수가 `done`이라는 함수 인자를 받도록 수정하고, `done` 함수를 콜백 함수의 제일 마지막에 호출하도록 수정한다.

```javascript
test('fetch a user', done => {
    fetchUser(2, user => {
        expect(user).toEqual({
            id: 1,
            name: 'User1',
            email: '1@test.com'
        });
        done();
    });
});
```

```bash
> npm test

> my-jest@1.0.0 test /my-jest
> jest

 FAIL  ./async.test.js
  ✕ fetch a user (124ms)

  ● fetch a user

    expect(received).toEqual(expected)

    Difference:

    - Expected
    + Received

      Object {
    -   "email": "1@test.com",
    -   "id": 1,
    -   "name": "User1",
    +   "email": "2@test.com",
    +   "id": 2,
    +   "name": "User2",
      }

      13 | test('fetch a user', done => {
      14 |   fetchUser(2, user => {
    > 15 |     expect(user).toEqual({
         |                  ^
      16 |       id: 1,
      17 |       name: 'User1',
      18 |       email: '1@test.com'

      at toEqual (async.test.js:15:18)
      at cb (async.test.js:9:5)

  console.log async.test.js:3
    wait 0.1 sec.

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        0.894s, estimated 1s
Ran all test suites.
```

- `fetchUser()` 함수를 호출할 때 인자를 `2`대신 다시 `1`을 넘기도록 코드를 수정하면 콜백 함수도 호출되면서 Jest는 모든 코드를 빠짐없이 실행하면서 테스트를 통과시킨다.

#### 2. Promise 테스트

```javascript
function fetchUser(id) {
    return new Promise(resolve => {
        setTimeout(() => {
            console.log('wait 0.1 sec.');
            const user = {
                id: id,
                name: 'User' + id,
                email: id + '@test.com'
            };
            resolve(user);
        }, 100);
    });
}
```

- 인자로 콜백 함수를 받는 대신 0.1초를 기다렸다가 사용자 객체를 제공(resolve)하는 Promise 객체를 리턴한다.

```javascript
test('fetch a user', () => {
    fetchUser(2).then(user => {
        expect(user).toEqual({
            id: 1,
            name: 'User1',
            email: '1@test.com'
        });
    });
});
```

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 PASS  ./promise.test.js
  ✓ fetch a user (1ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.766s, estimated 1s
Ran all test suites.
```

- 실행 시간이 1ms이고, 콘솔에 `wait 0.1 sec.`도 출력되지 않아 `fetchUser()` 함수에서 리턴된 Promise 객체의 `then()` 메서드가 실행되지 않은 것을 알 수 있다.

```javascript
test('fetch a user', () => {
    return fetchUser(2).then(user => { 
        expect(user).toEqual({
            id: 1,
            name: 'User1',
            email: '1@test.com'
        });
    });
});
```

- `return`문을 추가하여 테스트 함수가 Promise를 리턴하면 Jest Runner는 리턴된 Promise가 resolve될 때까지 기다린다.

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 FAIL  ./promise.test.js
  ✕ fetch a user (117ms)

  ● fetch a user

    expect(received).toEqual(expected)

    Difference:

    - Expected
    + Received

      Object {
    -   "email": "1@test.com",
    -   "id": 1,
    -   "name": "User1",
    +   "email": "2@test.com",
    +   "id": 2,
    +   "name": "User2",
      }

      15 | test('fetch a user', () => {
      16 |   return fetchUser(2).then(user => {
    > 17 |     expect(user).toEqual({
         |                  ^
      18 |       id: 1,
      19 |       name: 'User1',
      20 |       email: '1@test.com'

      at toEqual (promise.test.js:17:18)

  console.log promise.test.js:4
    wait 0.1 sec.

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        1.021s
Ran all test suites.
```

#### 3. Async/Await 테스트

- 테스트 함수 맨 앞에 `async`를 추가하고, Promise를 리턴하는 함수 앞에 `await`을 붙이면 동기 코드처럼 보이는 테스트를 작성할 수 있다.

```javascript
test('fetch a user', async() => {
    const user = await fetchUser(2);
    expect(user).toEqual({
        id: 1,
        name: 'User1',
        email: '1@test.com'
    });
});
```

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 FAIL  ./promise.test.js
  ✕ fetch a user (114ms)

  ● fetch a user

    expect(received).toEqual(expected)

    Difference:

    - Expected
    + Received

      Object {
    -   "email": "1@test.com",
    -   "id": 1,
    -   "name": "User1",
    +   "email": "2@test.com",
    +   "id": 2,
    +   "name": "User2",
      }

      15 | test('fetch a user', async () => {
      16 |   const user = await fetchUser(2);
    > 17 |   expect(user).toEqual({
         |                ^
      18 |     id: 1,
      19 |     name: 'User1',
      20 |     email: '1@test.com'

      at Object.toEqual (promise.test.js:17:16)

  console.log promise.test.js:4
    wait 0.1 sec.

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        0.997s, estimated 1s
Ran all test suites.
```

```javascript
test('fetch a user', async () => {
  const user = await fetchUser(2);
  expect(user).toEqual({
    id: 2,
    name: 'User2',
    email: '2@test.com'
  });
});
```

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 PASS  ./promise.test.js
  ✓ fetch a user (119ms)

  console.log promise.test.js:4
    wait 0.1 sec.

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.476s
Ran all test suites.
```

