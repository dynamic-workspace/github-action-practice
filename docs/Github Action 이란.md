# Github Action 이란?

Github의 Workflow 자동화 도구

## 컴포넌트 소개 

### workflow

- 하나 이상의 작업을 실행하는 자동화 프로세스
- .github/workflows 경로에 파일이 있어야 실행
- 여러 workflow 생성 및 사용 가능

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/21b5dc83-922a-4b23-a85c-62b69fda43ab)

- 이벤트에 의해 트리거된 workflow

### event

- workflow 실행을 트리거하는 깃헙 레포 내 특정 활동
- 트리거 방식
  - 레포 내 이벤트 (push, pull request, ...)
  - 수동
  - 스케줄
  - 레포 외부에서도 가능

### ruuner

- job을 실행할 수 있는 서버로 각 러너는 하나의 job을 실행
- 지원되는 러너 : Linux, Windows, macOS

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/8fc57329-6797-4707-9af2-419cca58cd4d)

### job

- 러너에서 실행되는 step의 집합
- 액션과 스크립트 등을 step에 정의해서 사용
- 기본적으로 각 job은 병렬로 실행
  - 만약 순차적으로 실행되어야 하는 경우 `needs` 키워드를 사용할 수 있다.

### step

- job이 실행하는 개별 명령
- 순차적으로 실행되며, 한 step이 실패하면 그 다음 step은 실행 X

### action

- 특정 작업을 수행하는 코드 조각
- uses 라는 키워드를 이용해서 action을 로드
- 하나의 step에서 하나의 action만 가능
  - Github 공식 제공 action
  - Github Community에서 만든 action
  - 사용자가 직접 만든 action

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/6927ba2a-138e-4fc6-9547-dcdbe2dd70ec)

- use 키워드를 통해 actions을 사용한다.
- ex. 깃헙의 코드를 받아오기 위해서 git checkout 커맨드가 아닌 checkout 이라는 action을 로드해서 쉽게 깃헙 코드를 불러올 수 있다.

## Github Marketplace

- action 저장소
- 커스텀 action을 publish 해서 업로드
- 이미 있는 액션을 사용 가능

https://github.com/marketplace?type=actions

### Checkout Action

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/99ccd5a6-4b5c-4517-91ed-4aa89e1e4b5a)

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/c2de29b6-7c36-4f0c-9ba3-8e74eab456ad)

- 특정 액션은 추가적인 input 값을 with 키워드를 통해 전달할 수 있다.
- checkout action은 현재 workflow 파일이 존재하는 레포 코드를 러너로 로드하는데, 만약 다른 레포의 코드나 특정 브랜치, 태그를 러너로 가져오기 위해서 별도의 설정이 가능하다.

## 사전 준비

1. settings -> Actions -> General -> `Read and write permissions`

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/6ae668fe-0a50-477d-9575-83285991d910)

2. personal token create

Settings -> Developer Settings -> Personal access tokens -> Tokens(classic)


