## 라우팅 테스트

- **테스트 날짜/시간**
2025년 11월 27일
  
- **결과 표**

| **엔드포인트** | **응답 코드** | **응답 시간** | **성공 여부** |
| --- | --- | --- | --- |
| /api/orders | 200 | 36ms | ✅ |

- Postman 결과

<img width="964" height="782" alt="image" src="https://github.com/user-attachments/assets/db670081-dd55-4531-bf4c-b98f59bcf6aa" />

- Target Groups

<img width="1349" height="798" alt="image" src="https://github.com/user-attachments/assets/a1543d25-9a28-46b9-b50b-301b946ec594" />


---

## **헬스체크 테스트**

**타임라인** 

- 12:01:00 - Task 종료
- 12:01:10 - Unhealthy 상태 감지
- 12:01:00 - 새 Task 시작
- 10:02:00 - 새 Task Healthy
- 10:03:00 - 복구 완료

<img width="754" height="196" alt="image" src="https://github.com/user-attachments/assets/f9b4a19c-5e26-4638-ba2b-ffc96108c38a" />


**요청 성공/실패 통계**

- 총 요청: 600회
- 성공: 596회
- 실패: 4회 (0.6%)
- 실패 시점: Task 종료 직후

```markdown
for i in {1..600}; do
	curl [http://swiftlogix-alb-699706001.ap-northeast-2.elb.amazonaws.com/](http://swiftlogix-alb-699706001.ap-northeast-2.elb.amazonaws.com/health)api/orders 
	sleep 1
done
```

---

**헬스체크 테스트 :**

1. Task 종료 전 - 정상 상태

<img width="1469" height="250" alt="image" src="https://github.com/user-attachments/assets/4f93e2fd-d7da-44e4-a7f0-529c30b3e19a" />


2. Task 종료 후 - Unhealthy 상태

<img width="1335" height="298" alt="image" src="https://github.com/user-attachments/assets/4f885005-f3aa-40c8-b885-173a942f98ec" />


**CloudWatch 그래프 스크린샷**

<img width="1464" height="715" alt="image" src="https://github.com/user-attachments/assets/9681139b-bb54-4ad3-a1bc-0b1ce036ef8f" />

- HealthyHostCount가 잠시 1로 감소했다가 다시 2로 복구
- 5xx 오류가 일시적으로 증가했으나 배포 완료 후 0으로 안정화
- TargetResponseTime(응답시간)이 순간 상승했다가 다시 1초 미만으로 정상화

**결론**

- 자동 복구가 정상 작동함

---

## 무중단 배포 테스트 

### 배포 과정
- 배포 시작 시간: 12:28:00
- 새 Task 시작: 12:28:30
- 기존 Task 종료: 12:30:00
- 배포 완료: 12:37:00

### 요청 결과
- 배포 중 요청: 120회
- 모두 성공: 120회
- 실패: 0회

### 결론
무중단 배포 성공

```markdown
for i in {1..120}; do
	curl [http://swiftlogix-alb-699706001.ap-northeast-2.elb.amazonaws.com/](http://swiftlogix-alb-699706001.ap-northeast-2.elb.amazonaws.com/health)api/orders >> test.log
	sleep 1
done
```

<img width="1405" height="226" alt="image" src="https://github.com/user-attachments/assets/d3049dfe-060e-4a24-bf6d-0c5a45f07b77" />

<img width="716" height="569" alt="image" src="https://github.com/user-attachments/assets/ae55f814-e32a-4b17-a79a-0a81fc07f7d1" />

---

## **최종**

테스트 완료 체크리스트

```markdown
## ALB 통합 테스트 체크리스트

### 라우팅
- [x] 모든 엔드포인트 정상 라우팅
- [x] Target Groups Healthy 상태

### 자동 복구
- [x] Task 종료 감지
- [x] 자동 재시작
- [x] 요청 실패 < 1%

### 무중단 배포
- [x] 배포 중 요청 성공
- [x] 점진적 Task 교체

### 모니터링
- [x] CloudWatch 메트릭 확인
- [x] 알람 설정 (선택)

✅ 모든 테스트 통과
```
