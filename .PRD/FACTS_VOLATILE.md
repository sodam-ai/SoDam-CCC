# SoDam-CCC — 휘발성 사실 (낡을 수 있는 정보)

> **이 문서는 낡습니다. 그게 정상입니다.**
> 여기 있는 사실은 버전·명령어·경로처럼 **바뀔 수 있는 것들**입니다.
> 다른 PRD 문서에는 이런 값을 직접 쓰지 않고, **항상 이 문서를 참조**합니다.

**검증 기준일: 2026-08-09**
**검증 방법: 이 PC에서 직접 실행 + 공식 문서 원문 확인 + 플러그인 소스 직접 확인**

---

## 왜 이 문서를 분리했나

Channels는 공식 문서가 *"`--channels` 명령 형식과 프로토콜 규약이 바뀔 수 있다"* 고 **명시한 research preview** 기능입니다.

그리고 실제로 낡습니다:
- 리서치 자료 `01`은 작성된 지 며칠 만에 `02`·`03`·`04`에 의해 **5건이 정정**됐습니다.
- 공개 이슈 #37342: **2.1.126에서 먹통 → 2.1.114로 내리니 정상**.

버전 번호와 명령어를 PRD 본문에 박아두면, **한 달 뒤 PRD 전체의 신뢰가 무너집니다.**
그래서 물리적으로 분리합니다.

> 형제 프로젝트 `StockCheck`에서 **문서-현실 드리프트 6건**이 실제로 발생했습니다. 그 재발 방지책입니다.

---

## 1. 이 PC의 환경 (실측)

| 항목 | 값 | 확인 명령 | 검증일 |
|---|---|---|---|
| Claude Code 버전 | ⚠️ `2.1.251` (2026-08-17 실측 `2.1.233`에서 추가 드리프트 — research preview 특성상 회귀 가능성 있음, R2. 이번 버전에서 텔레그램 채널 로드 자체는 정상 확인됨) | `claude --version` | 2026-08-31 |
| Bun 버전 | `1.3.14` (변동 없음) | `bun --version` | 2026-08-17 재확인 |
| Telegram 플러그인 버전 | `0.0.6` (변동 없음) | 플러그인 캐시 폴더명 확인 | 2026-08-17 재확인 |
| 자동 업데이트 | **꺼짐** (`env.DISABLE_AUTOUPDATER = "1"`, `~/.claude/settings.json`의 `env` 객체 안에 중첩 — 정확한 JSON 경로 2026-08-20 재확인) | 설정 파일 직접 열람 | 2026-08-20 재확인 |
| 실행 중 claude 프로세스 | ⚠️ **40개** (2026-08-09 대비 **3.6배 증가**, 11→40) | `Get-Process claude` | 2026-08-17 — R3(자원 경합) 재평가 신호. **CCC 범위 밖**(Kill-Claude_Code-Zombies 소관), Phase 1은 이 수치를 늘리지 않는 것만 지킴(C3) |
| 기준 OS | Windows 11 | — | 2026-08-09 |

### 채널 설정 상태 (실측)
| 채널 | 상태 | 근거 |
|---|---|---|
| **Telegram** | 🔧 **2026-08-09 재실측 — "완료" 아님.** `.env`·`access.json`·`bot.pid` 존재, 채널 서버(`bun`, PID 13032) **가동 중**. 그러나 **`dmPolicy="allowlist"` 조건 미충족**(실제값 `"pairing"` → **CHK-08 🚫 차단**) · **`ackReaction` 미설정**(CHK-11 실패) · `approved/` 폴더는 있으나 **내용물 0개**(참고용, CHK-07은 `allowFrom.length=1` 로 통과). 즉 **12/12 아님** | `access.json` 원문 실측(`dmPolicy`·`allowFrom`·`groups`·`pending` 필드만 확인, 토큰은 미열람) |
| **Discord** | **8단계에서 정지** — `.env` 만 있고 `access.json` **없음**(`approved/` 하위 폴더 자체가 없음) (2026-07-09부터 방치) | 폴더 조회 |

