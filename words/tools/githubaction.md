# github action 학습

- 아티팩트를 이용해서 해당 파일을 build 할때 즉 github action이 실행할때 실행되게 하고 그 다음에는 그 이름을 다른 jobs에서 사용이 가능하게 하는 것이 중요하다. 

- env 속성을 이용해서 환경변수를 설정할 수 있다.

- .github/workflows에 민감한 정보를 적지 않는 방법도 고민을 해봐야 하는 것이다. 

- 환경변수를 가장 위에 적고 그 아래에 이 환경변수에 모두 접근하게 할 수 있다. 따라서 환경 변수를 이에 따라서 설정하는 것이 매우 중요하다는 점을 알 수 있다. 

- 중괄호를 사용해서 해당 지점에 대해서 환경변수를 지정할 수 있다는 점이다. 

- 루트에서도 env를 정의할 수 있다는 점이다 이를 이용해서 환경변수를 다른 곳에서 사용할 수 있다.

- docker도 github actions에서 사용이 가능한 점이고 이를 이용해서 처리가 가능하다는 점이다. 

- docker login에서 환경 변수에는 비밀번호 username등을 입력이 가능하고 이에 대해서 처리할 수 있다. 

- github에서 환경변수를 따로 정의해서 이를 이용해서 환경변수를 주입하려면 재정의가 필요하다는 점이다. 

- github actions이나 이런 부분에서 환경변수를 따로 관리하고자 한다면 그 레벨 안에서 따로 관리할려면 공통으로 관리하는 것이 필요하다는 것이다. 

- 프로젝트 수준에서 즉 rep 수준에서 사용을 하려면 setting에 들어가서 왼쪽에 security에서 이에 대해서 정의를 할 수 있다.

- secrets and variables에서 repository variable은 모든 workflow에서 사용가능한 환경변수를 만들고 싶을때 사용하면 좋다.

- 환경변수 중에서 비밀적으로 관리하고 싶은것은 Secret 부분에 입력하고 변수 그냥 노출해도 되는 것은 variable을 이용해서 관리하면 좋다.

- 이 값들을 사용하려면 --password=${{ secrets.DOCKER_PASSWORD }}이런식으로 설정을 해야 환경변수에서 비밀번호를 설정할 수 있는 것이다.

- 그냥 환경변수로 사용을 했다면 그냥 $DOCKER_USERNAME 이런 식으로 환경변수를 업데이트 하면 되는 부분이다. 

- github actions로 push, pr 등 외부 이벤트에 대해서 반응하게 할 수 도 있고 

- 이벤트에는 push, pull_request 등과 같이 여러 이벤트에서 trigger로 작용이 가능하다. 

- schedule을 이용해서 crontab 작업 또한 할 수 있다 즉 일정하게 작업을 하도록 만들수도 있다는 것이다.

- workflow_dispatch를 이용해서 우리가 어떤 작업을 하는데 button을 누르거나 할때 trigger가 작용하게 할 수도 있다. - 제한을 둘 수 있다는 점이다.

```yaml
name: Manual Deploy

on:
  workflow_dispatch:
    inputs:
      environment:
        description: '배포할 환경 선택'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: 소스 체크아웃
        uses: actions/checkout@v4

      - name: 배포 스크립트 실행
        run: echo "Deploying to ${{ github.event.inputs.environment }}"

```

- 워크플로에 보면 commit 하고 여기에 문자가 존재한다.

- workflow_dispatch에 경우 workflow에 들어가서 이에 대해서 버튼을 눌렀을때 트리거를 정할 수 있다.

- workflow event brach를 설정해서 이를 정의 할 수 있다. 

- github actions를 이용해서 workflow를 이용해서 이벤트 기반으로 표시할 수 있도록 한다. 

- concurrency를 이용하면 진행중이던 workflow를 취소할 수 있다. (그전에 작업 중이던 workflow를)

- 예를 들어서 진행중이던것을 끈다면 이를 이용해서 더 최신에 나온것을 주로 수행하도록 할 수 있을 것이다.

- 만약에 동시성이 false이면 반드시 앞에 있는 작업이 끝날때 까지 그다음 작업은 하지 않도록 한다.

- 기본적으로 github는 workflow가 6시간이면 작업을 멈추는데 차임아욱 옵션은 지정된 시간 동안 워크플로우가 지속되면 종료 한다. 이때 너무 오랫 동안 워크플로우가 진행되는것을 방지하기 위해서 timeout-minutes를 이용해서 시간 제한을 줄 수 있다. 

- 시간 제한도 정할 수 있다는 점이다. -> workflow에 경우 이 시간을 정해서 timeout을 만들 수 있다.

- 여러 운영체제에 대해서 배포를 할 수 있게 할 수 있고 이에 대해서 병렬로 처리할 수 있게 한다.

- strategy를 사용하면 여러개의 버전이나 운영체제에서 job을 실행하도록 할 수 있는데 이때 사용하는것이 바로 startegy에 matrix이다. 

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16, 18, 20]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm install
      - run: npm test

```

- 작업이 실행되는 것을 병렬로 할 수 있고 이에 대해서 같이 작업하도록 할 수 있다.

- docker에 alpine이미지는 윈도우에서는 잘 작동을 하지 않는다. 

- 