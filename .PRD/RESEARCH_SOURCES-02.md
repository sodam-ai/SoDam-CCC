# RESEARCH_SOURCES-02

# ✅ 최종 결론

2026년 8월 9일 기준 공식 Anthropic 문서와 공식 `claude-plugins-official` 저장소를 다시 확인했습니다.

**Claude Code Channels는 별도의 “Channels 프로그램”을 설치하는 방식이 아닙니다.** Claude Code에 `Telegram`, `Discord` 같은 **Channel 플러그인**을 설치하고, Claude Code를 `--channels` 옵션으로 실행하는 구조입니다. Channels는 MCP 서버가 Telegram 메시지, Discord 채팅, 웹훅 같은 외부 이벤트를 **현재 실행 중인 Claude Code 세션으로 밀어 넣는 기능**입니다. 현재도 `research preview` 계열 기능이며, 최신 Claude Code에서 계속 수정·개선되고 있습니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/mcp))

사용자처럼 **Windows + Claude Code + 스마트폰 원격 사용** 목적이라면 우선 **Telegram Channels**로 시작하는 것을 권장합니다.

------

# 1. 먼저 전체 구조 이해

설치 후 구조는 이렇게 됩니다.

```text
스마트폰
   ↓
Telegram
   ↓
내가 만든 Telegram Bot
   ↓
Claude Code Telegram Channel Plugin
   ↓
내 Windows PC에서 실행 중인 Claude Code
   ↓
내 프로젝트 파일 / 터미널 / MCP / 도구
```

예를 들어 밖에서 스마트폰으로 Telegram 봇에게:

```text
현재 프로젝트 확인하고 오류가 있는지 검사해줘.
```

라고 보내면 메시지가 **집/회사 PC에서 실행 중인 Claude Code 세션으로 전달**됩니다. Claude Code가 작업하고 Telegram으로 다시 답할 수 있습니다. 공식 Telegram 플러그인은 답장, 반응, 메시지 수정, 파일 전송 기능도 제공합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 2. 설치 전 필요한 것

필수는 사실상 다음 네 가지입니다.

| 항목                            | 필요 | 이유                                  |
| ------------------------------- | ---- | ------------------------------------- |
| Claude Code                     | ✅    | 실제 AI 작업을 수행                   |
| Claude 유료 계정 또는 지원 인증 | ✅    | Claude Code 사용                      |
| Bun                             | ✅    | Telegram Channel MCP 서버 실행        |
| Telegram                        | ✅    | 스마트폰에서 명령 전달                |
| Node.js                         | ❌    | Channels 때문에 별도 설치할 필요 없음 |
| Docker                          | ❌    | 필요 없음                             |
| WSL                             | ❌    | 필요 없음                             |