> Discord의 이 상태가 **Phase 1 완료 판정 S1의 실전 테스트 케이스**입니다.
> ⚠️ **Telegram도 현재 보안 미완료 상태**입니다 — `dmPolicy="pairing"` 인 채로 방치되어 있어 **모르는 사람이 봇에 DM을 보내면 페어링 코드를 받을 수 있는 상태**입니다(01_PRD.md 위험 R14 참조). `status` 는 이걸 **1순위 경고**로 띄워야 합니다.
>
> 🔧 **2026-08-17 재확인**: `dmPolicy="pairing"` **그대로**(R14 위험 아직 미해소 — 사용자가 공식 명령으로 직접 잠가야 함, CCC가 대신 실행하지 않음/G-1b). `allowFrom` 길이 1, `groups` 없음, `ackReaction` 여전히 미설정. `bot.pid` 는 이번엔 **없음**(현재 `--channels` 세션이 열려 있지 않다는 뜻 — 정상. 세션을 열 때만 생기는 값이라 CHK-09 판정 자체가 원래 매번 달라짐). Discord `.env`만 존재·`access.json` 없음도 그대로.
>
> ✅ **2026-08-20 R14 해소 확인** — 사용자가 `access.json`을 직접 열어 `dmPolicy`를 `"pairing"`→`"allowlist"`로 수정·저장. AI(Edit 도구)로 먼저 시도했으나 이 PC의 파일 보호장치가 차단(G-1c 취지와 별개로 시스템 레벨에서도 한 번 더 막힘), 안내받은 대로 사람이 메모장으로 직접 수정해 해결. 저장 직후 재검사로 `dmPolicy="allowlist"` 실측 확인 — **CHK-08 통과, R14 위험 해소**. `allowFrom` 길이 1(변동 없음), `groups` 없음(변동 없음), `ackReaction` 여전히 미설정(CHK-11), `bot.pid` 여전히 없음(현재 `--channels` 세션 미가동, 정상). 진도 재계산: 8/12 → **9/12**(11단계 안전 잠금 추가 통과). 남은 미완료는 12단계(ackReaction, 선택사항)와 실제 폰 왕복 검증(S4)뿐.
>
> 🔴 **2026-08-31 재실측 — CHK-01~16 전체 라이브 실행 + 봇 삭제 확정**: `dmPolicy="allowlist"`(유지) · `allowFrom` 1명(유지) · `groups` 0개(유지) · **`ackReaction="👀"` 이번엔 설정 확인됨**(CHK-11 통과, 08-20 이후 언제 반영됐는지는 불명) → 진도 **9/12 → 11/12**. `bot.pid` 없음·`bun` 프로세스 0개(CHK-09 미통과, 세션 미가동이라 정상 범주였음). 검증을 위해 실제로 `claude --channels plugin:telegram@claude-plugins-official`을 새 창으로 띄웠으나 26초+ 대기 후에도 `bun` 자식 프로세스가 전혀 뜨지 않음(자식은 `node`·`cmd`뿐) — **원인을 사용자가 직접 알려줌: `test0000001`을 보안 사유로 방금 삭제했다.** 즉 CHK-09 실패의 진짜 원인은 "낡은 프로세스"(08-21 CHECKPOINT.md 기록)가 아니라 **토큰이 죽어 `bun` 서버가 인증 단계에서 기동을 못 한 것**. 새 창은 사용자 동의 받아 정확한 PID(66100, `--channels` 인자로 재확인 후)만 종료. `.env`는 죽은 토큰 그대로 남아있음(무해, 새 토큰 등록 시 자동 갱신). **CHK-06("존재 여부만 확인")은 토큰 유효성을 판별 못 하므로 이런 "진도는 높은데 실제로 안 되는" 상황이 재발할 수 있음 — 설계 트레이드오프로 인지, 코드 변경 없음.**

---

