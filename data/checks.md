# CHECK 15개 — 정본

> 이 문서가 **CHK-01~CHK-16(14는 결번)의 유일한 정본**입니다.
> `skills/*/SKILL.md`는 이 문서를 참조만 하고, 판정 방법을 스킬마다 다시 적지 않습니다.
> (정본 중복 방지 — `.PRD/04_PROJECT_SPEC.md`의 "정본 순위" 원칙을 프로젝트 내부 데이터에도 그대로 적용)
>
> 근거 문서: [.PRD/02_DATA_MODEL.md](../.PRD/02_DATA_MODEL.md) · 경로/스키마: [.PRD/FACTS_VOLATILE.md](../.PRD/FACTS_VOLATILE.md) §2·§3
> 마지막 검증: 2026-08-17 — 아래 방법을 이 PC에서 **직접 실행**해 확인(추측 없음)

## 절대 원칙

- 모든 검사는 **읽기 전용**. 판정 결과 외 화면·기록 출력 금지.
- `.env`는 **존재 여부만**. 내용은 절대 열지 않는다.
- 🚫(차단) 등급은 **CHK-08 하나뿐**. 나머지는 ⚠️경고 또는 ℹ️안내.
- CHK-13의 `allowFrom`, CHK-07의 사용자 ID는 **값을 절대 출력하지 않는다** — 개수만.

## 경로 기준점 (고정, 조합 금지 — `..` 포함 경로 거부)

```
채널 루트 = $env:USERPROFILE\.claude\channels\
설정 루트 = $env:CLAUDE_CONFIG_DIR   (미설정 시 $env:USERPROFILE\.claude 로 대체)
```
두 루트는 역할이 다르다. 채널 설정(토큰·허용목록)은 항상 **채널 루트**, 플러그인 캐시·설치기록·전역 설정은 **설정 루트**. 섞어 쓰지 않는다.

## 일반 검사 12개

