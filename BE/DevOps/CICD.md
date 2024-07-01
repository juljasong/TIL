# GitHub Action
## CI/CD
### CI : 지속적 통합(Continuous Integration)
- build와 test의 자동화
- 변경 사항을 자동으로 테스트 해 애플리케이션에 문제가 없음을 보장
- 코드를 정기적으로 빌드 및 테스트하여 여러명이 동시에 작업하는 경우 충돌을 방지하고 모니터링할 수 있음

### CD : 지속적 제공/배포(Continuous Delivery/Deployment)
- CI 이후 작업
- 배포 준비가 된 코드를 자동으로 서버에 배포하는 작업을 자동화
- **지속적 제공에서의 CD**
  - 애플리에킹션에 적용한 코드의 빌드와 테스트를 성공적으로 진행했을 때 깃허브와 같은 코드 저장소에 자동으로 업로드하는 과정
  - 최소의 노력으로 코드 배포를 쉽게하는 것이 목표
- **지속적 배포에서의 CD**
  - 지속적 제공을 통해 병합한 코드 내역을 AWS와 같은 배포 환경으로 보내는 것(Release)

## GitHub Action
- [Quick Start](https://docs.github.com/ko/actions/quickstart)
- 리포지토리에 특정 이벤트가 발생하면 특정 작업을 하거나, 주기적으로 특정 작업을 반복할 수 있게 해줌