## 2. 명령어 (공식 문서 기준)

> ⚠️ 공식 문서가 "형식이 바뀔 수 있다"고 명시한 항목입니다.

| 용도 | 명령 | 출처 |
|---|---|---|
| 채널 모드로 실행 | `claude --channels plugin:telegram@claude-plugins-official` | 공식 channels |
| 여러 채널 동시 | `--channels` 뒤에 **공백으로 구분해 나열** | 공식 channels |
| 마켓플레이스 추가 | `/plugin marketplace add anthropics/claude-plugins-official` | 공식 channels |
| **카탈로그 수동 갱신** | `/plugin marketplace update claude-plugins-official` | 공식 channels — **자동갱신을 껐을 때 필요. 이 PC 해당** |
| 플러그인 설치 | `/plugin install telegram@claude-plugins-official` | 공식 |
| 플러그인 활성화 | `/reload-plugins` — **조건부**. 설치 요약에 안내가 뜰 때만 | 공식 |
| 토큰 등록 | `/telegram:configure <토큰>` | 공식 |
| 페어링 | `/telegram:access pair <코드>` | 공식 |
| **안전 잠금** | `/telegram:access policy allowlist` | 공식 |
| 받음 표시 | `/telegram:access set ackReaction 👀` | 플러그인 `ACCESS.md:85` |
| 상태 확인 | `/telegram:access` | 공식 |
| MCP 상태 진단 | `/mcp` | 공식 channels-reference |
| 개발용 채널 로드 | `--dangerously-load-development-channels` | 공식 (CCC는 사용 안 함) |

> **주의**: `--channels` 와 `--dangerously-load-development-channels` 는 **`claude --help` 에 표시되지 않습니다.** preview 기간의 정상 동작입니다 (공식 명시).

---

## 3. 파일 경로

> 🔧 **2026-08-09 실측 확정 — 설정 루트가 두 곳으로 갈립니다.** §7-B 가 "구현 시 두 경로 모두 확인"이라고 경고했던 것이 **사실로 확인**됐습니다. 하나가 아니라 **역할별로 고정된 두 루트**입니다.

```
채널 루트   = $env:USERPROFILE\.claude\channels\        ← 봇 설정·토큰·페어링 (예외적으로 여기)
설정 루트   = $env:CLAUDE_CONFIG_DIR                     ← 플러그인 캐시·설치기록·전역 settings.json
             (이 PC 실측값 = C:\Users\<사용자>\AppData\Roaming\claude-code)
             CLAUDE_CONFIG_DIR 미설정 환경이면 $env:USERPROFILE\.claude 로 대체
```

