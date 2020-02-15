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

