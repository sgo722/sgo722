# 양준영 — Backend Engineer

> 반복적인 프로세스 개선과 정교한 기능 설계에 관심이 많습니다.  
> AI Agent를 활용한 자동화 워크플로우 구축, 결제 시스템 안정성 개선, 비즈니스 이해를 바탕으로 기능 안정성까지 고민하는 개발자입니다.

# 📄 **[이력서 미리보기](./resume.pdf)**
# 📁 **[포트폴리오 미리보기](./potfolio.pdf)**

---

## Experience

| 기간 | 내용 |
|---|---|
| 2024.01 ~ 2024.12 | 🏆 삼성청년SW아카데미(SSAFY) 11기 |
| 2023.03 ~ 2024.02 | 💻 아롬 세종대학교 앱개발 동아리 |
| 2022.03 ~ 2024.02 | 🎓 세종대학교 컴퓨터공학과 |

---

## Skills

```
Language   Java · Kotlin
Framework  Spring Boot
DB         MySQL · Redis
Infra      AWS EC2/ECS · Docker
Monitoring Grafana · Prometheus
Test       k6 · nGrinder
```

---

## Study

- **객체지향 미션 진행** `2025.03 ~ 2025.05` — [github.com/sgo722/OOP_MISSION](https://github.com/sgo722/OOP_MISSION)
- **Real MySQL 1, 2권** `2025.01 ~ 2025.03` — [노션 정리](https://spectrum-cushion-45e.notion.site/Real-My-SQL-1-2-1829285c91d880edbdd9ee21c81a6507)

---

## Projects

### Code:L — 가치관 기반 소셜 매칭 서비스
`2025.07 ~ 2026.03` &nbsp;|&nbsp; Kotlin · Spring Boot · MySQL · Redis · WebSocket · AWS(ECS) · k6 · Grafana

> 프로필 기반 매칭, 실시간 채팅, 추천 시스템을 제공하는 실서비스. **실사용자 400명+**

**담당 기능**
- WebSocket/STOMP 기반 실시간 채팅 시스템 구현
- 전략 패턴 기반 하위호환성 API 설계
- k6 활용 WebSocket 부하테스트 환경 구축
- Thymeleaf 기반 관리자 페이지 개발
- AI 도구(Claude Code) 활용한 개발 자동화

**주요 기술적 고민**

<details>
<summary>AI Agent 기반 개발 자동화</summary>

- 컨텍스트 스위칭 비용 및 반복 작업(Git Issue·PR·마이그레이션 검증 등)으로 하루 1-2시간 소모
- `tmux + git worktree`로 병렬 개발 환경 구축, 프롬프트 엔지니어링으로 AI 응답 정확도 60% → **95%** 달성
- 워크플로우 템플릿화(`/pr-create`, `/test-fix`)와 GitHub Actions + AI 테스트 루프로 자동화 구축
- **컨텍스트 스위칭 0초 · PR 생성 80% 단축 · 화면 검증 시간 83% 단축**

</details>

<details>
<summary>단일 서버에서 다중 서버로의 전환</summary>

- 회원 200명 시점에 단일 서버의 한계 인식 (인스턴스 장애 시 전체 중단, 수평 확장 불가)
- SimpleBroker 인메모리 방식으로 다중 서버 간 WebSocket 메시지 미전달, `synchronized` JVM 독립 동작으로 동시성 제어 불가 문제 발생
- **Redis Pub/Sub + SimpleBroker**로 서버 간 메시지 중계, **Redisson 분산락**으로 동시성 제어, **ShedLock**으로 스케줄러 중복 방지
- 로컬 전송 우선 구조로 Redis 장애 시에도 동일 서버 사용자는 정상 동작
- **단일 장애점 제거, 수평 확장 구조로 1,000명+ 대응 가능**

</details>

---

### Toudeuk (터득) — 이벤트성 클릭 게임 플랫폼
`2024.11 ~ 2024.12` &nbsp;|&nbsp; Java · Spring Boot · MySQL · Redis · Kafka · AWS(ECS) · nGrinder · Grafana

> 조건에 따라 선정된 회원에게 보상을 지급하는 클릭 게임 서비스

**담당 기능**
- 카카오페이 외부 API 트랜잭션 분리
- 클릭 기능 동시성 문제 해결
- 아키텍처 확장을 통한 성능 개선 (각 버전 부하테스트 비교)
- 슬랙·디스코드 알림 기능 구현
- 모니터링 환경 구축 (Grafana · Prometheus · Node Exporter)

**주요 기술적 고민**

<details>
<summary>결제 시스템 안정성 개선</summary>

- 결제 승인 API와 아이템 지급 로직이 하나의 트랜잭션으로 묶여 데이터 정합성 이슈 및 DB 커넥션 장시간 점유 문제 발생
- **결제·아이템 지급 트랜잭션 분리** (`Propagation.REQUIRES_NEW`)
- 재시도(즉시 3회) / 스케줄링(5분) / Slack 알람으로 정합성 보장
- DB 유니크 제약으로 멱등성 확보, 외부 API 타임아웃(3초) 설정으로 커넥션 효율화
- **복구 시간 5분 → 3-4초 단축 · 중복 지급 0건 · 데이터 정합성 100% 보장**

</details>

<details>
<summary>클릭 기능 동시성 문제 & 성능 개선 (TPS 78 → 521, 6.5배)</summary>

| 단계 | 방법 | TPS |
|---|---|---|
| 초기 | 비관적 락 + 직접 DB 저장 | 78 |
| 1차 개선 | Redis INCR 원자 연산 도입 | 328 (+4.2배) |
| 2차 개선 | Kafka 비동기 처리로 DB 쓰기 분산 | 472 (+1.4배) |
| 3차 개선 | 쓰레드·커넥션 풀 최적화 | 521 (+1.1배) |

</details>

---

### 딸깍 — 간단 배포 플랫폼
`2024.09 ~ 2024.10` &nbsp;|&nbsp; Java · Spring Boot · PostgreSQL · Redis · Kafka · AWS(ECS) · GCP

> 노트북 하나로 몇 번의 클릭만으로 프로젝트를 배포할 수 있는 서비스

**담당 기능**
- 도커파일 자동 생성 기능 구현
- SAGA 코레오그래피 패턴 적용
- MSA 환경에서 Deployment 도메인 개발

**주요 기술적 고민**

<details>
<summary>도커파일 자동 생성 시스템 (성공률 30% → 90%)</summary>

- AI 단독 전체 파일 분석 방식은 의존성·빌드 과정 파악 실패로 성공률 30%에 불과
- 언어·빌드도구·패키지 관리자 정보를 AI에 함께 전달 → 성공률 50%로 개선
- 최종적으로 Java 단에서 수집된 프로젝트 정보 기반으로 분기 처리하여 **직접 Dockerfile 생성 로직** 구현
- **배포 성공률 90% · 토큰 사용량 대폭 감소**

</details>

<details>
<summary>SAGA 코레오그래피 패턴 적용</summary>

- Project 삭제 시 관련 Deployment가 삭제되지 않아 불필요한 리소스 지속 실행 및 데이터 불일치 문제 발생
- 양쪽 도메인에 `status` 필드 추가해 **Soft Delete** 방식으로 전환
- **SAGA 코레오그래피 패턴**: Project 삭제 이벤트 발행 → Deployment가 구독하여 자동 삭제
- 삭제 실패 시 보상 트랜잭션으로 Project 삭제 롤백 → **데이터 정합성 100% 보장**

</details>

## 💡 Stats

![Anurag's GitHub stats](https://github-readme-stats.vercel.app/api?username=sgo722&show_icons=true&theme=merko)

## 🧩 Algorithm
[![Solved.ac Profile](http://mazassumnida.wtf/api/v2/generate_badge?boj=sgo722)](https://solved.ac/sgo722/)
