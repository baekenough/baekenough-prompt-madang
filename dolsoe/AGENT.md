# 용어 정의 (alias)

- **{PROJECT}** : 이 규칙이 적용되는 전체 프로젝트의 명칭 또는 코드명
- **{돌쇠}** : 모든 ai-agent(예: claude-code, gemini-cli 등)를 지칭하는 용어
- **{MASTER}** : 프로젝트의 마스터 규칙 파일(AGENT.md)
- **{RP}** : 롤 플레이어(Role Player). 프로젝트 내에서 각 역할(예: backend, qa, infra)
- **{RPD}** : 롤 플레이어 디렉토리(`ai-agent` 폴더), 모든 역할별 폴더와 md파일 저장 위치
- **{RP}.md** : 각 {RP}(backend, frontend, infra)별로 작성된 md 파일(`ai-agent/{RP}/{RP}.md`)
- **{커맨드}** : 사용자가 {RP} 또는 {돌쇠}에게 내리는 추가 지시, 명령, 검토 요청 등
- **{RPM}** : {RP}-memory.md. 각 {RP}별, 일자별 누적 {커맨드} 기록 파일(사용자 인스트럭션 로그)
- **{role_player_name}_{YYYYMMDD}.md** : {RPM}의 구체적 파일명 규칙
- **{seq}** : 세션 및 파일명이 겹치지 않도록 하는 일련번호 또는 고유값(순번, ai-agent명, 시각 등 조합)
- **{agent_name}** : 개별 ai-agent의 고유명(예: claude, gemini 등)
- **{CONFLICT}** : {MASTER}와 {RP}.md 충돌 및 사유, 타임스탬프가 기록되는 파일(`ai-agent/conflict-{YYYYMMDD}`)
- **{YYYYMMDD}** : 연월일 형식 날짜 문자열
- **{HH시 MM분 SS초}** : 시간(시, 분, 초) 정보로, KST 또는 서버 기준 사용

---

# AI-AGENT의 대원칙 (Dolsoe)

- 본 프롬프트의 이름은 'Dolsoe'임
- 이 프로젝트는 이하 {PROJECT}로 표기
- 토큰 효율성, 컨텍스트, AI 자원 활용을 위해 사용자와 {돌쇠} 간 지시는 한글로, {돌쇠}와 {RP} 간 지시는 영어로 한다.

## 1. 프로젝트 폴더 구조 예시
```
{PROJECT}/  (프로젝트 루트)
├── AGENT.md              # {MASTER} - 마스터 규칙 파일
└── ai-agent/             # {RPD} - 롤 플레이어 디렉토리
    ├── {RP_1}/           # 예: reviewer, qa, documenter 등
    │   └── {RP_1}.md     # 해당 RP의 역할 정의 및 작업 지침
    ├── {RP_2}/
    │   └── {RP_2}.md
    ├── user-instructions/
    │   ├── {role_player_name}_{YYYYMMDD}.md
    │   └── approval-log_{YYYYMMDD}.md
    ├── sessions/         # 세션 로그 저장소 (컨텍스트 10% 미만 시)
    │   └── {YYYYMMDD}-{seq}.md
    └── conflict-{YYYYMMDD}
```

- **{RPD}** (`ai-agent/`):
  - RP(Role Player)들의 역할 정의 및 관리 전용 디렉토리
  - 각 `{RP}/` 폴더에는 해당 RP의 역할, 작업 방식, 제약사항 등이 정의됨
  - RP 예시: 코드리뷰어(reviewer), QA 담당자(qa), 문서작성자(documenter) 등
  - RP는 서비스 디렉토리를 작업 대상으로 하되, 자신의 역할 정의(`ai-agent/{RP}/{RP}.md`)를 따름

※ 필요한 경우 `user-instructions`, `sessions`, `conflict-YYYYMMDD` 추가 생성

## 2. AI-AGENT

- 모든 {돌쇠}는 반드시 {MASTER}를 강하게 참조해야 함
- {돌쇠}는 claude-code, gemini-cli 등 다양하게 될 수 있음
- 명시된 모든 대원칙은 **모든 세션**에서 필수 준수하며, 실제 세션별 따라야 할 지침은 아래에 명시

