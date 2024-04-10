# Github Action의 다양한 기능

## checkout

- 깃헙 레포를 가쟈와 작업을 수행
- 깃헙 마켓플레이스에 정의된 공식 액션
- 테스트 및 빌드 작업 수행
- 레포지토리 내용을 CICD 워크플로우에서 사용 가능

```yaml
name: checkout
on: workflow_dispatch

jobs:
  no-checkout:
    runs-on: ubuntu-latest
    steps:
      - name: check file list
        run: cat README.md

  checkout:
    runs-on: ubuntu-latest
    steps:
      - name: use checkout action
        uses: actions/checkout@v4
      - name: check file list
        run: cat README.md 
```

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/22c7dcf6-ac84-45f3-b3fc-8d65c55cfcaf)

- checkout 없이 cat 명령을 수행하면 파일을 찾을 수 없다는 에러로 실패하게 된다.

## context

- 워크플로우를 실행하는 환경에 대한 정보

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/3cff85e7-c763-4feb-af37-cef0ce96859e)

- https://docs.github.com/en/actions/learn-github-actions/contexts#about-contexts

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/fead0cb6-6c73-4472-ac50-f0f12e342f0e)

**Context를 왜 사용할까?**

- 더 유연한 워크플로우를 만들기 위해
- 예를 들어 github context를 사용해서 조건 설정이 가능하다.
  - dev branch에 push 이벤트가 발생하면, workflow에서 1번 job을 실행
  - main branch에 push 이벤트가 발생하면, workflow에서 2번 job을 실행

```yaml
name: context
on: workflow_dispatch

jobs:
  context:
    runs-on: ubuntu-latest
    steps:
      - name: github context
        run: echo '${{ toJson(github) }}'
```

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/d23a2c9a-1529-456d-a9c9-b27c5413ebca)