특히 **Bun은 반드시 설치해야 합니다.** 공식 Telegram 플러그인의 MCP 서버 자체가 Bun으로 실행되기 때문입니다. Bun은 JavaScript/TypeScript 프로그램을 실행해주는 런타임이라고 생각하면 됩니다. ([github.com](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 3. Claude Code 최신 버전 확인

먼저 **Windows PowerShell**을 여세요.

```powershell
claude --version
```

2026년 8월 8일 공개된 공식 최신 릴리스는 검색 시점 기준 **Claude Code 2.1.226**입니다. Channels는 2.1.80에서 처음 `--channels` research preview로 추가된 이후 여러 수정이 들어왔기 때문에, 오래된 버전을 그대로 사용하는 것보다 최신 버전을 권장합니다. ([Claude](https://code.claude.com/docs/en/changelog?utm_source=chatgpt.com))

다음도 실행해보세요.

```powershell
claude doctor
```

Claude Code 설치와 업데이트 상태 등을 검사합니다. ([Claude](https://code.claude.com/docs/en/setup?utm_source=chatgpt.com))

### 업데이트

Native 설치를 사용한다면 보통 자동 업데이트됩니다.

수동으로 업데이트하려면:

```powershell
claude update
```

WinGet으로 설치했다면:

```powershell
winget upgrade Anthropic.ClaudeCode
```

공식 문서에서도 WinGet 설치는 자동 업데이트가 기본이 아니므로 수동 업데이트가 필요하다고 안내합니다. ([Claude](https://code.claude.com/docs/en/setup?utm_source=chatgpt.com))

------

# 4. Bun 설치

## Windows PowerShell에서 실행

**Claude Code 안에서 입력하는 명령이 아닙니다.**

Windows PowerShell에서:

```powershell
powershell -c "irm bun.sh/install.ps1|iex"
```

Bun 공식 Windows 설치 명령입니다. ([Bun](https://bun.sh/docs/installation?utm_source=chatgpt.com))

설치 후 PowerShell을 한번 닫았다가 다시 열고:

```powershell
bun --version
```

정상이라면 버전 번호가 표시됩니다.

예:

```text
1.x.x
```

그러면 Bun 설치 완료입니다.

------

# 5. Telegram Bot 만들기

스마트폰이나 PC에서 Telegram을 실행합니다.

Telegram 검색창에서:

```text
@BotFather
```

를 찾습니다.

Anthropic 공식 Telegram 플러그인도 BotFather를 사용해 봇을 만들도록 안내합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

BotFather 채팅에서:

```text
/newbot
```

입력합니다.

그러면 BotFather가 먼저 봇 이름을 물어봅니다.

예:

```text
My Claude Code
```

그 다음 Username을 물어봅니다.

Username은 반드시 `bot`으로 끝나야 합니다.

예:

```text
my_claude_code_bot
```

성공하면 BotFather가 이런 형태의 **Bot Token**을 줍니다.

```text
123456789:AAxxxxxxxxxxxxxxxxxxxxxxxx
```

### 중요

이 Token은 비밀번호와 비슷합니다.

다른 사람에게 보여주거나 GitHub에 올리면 안 됩니다.

------

# 6. 이제 Claude Code 실행

여기서부터 위치가 중요합니다.

PowerShell에서 작업할 프로젝트 폴더로 이동합니다.

예:

```powershell
cd D:\MyProject
```

그리고:

```powershell
claude
```

실행합니다.

이제 화면이 **Claude Code 입력창**으로 바뀝니다.

------

# 7. Telegram 플러그인 설치

⚠️ 지금부터 아래 `/...` 명령은 **PowerShell 명령이 아닙니다.**

**실행 중인 Claude Code 안에 입력합니다.**

```text
/plugin install telegram@claude-plugins-official
```

설치 후:

```text
/reload-plugins
```

Anthropic 공식 Telegram 플러그인의 현재 설치 절차가 정확히 이 방식입니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 8. Telegram Bot Token 연결

아직 Claude Code 안입니다.

다음처럼 입력합니다.

```text
/telegram:configure 여기에_봇토큰
```

예:

```text
/telegram:configure 123456789:AAxxxxxxxxxxxxxxxxxxxx
```

이 명령을 실행하면 토큰이 기본적으로 다음 위치에 저장됩니다.

```text
~/.claude/channels/telegram/.env
```

Windows에서는 개념적으로 사용자 Claude 설정 폴더 아래에 저장되는 것입니다. 공식 플러그인은 여기에 `TELEGRAM_BOT_TOKEN`을 기록합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 9. Claude Code 종료

여기가 **매우 중요합니다.**

플러그인 설치만 해서는 Channels가 작동하지 않습니다.

Claude Code를 종료합니다.

```text
/exit
```

또는:

```text
Ctrl + C
```

------

# 10. Channels 모드로 Claude Code 실행

이제 다시 **PowerShell**입니다.

프로젝트 폴더에서:

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

이 명령이 핵심입니다.

공식 설명에서도 **이 옵션으로 다시 실행하지 않으면 Telegram 서버가 Channel로 연결되지 않는다**고 명시하고 있습니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

즉 평소:

```powershell
claude
```

대신 Channels 사용할 때는:

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

입니다.

------

# 11. 스마트폰 Telegram에서 Bot에게 메시지 보내기

아까 만든 봇을 Telegram에서 엽니다.

예:

```text
@my_claude_code_bot
```

그리고 아무 메시지나 보내세요.

예:

```text
안녕
```

정상이라면 봇이 **6자리/6문자 Pairing Code**를 보내줍니다.

예:

```text
A4F91C
```

처음 온 메시지는 Claude에게 바로 전달되지 않고, 본인 확인을 위한 페어링 절차를 먼저 거칩니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/ACCESS.md))

------

# 12. Pairing 승인

PC의 Claude Code로 돌아갑니다.

Claude Code 안에:

```text
/telegram:access pair A4F91C
```

처럼 입력합니다.

물론 `A4F91C` 부분에는 **Telegram에서 실제 받은 코드**를 입력합니다.

공식 Telegram Channels 접근제어는 이 페어링을 통해 Telegram 사용자의 숫자 ID를 자동으로 허용 목록에 등록합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/ACCESS.md))

------

# 13. 이제 실제 사용 가능

Telegram에서 다시:

```text
안녕
```

을 보내보세요.

이번에는 Claude Code에 전달됩니다.

예를 들어 스마트폰 Telegram에서:

```text
현재 프로젝트 구조 확인해줘.
```

또는:

```text
현재 작업 상태 확인하고 문제가 있으면 알려줘.
```

또는:

```text
npm run build 실행해서 오류 확인해줘.
```

또는:

```text
현재 구현된 웹페이지에 UI 문제가 있는지 검사해줘.
```

같이 **평소 Claude Code에 자연어로 입력하던 내용을 Telegram으로 보내면 됩니다.** Telegram 메시지가 실행 중인 Claude Code 세션으로 전달되고, Claude가 Telegram 봇을 통해 답장할 수 있습니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 14. 설치 직후 반드시 보안 잠금

처음에는 페어링을 쉽게 하기 위해 기본 정책이:

```text
pairing
```

입니다.

즉 모르는 사람이 봇을 발견해서 DM을 보내면 페어링 코드를 받을 수 있습니다.

본인 연결이 끝났으면 Claude Code에서:

```text
/telegram:access policy allowlist
```

를 실행하는 것을 **강력 권장합니다.**

그러면 이미 허용된 사용자만 접근할 수 있고, 모르는 사람이 메시지를 보내도 페어링 응답 자체를 보내지 않습니다. Anthropic 공식 문서 역시 초기 페어링 후 `allowlist` 정책으로 잠그는 것을 권장합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

현재 상태를 확인하려면:

```text
/telegram:access
```

실행하면 됩니다.

------

# 15. 앞으로 사용할 때마다 어떻게 실행?

초기 설치는 **한 번만** 하면 됩니다.

다음부터는 Bun 재설치, Bot 재생성, 플러그인 재설치가 필요 없습니다.

Windows에서 프로젝트 폴더를 열고:

```powershell
cd D:\내프로젝트
```

그다음:

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

이것만 실행하면 됩니다.

그리고 스마트폰 Telegram에서 메시지를 보내면 됩니다. 플러그인의 Channel 서버는 실행 중인 Claude Code 세션에 메시지를 전달하는 구조이므로, **PC가 켜져 있고 해당 Claude Code Channels 세션이 실행 중이어야 합니다.** ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 16. 가장 중요한 부분 — 일반 Claude Code와 차이

일반 실행:

```powershell
claude
```

구조:

```text
PC
↓
Claude Code
```

Channels 실행:

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

구조:

```text
스마트폰 Telegram
       ↓
Telegram Bot
       ↓
Channels Plugin
       ↓
PC의 Claude Code
```

즉 **Telegram이 Claude 자체를 실행하는 것이 아닙니다.**

스마트폰이 **PC에서 실행되고 있는 Claude Code의 원격 입력/출력 통로 역할**을 하는 것입니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/mcp))

------

# 17. Telegram으로 이미지도 보낼 수 있음

현재 공식 Telegram 플러그인은 Telegram으로 들어온 사진을:

```text
~/.claude/channels/telegram/inbox/
```

에 자동 다운로드하고 그 로컬 파일 경로를 Claude Code에 전달합니다.

따라서 스마트폰에서 스크린샷을 보내고:

```text
이 오류 화면 확인해서 원인 찾아줘.
```

같은 식으로 사용할 수 있습니다. Telegram 일반 사진 전송은 압축되므로 원본 품질이 중요하면 Telegram에서 **파일로 보내기**를 사용하는 것이 공식 권장 방식입니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 18. 반대로 Claude Code가 파일을 Telegram으로 보낼 수도 있음

공식 Telegram 플러그인의 `reply` 도구는 텍스트뿐 아니라 파일 첨부도 지원합니다.

현재 공식 README 기준 최대:

```text
50 MB / 파일
```

이며 JPG, PNG, GIF, WebP 등은 사진으로 표시하고 다른 형식은 문서로 전송합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

예를 들어 Telegram에서:

```text
현재 보고서를 markdown 파일로 만들어서 여기로 보내줘.
```

같은 형태의 활용이 가능합니다.

------

# 19. 안 될 때 가장 먼저 확인

설치 후 Telegram 봇에게 메시지를 보냈는데 반응이 없다면 순서대로 확인하세요.

```powershell
bun --version
```

Bun이 정상인지 확인.

```powershell
claude --version
```

Claude Code가 최신인지 확인.

```powershell
claude doctor
```

Claude Code 상태 확인.

그리고 **가장 흔한 실수**는 Claude Code를 그냥:

```powershell
claude
```

로 실행해 놓은 경우입니다.

반드시:

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

로 실행해야 합니다. 공식 Telegram README에서도 봇이 응답하지 않을 경우 세션이 `--channels`로 실행됐는지 확인하라고 안내합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 20. 만약 `/plugin install telegram...`에서 못 찾는다고 나오면

공식 Anthropic Marketplace가 정상 등록되지 않은 경우 수동으로 추가할 수 있습니다.

Claude Code에서:

```text
/plugin marketplace add anthropics/claude-plugins-official
```

그다음:

```text
/plugin install telegram@claude-plugins-official
```

공식 Claude Code 설정 문서는 공식 Marketplace가 일반적으로 자동 등록되지만 일부 환경에서는 자동 등록이 누락될 수 있으며, 이 경우 `anthropics/claude-plugins-official` Marketplace를 수동으로 등록할 수 있다고 설명합니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/settings))

------

# 21. 회사 Team / Enterprise 계정이면 추가 확인

개인 계정과 달리 **Team/Enterprise 조직 관리자가 Channels를 차단할 수 있습니다.**

관리 정책에서는:

```json
{
  "channelsEnabled": true
}
```

가 필요할 수 있습니다.

그리고 조직 관리자가 어떤 Channel 플러그인을 허용할지도 제한할 수 있습니다. 공식 설정 문서에 `channelsEnabled`와 `allowedChannelPlugins`가 별도로 존재합니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/settings))

