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

```yaml
name: issue-workflow
on:
  issues:
    types: [ opened ]

# ...
```

- issue 이벤트는 push, pull_request 이벤트와 다르게 default branch 에서만 트리거된다.

## issue comment

issue에 comment를 달거나 pull request에 comment를 다는 행위가 issue comment 이벤트이다.
- issue 이벤트처럼 default branch에서만 트리거된다.

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/04e083d3-a788-418d-991c-552696300efa)

- 모든 Activity type이 default

```yaml
name: issue-comment-workflow
on: issue_comment

jobs:
  pr-comment: # PR에 comment를 추가할 때 실행시킬 job
    if: ${{ github.event.issue.pull_request }} # 조건을 추가할 수 있다
    runs-on: ubuntu-latest
    steps:
      - name: pr comment
        run: echo ${{ github.event.issue.pull_request }}
  
  issue-comment: # PR에 comment를 추가할 때 실행시킬 job
    if: ${{ !github.event.issue.pull_request }}
    runs-on: ubuntu-latest
    steps:
      - name: issue comment
        run: echo ${{ !github.event.issue.pull_request }}
```

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/8a772aef-ad19-4f36-8525-c60d66bcf68b)

- if 문에 의해 skip

## schedule

특정 시간마다 github action workflow를 실행하는 이벤트

```yaml
name: schedule-workflow
on:
  schedule:
    - cron: '15 * * * *'

jobs:
  schedule-job:
    runs-on: ubuntu-latest
    steps:
      - name: schedule test
        run: echo hello world
```

- default branch에 workflow가 존재해야지만 동작한다.

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/f7f6c25a-6794-458c-ba59-b7964a8552ad)

- 위 문서에서 보다싶이 github action 사용에 대한 높은 부하가 존재하면 작업이 밀리거나 실행되지 않을 수 있다.
- 따라서 반드시 실행되야하는 작업이라면 github action schedule 이벤트는 적합하지 못하다.

## workflow dispatch

- 수동으로 트리거 
- default branch 에서만 동작

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/24723257-9b04-4398-bb36-c4f5740cb02b)

- input 값을 넣을 수 있다.
- input 값을 넣으면 해당 값을 github action job에서 활용할 수 있다.

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/48bb0133-2c57-4ea0-be34-93e5d45d35b3)

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/7e872596-01f4-4fbc-8e9e-0e948a7b6071)

- 다양한 타입을 제공한다. string, number, boolean, choice(boolean)

```yaml
name: workflow_dispatch
on:
  workflow_dispatch:
    inputs:
      name:
        description: 'set name' # name input에 대한 설명
        required: true # 해당 input이 필수값?
        default: 'github-actions' # 해당 input 값의 기본값(prefill)
        type: string # 타입
      environment:
        description: 'set env'
        required: true
        default: 'dev'
        type: choice
        options:
          - dev
          - prod

jobs:
  workflow-dispatch-job:
    runs-on: ubuntu-latest
    steps:
      - name: step1 echo hello world
        run: echo Hello, world!
      - name: step2 echo github action
        run: |
          echo Hello, world!
          echo github action!
      - name: echo inputs
        run: |
          echo "NAME: ${{ inputs.name }}"
          echo "ENVIRONMENT: ${{ inputs.environment }}"
```

## 다양한 이벤트로 하나의 워크플로우 트리거하는 방법

앞선 과정에서 on 키워드를 통해 이벤트를 지정했다.
하나의 workflow를 트리거할 수 있는 여러 개의 이벤트가 필요할 수 있다. 

```yaml
on:
  push: 
  issue:
    types: [ opened ]
  workflow_dispatch:

# ...
```

## needs

하나의 워크플로우 안에서 여러 개의 잡을 사용하는 경우가 많은데 이러한 각 job들은 병렬로 실행된다.
상황에 따라서 특정 job이 완료되고 특정 job이 실행되어야 하는 종속성이 필요한 경우 `needs`를 사용해볼 수 있다.

- 여러 job의 실행 순서를 조절
- 예를 들어 테스트 job이 실행되고, 그 job이 성공할 때 배포 job이 실행되도록 구성해볼 수 있다.

```yaml
name: needs
on: push

jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo "job1 done"

  job2:
    runs-on: ubuntu-latest
    needs: [ job1 ]
    steps:
      - name: echo
        run: echo "job2 done"

  job3:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: |
          echo "job3 failed"
          exit 1

  job4:
    runs-on: ubuntu-latest
    needs: [ job3 ]
    steps:
      - name: echo
        run: echo "job4 done"
```

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/c7235b73-5b5a-40e0-a111-a552364e86f3)

- job1, job3은 종속성이 걸려있지 않으므로 병렬로 실행
- job1이 완료된 후 job2가 실행되며 job4는 job3의 실패로 실행되지 않는다.

이렇게 실패한 job4를 실행시킬 수 있는 방법이 없을까?
Github action에서는 이러한 상황을 위해서 종속성에 의해 실패한 job이 실행될 수 있는 방법을 제공한다. (if-condition)

## re-run

- 과거에 실행된 워크플로우를 재실행
- 성공, 실패 여부와 상관없이 재실행이 가능하다.
- 트리거된 그 시점을 다시 실행

```yaml
name: re-run
on: push

jobs: 
  test:
    runs-on: ubuntu-latest
    steps: 
      - name: checkout
        uses: actions/checkout@v4
      - name: cat readme
        run: cat README.md
```

- checkout은 repository 코드를 github action job 에서 받아올 수 있는 action