| 대상 | 경로 | CCC 취급 |
|---|---|---|
| 채널 상태 폴더 | `<채널 루트>\telegram\` | 읽기 전용 |
| 토큰 | `<채널 루트>\telegram\.env` | **존재 여부만** |
| 허용목록·설정 | `<채널 루트>\telegram\access.json` | 읽기 전용 |
| 페어링 완료 마커(참고용) | `<채널 루트>\telegram\approved\` | 읽기 전용. **개수만 참고**(완료 판정 근거 아님 — CHK-07 정정 참조) |
| 봇 서버 PID | `<채널 루트>\telegram\bot.pid` | 읽기 전용. CHK-09 판정에 사용 |
| 받은 사진 | `<채널 루트>\telegram\inbox\` | 읽기 전용. **개수만** |
| 디버그 로그 | `<설정 루트>\debug\<세션ID>.txt` | 읽기 전용 |
| 플러그인 캐시(소스) | `<설정 루트>\plugins\cache\claude-plugins-official\telegram\<버전>\` | 읽기 전용 |
| 플러그인 마켓플레이스 카탈로그 | `<설정 루트>\plugins\marketplaces\claude-plugins-official\` | 읽기 전용. CHK-03 판정 |
| 설치 기록(스코프 판정) | `<설정 루트>\plugins\installed_plugins.json` | 읽기 전용. CHK-05 판정 — `.plugins["telegram@claude-plugins-official"][0].scope` |
| 전역 설정 | `<설정 루트>\settings.json` **및** `$env:USERPROFILE\.claude\settings.json` **둘 다** | 읽기 전용. `DISABLE_AUTOUPDATER` 는 이 PC 기준 `~/.claude/settings.json` 에 있었음(CHK-04) |

### access.json 실제 스키마 (2026-08-09 실측 — `server.ts:102~117` 필드명만 인용, 코드 비복제)
```
{
  dmPolicy: 'pairing' | 'allowlist' | 'disabled',   ← CHK-08 (원안의 "policy" 아님)
  allowFrom: string[],                                ← CHK-07·CHK-13 (사용자 ID 배열, 개수만 사용)
  groups: { [groupId]: { requireMention, allowFrom } }, ← CHK-15 (객체 — 키 개수로 판정)
  pending: { [code]: {...} },                         ← 페어링 대기 중 코드 (CCC 미사용)
  ackReaction?: string,                               ← CHK-11
  mentionPatterns?, replyToMode?, textChunkLimit?, chunkMode?  ← Phase 1 미사용(F6 이후 검토)
}
```

---

## 4. 플러그인 능력 (소스 직접 확인 — 최상위 근거)

`telegram/0.0.6/server.ts` 에서 직접 확인한 사실:

| 사실 | 근거 (줄 번호) | 의미 |
|---|---|---|
| **폰 승인 지원** | `server.ts:394` (권한 릴레이 capability 선언 확인) | 권한 승인을 폰으로 받을 수 있음 |
| 승인 요청 발송 | `server.ts:414` (허용목록 전원에게 승인요청을 보내는 로직 확인) | **허용목록 전원**에게 감 → 보안 규칙의 근거 |
| **텍스트 승인** | `server.ts:84` (정규식으로 승인 문구 형식을 확인) | 폰에서 `yes abcde` 형식으로 승인 |
| **버튼 승인** | `server.ts:733` (정규식으로 버튼 콜백 형식을 확인) | 허용 / 거절 / **전문 보기** 버튼 |
| 설정 키 | `ACCESS.md:107` — `ackReaction`, `replyToMode`, `textChunkLimit`, `chunkMode`, `mentionPatterns` | 답변 잘림 등 조정 |
| 그룹 허용 | `ACCESS.md:49` `/telegram:access group add <ID>` (`--no-mention`, `--allow` 플래그) | **CCC 범위 밖** |

> **🔧 2026-08-20 정정 — B-4(코드 복사 금지) 준수**: 위 표는 원래 서버 소스의 정규식·객체 리터럴을 그대로 옮겨 적고 있었습니다. `01_PRD.md §7.4 L-B B-4`(Must)와 `04_PROJECT_SPEC.md`가 "인용은 경로·줄 번호로만" 하라고 못박고 있어, 코드 원문을 지우고 **줄번호 + 말로 푼 설명**으로 바꿨습니다. 사실 관계(무슨 기능이 어디 있는지)는 그대로 유지됩니다 — 코드 문자열만 뺐습니다. "설정 키"·"그룹 허용" 두 줄은 각각 설정 항목 이름과 사용자가 직접 입력하는 명령 구문이라 이 규칙 대상이 아니라 그대로 뒀습니다.

> ⭐ **이 표가 `RESEARCH_SOURCES-04 §12` 를 반증합니다.** 04는 *"폰 원격 승인은 안 되는 걸로 보는 게 안전"* 이라고 썼지만, 코드에는 구현돼 있습니다. **문서에 없다고 기능이 없는 게 아닙니다.**

---

## 4-B. 라이선스 상태 (실측 — 2026-08-09)

> ⚠️ 법률 자문이 아닙니다. **확인된 사실만** 적습니다. 해석이 필요한 부분은 [01_PRD.md §7.6](./01_PRD.md#76-확인됨-vs-검토-필요--반드시-구분).

| 대상 | 라이선스 | 확인 방법 |
|---|---|---|
| **SoDam-CCC** | **미지정** — `LICENSE` 파일 **없음** | 프로젝트 폴더 조회 |
| Kill-Claude_Code-Zombies | **Apache License 2.0** / `Copyright 2026 SoDam AI Studio` | `LICENSE` 파일 |
| SoDam-Design-Kit | **Apache License 2.0** / `Copyright 2026 SoDam AI Studio` | `LICENSE` 파일 |
| SoDam-Reverse-Eng | **Apache License 2.0** / `Copyright 2026 SoDam AI Studio` | `LICENSE` 파일 |
| 공식 Telegram 플러그인 | **Apache License 2.0** | 플러그인 폴더의 `LICENSE` |
| **CCC 외부 의존성** | **0개** | 설계 결정 (§6.3 F-1) |

### ⚠️ 확인된 불일치
**NOTICE 파일**: 형제 3곳 중 **Reverse-Eng 1곳만 보유**. Zombies·Design-Kit는 없음.
→ 가족 관례가 통일돼 있지 않습니다. 기준 결정 필요 (**C8**).

### 재확인 시점
라이선스는 자주 바뀌지 않지만, **공개·배포·납품 직전(G1~G6)** 에는 반드시 다시 확인합니다.

---

## 5. 공식 제약 (변경 가능성 있음)

| 제약 | 내용 | 출처 |
|---|---|---|
| Research Preview | 명령 형식·프로토콜 규약 변경 가능 | 공식 channels |
| 인증 | claude.ai 계정 또는 Console API 키 필요 | 공식 |
| 미지원 환경 | Amazon Bedrock · Google Cloud Agent Platform · Microsoft Foundry | 공식 |
| 조직 정책 | Team/Enterprise는 관리자가 `channelsEnabled` 켜야 함. **Pro/Max 개인은 해당 없음** | 공식 |
| 런타임 | 공식 채널 플러그인은 **Bun 필수**. 직접 만드는 채널은 Node/Bun/Deno 아무거나 | 공식 |
| 세션 의존 | **세션이 떠 있을 때만** 메시지가 도착. 상시가동은 백그라운드/영구 터미널 | 공식 |
| 권한 대기 | 승인창이 뜨면 **세션이 멈춘 채 대기** | 공식 |
| Telegram 한계 | 과거 메시지 조회·검색 **불가** (`fetch_messages` 없음). 파일 최대 **50MB** | 공식 README |
| 슬래시 명령 | `/clear`·`/compact` 등은 채널로 실행되지 않음 (기능 요청 #37342) | 공개 이슈 |
| 커스텀 채널 | 자체 마켓플레이스로 배포해도 **공식 allowlist 진입 불가** | 공식 channels-reference |
| **Remote Control 은 별도 공식 기능** | `claude --help` 에 **`remote-control` 명령이 실제로 표시됨**(이 PC 실측, `--channels`는 preview라 미표시). 봇·Bun·토큰·페어링 없이 앱에서 기존 세션을 직접 조작. **CCC 범위 밖**(목적이 다름 — 01_PRD.md §8 참조) | 실측 + 자료 01·02·03·04 전부 언급 |
| **fakechat = 공식 개발용 시험 채널** | 이 PC 마켓플레이스 카탈로그에 telegram·discord·imessage 와 나란히 **실재 확인**(`localhost:8787`, 외부 계정 불필요) | 실측(카탈로그 조회) + 자료 03 §6 |

---

## 6. 알려진 버그 (추적 대상)

| 이슈 | 내용 | 확인일 |
|---|---|---|
| `claude-code#37342` | 2.1.126 텔레그램 수신 먹통 → 2.1.114 정상 / 채널에서 슬래시 명령 실행 요청 | 자료 인용 |
| `claude-plugins-official#1680` | Telegram MCP가 종료 후 자동 재연결 안 됨 | 자료 인용 |
| `claude-plugins-official#1448` | Discord `DISCORD_STATE_DIR` 일부 스킬이 미반영 | 자료 인용 |
| `claude-plugins-official#1292` | 승인된 플러그인이 `external_plugins`에 안 나타남 | 자료 인용 |

