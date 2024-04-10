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

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/08623aa6-6557-4a99-8011-2ecbc2f89533)

## cache

- 자주 사용되는 데이터를 빠르게 불러올 수 있도록 저장하는 기능을 제공한다.
- 의존성 설치 시간을 단축한다.
- 깃헙 마켓플레이스에 정의된 공식 액션

```yaml
name: cache
on:
  push:
    paths:
      - 'src/**' # src 파일 하위 모든 파일

jobs:
  cache:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v4
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: 17
          distribution: 'temurin'
      - name: Gradle Caching
        uses: actions/cache@v3
        with:
          path: | # 캐싱할 경로
            ~/.gradle/caches
            ~/.gradle/wrapper
          key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*', '**/gradle-wrapper.properties') }} # 캐시의 키, 해당 위치의 파일이 변경될 때마다 hash 값이 변경되므로 새로운 의존성이 추가되거나 변경되는 경우 새롭게 캐싱된다.
          restore-keys: | # 캐시 복구를 위한 키 
            ${{ runner.os }}-gradle-
      - name: Grant execute permission for gradlew
        run: chmod +x gradlew

      - name: Build with Gradle
        run: ./gradlew build
        shell: bash
```

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/2f1047f4-4b29-46ca-86d7-d3276efa3eee)

- Action -> caches 탭에서 캐시를 확인할 수 있다.

## artifact

- 워크플로우 실행 중 생성된 파일 또는 파일 모음
- 동일한 워크플로우 내에서 job 사이에 데이터 공유 
- 워크플로우가 종료된 후에도, 데이터를 유지한다 (90일)
- 필요하다면 다운로드도 가능하다.
- 깃헙 마켓플레이스에 정의된 공식 액션
  - upload-artifact
  - download-artifact

```yaml
name: artifact
on: push

jobs:
  upload-artifact:
    runs-on: ubuntu-latest
    steps:
      - name: echo
        run: echo hello-world > hello.txt
      - name: upload artifact
        uses: actions/upload-artifact@v3
        with:
          name: artifact-test
          path: ./hello.txt
  download-artifact:
    runs-on: ubuntu-latest
    needs: [upload-artifact]
    steps:
      - name: download artifact
        uses: actions/download-artifact@v3
        with:
          name: artifact-test
          path: ./ # 다운로드 위치
      - name: check
        run: cat hello.txt
```

## output

- 한 job에서 생성된 데이터
  - 동일한 job의 step 또는 다른 job 들과 데이터를 공유할 수 있다.
  - 다른 job 에서 output을 사용하려면 needs, job level output 설정이 필요하다.
- 여러 step과 job 간에 데이터를 손쉽게 전달 가능
- artifact 는 파일 또는 파일 모음으로 데이터를 공유하는 반면에 output은 단순한 값을 전달할 때 사용한다. (key-value 형태로 데이터를 저장)
- echo "{key}={value}" >> $GITHUB_OUTPUT
- output 을 사용하려면 아웃풋이 정의된 스텝의 고유 id를 사용해야 한다.

```yaml
name: output
on: push

jobs:
  create_output:
    runs-on: ubuntu-latest
    outputs: # job level output
      test: ${{ steps.check-output.outputs test }}
    steps:
      - name: echo output
        id: check-output
        run: echo "test=hello" >> "$GITHUB_OUTPUT" # step level output
      - name: check output
        run: |
          echo ${{ steps.check-output.outputs.test }}
          
  get-output:
    needs: [ create-output ]
    runs-on: ubuntu-latest
    steps:
      - name: get output
        run: |
          echo ${{ needs.create-output.outputs.test }}
```

## env variables

- step이나 job에서 사용할 수 있는 환경 변수
- key-value 형태로 데이터를 저장
- output은 같은 job, 다른 job 간에 데이터 공유가 가능한 반면에 env는 동일한 job에서만 데이터 공유가 가능하다.
- 구성 방법: env 사용, 미리 정의

### env 사용

### 워크플로우 내에서 정의하는 방법

- workflow level, job level, step level
- 하위 레벨에 동일한 key의 env가 존재한다면 해당 env를 사용한다. (step >> job >> workflow)
- echo "{key}={value}" >> $GITHUB_ENV 도 사용가능

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/8df333ad-b548-4f05-8992-c3317fa19e0c)

```yaml
name: env-var-1
on: push

env:
  level: workflow

jobs:
  get-env-1:
    runs-on: ubuntu-latest
    steps:
      - name: check env
        run: echo "LEVEL ${{ env.level }}"

  get-env-2:
    runs-on: ubuntu-latest
    env:
      level: job
    steps:
      - name: check env
        run: echo "LEVEL ${{ env.level }}"

  get-env-3:
    runs-on: ubuntu-latest
    steps:
      - name: check env
        env:
          level: step
        run: echo "LEVEL ${{ env.level }}"

  get-env:
    runs-on: ubuntu-latest
    steps:
      - name: create env
        run: echo "level=job" >> $GITHUB_ENV
      - name: check env
        run: echo "LEVEL ${{ env.level }}"
```

### 워크플로우 밖에서 미리 정의하는 방법

- 미리 환경변수를 정의한 후에
  - ${{ vars.<environment-variables> }}
  - ex. ${{ vars.LEVEL }}
