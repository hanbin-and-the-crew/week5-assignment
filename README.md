# week5-assignment - ALB 통합 테스트 및 운영 안정화

### 과제 개요

본 과제를 통해 AWS Application Load Balancer(ALB)와 ECS Fargate 기반 서비스가 정상적으로 연동되는지 검증하고,
실제 장애 상황에서 서비스가 자동 복구되는 과정을 경험합니다.

목표 : 단순 서비스 배포가 아닌 운영 환경 수준의 안정성 확보

### 단계별 상세 목표

| **목표** | **설명** |
|---|---|
| 1. ALB 통합 검증 | 요청이 올바른 서비스로 라우팅 되는지 확인 |
| 2. 무중단 배포 경험 | 기존 요청이 끊기지 않고 배포가 진행되는지 확인 |
| 3. 헬스 체크 검증 | Health Check 실패 시 새로운 Task가 자동으로 생성되는지 확인 |
| 4. 자동 복구 확인 | 서비스 장애 상황에서 운영 개입 없이 정상 상태로 돌아오는지 확인 |

### 테스트 환경

| **항목** | **구성** |
|---|---|
| AWS ECS | Fargate, 최소 2 Task |
| ALB Health Check Path | `/actuator/health` |
| 배포 방식 | Rolling Update |
| 모니터링 | CloudWatch Metrics, Logs |


### 테스트 항목

| **테스트** | **설명** | **성공 기준** |
|---|---|---|
| 헬스체크 동작 확인 | ALB가 Unhealthy Task를 감지하는지 확인 | Unhealthy → Healthy 자동 복구 |
| Task 강제 종료 실험 | 기존 Task를 종료했을 때 새 Task 생성 여부 확인 | 60~90초 이내 복구 |
| 무중단 배포 테스트 | 배포 중 요청 실패 여부 확인 | 요청 실패율 1% 미만 |
| CloudWatch 분석 | HealthyHostCount, 5xx 발생 시점 확인 | 그래프 상 정상 복구 패턴 존재 |


### 문서 소개 
테스트 계획 수립 : [docs/ALB_TEST_PLAN.md](https://github.com/hanbin-and-the-crew/week5-assignment/blob/main/docs/ALB_TEST_PLAN.md)

실제 테스트 수행 보고서 : [docs/ALB_TEST_REPORT.md](https://github.com/hanbin-and-the-crew/week5-assignment/blob/main/docs/ALB_TEST_REPORT.md)


----
과제 수행일 : 2025.11.27