> 이 4건은 **리서치 자료의 인용**이며, 제가 이슈 페이지를 직접 열어 확인하지는 않았습니다. → §7 미확인 목록

---

## 7. 미확인 사항 (추측 금지 — 확인 전까지 "모른다"로 둔다)

> **규칙**: 여기 있는 항목은 "될 것이다 / 안 될 것이다"로 쓰지 않습니다. 확인한 뒤에 위 표로 옮깁니다.

### ✅ 2026-08-09 실측으로 해결됨 (이 PC에서 직접 확인)

| # | 답 |
|---|---|
| U2 | 설치 스코프 = `<설정 루트>\plugins\installed_plugins.json` → `.plugins["telegram@claude-plugins-official"][0].scope` = `"user"` |
| U3 | `Get-CimInstance Win32_Process -Filter "Name='claude.exe'"` 로 `CommandLine` 읽힘(188개 프로세스 전수 실측, **관리자 권한 불필요**). 단 CHK-09 자체는 이 방법을 안 씀(→ CHK-09 정정 참조, `claude` 프로세스엔 `--channels` 인자가 안 남음) |
| U7 | `CLAUDE_CONFIG_DIR` 환경변수가 설정 루트를 결정(이 PC=`AppData\Roaming\claude-code`). **단 채널 폴더는 예외적으로 항상 `~/.claude/channels/`** — §3 경로표 참조 |
| U8 | `~/.claude/skills/aurakit/hooks/security-scan.js` + `lib/common.js` 의 `SECRET_PATTERNS` 재사용 가능(정규식 패턴 목록 방식) |
| U10 | `access.json` 스키마 = `dmPolicy`(문자열)·`allowFrom`(배열)·`groups`(객체)·`pending`(객체) — §3 스키마 블록 참조 |
| U11 | `Get-CimInstance Win32_Process` 의 `CommandLine` 에서 `-like "*--dangerously*"` 로 검색(이 PC 실측 0건) |
| U9 | 🔧 **2026-08-31 코드 대조로 확인** — 실측이 아니라 `skills/tone/SKILL.md`를 직접 읽어 확인: 마스킹 대상은 **봇 토큰 형식**(숫자 1개 이상 + `:` + 영문·숫자·`-`·`_` 35자)이 이미 구현돼 있음. `FACTS_VOLATILE.md`가 "미확인"으로 남아있었을 뿐, 실제 스킬 코드에는 이미 반영돼 있던 것 — 문서가 코드보다 뒤처져 있던 사례 |

