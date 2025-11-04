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

- 