- 정의된 환경변수를 바꾸면 워크플로우 결과가 달라질 수 있다.
- Settings -> Secrets and variables -> Actions -> Variables -> New repository variables

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/555e00db-cb99-49fe-9939-937d1525673a)

```yaml
name: env-var-2
on: push

jobs:
  get-var:
    runs-on: ubuntu-latest
    steps:
      - name: get var
        run: echo ${{ vars.LEVEL }}"
```

> 외부 환경변수를 주입받기 때문에 re-run을 하면 결과가 달라진다.

## secret

- 민감한 데이터를 안전하게 저장해서 워크플로우에서 사용
- 민감 정보를 코드와 분리하여, 노출되는 것을 방지할 수 있다.
  - ex. API Key, 암호, 인증 토큰
- 깃헙을 통한 안전한 저장
- 로깅 방지 (출력 시 마스킹)
- 워크플로우 실행중에만 접근이 가능하다. (접근 제한)
- secret도 미리정의한 env와 마찬가지로 정의된 시크릿을 바꾸면 워크플로우 결과가 달라질 수 있다.
- ${{ secrets.<secret-name> }}
- Settings -> Secrets and variables -> Actions -> Secrets -> New repository secrets

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/226b7fb0-50e3-4438-96a6-6077ea5eb847)

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/84e299c8-8e43-4efe-bf35-e5471dcf15f9)

## environment

- 특정 환경에서만 사용 가능한 환경변수와 시크릿 관리를 정의할 수 있는 기능
- 레포지토리에서 정의할 수 있다.
- 환경변수와 시크릿은 다양한 레벨에서 정의가 가능하다.
  - organization level, repository level, environment level
  - 하위 레벨에서 정의한 값을 우선으로 사용한다. (environment >> repo >> org)

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/ca64112e-2dd5-4a09-8912-da4448d8031a)

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/603625f4-ff84-4f5a-9557-8677bbbafc2d)

```yaml
name: environment
on: push

jobs:
  get-env:
    runs-on: ubuntu-latest
    steps:
      - name: check env & secret
        run: |
          echo ${{ vars.level }}
          echo ${{ secrets.key }}
          
  get-env-dev:
    runs-on: ubuntu-latest
    environment: dev
    steps:
      - name: check env & secret
        run: |
          echo ${{ vars.level }}
          echo ${{ secrets.key }}
```

**environment 만들기**

Settings -> Environments -> New environment

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/3314f69b-3a4e-4fb6-bedb-9d0a2e8a6e88)

## matrix

- 변수 기반으로 여러 job을 실행하는 기능
- matrix를 사용해서 하나의 잡을 구성하면 여러 개의 잡을 실행하도록 할 수 있다.
- 변수로 window, linux, macOS 설정
  - 하나의 잡을 구성해서 러너가 다른 잡을 3개 실행 (window 러너, linux 러너, macOS 러너)
- 변수로 python3.7 ~ python3.9
  - 하나의 잡을 구성해서 같은 코드베이스에서 각 버전별로 테스트 구성
- 만약 matrix 기능이 없다면 하나의 잡 구성이 아니라 여러 개의 잡을 구성해야할 수 있다.
- job.strategy.matrix.<변수명>
- matrix 값을 사용하려면 ${{ matrix.<변수명> }}

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/461473aa-2f11-4ea1-a973-cd6a187666ff)

```yaml
name: matrix
on: push

jobs:
  get-matrix:
    strategy:
      matrix:
        os: [ macos-latest, windows-latest, ubuntu-latest ]
        version: [ 12, 14, 16 ]
    runs-on: ${{ matrix.os }}
    steps:
      - name: echo matrix value
        run: |
          echo ${{ matrix.os }} 
          echo ${{ matrix.version }} 
```

- 변수의 모든 가능한 조합에 대해 별도의 잡을 생성 -> 총 9개의 job이 실행된다.

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/0cf4c413-9a33-4c17-9baf-112a01cf2d20)

## if condition

- 특정 조건이 충족될 때 실행되도록 하는 데 사용 

https://docs.github.com/en/actions/using-jobs/using-conditions-to-control-job-execution

https://docs.github.com/en/actions/learn-github-actions/expressions#operators

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/661f4380-6888-440d-90d1-4456baf98c10)

- operator를 통해 적절한 조건을 구성하면 된다.
- ex. if: github.event_name == 'push'
- job level, step level 에서 정의하여 job, step의 실행여부를 결정할 수 있다.

**filter와 비교**

- filter
  - workflow 트리거를 더 세밀하게 제어
  - branch filter: [ dev, main ]
- if condition
  - workflow가 트리거된 이후 job과 step을 세밀하게 제어
  - ex. dev branch 일 때 첫 번째 job만 실행한다.

### 특정 job과 step을 강제로 실행 

- if: always()

![image](https://github.com/yoon-youngjin/spring-study/assets/83503188/146cc6d2-fd18-4401-851a-1d145452fdf8)

- needs 에 의해 job2 는 실행되지 않는데, 여기서 `if: always()` 를 통해 Job2를 실행하도록 할 수 있다.
- job level, step level 에서 모두 사용 가능하다.