### 미확인 유지

| # | 미확인 내용 | 확인 방법 | 필요 시점 |
|---|---|---|---|
| U1 | 위 §6 이슈 4건의 **현재 상태** (열림/닫힘) | 이슈 페이지 직접 확인 | Phase 3 |
| U4 | BotFather에서 **기존 토큰을 다시 확인**하는 정확한 경로 | 폰에서 직접 확인 | Phase 1 (재설치 시나리오) |
| U5 | **세션별로 MCP 도구를 최소화**해 띄울 수 있는지 | 실측 | Phase 2 (C3 대응) |
| U6 | 공식 `scheduled-tasks` 로 회귀 감시를 **대체 가능한지** | 공식 문서 확인 | Phase 3 |
| U12 | ⚖️ **프로젝트 라이선스** — 무엇으로 할지 | **사용자 결정** (C7). 임의 확정 금지 | 공개·배포 직전 |
| U13 | ⚖️ **NOTICE 파일 필요 여부·가족 기준** | 형제 관례 불일치(3곳 중 1곳) → 통일 결정 (C8) | 공개·배포 직전 |
| U14 | ⚖️ **AI 생성물의 권리 관계** | 도구 약관·관할에 따라 해석 상이 — **단정 불가** | 공개·납품 직전 (법무) |
| U15 | ⚖️ **상표 지시적 사용 허용 범위** ("Claude Code 용 도구" 표현) | 법무 검토 | 공개·납품 직전 |
| U16 | 공식 플러그인이 포함한 **제3자 패키지 라이선스** | CCC가 번들 안 하므로 현재 미발동 | 번들하게 되면 |

