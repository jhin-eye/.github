## 주요 기능 및 아키텍처

#### #아키텍처
![image-20250427234216718](https://github.com/user-attachments/assets/7d0d3253-1924-4381-919e-aac760c33fcd)



#### #키워드 설정 기능
![image-20250427234340856](https://github.com/user-attachments/assets/862a7bf1-4883-4984-a72d-2e611a381934)


#### #설정한 키워드 게시글 모아보기 기능
![image-20250427234323169](https://github.com/user-attachments/assets/df3bd3fe-44dc-4ae8-b894-2c0bcfc000b2)



#### #설정한 키워드 게시글 등록 알림 기능
![image-20250427234402789](https://github.com/user-attachments/assets/12df1c5d-5842-44a8-b157-d55ba2a51bab)



## 주요 업무 및 성과

- **다수 관공서 홈페이지 신규 게시글 크롤링 시스템 구축**

  - 크롤러 서비스, 유저 서비스, 메신저 알림 서비스, 이벤트 퍼블리셔 등으로 구성된 멀티 컨테이너 구조
  - Docker Compose 기반 개발환경 → AWS EKS + Helm을 통한 운영환경 전환

- **Kafka 기반 이벤트 아키텍처 구현**

  - 게시글 등록 이벤트를 Kafka 토픽으로 발행하고, 유저 서비스 및 메신저 서비스에서 독립적으로 소비
  - Consumer group 분리 및 멀티 소비 구조 설계
  - CompletableFuture 활용하여 Kafka 발행 완료 후 Outbox 테이블 published 상태 갱신

- **건의사항(Suggestion) 알림 분산 처리**

  - Kafka 파티션을 최대 관리자 수 기준으로 고정 생성
  - KafkaListener의 concurrency를 현재 관리자 수로 설정
  - Partition 번호를 기준으로 관리자별 메시지 분산 처리
  - UUID 기반 랜덤 Key 전송을 통해 Kafka 파티션 균등 분산 유도

- **크롤러 서비스 최적화**

  - ApplicationReadyEvent를 활용한 크롤러 클래스 자동 등록 및 DB 동기화
  - TaskScheduler를 통한 스케줄링 구현 및 각 크롤러 트랜잭션 분리로 장애 전파 차단
  - WebDriverProvider에 ThreadLocal 적용하여 멀티스레드 환경에서 독립적인 브라우저 인스턴스 관리
  - 크롤러 실패 시 에러 로깅 및 텔레그램 알림 전송

- **User Service 및 인증 시스템 구축**

  - Kakao OAuth2 로그인 및 JWT 기반 인증/인가
  - SecurityAutoConfiguration 비활성화 후 직접 인터셉터를 구현해 인증 관리
  - AccessToken/RefreshToken 쿠키 저장 및 Authorization Header 동시 지원

- **Event Publisher Service 안정성 강화**

  - Outbox 테이블 기반 이벤트 발행
  - 2개 컨테이너 간 Redis 분산락 적용하여 단일 장애 지점 제거

- **AWS EKS 기반 인프라 구축 및 Helm 배포**

  - EKS 클러스터 생성 및 운영, Helm Chart를 통한 PostgreSQL, Kafka, Redis 배포
  - StorageClass 설정 및 PVC 이슈 해결 경험
  - IAM Role 권한 수정(AmazonEBSCSIDriverPolicy) 및 PVC Bound 상태 정상화
  - Kubernetes readinessProbe 설정을 통한 비정상 파드 트래픽 차단

  

------

## 설계 및 운영 전략

- **이벤트 기반 시스템 설계**
  - Kafka 기반 비동기 이벤트 발행 및 소비 구조 구축
  - Transactional Outbox 패턴 적용으로 데이터 일관성 확보
- **크롤러 서비스 안정성 확보**
  - 런타임 중 크롤러 추가/삭제/수정 동기화 로직 구현
  - 크롤링 중 장애 발생 시 다른 크롤러 작업 영향 최소화
- **Kubernetes 운영 경험**
  - PostgreSQL, Kafka, Redis 등 Helm Chart 기반 배포
  - PVC 문제, IAM 권한 이슈, StatefulSet 운영 이슈 직접 해결
  - readiness, liveness Probe 설정을 통한 파드 준비 상태 검증

------

## 핵심 성과 요약

- Kafka 기반 멀티 서비스 이벤트 아키텍처 설계 및 구축

- Kubernetes 환경에서 PostgreSQL, Kafka, Redis 인프라 직접 배포 및 운영

- 분산 락 및 트랜잭션 분리 설계를 통한 시스템 안정성 강화
