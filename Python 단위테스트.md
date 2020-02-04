## Python 단위테스트

- **테스트**란? 소프트웨어가 요구사항에 의해 개발된 산출물이 요구사항과 부합하는지 여부를 검증하기 위한 작업
- **단위 테스트**란? 모듈 또는 응용 프로그램 내의 개별 코드 단위가 예상대로 작동하는지 확인하는 반복 가능한 활동

#### 테스트의 일반 원칙

- 테스트 유닛은 각 기능의 가장 작은 단위에 집중하여, 해당 기능이 정확이 동작하는지를 증명한다.
- 각 테스트 유닛은 반드시 독립적이어야 한다. 새로운 데이터셋으로 각각의 테스트를 로딩해야 하고, 그 실행 결과는 꼭 삭제해야 한다. `setUp()` 과 `tearDown()` 메소드로 이런 작업을 한다.
- 테스트가 빠르게 돌 수 있도록 만들어야 한다. 무거운 테스트는 따로 분리하여 별도로 만들어 두고 스케쥴 작업을 걸어 둔다.
- 테스트 함수에는 길고 서술적인 이름을 사용해야 한다. 이런 함수명들은 테스트가 실패할 때나 보이기 때문에 가능한 서술적인 이름을 붙여야 한다.
- 이미 만들어져 있는 코드에서 작업할 경우, 관련 테스트 코드를 돌려보면 어느 지점이 문제인지, 수정하기 어려운 곳은 어디일지, 막다른 골목은 어디일지를 발견하게 된다.
- 몇 가지 기능을 추가해야 한다면 가장 먼저 해야할 일은 새로운 기능이 아직 돌아가지 않음을 확인할 수 있는 테스트를 붙여넣는 것이다.

#### [unittest](https://docs.python.org/3/library/unittest.html)

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

```python
import unittest
import os

def custom_function(file_name):
    with open(file_name, 'rt') as f: # 'rt' 텍스트모드 읽기
        return sum(1 for _ in f)
    
# TestCase를 작성
class CustomTests(unittest.TestCase):
    def setUp(self):
        """테스트 시작되기 전 파일 작성"""
        self.file_name = 'test_file.txt'
        with open(self.file_name, 'wt') as f:
            f.write("""
            파이썬에는 단위테스트 모듈이
            기본적으로 포함되어 있습니다.
            단위테스트를 잘 수행해보고 싶습니다.
            """.strip())
            
    def tearDown(self):
        """테스트 종료 후 파일 삭제"""
        try:
            os.remove(self.file_name)
        except:
            pass
        
    def test_runs(self):
        """단순 실행여부 판별하는 테스트 메소드"""
        custom_function(self.file_name)
        
    def test_line_count(self):
        self.assertEqual(custom_function(self.file_name), 3)
        
# unittest를 실행
if __name__ = '__main__':
    unittest.main()
```

- `setUp()` : Fixture로, 테스트 전에 수행된다.
- `tearDown()` : Fixture로, 테스트 후에 수행된다.
- `setUp()` 에서 파일을 생성하고, `tearDown()`에서 파일을 삭제한다.
- `custom_function()` 함수는 `file_name` 을 인수로 받아 파일의 라인을 합산해서 리턴한다.
- `self.assertEqual(custom_function(self.file_name), 3)` 구문은 `custom_function(file_name)` 을 수행하고 결과가 3이면 테스트를 통과한다.

![image-20200203234732866](images\image-20200203234732866.png)

- 에러를 확인하는 테스트는 `aasertRaises()` 메소드를 사용하며 with 문으로 감싸면서 내부의 코드가 에러가 발생하는지를 확인한다.

  ```python
  # ...
  class CustomTests(unittest.TestCase):
      def test_no_file(self):
          with self.assertRaises(IOError):
              custom_function(self.file_name)
  # ...
  ```

- 3개의 테스트 실행 중 1개가 실패했다고 나온다. 3번째 테스트는 에러가 발생해야 테스트가 성공한다. 의도한 에러가 나는지 확인하는 테스트이다.

  ![image-20200203235149960](images\image-20200203235149960.png)

- 3번째 테스트메소드에서 의도적으로 에러를 발생시키기 위해 인자를 변경하면 3가지 테스트 모두 성공을 확인할 수 있다.

  ```python
  # ...
  def test_no_file(self):
      with self.assertRaises(IOError):
          custom_function('abc.txt')
  # ...
  ```

  ![image-20200203235455617](images\image-20200203235455617.png)

