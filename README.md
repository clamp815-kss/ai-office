# 현우 AI 오피스 · 3D & 2D 도트 멀티 에이전트

가짜 봇 없이, 내가 직접 등록한 AI 에이전트만 움직이는 개인용 AI 오피스입니다.
정적 페이지 하나(`index.html`)로 동작하며 GitHub Pages에서 바로 열립니다.

## 에이전트 직접 추가

우측 상단 **＋ 에이전트 추가**에서 만듭니다.

- **AI 종류 프리셋**: 클로드(Claude) · GPT(OpenAI) · 제미나이(Gemini) · 피클(Pickle) · 부이(Vooy) · 인스팅스(Instings) · 직접 입력
- **정할 수 있는 것**: 이름, 역할(기획·개발·디자인·리서치·마케팅·총괄·커스텀), 역할 설명, 입장 방, 컬러
- **연결 방식**
  - `웹훅 URL` — 내 서버·n8n·Make 등으로 `POST {agent, provider, role, room, message}`. 응답 JSON의 `reply`/`text`/`message`를 회의실에 표시합니다.
  - `API 키` — 클로드·GPT·제미나이 API를 브라우저에서 직접 호출합니다. 키는 이 브라우저(localStorage)에만 저장됩니다.
  - `로컬 시뮬레이션` — 테스트용. 응답은 `[시뮬레이션]` 표시가 붙은 예시 문구로만 옵니다.
  - `수동 응답` — 자동 응답 없음. 대기 후 회의실에서 직접 답을 넣거나 터미널에 `reply 이름 내용`.
- **응답 시간**: 30초 / 1분 / 5분 / 10분 / 20분 / 30분 / 60분

에이전트는 `localStorage`(`ghoo.v2`)에 저장되어 새로고침해도 유지되고, 카드의 ✎ 편집 · 🗑 삭제로 언제든 바꿀 수 있습니다.

## 응답 시간이 쓰이는 곳

- 지시를 보내면 에이전트별로 **응답 한도**가 걸리고, 3D·회의실의 *응답 대기* 패널과 에이전트 카드에 `4:59` 같은 카운트다운이 표시됩니다.
- 한도를 넘기면 **지연**으로 표시되고, 웹훅/API 실패는 터미널과 회의실에 실제 오류로 남습니다. 만들어낸 답장은 없습니다.

## 호출 대상 지정 (특정 에이전트 · @전체)

회의실 입력창 위 **호출 대상** 바에서 고릅니다.

- `@전체` — 켜져 있는 모든 에이전트가 각자 역할대로 응답
- `@이름` 칩 — 클릭한 에이전트만 응답 (여러 명 동시 선택 가능, 다시 누르면 해제)
- 아무것도 고르지 않으면 예전처럼 지시 내용에 맞춰 자동 배분
- 입력창에 직접 `@이름` / `@전체`를 타이핑해도 되고, `@`를 치면 자동완성 목록이 뜹니다(`@이름`만 치고 보내면 그 에이전트 선택)

## 총괄 에이전트 · 하위 지시(오케스트레이션)

1. **에이전트 추가/편집** 모달의 `👑 총괄 에이전트로 지정` 토글, 또는 에이전트 목록·카드의 `👑` 버튼으로 총괄을 정합니다(한 명).
2. 총괄 칩을 고르거나 `@총괄이름 지시...`로 보내면, 총괄이 먼저 계획을 세우고 `@이름 → 하위 지시` 형식으로 팀원에게 일을 나눕니다.
3. 지시받은 팀원은 각자 **연결 방식**(웹훅/API/로컬 시뮬레이션/수동)과 **응답 시간**대로 이어서 보고합니다. `총괄 → 하위` **1단계**까지만 전파해 무한 루프를 막습니다.
4. 웹훅·API로 실제 AI에 연결된 경우, 총괄에게 보내는 프롬프트에 팀원 목록(이름·역할)과 `@이름 → 내용` 형식 지시가 함께 들어가고, 돌아온 응답의 `@이름` 멘션을 파싱해 자동으로 하위 호출로 연결합니다. 웹훅 본문에는 `orchestration: true`, `instruction`, `teammates`도 함께 실립니다.
5. 3D·2D 지도에서는 지시를 받아 작업 중인 에이전트의 발밑에 진행 링이 돌고, 이름표에 `⚡남은시간`이 붙습니다. 총괄은 `👑`로 표시되고 지휘실로 이동합니다.

