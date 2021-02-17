# Django-Study

# TIL 21/02/17
## Two Scoops of Django

### CH 1. 코딩 스타일
  - PEP 8 (flake8 library 사용하면 더 쉽게 따를 수 잇음)
  - 79 칼럼 제약
  - url pattern 의 name 에는 (-) 대신 (_) 사용

### CH 2. 최적화된 장고 환경 꾸미기

 - 로컬 환경과 운영 환경에서 같은 DB 사용하기 (PostgreSQL 추천)
 - virtualenv 을 이용한 파이썬 라이브러리 의존성 유지
 - version control (git)
 - 프로젝트 개발 팀원 모두에게 똑같은 개발 환경 제공 (Virtual Box or Vagrant or Doker)

### CH 3. 어떻게 장고 프로젝트를 구성할 것인가
  - 쿠키커터로 프로젝트 구성 템플릿 만들기
  - pip install cookiecutter
  - cookiecutter gh:pydanny/cookiecutter-django (원래 하던 것 보다 훨씬 파일이 생겼다... 너무 많은데?)
  - [cookie cutter 참고](https://medium.com/@jsh901220/django%EC%99%80-cookiecutter-django-%EA%B0%84%EB%8B%A8-%EC%84%A4%EB%AA%85-898d063d38ff
)

### CH 4. 장고 앱 디자인의 기본
 - 각 장고 앱은 그 앱 자체가 지닌 한 가지 역할에 초점이 맞추어져야 하며 단순하고 쉽게 기억되는 이름을 가져야 한다.
 - 앱의 기능이 너무 복잡하다면 여러 개의 작은 앱으로 나눈다.

### CH 5. settings 와 requirements 파일

 - settings 의 Secret Key 는 환경 변수에 보관한다. (찾아보니 어려워서 json 파일에 보관 후 .gitignore 에 추가하기로 함)
 - requirements 와 settings 파일은 베이스, 로컬, 배포 환경 각각을 구분하여 만든다.
 - settings 에서 경로를 지정할 때 BASE_DIR 을 적극 이용한다.

### CH 6. 장고에서 모델 이용하기

 - 모델 상속 abstract base class, multi-table inheritance, proxy model
   ```text
   모델 사이에서 중복되는 내용이 최소라고 한다면 모델의 상속이 필요없다.
   모델들 사이에 중복된 필드가 많을 때, 추상화 기초 모델 (abstract base class) 을 사용한다.
   ```
 - 장고 모델 정규화 (데이터베이스 정규화) 필수임 필수
 - 거대 모델 [거대 모델을 다루는 방법](https://americanopeople.tistory.com/304)
거대 모델, Model Manager, mixins, utils, view, query
   
## 클린 코드를 위한 테스트 주도 개발

 - 첫 테스트
```python
 # 가상환경 만들고 selenium, django 설치
 #functional_test.py 
from selenium import webdriver

browser = webdriver.Chrome('C:\chromedriver_win32/chromedriver')
browser.get('http://localhost:8000')

assert 'Django' in browser.title
# 서버를 실행시키지 않았을 때 Assertion Error 발생, 서버를 실행시키면 통과 (테스트 통과)
```

 - unittest 내장 라이브러리

```python
from selenium import webdriver
import unittest

class NewVisitorTest(unittest.TestCase):

    def setUp(self):
        self.browser = webdriver.Chrome('C:\chromedriver_win32/chromedriver')

    def tearDown(self):
        self.browser.quit()

    def test_can_start_a_list_and_retrieve_it_later(self): # test 코드는 test 로 시작해야함! 이름이!
        self.browser.get('http://localhost:8000')

        self.assertIn('To-Do', self.browser.title)
        self.fail('Finish the test!')

if __name__ == '__main__':
    unittest.main(warnings='ignore')
```

 - unit test 와 functional test 의 차이점

unit test 는 개발자 관점에서 app 의 내부를 테스트하는 것, functional test 는 사용자 관점에서 app 의
외부를 테스트하는 것

 - 테스트를 이용해 작업하는 순서
```text
1. 기능 테스트를 먼저 작성해서 사용자 관점의 새로운 기능성을 정의한다.
2. 기능 테스트가 실패하고 나면 테스트를 통과할 방법을 생각한다.
이때 단위 테스트를 이용하여 어떻게 코드가 동작하는지 정의한다.
3. 단위 테스트가 실패하고 나면 단위 테스트를 통과할 정도의 최소한의 코드를 작성한다
4. 기능 테스트를 재실행해서 통과하는지 확인한다.
5. 기능 테스트가 완전해질 때까지 2~4 번을 반복한다.
```