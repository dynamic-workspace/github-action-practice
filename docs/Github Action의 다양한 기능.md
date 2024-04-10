# Github Action의 다양한 기능

## Checkout

- 깃헙 레포를 가쟈와 작업을 수행
- 깃헙 마켓플레이스에 정의된 공식 액션
- 테스트 및 빌드 작업 수행
- 레포지토리 내용을 CICD 워크플로우에서 사용 가능

```yaml
name: checkout
on: workflow_dispatch

jobs:
  no-checkoit:
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