## 화면

- **3D 오피스** — 드래그 회전 · 휠 줌 · 에이전트 클릭으로 편집
- **2D 도트** — 48px/m 고해상도 도트(768×528). 나무 바닥, 슬레이트 타일 회의실, 유리 테이블, 듀얼 모니터·키보드·머그, 책장, LED가 깜빡이는 서버랙, 화분, 바 카운터, 브랜드별 의상 캐릭터
- **회의실** — 방별 대화, ON/OFF, 응답 대기 스트립, 호출 대상 바
- **터미널** — `help` · `status` · `agents` · `add` · `invite` · `lead` · `call` · `orchestrate` · `reply` · `run` · `link` · `hook`

## 초대 URL · 웹훅

- `?invite=1&name=이름&role=역할&room=work&provider=claude&interval=5` — 주소를 열면 같은 설정으로 입장
- `?hook=1&agent=이름&room=meeting&msg=내용` — 외부 자동화가 메시지를 밀어넣는 엔드포인트

## GET 한 번으로 팀 일괄 초대 (피클 · 부이 · 인스팅스)

아래 주소를 주소창에 붙여 열면 세 에이전트가 **한 번에 대회의실(대화방)** 로 들어오고, 연결이 `웹훅(GET)` + `https://ai-office-webhook.clamp815.workers.dev/` 로 맞춰집니다. 피클이 👑 총괄이 됩니다.

```
https://clamp815-kss.github.io/ai-office/?invite=pickle,vooy,instings&room=meeting&conn=webhook&endpoint=https://ai-office-webhook.clamp815.workers.dev/&method=get&lead=pickle
```

지원 파라미터

| 파라미터 | 값 | 설명 |
|---|---|---|
| `invite` | `pickle,vooy,instings` 또는 `1` | 쉼표 목록 그대로 일괄 초대. `1` 이면 `agents` 목록을 사용 |
| `agents` | `pickle,vooy,instings` | `invite` 없이도 동작 |
| `room` | `meeting` · `command` · `lounge` · `work` | 여러 명이면 기본 `meeting` |
| `conn` | `webhook` · `api` · `sim` · `manual` | `endpoint` 가 있거나 일괄 초대면 기본 `webhook` |
| `endpoint` | 웹훅 URL | 없으면 `ai-office-webhook` 주소를 기본값으로 연결 |
| `method` | `get` · `post` | `get` 이면 `?agent=…&provider=…&role=…&room=…&message=…` 로 호출 |
| `lead` | `pickle` 등 | 총괄로 지정 |
| `interval` | `0.5`~`60` (분) | 일괄 초대 기본 0.5(30초) |
| `view` | `meet` · `v3d` · `v2d` · `term` | 다중 초대 + `room=meeting` 이면 자동 `meet` |

별칭: `pickle`/`피클`/`uriel`/`우리엘` · `vooy`/`부이` · `instings`/`인스팅스` · `claude`/`클로드` · `gpt`/`지피티` · `gemini`/`제미나이`. 같은 이름·제공자의 에이전트가 이미 있으면 새로 만들지 않고 그 방으로 옮기며 연결만 갱신합니다. 처리 후 쿼리스트링은 `history.replaceState` 로 정리되어 F5 로 새로고침해도 중복 실행되지 않습니다.

터미널에서도 `invite pickle,vooy,instings` 로 같은 일괄 초대가 됩니다. 초대 주소 모달(`🔗 초대 주소`)에는 **👥 피클 · 부이 · 인스팅스 대화방 일괄 초대** 카드가 있습니다.

### 웹훅 GET 호출

```
https://ai-office-webhook.clamp815.workers.dev/?agent=피클&provider=pickle&role=총괄&room=meeting&message=할말
```

브라우저 주소창에서 열면 답이 바로 보이고(`text/html`), 앱·스크립트에서 `Accept: application/json` 으로 부르면 `{"reply": "…", "model": "…"}` 를 돌려줍니다. POST JSON 방식도 그대로 동작합니다.
