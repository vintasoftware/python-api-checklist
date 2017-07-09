## 간결함
- [ ] README에서 샘플 코드 제공
      사례: Pendulum의 README는 [샘플 코드](https://github.com/sdispater/pendulum)로 시작한다.

- [ ] 보일러 플레이트 코드 줄이기: 처음으로 유용한 API를 호출하는 코드까지의 라인수 세어보기
      사례: urllib2는 requests 라이브러리에 비해 HTTP 요청을 수행하는데 필요한 [보일러 플레이트 코드가 많다](https://gist.github.com/kennethreitz/973705).

- [ ] 유즈케이스 문서화하기
      사례: [python-social-auth 라이브러리는 일반적인 유즈케이스들을 문서화 하고있다](http://python-social-auth-docs.readthedocs.io/en/latest/use_cases.html)

- [ ] [단계적인 정보 공개](https://en.wikipedia.org/wiki/Progressive_disclosure) 실천: 타당한 기본값(default) 사용
  * - [ ] 인자의 기본값(default)은 가장 일반적인 유즈케이스에서의 값으로 결정
  * - [ ] 설정의 기본값(default)은 가장 일반적인 유즈케이스에서의 값으로 결정
  * - [ ] 인자 순서는 가장 많이 사용되는 것부터 나열하고 관련된 것들끼리는 그룹핑
          사례: JavaScript의  [`history.pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History_API#The_pushState()_method)는  `state, title, URL`의 인자 순서를 갖고 있는데 이는 별로 좋지 않다. 대다수의 API 사용자는 history에 URL만 추가하고자 할텐데 이는 state와 title을 지정하도록 강제하고 있기 때문이다.
  * - [ ] 코드 스니펫의 복사/붙여넣기가 필요없게 만들기

- [ ] 다루기 번거로운 입력(input)은 지양하기
  * - [ ] 파라미터명이 오해의 소지가 있는지 확인하라
          사례: Scrapy 1.2에서 send 메서드는 "to" 파라미터를 문자열의 리스트로 받는다. 만약 클라이언트가 단일 문자열을 전달하면, 메서드는 문자열을 순회하여 각 문자별로 이메일을 보낼 것이다. Scrapy 1.3에서는 단일 문자열과 문자열 리스트 모두를 받을 수 있도록 수정되었다.
  * - [ ] 사용자가 API를 호출하기 위해 무언가를 인스턴스화하고 있는지 확인하라. 만약 그렇다면 래핑된 값을 받는 것을 고려
          사례: 파일 유사 객체만을 받는 함수는 클라이언트가 문자열을 전달하려는 경우 `StringIO`를 사용하도록 강제해야한다.
  * - [ ] 커스텀 타입을 빌트인 타입으로 대체할 수 있는지 또는 둘 모두를 지원할 수 있는지 확인하라

- [ ] [최대한 사용자를 놀라게하지 말아야한다 (POLA, Principle of least astonishment)](https://en.wikipedia.org/wiki/Principle_of_least_astonishment) 법칙 따르기: "만약 어떤 기능이 예상치 못한 일을 많이 일으킨다면, 이는 재설계가 필요할 수도 있다"
  * - [ ] 기본 동작이 클라이언트가 기대하는대로 돌아가는가?
  * - [ ] 기본 동작이 성능, 부작용, 안전성, 보안, 제한 등에 대한 클라이언트의 기대치와 일치하는가?

- [ ] 추상화
  * - [ ] 솔루션이 물리적으로 어떻게 동작하는지보다는 본질적으로 **무얼** 하는지에 대해 초점을 맞춰라
        사례: 간단한 Celery 태스크를 만들기 위해 개발자가 태스크 큐, 워커, 메시지 브로커, 시리얼라이제이션 등에 대해 많은 걸 알 필요는 없다. 그들은 단지 `@app.task` 데코레이터만을 필요로한다. API는 태스크의 내부적인 동작방식보다는 태스크를 정의하고 만드는일에 초점을 맞춰야한다.
  * - [ ] 추상화되면 안되는 API가 있는가? [허술한 추상화의 법칙](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/)을 주의하자.
          예시: [RPC는 거의 본질적으로 문제가 있는데](https://www.joelonsoftware.com/2000/08/22/three-wrong-ideas-from-computer-science/) 이는 원격 리소스를 로컬 리소스로 추상화하기 때문이다. 그러나 이는 로컬 리소스와는 다르게 처리되어야한다.

- [ ] 파이썬답게 (Be Pythonic)
  * - [ ] 파이썬다운 (Pythonic) 코드를 작성하려고 노력하고, API 호출을 Python의 빌트인 API 호출 형태와 유사하게 만들어라
        사례: Python 2에서 [`ConfigParser.get`](https://docs.python.org/2/library/configparser.html#ConfigParser.RawConfigParser.get)은 `section`과 `option` 파라미터를 받는다. 그러나 Python에서 이는 부자연스러운데 Python의 빌트인 데이터구조인 `dict`에서 `get`은 `key`와 `default` 값을 파라미터로 받기 때문이다. 이는 Python 3에서 [`ConfigParser`를 위한 dict 유사 인터페이스](https://docs.python.org/3.6/library/configparser.html#mapping-protocol-access)가 나오면서 수정되었다.

## 일관성
- [ ] 네이밍: 네이밍에 일관성이 있는지 확인
  * - [ ] 단어 순서: `string_encode` vs `decode_string`.
  * - [ ] 축약어: `activate_prev` vs `fetch_previous`, `bin2hex` vs `strtolower`.
  * - [ ] 언더스코어 여부: `gettype` vs `get_class`
  * - [ ] 복수형: `values_list` vs `value_list`
  * - [ ] 부정형: `button.enabled == True` vs `button.disabled == True`
- [ ] 빈 값: 빈 값의 의미에 일관성이 있는가? 그게 최선의 표현인가?
  * - [ ] 빈 값으로 무엇을 사용할지 결정하라: `None`, `False`, `[]`, `''`, `0` 등
  * - [ ] 예기치 않은 false 값에 주의하라 : Python < 3.5에서는 `bool(datetime.time(0)) == False`가 성립
- [ ] 파라미터: 파라미터의 순서에 일관성이 있는가?
  예시:  `datetime.datetime(year, month, day, minute, second, microsecond)` vs `datetime.timedelta(days, seconds, microseconds, milliseconds, minutes, hours, weeks)`
- [ ] 동작: 유사한 동작은 유사하게 표현되고 다른 동작은 다르게 표현되고 있는가? 동작의 비대칭성은 비대칭적 형태로 반영되어야한다.
  좋은 예시: 제자리 정렬 `numbers.sort()` vs 비제자리 정렬 `sorted(numbers)`


## 유연함
- [ ] [통합의 불연속성](https://mollyrocket.com/casey/stream_0028.html) 줄이기
  * - [ ] 클래스가 [단일 책임 원칙](https://en.wikipedia.org/wiki/Single_responsibility_principle)을 지키고 있는지 확인하고, 만약 아니라면 클래스를 여러개의 클래스로 분할하라
        예시: 캐시로부터/캐시로 항목을 가져오고/캐싱하는 클래스는 캐시 서버에 연결하는 동작을 별도의 클래스로 분리해야한다.
  * - [ ] 함수명에 "and"가 있거나 함수명이 여러 작업을 수행함을 의미하고 있는지를 확인하라. 만약 그렇다면 여러개의 함수로 분리하라. 다만, 이미 많은 사용자가 이를 사용하고 있다면 결합된 상태 그대로 두는게 좋다
        예시: `print_formatted` 함수는 `print`와 `formatted`로 분리하면 좋다.
  * - [ ] API 사용자가 어떤 동작을 변경하기 위해서 해당 코드를 복사/붙여넣기 해야하는지를 확인해보라. 코드를 복사/붙여넣기하는 대신에 후킹이나 오버라이딩 할 수 있는 메서드를 제공하라
  * - [ ] 내부 클래스 동작에 사용되는 속성(attribute)값이 `get_something` 메서드가 될 수 있는지 확인하라
          사례: Django REST Framework의 CursorPagination 클래스는 `get_page_size` 메서드가 없었기 때문에 고정된 `page_size`만을 지원했었다. 이는 해당 메서드를 추가함으로써 [수정되었다](https://github.com/encode/django-rest-framework/pull/3147).
  * - [ ] 유용하게 사용될 수 있는 파라미터는 숨기지 않는게 좋다
          예시: API가 또 다른 로우 레벨 API를 호출하지만 로우 레벨 API가 지원하는 유용한 파라미터들을 노출하지 않는 경우
  * - [ ] API 사용자가 필요로 하는 것들은 모두 반환하라
  * - [ ] API 클라이언트가 코드를 호출 할 때 필요한 모든 것을 전달하라 ([제어의 역전 (IoC)](https://en.wikipedia.org/wiki/Inversion_of_control)이 존재하는 경우)
  * - [ ] API 테스트에 존재하는 모든 `mock.patch`를 평가해보라. 이는 런타임중에 무언가가 유연하게 변경되기 어려움을 나타낸다. 이들을 변경할 수 있는 파라미터 지원을 고려해보라
          사례: Python의 빌트인 [sched.scheduler](https://docs.python.org/3.6/library/sched.html)는 `timefunc`와 `delayfunc`를 전달 받기 때문에 테스트는 `time.monotonic`이나 `time.sleep`을 모킹할 필요가 없으며 클라이언트는 이 동작들을 변경할 수 있다

- [ ] 추상화
  * - [ ] 복수의 로우 레벨 구현체에 대한 공통적인 리소스 및 기능들을 래핑하라
          사례: BeautifulSoup은 [여러개의 서로 다른 파서들](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#installing-a-parser)에 대해 동일한 API를 제공한다 
  * - [ ] 가장 간단한 것부터 가장 커스터마이징 수준이 높은 것까지 다양한 추상화 레벨을 제공하라
          사례: Celery는 `@app.task` 데코레이터와 `celery.Task`로부터 상속한 [커스텀 태스크 클래스](http://docs.celeryproject.org/en/latest/userguide/tasks.html#custom-task-classes) 모두를 제공한다
  * - [ ] 추상화에서 빠져나올 수 있는 방법을 제공하고 추상화된 리소스 및 기능들을 직접 사용할 수 있도록 하라
          사례: Django 쿼리셋은 ORM으로 생성된 쿼리에 커스텀 SQL을 붙여서 사용할 수 있게 하는 `.extra`를 지원한다. 이는 또한 완전한 원시 SQL 쿼리를 작성할 수 있는 `.raw`도 지원한다.
  * - [ ] 다중 로우 레벨 구현체들에서 나타나는 일반적인 에러들을 하나로 래핑하고 로우 레벨 에러가 API 클라이언트에게 누출되지 않도록 하라
          예시: 여러분의 API가 여러개의 데이터베이스 엔진을 지원한다면 데이터베이스 커넥션 에러는 엔진과 무관하게 동일해야한다. 이는 API 클라이언트가 많은 코드를 변경하지 않으면서 엔진을 쉽게 변경할 수 있도록 한다.

- [ ] 파이썬답게 (Be Pythonic)
  * - [ ] 간단한 getter 및 setter에는 프로퍼티를 사용하라
  * - [ ] 연산자 오버로딩에는 매직 메서드를 사용하라
  * - [ ] 간단한 디버깅에는 `__repr__` 매직 메서드를 사용하라
  * - [ ] open-close/start-end의 라이프사이클을 갖는 작업에는 with문 컨텍스트를 사용하라
  * - [ ] 일반적인 동작들을 간편히 구성하거나 무언가를 등록하기 위해서는 데코레이터를 사용하라
  * - [ ] lazy한 이터레이블 객체에는 이터레이터를 사용하라
  * - [ ] 로직을 교차시키기 위해서는 제너레이터를 사용하라
  * - [ ] 비동기 코드에는 asyncio를 사용하라
  * - [ ] 커스텀 저장소 대신 빌트인 기능들을 사용하라
  * - [ ] 간단한 제어의 역전 (IoC)를 위해 간단한 고차원 함수를 허용하라
          예시: `list.sort`는 순서를 계산하기 위한 랭커함수로써 `key` 파라미터를 받는다
  * - [ ] 복잡한 흐름의 경우 함수/클래스 파이프 라인, 상속 또는 제너레이터를이 순서대로 고려하라
          사례: 파이프라인: [python-social-auth pipelines](python-social-auth), 상속: [Django class-based views](https://docs.djangoproject.com/en/1.11/topics/class-based-views/), 제너레이터: [Scrapy spiders](https://doc.scrapy.org/en/latest/intro/tutorial.html)
  * - [ ] [덕 타이핑과 "허가보다 용서를 구하는 것이 더 쉽다"](https://docs.python.org/3/glossary.html#term-duck-typing)를 존중하라

- [ ] 최종 사용자가 볼 문자열을 국제화(I18N, Internationalization)하라


## 안전성
- [ ] 기능의 동작을 설명하는 문서에 "warning", "careful", "remember to", "don't forget"과 같이 주의하라는 단어가 있는지 확인. 만약 그렇다면, 해당 기능을 좀 더 안전하게 변경할 수는 없는지 고려해보라
- [ ] 일반적인 실수를 감지하고 이들에 대한 warning을 로깅하기 위해 Python의 빌트인 모듈인 [warnings](https://docs.python.org/3.6/library/warnings.html)을 사용하라
- [ ] 안전하지 않은 동작을 명시적으로 드러나게끔 요구하라
  * 무언가가 설정되지 않은 경우엔 모두 표시하지 말아라. 예를 들어 `fields = None`은 모든 필드를 의미하지는 않는다
- [ ] 코드를 암시적으로 이름이나 모듈로 연결하지 말고, 레지스트리나 레지스트리 데코레이터를 사용하라
  * 예시: django-admin의 [registry](https://docs.djangoproject.com/en/1.11/ref/contrib/admin/)는 함수를 사용한 등록과 데코레이터를 사용한 등록 모두를 지원한다
- [ ] 메서드 호출 순서에 의존하는것을 지양하고 대신 with문 컨텍스트를 사용하라
- [ ] 빠른 실패
  * - [ ] 라이브러리가 오버플로우, 형식이 잘못되었거나 잘못된 상태와 같은 잘못된 인자를 받으면 `ValueError` 예외를 발생시켜라
  * - [ ] 라이브러리가 호환되지 않는 타입의 인자를 받으면 `TypeError` 예외를 발생시켜라. 다만, 내부에 `if isinstance(duck, LibDuck)`이나 `if type(duck) == LibDuck`와 같은 코드를 넣지 말고 먼저 기대하는 타입이 가진 메서드를 실행해보고 에러가 나면 `TypeError` 예외를 발생시켜라

## 결론
- [ ] 내 API는 간단한걸 쉽게 만들고 복잡한걸 가능케하며 잘못된건 불가능하게 한다
