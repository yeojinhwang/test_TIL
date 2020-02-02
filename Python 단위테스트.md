## Python 단위테스트

- **테스트**란? 소프트웨어가 요구사항에 의해 개발된 산출물이 요구사항과 부합하는지 여부를 검증하기 위한 작업
- **단위 테스트**란? 모듈 또는 응용 프로그램 내의 개별 코드 단위가 예상대로 작동하는지 확인하는 반복 가능한 활동

#### unittest

- Python에 포함된 다양한 테스트를 자동화할 수 있는 기능이 포함된 표준 라이브러리
- unittest에 포함된 주요 개념
  - TestCase: unittest 프레임 워크의 테스트 조직의 기본 단위
  - Fixture
    - 테스트 함수의 전 또는 후에 실행
    - 테스트가 실행되기 전에 테스트 환경이 예상된 상태에 있는지 확인하는 데 사용
    - 테스트 전에 데이터베이스 테이블을 만들거나 테스트 후에 사용한 리소스를 정리하는데 사용
  - assertion
    - unittest가 테스트 통과하는지 또는 실패 하는지를 결정
    - bool test, 객체의 적합성, 적절한 예외 발생 등 다양한 점검을 할 수 있음
    - assertion이 실패하면 테스트 함수가 실패한다.

#### unittest 모듈의 사용

- 예제 코드를 작성한다.
- `test.py` 파일을 만들고, 코드를 아래와 같이 작성한다.
- `TestCase`를 작성하기 위해 `unittest.TestCase`를 상속한 테스트 클래스를 작성한다.
- `test_` 라는 이름으로 시작하는 메소드는 모두 테스트 메소드가 된다.
- `test_run()` 메소드는 단순 실행여부만 판별한다.
- `unittest.main()` 코드를 통해 테스트가 수행된다.

```python
import unittest

# TestCase를 작성
class CustomTests(unittest.TestCase):
    
    def test_runs(self):
        """단순 실행여부 판별하는 테스트 메소드"""
        custom_function()
        
# unittest를 실행
if __name__ == '__main__':
    unittest.main()
```

- 저장 후, 터미널 화면에서 실행하면 1개의 test가 실행되며 `custom_function()`를 작성하기 않았기에 실패한다.

![image-20200202234007777](images\image-20200202234007777.png)

```python
# custom_function 함수 작성
import unittest

def custom_function():
    pass

# TestCase를 작성
class CustomTests(unittest.TestCase):
    # ...
```

![image-20200202234206421](images\image-20200202234206421.png)