개인 Claude Code를 사용하는 경우에는 보통 이 부분을 직접 건드릴 필요가 없습니다.

------

# 22. Discord도 원리는 동일

Discord를 쓰고 싶다면 공식 플러그인이 따로 있습니다.

Claude Code에서:

```text
/plugin install discord@claude-plugins-official
/reload-plugins
```

Token 등록:

```text
/discord:configure 내_DISCORD_BOT_TOKEN
```

Claude Code를 다시 실행:

```powershell
claude --channels plugin:discord@claude-plugins-official
```

다만 Discord는 Telegram보다 초기 설정이 많습니다. Discord Developer Portal에서 Application/Bot 생성, `Message Content Intent` 활성화, OAuth2로 서버에 봇 초대, 권한 설정까지 해야 합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/discord/README.md?utm_source=chatgpt.com))

그래서 **개인 스마트폰 원격 사용 목적이면 Telegram이 더 간단합니다.**

------

# ⭐ 사용자에게 권장하는 실제 설치 순서

처음 설치할 때 아래 순서 그대로 따라가면 됩니다.

```text
① Claude Code 최신 버전 확인
        ↓
② Bun 설치
        ↓
③ Telegram @BotFather에서 Bot 생성
        ↓
④ Bot Token 복사
        ↓
⑤ claude 실행
        ↓
⑥ /plugin install telegram@claude-plugins-official
        ↓
⑦ /reload-plugins
        ↓
⑧ /telegram:configure BOT_TOKEN
        ↓
⑨ Claude Code 종료
        ↓
⑩ claude --channels plugin:telegram@claude-plugins-official
        ↓
⑪ 스마트폰에서 Bot에게 메시지
        ↓
⑫ Pairing Code 확인
        ↓
⑬ /telegram:access pair CODE
        ↓
⑭ /telegram:access policy allowlist
        ↓
✅ 완료
```

**그다음부터는 아래 한 줄만 실행하면 됩니다.**

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

그리고 스마트폰 Telegram에서 평소 Claude Code에 하듯 자연어로 명령하면 됩니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

### 특히 중요한 점

Channels와 **Remote Control은 비슷해 보여도 다릅니다.** 단순히 스마트폰에서 내 PC의 Claude Code를 그대로 보고 조작하는 것이 목적이라면 Anthropic의 공식 **Remote Control**은 별도 Bot이나 Bun 설치 없이 `claude remote-control` 또는 기존 세션에서 `/remote-control`만 실행하면 되고, Claude 모바일 앱/`claude.ai/code`에서 연결할 수 있습니다. 반대로 **Telegram을 Claude Code 명령창처럼 쓰고 싶다면 지금 설명한 Channels가 맞습니다.** ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/remote-control))

원하시면 다음 답변에서 제가 **「Windows 왕초보용 Claude Code Telegram Channels 설치 가이드」를 1번부터 마지막까지, 복붙할 명령어만 순서대로 놓고 각 단계에서 화면에 무엇이 나와야 정상인지까지** 더 쉽게 만들어드릴 수 있습니다.
