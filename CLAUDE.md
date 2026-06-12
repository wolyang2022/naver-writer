# 프로젝트: 사진 → 블로그 자동화 (학습 프로젝트)

## 배경
- 사용자(월양)는 정보보안 전문가(CISO급, ISMS-P / SOAR 자동화 경험 다수)이며,
  "아빠와 딸의 성장여행" 블로그를 운영 중인 블로거.
- 이전 대화(Claude.ai)에서 "사진 업로드 → n8n → Claude Vision/Text API → Notion 초안 저장"
  자동화 파이프라인의 개념 설계는 이미 완료됨.
- 현재 목표: 이 자동화를 직접 이해하고 구축할 수 있도록 "처음부터" 학습 중.
  Claude Code는 학습 보조 + 실제 n8n 워크플로우 JSON / 연동 코드 작성을 담당.

## 사용자 환경 수준 (중요)
- 맥북 터미널 / git / GitHub 사용 경험이 전혀 없음 (완전 처음).
- 모든 git, GitHub 관련 명령어는 "이 명령이 뭘 하는 명령인지" 한 줄로 먼저 설명한 뒤
  실행할 것. 결과(특히 에러)는 바로 해석해서 알려줄 것.

## 학습 스타일 (항상 유지)
- 개념 설명 → 실습(따라 만들기) 순서로 진행.
- SOAR 자동화 비유(Playbook=워크플로우, Trigger/Action, Credential Vault 등)를
  활용하면 이해가 빠름.
- 작은 단계(Step)로 나눠 진행, 매 단계 후 확인받고 다음으로 넘어감.

## 기술 스택
- 트리거: Google Drive (사진 업로드 폴더). "폴더 완성 신호"로 done.txt 파일을
  올리는 방식 채택 예정 (사진 한 장씩이 아닌 폴더 단위 처리).
- 오케스트레이션: n8n. 대부분의 API 호출은 HTTP Request 노드 하나로 처리.
- AI: Claude API — 2단계 호출 패턴
  1) Vision: 사진들 → 장면/장소/아이 활동/계절 등 JSON으로 추출
  2) Text: 1번 결과 + 블로그 글쓰기 프롬프트(글쓰기 지침서 v2) → 블로그 초안 생성
- 저장: Notion DB (제목/본문/태그/SEO설명/발행여부)
- 버전관리: GitHub 개인 리포지토리 (자격증명/세션 파일은 .gitignore로 제외)

## 진행 현황 (학습 로드맵, 총 6차시)
1. [완료] 1차시 — API / Webhook / n8n 핵심 개념 (SOAR 비유로 매핑)
2. [완료] 2차시 — n8n 화면 구조: 캔버스/노드(Input-Parameters-Output)/Connection/
   Executions/Credentials, Trigger vs Action 구분
3. [진행 중] 3차시 — Manual Trigger + Edit Fields(Set) 노드로 첫 테스트 워크플로우
   - Step 1~4 완료 (워크플로우 생성, Manual Trigger 확인, Edit Fields 추가,
     fileName/location 필드 입력)
   - 남은 작업: 실행 → Executions 탭에서 JSON 출력 확인 (Claude.ai 채팅으로 복귀해
     이어서 진행 예정)
4. [대기] 4차시 — Google Drive Trigger로 교체
5. [대기] 5차시 — Claude API 2단계 호출 노드 구성
6. [대기] 6차시 — Notion API 연동, 전체 조립 + 에러 핸들링

## 지금 당장 할 작업 (Claude Code, 신규)
로컬 폴더 생성 + 이 CLAUDE.md 저장까지는 완료됨. 이제 GitHub 연동을 처음부터 진행:
1. git이란 무엇인지 / GitHub과의 관계 1~2줄 설명
2. `git init` — 이 폴더를 git 저장소로 초기화 (명령어 설명 후 실행)
3. `.gitignore` 작성 — n8n 자격증명, .env, API 키 등 절대 올리면 안 되는 파일 제외 패턴 포함
4. 첫 commit (CLAUDE.md, .gitignore)
5. GitHub에 새 저장소 생성 (private 권장) — 웹 UI로 만들지, gh CLI로 만들지 사용자에게 확인
6. 인증 방식 확인 (HTTPS+Personal Access Token vs SSH key) — 처음이라면 어떤 방식이
   더 쉬운지 설명 후 선택
7. remote 연결 + 첫 push
8. 완료 후, 다음에 다시 Claude.ai 채팅으로 돌아가 "3차시 남은 작업(Executions 확인)"
   부터 이어가면 된다고 안내
