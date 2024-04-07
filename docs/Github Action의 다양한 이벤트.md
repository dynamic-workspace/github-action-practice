# Github Action의 다양한 이벤트

## push

```yaml
name: push-workflow # 액션 상 표시되는 이름
on: push # 트리거 타입(푸시 이벤트)

jobs: # job 집합
  push-job:
    runs-on: ubuntu-latest # runner 명시
    steps: # step 집합, job이 실행시킬 작업
      - name: step1 # step의 이름
        run: echo hello world
      - name: step2
        run: | # | 키워드를 통해 멀티 라인 커맨드를 작성할 수 있다
          echo hello world
          echo github action
```

`push-workflow` 라는 workflow는 push 이벤트가 발생할 때 트리거되고, `push-job` 이라는 이름의 job 1개가 실행이 되고,  
해당 job의 첫번째 step인 `step1`은 hello world 를 출력하고 `step2`는 hello world, github action을 출력하고 종료된다.

## pull request

```yaml
name: pull-request-workflow
on: pull_request

jobs:
  pull-request-job:
    runs-on: ubuntu-latest
    steps:
      - name: step1
        run: echo hello world
      - name: step2
        run: |
          echo hello world
          echo github action
```

pull_request 이벤트는 PR이 생성될 때도 실행되고 이미 생성된 PR에 commit이 추가돠어도 실행된다.
- 즉, PR이 open될 때 동기화될 때 모두 실행된다.

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/65172c5a-9c48-4316-bd5b-ee8e46b1e865)

이러한 이유는 `on: pull_request` 로 트리거 이벤트를 설정하면 default Activity type이 적용되기 때문이다.
- default Activity type: opened, synchronize, reopen
- 만약 PR이 열릴때만 job을 실행하고 싶다면 명시적으로 지정해야 한다.

```yaml
name: pull-request-workflow
on:
  pull_request:
    types: [ opened ]
```

## issue

> issue: Github repository 에서 버그, 기능 요청, 질문, 논의 등을 추적 및 관리하는 도구이다. 프로젝트의 버그나 기능 개발 등의 진행 상황을 파악하는데 사용한다. 

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/e97ca338-a680-418b-b5bb-0e4cf2c97fa6)

- issue 또한 PR처럼 activity type이 존재한다.
- default가 모든 type을 대상으로 트리거가 된다.