---

## 7-B. 📖 제품 `README.md` 와의 관계 — 중요

> **제품 README에는 버전 번호·명령어를 직접 쓰지 않습니다.** 이 문서를 참조하게 만듭니다.

| README 섹션 | 처리 |
|---|---|
| 4 필요한 프로그램 · 5 설치 방법 · 7 실행 방법 · 10 명령어 | **이 문서(§1·§2)를 참조** — "최신 값은 `.PRD/FACTS_VOLATILE.md` 확인" 한 줄 |
| 12 파일·문서 위치 | **이 문서(§3)를 참조** |
| 16 문제 대처 | 이 문서 **§6 알려진 버그** + `02_DATA_MODEL.md` 증상 사전 |

**이유**: 이렇게 하면 Claude Code가 업데이트돼도 **이 문서 한 곳만 고치면** README가 자동으로 맞아집니다.
반대로 README 본문에 `2.1.226` 같은 값을 박으면, 버전이 오를 때마다 README 전체를 다시 훑어야 하고 **결국 낡은 채 방치**됩니다.

---

## 8. 재확인 절차

> **언제**: 새 PC에서 시작할 때 / Claude Code를 업데이트한 뒤 / 갑자기 안 될 때 / 6개월마다

```
1. claude --version        → §1 표의 버전과 다르면 §6 이슈부터 확인
2. bun --version           → 없으면 Bun 재설치
3. 플러그인 캐시 폴더의 버전 확인 → 0.0.6과 다르면 §4 소스 재확인
4. 공식 문서 재확인:
     code.claude.com/docs/en/channels
     code.claude.com/docs/en/channels-reference
5. 바뀐 내용이 있으면 → 이 문서만 갱신 (다른 PRD 문서는 건드리지 않음)
```

