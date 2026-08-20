# RESEARCH_SOURCES-01

# ✅ 최종 결론

검색 결과를 기준으로 **“클로드코드 채널”은 Claude Code Channels 기능**을 뜻하는 것으로 보고 설명하겠습니다.

**Claude Code Channels**는 실행 중인 Claude Code를 **텔레그램·디스코드·외부 웹훅 같은 채널과 연결**하여, 컴퓨터 앞에 없어도 메시지나 외부 이벤트로 작업을 요청하고 결과를 받을 수 있게 하는 기능입니다. 공식 문서에서는 현재 로컬 세션을 다른 기기에서 직접 이어 쓰는 **Remote Control**과, 외부 메시지·이벤트를 세션으로 밀어 넣는 **Channels**를 별도 기능으로 구분합니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/overview?utm_source=chatgpt.com))

## 1. 쉽게 말하면

기존 방식:

```text
컴퓨터에서 터미널 실행
→ Claude Code에 명령 입력
→ 결과 확인
```

Channels 방식:

```text
스마트폰 텔레그램에서 명령 전송
→ 집이나 회사 PC에서 실행 중인 Claude Code가 명령 수신
→ 코드 수정·명령 실행·검사
→ 결과를 텔레그램으로 회신
```

즉, **텔레그램이나 디스코드를 Claude Code의 원격 명령창처럼 사용하는 구조**입니다.

------

## 2. 현재 가능한 대표 채널

### 공식 지원 플러그인

- **Telegram**
- **Discord**
- MCP 기반 사용자 정의 채널
- 자체 서버·웹훅 이벤트

공식 Claude Code 문서는 MCP 서버가 단순 도구 연결뿐 아니라, 텔레그램 메시지·디스코드 채팅·웹훅 이벤트를 실행 중인 세션으로 전달하는 채널 역할도 할 수 있다고 설명합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md?utm_source=chatgpt.com))

### 사용자 정의로 연결 가능한 예

```text
GitHub PR 댓글
CI/CD 빌드 실패
서버 오류 알림
모니터링 시스템
고객 문의
사내 업무 시스템
스케줄러·크론 작업
자체 웹사이트
```

사용자 정의 채널은 MCP 서버가 `notifications/claude/channel` 형식의 이벤트를 Claude Code 세션에 전달하는 방식으로 확장할 수 있습니다. 다만 직접 구현하려면 MCP와 인증·접근제어 구성이 필요합니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/mcp?utm_source=chatgpt.com))

------

## 3. 텔레그램 채널 설치 구조

Claude Code를 먼저 실행한 뒤 공식 플러그인을 설치합니다.

```text
/plugin install telegram@claude-plugins-official
/reload-plugins
```

BotFather에서 발급받은 텔레그램 봇 토큰을 등록합니다.

```text
/telegram:configure 텔레그램_봇_토큰
```

Claude Code를 채널 모드로 실행합니다.

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

텔레그램 봇에게 메시지를 보내면 6자리 페어링 코드가 표시되고, Claude Code에서 다음과 같이 승인합니다.

```text
/telegram:access pair 페어링코드
```

이후 허용된 텔레그램 계정의 DM이 Claude Code 세션으로 전달됩니다. 공식 텔레그램 플러그인은 사용자 ID를 직접 입력하는 대신 페어링 방식으로 접근 권한을 설정합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md?utm_source=chatgpt.com))

------

## 4. 반드시 적용해야 하는 보안 설정

Channels는 외부 메시지가 실제 로컬 Claude Code에 전달되는 구조이므로, 일반 챗봇보다 권한 위험이 큽니다.

### 강력 권장

```text
1. 본인 계정만 페어링
2. 허용 사용자·서버·채널 제한
3. 프로젝트 전용 폴더에서 실행
4. 관리자 PowerShell로 실행하지 않기
5. 중요 파일과 비밀키를 프로젝트에서 분리
6. Git 커밋·백업 후 사용
7. 자동 배포·삭제·결제 명령은 승인 유지
8. --dangerously-skip-permissions 사용 금지
```

