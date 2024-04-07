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