### 갱신 기록
| 날짜 | 내용 | 검증 방식 |
|---|---|---|
| 2026-08-09 | 최초 작성 | 이 PC 실측 + 공식 문서 원문 + 플러그인 소스 직접 확인 |
| 2026-08-09 | Phase 1 착수 전 PowerShell 전수 실측 — CHK-07·08·09·11·12·13·15 정정, 경로 2원화 확정, access.json 스키마 확보, U2·U3·U7·U8·U10·U11 해결, Telegram "완료" 판정 철회(현재 `dmPolicy=pairing` 미잠금 상태 발견), Remote Control·fakechat 실재 확인 | `Get-CimInstance Win32_Process`·`Get-ChildItem`·`server.ts` 소스 직접 대조. **access.json 내용 열람은 정책/필드명 확인 목적**(토큰 아님, 비밀 미노출) |
| 2026-08-17 | Phase 1 실구현 착수 전 재확인 — Claude Code `2.1.226→2.1.233` 드리프트 발견(회귀 위험 재평가 대상), claude 프로세스 `11→40` 급증 발견(R3 신호, CCC 범위 밖), `DISABLE_AUTOUPDATER` 위치 재확정(`~/.claude/settings.json`), Telegram `dmPolicy=pairing` 미해소 재확인(R14 그대로), Bun·플러그인 버전 변동 없음 확인. 프로젝트 git 저장소 신규 초기화(이전엔 git 미적용 상태였음 — PRD에 없던 빈틈, C-5 비밀스캔 전제조건 충족 목적) | PowerShell 직접 실측(`claude --version`·`bun --version`·`Get-Process`·`access.json` 필드 확인) |
| 2026-08-19 | 진도 계산 오차 발견·수정 — CHK-10(폰 승인 지원 여부)이 사용자 진행 상태와 무관한 정적 사실인데도 12단계 진도 계산에 포함되어 있어, 이 PC의 위 2026-08-17 실측값으로 재현한 결과 실제 완료 8단계가 "9/12"로 부풀려 표시됨을 확인. `02_DATA_MODEL.md`·`data/checks.md` STEP 표를 "12단계=CHK-11만으로 판정"으로 수정, `status/SKILL.md` 계산 규칙도 동기화. README §5·§7에 마켓플레이스 수동갱신 안내(N2)·"정상 표시"·FAQ 연결 보강 | 문서 대조 + 실측값 수기 재계산으로 수정 전/후 검증(코드 실행 아님) |
| 2026-08-20 | **첫 라이브 실행 테스트** — `/sodam-ccc:install`을 이 PC에서 실제로 돌려 CHK-01~16 전부를 실측(읽기 전용, `.env` 미개봉). 결과: 9/12단계, `dmPolicy="pairing"`(R14 그대로 미해소) 제외 전부 통과. 이 과정에서 CHK-04 확인 스크립트가 `DISABLE_AUTOUPDATER`를 최상위에서만 찾다가 실패 → 실제로는 `env` 객체 안에 중첩되어 있음을 발견(자동갱신은 여전히 꺼짐 상태로, 실제 값 변화는 없었음 — 제 확인 방식의 오류였음). `data/checks.md` CHK-04에 정확한 경로 추가 | PowerShell로 `access.json`·`settings.json`·플러그인 캐시·프로세스 상태 직접 실측(문서 대조 아닌 최초의 실제 실행) |
| 2026-08-20 | **`status`·`fix` 라이브 실행** — 새 세션에서 두 스킬을 실제로 돌려 S1(Discord 방치 상태 정확 판정)·S2(15검사+N/12)·F3(실패 우선순위 정렬) 전부 실증 확인. 이어서 **git 첫 커밋**(24개 파일, 비밀 스캔 통과 확인 후) 생성 — 그 전까지 이 저장소는 `git init`만 되고 커밋 0건 상태였음. **R14 안전 잠금 해소** — AI(Edit 도구)의 직접 수정 시도는 이 PC의 파일 보호장치에 차단됐고(공식 명령이 막혔던 것과 동일 계열의 보호), 사용자가 메모장으로 직접 `dmPolicy`를 `allowlist`로 수정·저장 → 재검사로 확인, 진도 **9/12**로 상승. 남은 것은 12단계(ackReaction, 선택)와 S4(폰 승인 실사용)뿐 | 스킬 실행 결과 직접 관찰 + `git log`/`git status` + `access.json` 수정 전후 재실측 |
| 2026-08-31 | **CHK-01~16 전체 라이브 재실행 + M9 전제 재정의** — `ackReaction` 이미 설정 확인(진도 9/12→11/12), Claude Code `2.1.233→2.1.251` 추가 드리프트 확인. `--channels`를 새 창으로 실제 실행해 자식 프로세스까지 추적(`bun` 없음, `node`·`cmd`만) → 원인 조사 중 **사용자가 `test0000001`을 보안 사유로 삭제했다고 알려줌** → 08-21 CHECKPOINT.md의 "낡은 프로세스" 진단이 이 시점 기준으로는 틀렸음을 확정(진짜 원인=죽은 토큰). 정확한 PID(`--channels` 인자로 재확인 후)만 사용자 동의 받아 종료. U9(마스킹 패턴)는 실측이 아니라 `skills/tone/SKILL.md` 코드 대조로 이미 구현돼 있음을 확인해 해결 처리. `CHECKPOINT.md` M9 섹션도 동시 갱신 | `data/checks.md` 방법 그대로 CHK 15개 라이브 실행 + `Get-CimInstance Win32_Process`로 부모/자식 프로세스 추적 + 스킬 파일 코드 직접 대조(추측 아님) |