| ID | 검사 | 방법 (읽기 전용) | 통과조건 | 등급 |
|---|---|---|---|---|
| CHK-01 | Claude Code 설치·버전 | `claude --version` | 버전 문자열 출력됨 | ℹ️ |
| CHK-02 | Bun 설치 | `bun --version` | 버전 문자열 출력됨 | ℹ️ |
| CHK-03 | 마켓플레이스 등록 | `<설정 루트>\plugins\marketplaces\claude-plugins-official\` 존재 확인 | 폴더 있음 | ℹ️ |
| CHK-04 | 마켓플레이스 카탈로그 신선도 | `<설정 루트>\settings.json` **및** `$env:USERPROFILE\.claude\settings.json` 둘 다에서 `env.DISABLE_AUTOUPDATER` 확인 — **최상위 키가 아니라 `env` 객체 안에 중첩**되어 있음(2026-08-20 라이브 검사로 실측 확정, 이 PC는 후자 파일에 있음) | 자동갱신 켜짐, 꺼짐이면 수동갱신 안내(`/plugin marketplace update claude-plugins-official`) 문구 표시 | ℹ️ |
| CHK-05 | telegram 플러그인 설치+스코프 | `<설정 루트>\plugins\installed_plugins.json` → `.plugins["telegram@claude-plugins-official"][0].scope` | 폴더 있음 **AND** scope = `"user"` | ℹ️ |
| CHK-06 | 토큰 설정됨 | `<채널 루트>\telegram\.env` **존재 여부만** | 파일 존재 | ℹ️ |
| CHK-07 | 페어링 완료 | `access.json` → `allowFrom` 배열 **길이** | 길이 ≥ 1 (`approved/` 폴더는 참고 표시로만, 완료판정 근거 아님) | ℹ️ |
| **CHK-08 🚫** | **접근 정책 = allowlist (유일한 차단 검사)** | `access.json` → `dmPolicy` | 값이 정확히 `"allowlist"`. 아니면 `install`이 "완료"를 선언하지 않는다 | **🚫** |
| CHK-09 | 채널 서버 가동 중 | `<채널 루트>\telegram\bot.pid`의 PID를 `Get-Process -Id <pid> -ErrorAction SilentlyContinue`로 조회 | 프로세스 생존 **AND** 이름이 `bun` — ⚠️ `claude` 프로세스 인자에서 `--channels` 찾는 방식은 Windows에서 100% 오탐이므로 **절대 쓰지 않는다** | ℹ️ |
| CHK-10 | 폰 승인(Permission Relay) 가능 | `<설정 루트>\plugins\cache\claude-plugins-official\telegram\<버전>\server.ts` 안에 `'claude/channel/permission'` 문자열 존재 확인 | 선언 발견됨 | ℹ️ |
| CHK-11 | 받음 표시(ackReaction) | `access.json` → 최상위 `ackReaction` | 값이 비어 있지 않음 | ℹ️ |
| CHK-12 | 조직 정책 차단 여부 | 시작 경고 문구 + 설정 파일의 `channelsEnabled`·`allowedChannelPlugins` | 차단 경고 없음 (Pro/Max 개인 계정은 해당 없음) | ℹ️ |

## 보안 검사 3개 (F9 보안 자가검증)

| ID | 검사 | 방법 | 통과조건 | 실패 시 | 등급 |
|---|---|---|---|---|---|
| CHK-13 | 허용목록 인원 = 1명 | `access.json` → `allowFrom.Count` (값 자체는 출력 금지, **개수만**) | = 1 | **0명**: 아직 페어링 전 — 정상적인 초기 상태이며 보안 경고 아님(CHK-07이 이미 이 상태를 다룸). **2명 이상**: 경고 — 허용된 전원이 `Bash`·`Write` 승인 가능 | ⚠️(2명 이상일 때만) |
| CHK-15 | 그룹 허용 여부 | `access.json` → `groups` 객체의 **속성 개수**(`.PSObject.Properties.Count` — 배열 아님, 객체) | = 0 | 경고 | ⚠️ |
| CHK-16 | 위험 플래그 사용 흔적 | 실행 인자에 `--dangerously-`로 시작하는 항목 (`Get-CimInstance Win32_Process`의 `CommandLine`) | 없음 | 경고 | ⚠️ |

> CHK-14는 CHK-08과 중복이라 삭제됨(번호 재사용 안 함). **총 15개**.

## 진도 계산 (STEP 12단계 — `status`의 "N/12")

일반 검사 12개(CHK-01~12) 기준. **보안 검사 3개는 진도에 포함하지 않고** 별도 경고 블록으로 표시.

| 단계 | 이름 | 검사 | 사람 손 |
|---|---|---|---|
| 1 | Claude Code 준비 | CHK-01 | ✕ |
| 2 | Bun 준비 | CHK-02 | ✕ |
| 3 | 마켓플레이스 확인 | CHK-03, CHK-04 | ✕ |
| 4 | 텔레그램 봇 만들기 | (CHK-06으로 역추론) | ○ (재설치 시 불필요 — 기존 봇 재사용) |
| 5 | 토큰 복사 | (CHK-06으로 역추론) | ○ |
| 6 | 플러그인 설치(scope=user) | CHK-05 | ✕ |
| 7 | 플러그인 활성화 | — (조건부: 설치요약에 안내 뜰 때만) | ✕ |
| 8 | 토큰 등록 | CHK-06 | ✕ |
| 9 | `--channels`로 재실행 | CHK-09 | ✕ |
| 10 | 페어링 | CHK-07 | ○ |
| 11 | 안전 잠금(allowlist) | CHK-08 | ✕ |
| 12 | 마무리 설정 | CHK-11 (CHK-10은 참고정보 — 아래 설명) | ✕ |

> ⚠️ **CHK-10은 12단계 진도 판정에서 제외됩니다.** CHK-10(폰 승인 가능 여부)은 사용자의 진행 상태가 아니라 **플러그인이 그 기능을 지원하는지** 보여주는 정적 사실이라, 6단계(플러그인 설치)만 끝나면 페어링·잠금 여부와 무관하게 항상 통과합니다. 그대로 진도에 포함하면 아직 안 끝난 사용자에게도 "N/12"가 1만큼 부풀려 보입니다(2026-08-19, 이 PC 실측 상태로 재현 확인). CHK-10 자체는 여전히 검사하고 결과는 보여주되, 진도 숫자에서만 뺍니다.

## 부가: 다른 채널 감지 (참고용 — Discord는 정식 지원 대상 아님)

> `.PRD/01_PRD.md` §8: Discord **지원**(설치·수리 기능) 자체는 "안 만드는 것"으로 확정. 그러나 §5 S1은 `status`가 **이 PC의 방치된 Discord 상태를 정확히 짚어내야 한다**고 요구한다. 두 요구를 동시에 지키려고, Discord는 **존재 여부만 참고로 보여주는 것까지만** 한다. 아래 표는 CHK 번호가 없고 **정본 15개에 포함되지 않는다** — `install`·`fix`는 이 표를 쓰지 않고, `status`만 참고용 한 줄로 붙인다.

| 판정 순서 | 확인 | 결과 | 사람이 보는 문장(예시) |
|---|---|---|---|
| 1 | `<채널 루트>\discord\.env` 존재? | 없음 | (표시 안 함 — 설정 시작 전) |
| 2 | 있음 → `<채널 루트>\discord\access.json` 존재? | 없음 | "Discord: 토큰까지는 등록됐지만 페어링이 안 된 상태로 보입니다 (참고용 — 이 도구는 Discord 설치를 도와주지 않습니다)" |
| 3 | 둘 다 존재 | — | "Discord: 설정이 어느 정도 진행된 상태로 보입니다 (참고용, 세부 단계는 판정하지 않음)" |

---

## 2026-08-17 실측 검증 로그 (이 PC, 추측 아님)

- CHK-01: `2.1.233` 정상 출력
- CHK-02: `1.3.14` 정상 출력
- CHK-04: `~/.claude/settings.json`에 `"DISABLE_AUTOUPDATER": "1"` 존재 확인 (2026-08-20 추가 실측: `env` 객체 안에 중첩된 위치 — `{ "env": { "DISABLE_AUTOUPDATER": "1" } }`)
- CHK-06: `telegram\.env` 존재=True / `discord\.env` 존재=True(단 Discord는 `access.json` 없음 = 8단계 정지 상태)
- CHK-07: `allowFrom.Count` = 1
- CHK-08: `dmPolicy` = `"pairing"` → **현재 이 PC는 🚫 차단 상태**(사용자가 공식 명령으로 직접 잠가야 함 — CCC는 대신 실행하지 않음, G-1b)
- CHK-11: `ackReaction` 비어 있음 → 실패 확인
- CHK-13: 개수 1 → 통과
- CHK-15: `groups` 없음/빈 값 → 통과(0으로 간주)
