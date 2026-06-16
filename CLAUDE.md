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
3. [완료] 3차시 — Manual Trigger + Edit Fields 워크플로우 실행 + Executions JSON 확인
4. [완료] 4차시 — Google Drive Trigger로 교체 (Blog 폴더 File Created 감시, 실제 테스트 완료)
5. [완료] 5차시 — Claude API HTTP Request 노드 구성 + 블로그 초안 생성 텍스트 호출 성공
   - 남은 작업: Claude Vision 노드 추가 (사진 실제 분석) → 6차시에서 진행
6. [대기] 6차시 — Claude Vision 추가 + Notion API 연동, 전체 조립 + 에러 핸들링

## 환경 정보 (재시작 시 참고)
- n8n 시작 명령어: `source ~/.nvm/nvm.sh && nvm use 20 && n8n start`
- n8n 접속 주소: http://localhost:5678
- GitHub 저장소: https://github.com/wolyang2022/naver-writer (private)
- Google Drive 감시 폴더: Blog
- Anthropic API 키: 애플 암호앱에 저장 (sk-ant-api03-...)

## Claude API HTTP Request 설정 템플릿
n8n HTTP Request 노드에 Claude API 연동 시 사용:

- Method: POST
- URL: https://api.anthropic.com/v1/messages
- Headers:
  - x-api-key: [Anthropic API 키]
  - content-type: application/json
  - anthropic-version: 2023-06-01
- Body (JSON):
  ```json
  {
    "model": "claude-sonnet-4-6",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "[프롬프트]"}]
  }
  ```

## GitHub 초기 연동 절차 (새 프로젝트 폴더 생길 때 참고)
1. `git init` — 폴더를 git 저장소로 초기화
2. `.gitignore` 작성 — API 키, .env, n8n 자격증명 등 제외
3. `git add [파일]` → `git commit -m "메시지"` — 첫 스냅샷
4. github.com에서 새 저장소 생성 (private, README/gitignore 체크 해제)
5. `git remote add origin https://github.com/wolyang2022/[저장소명].git`
6. `git branch -M main`
7. 아래 인증 방법으로 push

## GitHub 인증 방법 (HTTPS + PAT)
Claude Code 환경에서는 터미널 인터랙션이 안 돼서 아이디/비밀번호 입력창이 뜨지 않음.
토큰을 remote URL에 직접 포함하는 방식 사용:

```bash
git remote set-url origin https://wolyang2022:[토큰]@github.com/wolyang2022/[저장소명].git
git push -u origin main
```

- 토큰(PAT)은 애플 암호앱에 저장 (ghp_... 로 시작)
- .git/config 안에만 저장되며 GitHub에는 올라가지 않아 안전

## 워크플로우 GitHub 저장 절차
워크플로우 JSON에 API 키가 포함되므로 반드시 아래 순서 준수:
1. `n8n export:workflow --all --output=workflow.json`
2. workflow.json에서 API 키 → `YOUR_ANTHROPIC_API_KEY` 로 교체
3. git add → commit → push

## 기술 스택 결정사항
- 트리거: Google Drive File Created (Blog 폴더) — done.txt 방식 대신 파일 업로드 직접 감지로 변경
- 오케스트레이션: n8n 로컬 설치 (Node.js 20 + nvm)
- AI: Claude API (claude-sonnet-4-6) — HTTP Request 노드로 직접 호출
- 저장: Notion DB (6차시에서 연동 예정)
- 버전관리: GitHub private 저장소 (API 키 제외 후 push)
