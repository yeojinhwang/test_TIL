## Jest로 테스트 작성하기

- Jest는 페이스북에서 만들어서 React와 더불어 많은 자바스크립트 개발자들로부터 좋은 반응을 얻고 있는 테스팅 라이브러리

#### Jest: All-in-one 테스팅 라이브러리

- Jest는 라이브러리 하나만 설치하면, Test Runner와 Test Matcher, Test Mock 프레임워크까지 제공해준다.

#### 1) 프로젝트 생성

- 프로젝트 디렉토리를 생성하고 NPM 초기화 커맨드로 `package.json` 파일을 만든다.

  ```bash
  $ mkdir my-jest
  $ npm init -y
  $ ls
  package.json
  ```

#### 2) Jest 라이브러리 설치

```bash
$ npm i -D jest
```

#### 3) test 스크립트 수정

- `package.json` 파일을 열고 `test` 스크립트를 `jest`로 수정한다.

  ```json
  "scripts": {
      "test": "jest"
  }
  ```

- 터미널에 `npm test`라고 입력하면 `jest` 커맨드를 실행할 수 있다.

- `jest` 커맨드를 전역으로 설치할 수도 있지만, 실제 프로젝트에서는 스크립트로 등록해놓고 사용하는 경우가 많다.

#### 4) 테스트 코드 작성

- `test.js`라는 파일을 생성하고, 그 안에 다음과 같이 입력한다.

  ```javascript
  test('1 is 1', () => {
      expect(1).toBe(1);
  });
  ```

  ![image-20200204143725519](C:\Users\hbj\Desktop\test_TIL\images\image-20200204143725519.png)

- 기본적으로 테스트는 다음과 같이 일정한 패턴으로 작성한다.

  ```javascript
  test('테스트 설명', () => {
      expect('검증 대상').toXxx('기대 결과');
  });
  ```

  - `toXxx` 부분에서 사용되는 함수를 흔히 Test Matcher라고 한다. 사용된 `toBe()` 함수는 숫자나 문자와 같은 객체가 아닌 기본형(primitive) 값을 비교할 때 사용된다.

- `npm test`를 실행하면 프로젝트 내에 모든 테스트 파일을 찾아서 테스트를 실행해준다.

- Jest는 기본적으로 `test.js`로 끝나거나, `__test__` 디렉토리 안에 있는 파일들은 모두 테스트 파일로 인식한다.

- 특정 테스트 파일만 실행하고 싶은 경우에는 `npm test <파일명이나 경로>` 를 입력하면 된다.



## 자주 사용되는 Matcher

#### toEqual()

```javascript
function getUser(id) {
    return {
        id,
        email: 'user${id}@test.com'
    };
};
```

```javascript
test('return a user object', () => {
    expect(getUser(1)).toBe({
        id: 1,
        email: 'user1@test.com'
    });
});
```

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 FAIL  ./test.js
  ✕ return a user object (7ms)

  ● return a user object

    expect(received).toBe(expected) // Object.is equality

    Expected: {"email": "user1@test.com", "id": 1}
    Received: {"email": "user1@test.com", "id": 1}

    Difference:

    Compared values have no visual difference. Note that you are testing for equality with the stricter `toBe` matcher using `Object.is`. For deep equality only, use `toEqual` instead.

      11 |
      12 | test('return a user object', () => {
    > 13 |   expect(getUser(1)).toBe({
         |                      ^
      14 |     id: 1,
      15 |     email: `user1@test.com`
      16 |   });

      at Object.toBe (test.js:13:22)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        0.901s, estimated 1s
Ran all test suites.
npm ERR! Test failed.  See above for more details.
```

- 실제로 테스트 코드를 작성할 때는 객체를 검증해야할 일이 많기 때문에 `toEqual()` 함수를 가장 많이 접할 수 있다.

```javascript
test('return a user object', () => {
    expect(getUser(1)).toEqual({
        id: 1,
        email: 'user1@test.com'
    });
});
```

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 PASS  ./test.js
  ✓ return a user object (3ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.91s, estimated 1s
Ran all test suites.
```

#### toBeTruthy(), toBeFalsy()

- 숫자 `1`이 `true`로 간주되고, 숫자 `0`이 `false`로 간주되는 것과 같이 모든 타입의 값들을 `true` 아니면 `false`로 간주하는 규칙이 있다.
  - `toBeTruthy()`는 검증 대상이 이 규칙에 따라 `true`로 간주되면 테스트 통과이고, `toBeFalsy()`는 반대로 `false`로 간주되는 경우 테스트가 통과된다.

```javascript
test('number 0 is falsy but string 0 is truthy', () => {
	expect(0).toBeFalsy();
	expect('0').toBeTruthy();
});
```

#### toHaveLength(), toContain()

- `toHaveLength()`는 배열의 길이를 체크할 때 쓰이고, `toContain()`은 특정 원소가 배열에 있는지를 테스트할 때 쓰인다.

```javascript
test('array', () => {
    const colors = ['Red', 'Yellow', 'Blue'];
    expect(colors).toHaveLength(3);
    expect(colors).toContain('Yellow');
    expect(colors).not.toContain('Green');
});
```

- 어떤 Matcher 함수가 불만족하는지를 테스트할 때는 앞에 `not`을 붙여준다.

#### toMatch()

- 문자열의 경우에는 단순히 `toBe()`를 사용해서 문자열이 정확히 일치하는지를 체크한다.
- 정규식 기반의 테스트가 필요할 때는 `toMatch()` 함수를 사용한다.

```javascript
test('string', () => {
    expect(getUser(1).email).toBe('user1@test.com');
    expect(getUser(2).email).toMatch(/.*test.com$/);
});
```

#### toThrow()

- 예외발생 여부를 테스트해야 할 때는 `toThrow()` 함수를 사용하면 된다.
- `toThrow()` 함수는 인자로 받는데 문자열을 넘기면 예외 메세지를 비교하고 정규식을 넘기면 정규식 체크를 해준다.

```javascript
function getUser(id) {
    if (id <= 0) throw new Error('Invalid ID');
    return {
        id,
        email: 'user${id}@test.com'
    };
};
```

```javascript
// 테스트 코드 작성
test('throw when id is non negative', () => {
    expect(getUser(-1)).toThrow();
    expect(getUser(-1)).toThrow('Invalid ID');
});
```

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 FAIL  ./test.js
  ✕ throw when id is non negative (2ms)

  ● throw when id is non negative

    Invalid ID

      1 | function getUser(id) {
    > 2 |   if (id <= 0) throw Error('Invalid ID');
        |                      ^
      3 |   return {
      4 |     id,
      5 |     email: `user${id}@test.com`

      at Error (test.js:2:22)
      at Object.getUser (test.js:10:10)

Test Suites: 1 failed, 1 total
Tests:       1 failed, 1 total
Snapshots:   0 total
Time:        0.928s, estimated 1s
Ran all test suites.
npm ERR! Test failed.  See above for more details.
```

- `expect()` 함수에 넘기는 검증 대상을 함수로 한 번 감싸줘야 한다. 그렇지 않으면 예외 발생 여부를 체크하는 것이 아닌, 테스트 실행 도중 예외가 발생하여 테스트는 항상 실패하게 된다.

```javascript
test('throw when id is non negative', () => {
    expect(() => getUser(-1).toThrow());
    expect(() => getUser(-1).toThrow('Invalid ID'));
})
```

```bash
$ npm test

> my-jest@1.0.0 test /my-jest
> jest

 PASS  ./test.js
  ✓ throw when id is non negative (2ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.449s
Ran all test suites.
```