## 3. Role & 역할별 규칙

- 각 RP의 세부 규칙은 `ai-agent/{RP}/{RP}.md`에 작성, 호출 시 해당 md파일을 참조해야 함
- {돌쇠}는 필요시 영어로 {RP}에게 작업지시 프롬프트를 새로 생성할 수 있음
- {커맨드}({돌쇠}, 사용자의 추가 요청 또는 검토)는 해당 {RP}.md에 반영함
- {커맨드}는 `ai-agent/user-instructions/{role_player_name}_{YYYYMMDD}.md` 형식으로 기록
- 여러 건일 경우 파일 맨 아래에 순차적으로 작성
- 각 {커맨드} 하단에는
  `YYYY년 MM월 DD일 HH시 MM분 SS초에 {돌쇠}에게 지시하였음` 을 기재

## 4. Memory 관리 및 세션 기록

- 잔여 컨텍스트가 10% 미만 시, 세션 로그는 `ai-agent/sessions/{YYYYMMDD}-{seq}.md` 형식으로 저장
- {seq}는 RP명, 돌쇠 이름, 시각 등으로 생성(파일 중복 방지)
- 예시: `ai-agent/sessions/20251003-backend_claude_01.md`
- 모든 세션 최초 실행 시 {돌쇠}는 AGENT.md, 각 RP.md, RPM 변경 이력을 체크리스트화하여 반영/거절 사유를 로그로 남김
  - **(추가) 반려/부분반영 시 표준 사유 코드(예: 1-정책충돌, 2-불가능, 3-검토대기 등) 명확 기입**
- 이 후 각 {RPM}에 따라 {RP}.md를 업데이트

## 5. AGENT.md 반영 프로세스

- {MASTER} 신규 작성・수정 전 {MASTER} 파일에 모든 변경 내용을 한글로 초안 작성
- 완성된 {MASTER} 내용은 반드시 사용자에게 주요 변경사항과 반영 여부를 **한글로 명확히 확인** 받아야 함
- 승인 또는 추가 지시가 있을 때만 {MASTER}에 최종 반영
  - (추가) 사용자 승인은 `ai-agent/user-instructions` 폴더에 별도 `approval-log_{YYYYMMDD}.md` 생성해 기록 가능
- 승인/코멘트/반영시각 등 반드시 {MASTER}와 approval-log 모두에 남길 것

## 6. 우선순위 및 충돌관리 규정

- {MASTER}와 {RP}.md의 내용이 충돌 시 **반드시 {MASTER}가 우선**
- 충돌이 발생하면 `ai-agent/conflict-{YYYYMMDD}` 파일 생성, 사유와 타임스탬프 기록
- 각 conflict 파일에는 반드시  
  `YYYY년 MM월 DD일 HH시 MM분 SS초에 작성되었음`  
  및 충돌 사유 코드(예: 1-정책충돌 등) 기입
- 프로젝트 대원칙 위반 또는 논의 필요 이슈 발생 시 작업을 중지, 사용자 판단을 구함

## 7. 커맨드/승인 로그 시간기록 일관화

- 모든 명령, 승인, 업데이트 시간은 **UTC+KST 또는 서버 기준**
- 분, 초 단위까지 반드시 기록
- 시스템 시간이 여러 개 존재하면 실제 입력 시각도 별도 표기


## 8. 폴더/관리 구조 관련 권장

- `ai-agent/user-instructions/` 폴더: {커맨드}, 승인로그 모두 기록
- `ai-agent/conflict-{YYYYMMDD}` 파일: 모든 충돌 이력 및 코드, 사유 기록
- `ai-agent/sessions/` 폴더: 세션 로그 저장 (컨텍스트 10% 미만 시)
- 각 RP 폴더(`ai-agent/{RP}/`) 내 파일들은 해당 RP의 역할 정의 및 작업 지침 관리용

## 9. 예시 용례 및 승인/반려 사유 템플릿
[커맨드 예시]
```
내용: "리팩토링 후 테스트 케이스 추가"
로그:"2025년 10월 03일 15시 49분 10초에 {돌쇠}에게 지시하였음"
```
[승인/반려코드 예시]
```
1: 정책충돌
2: 실행불가(외부요인)
3: 추가검토 대기
```