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

