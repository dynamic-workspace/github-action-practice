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

## branch filter

- 이벤트가 특정 조건에 부합할 때 실행
  - 워크플로우 실행을 더 효과적으로 제어
- filter: branch, path, tag
  - 예를 들어 branch filter를 사용하면 dev, master branch 중에서 master branch 로 push 해야만 실행하도록 구성할 수 있다.

### branch filter

```yaml
name: branch-filter
on:
  push:
    branches: ["dev"]

jobs:
  branch-filter:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello
```

### path filter

특정 경로 내에 파일이 업데이트될 때만 실행하도록 구성할 수 있다.

```yaml
name: branch-filter
on:
  push:
    paths:
      - '.github/workflows/part1/*'
      - '!.github/workflows/part1/push.yaml' # 무시하도록 구성할 수도 있음

jobs:
  path-filter:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello
```

### tag filter

v1.0.0 으로 태깅해야지만 실행하도록 구성할 수 있다.

- tag filter는 push event에서만 사용할 수 있다.
- 태그 구성하는 방법: git tag <tag-name>

```yaml
name: branch-filter
on:
  push:
    tags:
      - 'v[0-9]+.[0-9]+.[0-9]+' # v1.0.0 or v2.2.2 , ... 트리거

jobs:
  tag-filter:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello
```

## timeout

- 특정 시간이상 실행되면 자동으로 중단되도록 설정
- 특정 job or 특정 step 무한 루프를 방지함으로써 불필요한 자원 소모를 방지한다.
  - default: 360분
- job-level, step-level 에서 timeout-minutes 를 이용하여 사용할 수 있다.

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/ad5741b8-a61a-4602-8caf-3bb32df4e07e)

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/51e12713-9fc7-4e94-b7d4-a78ff0e8720e)