특히 다음 실행 방식은 원격 메시지가 곧바로 강한 로컬 권한을 얻을 수 있으므로 권장하지 않습니다.

```powershell
claude --channels plugin:telegram@claude-plugins-official --dangerously-skip-permissions
```

------

## 5. Channels와 Remote Control 차이

| 구분        | Channels                    | Remote Control                   |
| ----------- | --------------------------- | -------------------------------- |
| 핵심 목적   | 외부 메시지와 이벤트 수신   | 기존 로컬 세션 원격 조작         |
| 대표 연결   | Telegram, Discord, Webhook  | Claude 앱·브라우저·다른 기기     |
| 작동 위치   | 사용자 PC의 Claude Code CLI | 사용자 PC의 Claude Code CLI      |
| 적합한 상황 | 봇 명령, 알림 대응, 자동화  | 이동 중 기존 작업 계속하기       |
| 확장성      | MCP로 자체 채널 개발 가능   | Claude 공식 원격 인터페이스 중심 |

공식 문서도 “휴대폰에서 기존 로컬 세션을 계속 사용”하려면 Remote Control, “텔레그램·디스코드·웹훅 이벤트를 세션으로 전송”하려면 Channels를 선택하도록 안내합니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/overview?utm_source=chatgpt.com))

------

## 6. 현재 주의할 점

Claude Code Channels는 비교적 새로운 기능이며, 버전별로 다음 문제가 실제 GitHub 이슈에 보고되어 있습니다.

- 채널이 설치됐지만 수신 메시지가 세션으로 전달되지 않는 문제
- 플러그인이 연결됨으로 표시되지만 텔레그램 폴링이 시작되지 않는 문제
- 특정 버전에서 정상 작동하다 업데이트 후 고장 나는 회귀 문제
- 텔레그램에서 `/clear`, `/compact`, `/cost` 같은 Claude Code 내장 명령을 직접 실행하지 못하는 제한

2026년 공개 이슈에는 Claude Code 2.1.126에서 텔레그램 수신이 작동하지 않고 2.1.114로 내리면 작동했다는 사례도 있으며, 다른 버전에서도 플러그인 프로세스가 시작되지 않는 사례가 보고됐습니다. 따라서 설치 전후에 최신 이슈와 릴리스 노트를 확인하고, 자동 업데이트 후에는 수신 테스트를 다시 해야 합니다. ([GitHub](https://github.com/anthropics/claude-code/issues/37342?utm_source=chatgpt.com))

진단할 때는 다음 명령을 사용합니다.

```powershell
claude --version
claude doctor
```

Claude Code는 설치 방식에 따라 자동 업데이트 또는 수동 업데이트되며, `claude doctor`로 최근 업데이트 상태를 확인할 수 있습니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/setup?utm_source=chatgpt.com))

------

## 7. 사용자에게 가장 적합한 선택

Windows에서 Claude Code를 휴대폰으로 원격 명령하려는 목적이라면 우선순위는 다음과 같습니다.

### 1순위: Telegram Channels

```text
개인 사용
스마트폰 원격 제어
설정이 비교적 단순함
DM 기반이라 접근 범위 관리가 쉬움
```

### 2순위: Remote Control

```text
기존 Claude Code 세션을 모바일에서 그대로 이어서 작업
별도 봇 운영이 부담스러운 경우
공식 Claude 인터페이스를 선호하는 경우
```

### 3순위: Discord Channels

```text
여러 사람과 공동 사용
프로젝트별 서버·채널 운영
팀 단위 개발 알림과 명령 처리
```

**개인용 원격 제어에는 텔레그램 Channels가 가장 직접적이지만, 안정성과 보안 편의성까지 고려하면 Remote Control도 함께 비교하는 것이 좋습니다.** Channels를 사용한다면 Claude Code가 실행 중인 PC가 켜져 있어야 하고, 해당 CLI 세션도 계속 실행 중이어야 합니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/overview?utm_source=chatgpt.com))
