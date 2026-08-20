# RESEARCH_SOURCES-03

# ✅ 최종 결론

**검증 기준일: 2026년 8월 9일**

이번에는 **Claude Code Channels(클로드 코드 채널)** 자체와, 이를 Claude Code·Codex 및 다른 AI 코딩 에이전트에서 활용·확장·대체할 수 있는 **공식 문서 / GitHub / Plugin / MCP / SDK / Telegram / Discord / iMessage / Webhook / Slack / WhatsApp / Codex Bridge / 원격제어 자료**까지 범위를 넓혀 확인했습니다.

가장 먼저 정확히 구분해야 할 점이 있습니다. **Claude Code Channels는 일반 MCP와 같은 기능이 아닙니다.** 일반 MCP는 Claude가 필요할 때 서버의 Tool/Resource를 호출하는 방식인 반면, Channels는 **MCP 서버가 실행 중인 Claude Code 세션으로 외부 이벤트를 먼저 밀어 넣는 Push 방식**입니다. Telegram 메시지, Discord 메시지, CI 결과, 모니터링 경고, Webhook 등이 사용자가 터미널 앞에 없어도 현재 열려 있는 Claude Code 세션에 들어올 수 있습니다. 양방향 Channel이라면 Claude가 다시 Telegram·Discord 등으로 답장할 수도 있습니다. ([Claude](https://code.claude.com/docs/en/channels))

현재 Anthropic 공식 문서가 명시하는 Research Preview용 완성형 Channels는 **Telegram / Discord / iMessage**이고, 개발·시험용으로 **fakechat**이 제공됩니다. 완성형 공식 Channel 플러그인은 Bun을 요구합니다. 반면 직접 Custom Channel을 만들 때는 Node.js·Bun·Deno 등 Node 호환 런타임을 사용할 수 있습니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 1. 가장 중요한 Claude Code Channels 공식 주소

가장 먼저 아래 자료들을 기준 문서로 보면 됩니다. 특히 `channels`와 `channels-reference` 두 문서는 반드시 같이 봐야 합니다. 전자는 설치·사용법, 후자는 **직접 Channel을 만드는 프로토콜·MCP 구조·Permission Relay·보안**을 설명합니다. ([Claude](https://code.claude.com/docs/en/channels))

```text
https://code.claude.com/docs/en/channels
https://code.claude.com/docs/en/channels-reference

https://code.claude.com/docs/en/mcp
https://code.claude.com/docs/en/plugins-reference
https://code.claude.com/docs/ko/plugins-reference

https://code.claude.com/docs/en/remote-control
https://code.claude.com/docs/en/slack

https://code.claude.com/docs/llms.txt

https://github.com/anthropics/claude-code
https://github.com/anthropics/claude-plugins-official
https://github.com/anthropics/claude-plugins-community
```

### 공식 Plugin Marketplace

```text
https://github.com/anthropics/claude-plugins-official
https://github.com/anthropics/claude-plugins-official/blob/main/.claude-plugin/marketplace.json
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins
```

Anthropic 공식 Marketplace는 Anthropic 자체 플러그인과 검토된 외부 플러그인을 함께 제공하지만, Anthropic도 외부 플러그인에 포함된 타사 MCP 서버나 소프트웨어까지 완전히 통제할 수는 없다고 명시합니다. 따라서 `claude-plugins-official`이라는 이름만 보고 모든 외부 코드까지 Anthropic 자체 개발 코드로 보면 안 됩니다. ([GitHub](https://github.com/anthropics/claude-plugins-official?utm_source=chatgpt.com))

------

# 2. Claude Code Channels가 정확히 무엇인지

Channel 서버는 현재 Claude Code와 **stdio 기반 MCP 서버**로 연결됩니다. 핵심은 일반 MCP 서버에 다음 Claude 전용 실험 기능을 추가한다는 것입니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

```text
capabilities.experimental["claude/channel"]
```

그리고 외부 이벤트가 들어오면 다음 Notification을 Claude Code에 보냅니다.

```text
notifications/claude/channel
```

구조를 아주 쉽게 표현하면:

```text
Telegram / Discord / Webhook / CI / Monitoring
                  ↓
          Channel MCP Server
                  ↓
       notifications/claude/channel
                  ↓
        실행 중 Claude Code
                  ↓
        분석 / 파일 수정 / 명령 실행
                  ↓
              reply tool
                  ↓
Telegram / Discord / 외부 시스템으로 응답
```

이 구조가 일반 MCP와 가장 큰 차이입니다. Claude 공식 문서도 **Standard MCP는 Claude가 조회하는 Pull 구조이고, Channels는 외부 이벤트가 현재 세션으로 들어오는 Push 구조**라고 명확히 구분합니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 3. 공식 Telegram Channel — 최우선 추천

## 공식 주소

```text
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/telegram

https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md
https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/ACCESS.md
https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/server.ts

https://core.telegram.org/bots/api
https://t.me/BotFather
```

현재 공식 Telegram Plugin은 Anthropic 공식 외부 플러그인 디렉터리에 실제로 존재합니다. Telegram Bot API를 이용해 메시지를 받고, Claude의 답장을 Telegram으로 전송합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/telegram))

## 설치

Claude Code 안에서:

```text
/plugin install telegram@claude-plugins-official
```

Marketplace가 없다면:

```text
/plugin marketplace add anthropics/claude-plugins-official
```

BotFather에서 받은 Token 설정:

```text
/telegram:configure <BOT_TOKEN>
```

Claude Code 재실행:

```bash
claude --channels plugin:telegram@claude-plugins-official
```

Telegram에서 봇에게 메시지를 보내면 Pairing Code가 나오고 Claude Code에서:

```text
/telegram:access pair <code>
```

그 다음 반드시:

```text
/telegram:access policy allowlist
```

공식 문서가 안내하는 현재 설치 흐름입니다. ([Claude](https://code.claude.com/docs/en/channels))

## Telegram Channel이 할 수 있는 것

공식 구현은 메시지 회신, Reaction, 봇이 보낸 메시지 수정, 파일·사진 전송 등을 지원합니다. 들어오는 사진은 로컬 Channel inbox에 저장돼 Claude가 읽을 수 있습니다. 반면 Telegram Bot API 특성상 **과거 전체 메시지 History 검색은 지원되지 않으며**, 봇이 실시간으로 받은 메시지를 중심으로 작동합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md?utm_source=chatgpt.com))

------

# 4. 공식 Discord Channel

## 공식 주소

```text
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/discord

https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/discord/README.md
https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/discord/ACCESS.md
https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/discord/server.ts

https://discord.com/developers/applications
https://docs.discord.com/developers/intro
https://docs.discord.com/developers/guides/bots
```

Discord 역시 현재 Anthropic 공식 Channels 문서와 공식 Plugin 디렉터리에 포함된 지원 대상입니다. ([Claude](https://code.claude.com/docs/en/channels))

## 설치

```text
/plugin install discord@claude-plugins-official
```

Token 설정:

```text
/discord:configure <BOT_TOKEN>
```

실행:

```bash
claude --channels plugin:discord@claude-plugins-official
```

Pairing:

```text
/discord:access pair <code>
```

접근 제한:

```text
/discord:access policy allowlist
```

Discord Developer Portal에서는 현재 공식 가이드 기준 `Message Content Intent`를 켜고, View Channels·Send Messages·Thread 전송·Read Message History·Attach Files·Add Reactions 등의 필요한 Bot 권한을 설정합니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 5. 공식 iMessage Channel

## 공식 주소

```text
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/imessage

https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/imessage/README.md
https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/imessage/ACCESS.md
https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/imessage/server.ts
```

iMessage Channel은 **macOS 전용**입니다. Telegram/Discord처럼 외부 Bot API를 쓰는 방식이 아니라 로컬 Mac의:

```text
~/Library/Messages/chat.db
```

를 읽고 AppleScript를 통해 Messages 앱으로 답장을 보내는 구조입니다. 따라서 Full Disk Access와 Messages 앱 Automation 권한이 필요합니다. ([Claude](https://code.claude.com/docs/en/channels))

설치:

```text
/plugin install imessage@claude-plugins-official
```

실행:

```bash
claude --channels plugin:imessage@claude-plugins-official
```

다른 사람 허용:

```text
/imessage:access allow +8210XXXXXXXX
```

기본적으로 자신의 메시지는 자동 허용되고 다른 발신자는 명시적으로 Allow 해야 합니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 6. fakechat — Channel 개발·시험용 공식 도구

## 공식 주소

```text
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/fakechat

https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/fakechat/README.md
https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/fakechat/server.ts
```

fakechat은 Telegram이나 Discord 계정을 만들기 전에 **Channels 구조가 정상적으로 작동하는지 로컬 브라우저에서 시험하는 공식 데모**입니다. 외부 서비스나 인증이 필요 없습니다. ([Claude](https://code.claude.com/docs/en/channels))

설치:

```text
/plugin install fakechat@claude-plugins-official
```

실행:

```bash
claude --channels plugin:fakechat@claude-plugins-official
```

웹 UI:

```text
http://localhost:8787
```

**Channel 자체를 개발하려는 경우 가장 먼저 fakechat으로 구조를 확인하는 것을 강력 추천**합니다.

------

# 7. 직접 Custom Claude Code Channel 만들기

이 부분이 상당히 중요합니다.

Telegram·Discord·iMessage 외에도 **Webhook, Slack, Mattermost, Teams, KakaoTalk 연동 서버, 사내 메신저, CI/CD, 서버 모니터링, 주식 알림, GitHub 이벤트 등 외부 이벤트를 Custom Channel로 만들 수 있습니다.** 공식 Channel Reference는 HTTP POST → Claude Code 구조의 Webhook 예제까지 제공합니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

## 핵심 공식 주소

```text
https://code.claude.com/docs/en/channels-reference
https://code.claude.com/docs/en/mcp

https://modelcontextprotocol.io/
https://modelcontextprotocol.io/docs/

https://github.com/modelcontextprotocol/typescript-sdk
https://github.com/modelcontextprotocol/modelcontextprotocol
```

## Runtime

```text
https://bun.sh/
https://nodejs.org/
https://deno.com/
```

공식 문서 기준 Custom Channel의 필수 핵심은 MCP SDK와 Node 호환 Runtime이며, Anthropic 완성형 Channel은 Bun을 사용하지만 직접 만든 Channel은 Bun만 강제되지 않습니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

설치 예:

```bash
mkdir webhook-channel
cd webhook-channel
bun add @modelcontextprotocol/sdk zod
```

Channel Capability:

```typescript
capabilities: {
  experimental: {
    "claude/channel": {}
  }
}
```

외부 이벤트:

```text
notifications/claude/channel
```

양방향 Channel이라면 MCP `tools` 기능까지 추가해 Claude가 `reply` 같은 Tool을 호출하도록 설계합니다. 공식 예제는 실제로 `reply` Tool을 구현하는 방법까지 제공합니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

------

# 8. Permission Relay — 휴대폰에서 Claude Code 권한 승인

이 기능 때문에 Channels가 단순 Telegram 챗봇보다 훨씬 강력합니다.

Claude가 다음 같은 Tool 실행을 요청할 때:

```text
Bash
Write
Edit
```

터미널에 뜨는 승인 요청을 Telegram·Discord 같은 양방향 Channel로 전달하고, **휴대폰에서 허용/거절**할 수 있습니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

Custom Channel에서 Capability는:

```text
claude/channel/permission
```

구조:

```typescript
experimental: {
  "claude/channel": {},
  "claude/channel/permission": {}
}
```

즉:

```text
스마트폰
   ↓
Telegram
   ↓
Claude Code
   ↓
"이 Bash 명령 실행할까요?"
   ↓
Telegram으로 승인 요청
   ↓
Approve
   ↓
Claude Code 작업 계속
```

이 기능 때문에 **Allowlist 보안이 매우 중요합니다.** 승인된 발신자는 단순 메시지 전송뿐 아니라 Bash·Write·Edit 승인까지 할 수 있기 때문입니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 9. 반드시 알아야 하는 Channel 보안

공식 Channel Reference에서 가장 강하게 경고하는 부분 중 하나입니다.

**Sender 검증 없는 Channel은 Prompt Injection 입구가 됩니다.** 공개 Webhook이나 그룹 채팅의 누구든 Claude에게 명령을 밀어 넣을 수 있기 때문입니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

잘못된 접근:

```text
채팅방 ID가 허용된 방인가?
```

이것만 확인하면 같은 방의 다른 사람이 Claude를 조종할 수 있습니다.

올바른 접근:

```text
실제 message.from.id가 허용된 사용자 ID인가?
```

즉 반드시:

```text
Sender Allowlist
```

기준으로 필터링해야 합니다. Telegram과 Discord 공식 Channel도 이 구조를 사용합니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

------

# 10. Channels Research Preview의 현재 제한

현재 Channels는 **Research Preview**입니다. Anthropic은 `--channels` 명령 형식과 프로토콜 계약이 변경될 수 있다고 명시합니다. 흥미롭게도 Preview 기간에는 `--channels`와 개발용 `--dangerously-load-development-channels`가 `claude --help`에 표시되지 않을 수 있지만 실제 기능은 동작한다고 공식 문서가 설명합니다. ([Claude](https://code.claude.com/docs/en/channels))

또한 현재 공식 문서 기준 Channels 인증은:

```text
claude.ai 인증
또는
Anthropic Console API key
```

를 사용할 수 있지만 다음 제공 환경에서는 Channels가 제공되지 않습니다.

```text
Amazon Bedrock
Google Cloud Agent Platform
Microsoft Foundry
```

Team·Enterprise 조직은 Owner/Admin이 Channels를 명시적으로 활성화해야 합니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 11. Custom Channel 개발용 공식 Plugin / Skill

## MCP Server 개발 Plugin

```text
https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev

https://github.com/anthropics/claude-plugins-official/blob/main/plugins/mcp-server-dev/README.md

https://github.com/anthropics/claude-plugins-official/blob/main/plugins/mcp-server-dev/skills/build-mcp-server/SKILL.md
```

`mcp-server-dev`는 Anthropic 공식 Plugin 저장소에 포함된 MCP Server 개발용 Skill 묶음입니다. MCP 서버 설계, Local stdio, Remote HTTP, MCPB 같은 구조를 선택하도록 도와줍니다. Custom Channel도 결국 MCP 서버이므로 관련성이 높습니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/plugins/mcp-server-dev/README.md?utm_source=chatgpt.com))

```text
/plugin install mcp-server-dev@claude-plugins-official
```

또는 직접:

```text
/mcp-server-dev:build-mcp-server
```

## Plugin 개발 Toolkit

```text
https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev

https://github.com/anthropics/claude-plugins-official/blob/main/plugins/plugin-dev/README.md

https://github.com/anthropics/claude-plugins-official/blob/main/plugins/plugin-dev/skills/mcp-integration/SKILL.md
```

Plugin 구조, MCP integration, Hook, 설정 파일 등을 함께 설계할 때 참고할 자료입니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/plugins/plugin-dev/README.md?utm_source=chatgpt.com))

------

# 12. Claude Code Channels와 Remote Control은 다름

## Remote Control 공식

```text
https://code.claude.com/docs/en/remote-control
https://claude.ai/code
```

**Remote Control**은 사용자가 Claude.ai 또는 Claude 모바일 앱으로 **기존 로컬 Claude Code 세션을 직접 조종하는 기능**입니다. ([Claude](https://code.claude.com/docs/en/remote-control))

반면 **Channels**는:

```text
Telegram 메시지
Discord 메시지
Webhook
CI 이벤트
Monitoring Alert
외부 프로그램 이벤트
```

같은 것을 Claude Code 세션으로 Push합니다. ([Claude](https://code.claude.com/docs/en/channels))

따라서 목적별로 보면:

```text
폰에서 내가 직접 Claude Code 조종
→ Remote Control

Telegram Bot에게 메시지 보내기
→ Channels

Discord 메시지로 Claude 호출
→ Channels

CI 실패가 발생하면 Claude가 자동 분석
→ Custom Channel

서버 장애가 발생하면 Claude가 자동 반응
→ Custom Channel
```

------

# 13. Claude Code in Slack도 Channels와 다름

공식 Slack 기능:

```text
https://code.claude.com/docs/en/slack
```

현재 Claude Code in Slack은 Slack의 `@Claude` 멘션을 분석해 **Claude Code on the web의 새로운 세션을 생성**하는 구조입니다. 현재 문서에서는 Team·Enterprise의 이전 Slack 방식이 Claude Tag 방향으로 전환되고 있고, Pro·Max는 기존 설정 경로를 유지한다고 설명합니다. ([Claude](https://code.claude.com/docs/en/slack))

즉:

```text
Claude Code Channel
= 실행 중인 로컬 세션으로 메시지 Push

Claude Code in Slack
= Slack에서 새 Cloud/Web Claude Code 세션 시작
```

둘을 같은 기능으로 보면 안 됩니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 14. Slack을 “진짜 Claude Code Channel”로 만드는 커뮤니티 프로젝트

## claude-code-slack-channel

```text
https://github.com/jeremylongshore/claude-code-slack-channel
```

이 프로젝트는 Slack을 Claude Code Channels 프로토콜과 연결하려는 별도 커뮤니티 구현입니다. 저장소에는 Claude Plugin 구조와 Channel 관련 코드뿐 아니라 정책·감사 로그 등 상당히 많은 운영 기능이 포함되어 있습니다. 다만 **Anthropic 공식 Channels 문서가 현재 지원 채널로 명시하는 Telegram/Discord/iMessage와 같은 등급의 기본 공식 Channel로 분류하면 안 됩니다.** ([GitHub](https://github.com/jeremylongshore/claude-code-slack-channel))

따라서 분류는:

```text
Telegram  → 공식 Channel
Discord   → 공식 Channel
iMessage  → 공식 Channel
fakechat  → 공식 개발 Channel

Slack Channel plugin
→ 서드파티/커뮤니티
```

------

# 15. WhatsApp Claude Code Channel

## 프로젝트

```text
https://github.com/Rich627/whatsapp-claude-plugin
```

Baileys 기반으로 WhatsApp linked device와 Claude Code Channel을 연결하고, 양방향 메시지, 미디어, Permission Relay, 접근 통제 등을 구현한 커뮤니티 프로젝트입니다. ([GitHub](https://github.com/Rich627/whatsapp-claude-plugin?utm_source=chatgpt.com))

설치 예:

```bash
claude plugin marketplace add Rich627/whatsapp-claude-plugin
claude plugin install whatsapp@whatsapp-claude-plugin
```

다만 중요한 주의점이 있습니다.

프로젝트는 Anthropic Marketplace 심사를 통과했다고 설명하지만, Anthropic 공식 GitHub에 2026년 4월부터 **“승인되었는데 external_plugins 디렉터리에 나타나지 않는다”**는 이슈가 열려 있었고, 제가 **2026년 8월 9일 현재 공식 `external_plugins` 디렉터리를 다시 조회했을 때도 WhatsApp 폴더를 확인하지 못했습니다.** 따라서 현재 답변에서는 **Anthropic 기본 공식 Channel이 아니라 별도 Marketplace/서드파티 Channel**로 분류합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/issues/1292?utm_source=chatgpt.com))

------

# 16. Codex에서 Claude Code Channels를 그대로 사용할 수 있는가?

## 결론: 현재는 **그대로는 안 된다고 보는 것이 정확**

Claude의 핵심 Push 규약은:

```text
notifications/claude/channel
```

이라는 **Claude 전용 실험 확장**입니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

2026년 8월 9일 현재 OpenAI Codex 저장소를 다시 확인한 결과, 다음 기능 요청이 여전히 존재합니다.

### 실행 중 Codex CLI에 MCP Notification Push

```text
https://github.com/openai/codex/issues/15299
```

### Custom MCP Notification을 Codex Session에 주입

```text
https://github.com/openai/codex/issues/17543
```

### MCP `notifications/message`를 모델 Conversation에 전달

```text
https://github.com/openai/codex/issues/18056
```

### Codex 공식 Telegram Plugin 요청

```text
https://github.com/openai/codex/issues/21166
```

특히 #15299는 **Claude-style Channel처럼 Telegram 등의 외부 메시지를 현재 Codex CLI 세션에 넣을 방법이 필요하다**고 직접 설명하고 있고 현재 Enhancement 상태입니다. #21166도 공식 Telegram 연동 자체를 요청하고 있습니다. 따라서 지금 시점에 `telegram@claude-plugins-official` 같은 Claude Plugin을 Codex에 그대로 설치할 수 있다고 설명하면 잘못입니다. ([GitHub](https://github.com/openai/codex/issues/15299?utm_source=chatgpt.com))

------

# 17. Codex 공식 대안 ① — Codex 모바일 / Remote 연결

OpenAI 공식:

```text
https://openai.com/index/work-with-codex-from-anywhere/

https://developers.openai.com/codex/remote-connections

https://github.com/openai/codex
```

OpenAI는 2026년 5월부터 ChatGPT 모바일 앱에서 Codex를 원격으로 사용하는 Preview를 제공하고 있습니다. Codex가 실행 중인 컴퓨터·개발환경의 Thread, 실행 결과, 승인, Diff, Terminal output 등을 휴대폰에서 보고 제어하는 구조입니다. ([OpenAI](https://openai.com/index/work-with-codex-from-anywhere/?utm_source=chatgpt.com))

따라서 목적이:

```text
Telegram Bot이 꼭 필요 없음
+
폰에서 Codex를 원격 조작하고 싶음
```

이라면 비공식 Telegram Bridge보다 **OpenAI 공식 Remote/Mobile 방식이 우선**입니다. ([OpenAI](https://openai.com/index/work-with-codex-from-anywhere/?utm_source=chatgpt.com))

------

# 18. Codex 공식 대안 ② — Codex MCP / App Server Interface

```text
https://github.com/openai/codex/blob/main/codex-rs/docs/codex_mcp_interface.md
```

Codex 저장소에는 로컬 Codex Engine을 다른 프로그램에서 제어하기 위한 MCP/App Server 계열 Interface가 존재합니다. Thread 시작·재개, Turn 처리, 이벤트, 승인 등을 다루는 구조라서 **Telegram·Discord·Claude Code Channels와 Codex를 연결하는 Bridge를 직접 개발할 때 핵심 기반**이 될 수 있습니다. ([GitHub](https://github.com/openai/codex/blob/main/codex-rs/docs/codex_mcp_interface.md))

------

# 19. Claude Code + Codex를 Telegram/Discord에 같이 연결 — 가장 중요한 서드파티

## Claude-to-IM-skill

```text
https://github.com/op7418/Claude-to-IM-skill
https://github.com/op7418/Claude-to-IM
```

현재 확인한 서드파티 중 **“Claude Code와 Codex를 같은 메신저 계열에 붙이고 싶다”는 목적에 가장 직접적으로 맞는 프로젝트 중 하나**입니다.

지원 대상으로 저장소가 명시하는 것은:

```text
Claude Code
Codex

Telegram
Discord
Feishu / Lark
```

이며 Skill·백그라운드 Daemon과 각 Agent SDK를 조합하는 구조입니다. ([GitHub](https://github.com/op7418/Claude-to-IM-skill))

Claude Code용:

```bash
npx skills add op7418/Claude-to-IM-skill
```

Codex용 설치 스크립트도 저장소가 제공합니다.

```bash
bash scripts/install-codex.sh
```

**중요:** 이것은 Anthropic의 `notifications/claude/channel` 공식 Plugin을 Codex에 그대로 이식하는 것이 아니라, Claude Code와 Codex 각각에 맞는 SDK/Skill/Daemon을 이용해 공통 메신저 Bridge를 만드는 방식입니다. ([GitHub](https://github.com/op7418/Claude-to-IM-skill))

------

# 20. Codex Telegram / Discord Bridge — OpenAB

```text
https://github.com/xx025/openab
https://github.com/openai/codex/discussions/13144
```

OpenAB는 Telegram·Discord에서 Codex CLI를 사용하는 얇은 Bridge입니다.

현재 공개 설명상:

```text
Telegram / Discord
        ↓
      OpenAB
        ↓
codex exec / resume
        ↓
       Codex
```

방식으로 작동하며 `~/.codex/sessions`와 History를 활용해 세션을 이어가는 기능도 제공합니다. OpenAI Codex Discussions에도 프로젝트가 소개돼 있지만 **OpenAI 공식 Plugin이라는 의미는 아닙니다.** ([GitHub](https://github.com/openai/codex/discussions/13144?utm_source=chatgpt.com))

------

# 21. Claude Code Channels ↔ Codex App Server 직접 Bridge

## agent-bridge

```text
https://github.com/raysonmeng/agent-bridge

https://github.com/openai/codex/issues/15359
https://github.com/openai/codex/discussions/15374

https://github.com/anthropics/claude-code/issues/36871
```

이 프로젝트는 상당히 흥미롭습니다.

구조:

```text
Codex App Server
      ↕
   Agent Bridge
      ↕
Claude Code Channel
```

Claude 방향은:

```text
notifications/claude/channel
```

을 사용하고, Codex 방향은 App Server의 JSON-RPC/Turn 처리를 이용해 **Claude Code와 Codex 사이 실시간 협업 Bridge**를 만듭니다. ([GitHub](https://github.com/openai/codex/discussions/15374?utm_source=chatgpt.com))

다만 **양사 공식 기능이 아니라 실험적 커뮤니티 Bridge**입니다.

------

# 22. 또 다른 Claude Code ↔ Codex Channel Bridge

## codex-claude-bridge

```text
https://github.com/abhishekgahlot2/codex-claude-bridge
```

구조:

```text
Claude Code
   ↓ Channels Push
Bridge MCP
   ↓
Codex MCP Tool
```

Claude는 Native Channel Push를 사용하지만 Codex는 현재 동등한 Push 기능이 없기 때문에 **Blocking MCP Tool + Queue/Poll 방식**을 사용하는 비대칭 구조입니다. 프로젝트 자체도 이 한계를 명시합니다. ([GitHub](https://github.com/abhishekgahlot2/codex-claude-bridge?utm_source=chatgpt.com))

설치 구조 예:

```bash
git clone https://github.com/abhishekgahlot2/codex-claude-bridge.git
cd codex-claude-bridge
bun install
```

Claude:

```bash
claude --dangerously-load-development-channels server:codex-bridge
```

Codex:

```text
~/.codex/config.toml
```

에 MCP Server를 등록합니다. 실험·학습용으로는 매우 좋은 참고 자료이지만 운영환경에서는 권한과 통신 구조 검증이 필요합니다. ([GitHub](https://github.com/abhishekgahlot2/codex-claude-bridge?utm_source=chatgpt.com))

------

# 23. Codex 전용 모바일/Discord 관련 추가 프로젝트

```text
https://github.com/NathanZane/codex-mobile
```

Codex 장기 실행 작업과 승인 등을 Discord 기반으로 원격 확인하려는 서드파티 프로젝트입니다.

추가 탐색:

```text
https://github.com/search?q=codex+telegram&type=repositories
https://github.com/search?q=codex+discord+bridge&type=repositories
https://github.com/search?q=codex+mobile&type=repositories
```

다만 Codex는 이미 공식 Mobile/Remote 기능이 빠르게 발전하고 있으므로, 단순 원격 조종 목적이라면 새 서드파티 Bridge보다 OpenAI 공식 기능을 먼저 비교하는 것이 안전합니다. ([OpenAI](https://openai.com/index/work-with-codex-from-anywhere/?utm_source=chatgpt.com))

------

# 24. 일반 Telegram 원격 Claude Code 대안

## claude-code-telegram

```text
https://github.com/RichardAtCT/claude-code-telegram
```

이 프로젝트는 공식 Channels가 나오기 전부터 사용되던 독립 Telegram Bridge 계열입니다.

따라서 현재 기준 우선순위는:

```text
1. Anthropic 공식 Telegram Channel
2. Claude-to-IM-skill — Claude + Codex 공통 필요할 때
3. claude-code-telegram — 별도 기능/기존 환경이 필요할 때
```

정도가 적절합니다. 공식 Telegram Channel이 이미 존재하므로 Claude Code만 원격 조작하는 단순 목적에서는 굳이 비공식 Bridge부터 시작할 필요는 없습니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 25. Channel용 Telegram / Discord 원천 API 문서

## Telegram

```text
https://core.telegram.org/bots/api
https://core.telegram.org/bots
https://t.me/BotFather
```

## Discord

```text
https://discord.com/developers/applications
https://docs.discord.com/developers/intro
https://docs.discord.com/developers/guides/bots
```

Custom Channel을 직접 만들 때 Anthropic Channel Reference와 **메신저 공식 API 문서**를 같이 보는 것이 가장 안전합니다. Claude 공식 Telegram/Discord Plugin도 각각 해당 플랫폼 Bot API에 의존합니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 26. MCP 핵심 원천 자료

Claude Channels는 MCP의 확장 구조이므로 MCP 자료도 반드시 포함해야 합니다.

```text
https://modelcontextprotocol.io/
https://modelcontextprotocol.io/docs/

https://github.com/modelcontextprotocol/modelcontextprotocol
https://github.com/modelcontextprotocol/typescript-sdk

https://registry.modelcontextprotocol.io/
```

다만 **일반 MCP Server가 있다고 자동으로 Channel이 되는 것은 아닙니다.**

일반 MCP:

```text
Claude → MCP Tool 호출
```

Channel:

```text
외부 이벤트 → MCP Server → Claude Code
```

이 차이가 핵심입니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 27. 현재 확인된 실제 문제·버그

Research Preview라는 말 그대로 아직 완전히 안정화됐다고 보기는 어렵습니다.

## Telegram MCP 재연결 문제

```text
https://github.com/anthropics/claude-plugins-official/issues/1680
```

Telegram MCP 서버가 `/mcp` Dialog나 다른 Plugin 재인증 과정에서 종료된 뒤 자동 재연결되지 않는 사례가 현재 Open Issue로 보고돼 있습니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/issues/1680?utm_source=chatgpt.com))

## Discord State Directory 문제

```text
https://github.com/anthropics/claude-plugins-official/issues/1448
```

Discord Server 자체는 `DISCORD_STATE_DIR`를 인식하지만 일부 configure/access Skill이 기본 경로를 고정적으로 참조하는 문제가 보고돼 있습니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/issues/1448?utm_source=chatgpt.com))

## Channels에서 `/clear`, `/compact`, `/cost`

```text
https://github.com/anthropics/claude-code/issues/37342
```

Telegram/Discord에서 Claude에게 일반 텍스트 명령을 보내는 것과 **Claude Code TUI 자체의 Slash Command를 실행하는 것은 같은 것이 아닙니다.** `/clear`, `/compact`, `/cost` 등을 Channel에서 직접 실행할 수 있도록 해 달라는 기능 요청이 존재합니다. ([GitHub](https://github.com/anthropics/claude-code/issues/37342?utm_source=chatgpt.com))

따라서 예를 들어 Telegram에:

```text
/compact
```

라고 보낸다고 반드시 로컬 Claude Code의 `/compact`가 실행되는 것으로 생각하면 안 됩니다.

------

# 28. 항상 켜진 Claude Code Channel 구성 시 주의

Channel 메시지는 **Claude Code 세션이 실행 중일 때만 들어옵니다.** 항상 켜진 형태를 원하면 Claude Code 프로세스 자체를 지속 실행해야 합니다. 공식 문서는 background process 또는 persistent terminal 같은 구성을 언급합니다. ([Claude](https://code.claude.com/docs/en/channels))

예:

```text
Claude Code PC
     ↓
항상 실행 중
     ↓
Telegram Channel Plugin
     ↓
Telegram Bot
     ↓
휴대폰
```

따라서 컴퓨터가 꺼져 있거나 Claude Code Channel 세션이 종료되면 Telegram 봇만 살아 있다고 해서 Claude Code가 작업하는 것은 아닙니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 29. `--dangerously-skip-permissions`는 기본 사용 금지 추천

공식 문서에는 무인 실행을 위해:

```bash
--dangerously-skip-permissions
```

옵션이 언급되지만 이름 그대로 매우 위험합니다. 대부분의 Tool 승인 과정을 건너뛰기 때문입니다. 일부 안전장치는 남아 있지만 일반 원격 Claude Code 환경에서는 **Permission Relay + Allowlist 방식이 훨씬 안전한 기본 구성**입니다. ([Claude](https://code.claude.com/docs/en/channels))

권장:

```text
Telegram
+
Pairing
+
Sender Allowlist
+
Permission Relay
+
Claude Code 정상 승인 모드
```

비추천:

```text
공개 Telegram Bot
+
아무 사용자나 접근
+
--dangerously-skip-permissions
```

------

# 30. 공식 Channel과 관련 도구 최종 분류

| 대상                           | Claude Code    | Codex               | 상태                   | 추천         |
| ------------------------------ | -------------- | ------------------- | ---------------------- | ------------ |
| Telegram Channel               | ✅ Native       | ❌ Native 확인 안 됨 | Anthropic 공식 Preview | ⭐⭐⭐⭐⭐        |
| Discord Channel                | ✅ Native       | ❌ Native 확인 안 됨 | Anthropic 공식 Preview | ⭐⭐⭐⭐⭐        |
| iMessage Channel               | ✅ Native/macOS | ❌                   | Anthropic 공식 Preview | ⭐⭐⭐⭐         |
| fakechat                       | ✅ Native       | ❌                   | 공식 Dev/Test          | ⭐⭐⭐⭐⭐ 개발용 |
| Custom Webhook                 | ✅              | 별도 Bridge 필요    | 공식 Channel API       | ⭐⭐⭐⭐⭐        |
| Permission Relay               | ✅              | 동급 Native 미확인  | 공식 Channel 기능      | ⭐⭐⭐⭐⭐        |
| Claude Remote Control          | ✅              | 해당 없음           | 공식                   | ⭐⭐⭐⭐⭐        |
| Codex Mobile/Remote            | 해당 없음      | ✅                   | OpenAI 공식            | ⭐⭐⭐⭐⭐        |
| Claude-to-IM-skill             | ✅              | ✅                   | 서드파티               | ⭐⭐⭐⭐½        |
| OpenAB                         | ❌/주목적 아님  | ✅                   | 서드파티               | ⭐⭐⭐⭐         |
| agent-bridge                   | ✅              | ✅                   | 실험적 Bridge          | ⭐⭐⭐⭐         |
| codex-claude-bridge            | ✅              | ✅                   | 실험적·비대칭          | ⭐⭐⭐½         |
| Slack native Channel community | ✅              | —                   | 서드파티               | ⭐⭐⭐⭐         |
| WhatsApp Channel               | ✅              | —                   | 서드파티               | ⭐⭐⭐½         |

위 분류는 현재 Anthropic 공식 Channels 지원 목록, OpenAI Codex의 미해결 Inbound Notification/Telegram 기능 요청, 각 커뮤니티 프로젝트의 실제 README를 교차 확인한 결과입니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 31. 가장 강력 추천 조합

## A. Claude Code만 휴대폰에서 사용

```text
Claude Code
+
공식 Telegram Channel
+
Permission Relay
+
Sender Allowlist
```

주소:

```text
https://code.claude.com/docs/en/channels
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/telegram
https://t.me/BotFather
```

**가장 추천합니다.** 공식 기능이고 설치 난도가 비교적 낮습니다. ([Claude](https://code.claude.com/docs/en/channels))

------

## B. Claude Code를 팀 Discord에서 사용

```text
Claude Code
+
공식 Discord Channel
+
Pairing / Allowlist
+
Permission Relay
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/discord
https://discord.com/developers/applications
```

공식 지원입니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/discord))

------

## C. Claude Code + Codex 둘 다 Telegram에서 조종

```text
Claude-to-IM-skill
https://github.com/op7418/Claude-to-IM-skill
https://github.com/op7418/Claude-to-IM
```

**Claude Code와 Codex를 같은 메신저 시스템으로 다루려면 현재 가장 먼저 검토할 만한 서드파티 후보입니다.** ([GitHub](https://github.com/op7418/Claude-to-IM-skill))

------

## D. Claude Code와 Codex가 서로 대화·협업

```text
agent-bridge
```

또는:

```text
codex-claude-bridge
```

주소:

```text
https://github.com/raysonmeng/agent-bridge
https://github.com/abhishekgahlot2/codex-claude-bridge
```

첫 번째는 Codex App Server와 Claude Channel을 연결하고, 두 번째는 Claude Push + Codex MCP Tool 구조를 사용합니다. ([GitHub](https://github.com/raysonmeng/agent-bridge?utm_source=chatgpt.com))

------

## E. GitHub/CI/모니터링 이벤트를 Claude가 자동 처리

```text
Custom Webhook Channel
https://code.claude.com/docs/en/channels-reference
https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev
```

예:

```text
GitHub CI 실패
↓
Webhook
↓
Claude Code Channel
↓
Claude가 로그 읽기
↓
문제 분석
↓
코드 수정
↓
테스트
↓
Telegram으로 결과 통보
```

공식 Channel Reference가 Webhook Receiver 구현 자체를 예제로 제공합니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

------

# 32. 최종 추천 URL만 압축

## 최우선 공식

```text
https://code.claude.com/docs/en/channels
https://code.claude.com/docs/en/channels-reference
https://code.claude.com/docs/en/mcp
https://code.claude.com/docs/en/plugins-reference

https://github.com/anthropics/claude-code
https://github.com/anthropics/claude-plugins-official
https://github.com/anthropics/claude-plugins-community
```

## 공식 Channel

```text
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/telegram
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/discord
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/imessage
https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/fakechat
```

## Custom Channel 개발

```text
https://code.claude.com/docs/en/channels-reference

https://github.com/modelcontextprotocol/typescript-sdk
https://modelcontextprotocol.io/

https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev
https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev

https://bun.sh/
https://nodejs.org/
https://deno.com/
```

## Telegram / Discord 원천

```text
https://core.telegram.org/bots/api
https://t.me/BotFather

https://discord.com/developers/applications
https://docs.discord.com/developers/intro
https://docs.discord.com/developers/guides/bots
```

## Claude 원격·Slack

```text
https://code.claude.com/docs/en/remote-control
https://claude.ai/code
https://code.claude.com/docs/en/slack
```

## Codex 공식

```text
https://github.com/openai/codex
https://openai.com/index/work-with-codex-from-anywhere/
https://developers.openai.com/codex/remote-connections
https://github.com/openai/codex/blob/main/codex-rs/docs/codex_mcp_interface.md
```

## Codex Channels 관련 현재 기능 요청

```text
https://github.com/openai/codex/issues/15299
https://github.com/openai/codex/issues/17543
https://github.com/openai/codex/issues/18056
https://github.com/openai/codex/issues/21166
```

## Claude + Codex 메신저 Bridge

```text
https://github.com/op7418/Claude-to-IM-skill
https://github.com/op7418/Claude-to-IM

https://github.com/xx025/openab
https://github.com/NathanZane/codex-mobile

https://github.com/raysonmeng/agent-bridge
https://github.com/abhishekgahlot2/codex-claude-bridge
```

## 추가 Channel

```text
https://github.com/jeremylongshore/claude-code-slack-channel
https://github.com/Rich627/whatsapp-claude-plugin
https://github.com/RichardAtCT/claude-code-telegram
```

## 관련 GitHub Issue / 문제 추적

```text
https://github.com/anthropics/claude-plugins-official/issues/1680
https://github.com/anthropics/claude-plugins-official/issues/1448
https://github.com/anthropics/claude-code/issues/37342
https://github.com/anthropics/claude-plugins-official/issues/1292
```

------

# 33. 계속 최신 Channels 프로젝트를 찾기 위한 검색 주소

```text
https://github.com/search?q=%22Claude+Code+Channels%22&type=repositories
https://github.com/search?q=%22claude%2Fchannel%22&type=code
https://github.com/search?q=%22notifications%2Fclaude%2Fchannel%22&type=code

https://github.com/search?q=claude+code+telegram+channel&type=repositories
https://github.com/search?q=claude+code+discord+channel&type=repositories
https://github.com/search?q=claude+code+slack+channel&type=repositories
https://github.com/search?q=claude+code+whatsapp+channel&type=repositories

https://github.com/search?q=codex+telegram&type=repositories
https://github.com/search?q=codex+discord&type=repositories
https://github.com/search?q=codex+channel+mcp&type=repositories

https://github.com/anthropics/claude-plugins-official/issues
https://github.com/anthropics/claude-code/issues
https://github.com/openai/codex/issues
```

------

# 34. 최종 검증 결과

이번 조사에서 특히 오류가 발생하기 쉬운 부분을 다시 교차검증했습니다.

**첫째**, 현재 공식 Claude Channels는 **Telegram + Discord + iMessage**, 개발용 fakechat이라는 점을 Anthropic 최신 공식 문서와 현재 GitHub 디렉터리를 양쪽에서 확인했습니다. ([Claude](https://code.claude.com/docs/en/channels))

**둘째**, `Slack`이라는 단어가 있다고 무조건 Claude Code Channels라고 분류하지 않았습니다. Anthropic의 **Claude Code in Slack은 별도의 Cloud/Web Session 기능**이고, `claude-code-slack-channel`은 별도의 커뮤니티 Channel 프로젝트입니다. ([Claude](https://code.claude.com/docs/en/slack))

**셋째**, WhatsApp 프로젝트는 개발자가 Marketplace 승인이라고 설명하지만 현재 Anthropic 공식 `external_plugins` 디렉터리에서는 확인되지 않기 때문에 **기본 공식 Channel이 아닌 서드파티/별도 Marketplace**로 분류했습니다. ([GitHub](https://github.com/Rich627/whatsapp-claude-plugin?utm_source=chatgpt.com))

**넷째**, Claude Code Channel Plugin을 Codex에도 그대로 설치된다고 잘못 설명하지 않았습니다. 현재 OpenAI Codex 저장소에는 **Inbound MCP Push와 Telegram 공식 Plugin을 요구하는 Open Feature Request가 존재**하므로 Codex는 별도 Bridge/SDK/App Server 또는 공식 Mobile/Remote 구조를 사용해야 합니다. ([GitHub](https://github.com/openai/codex/issues/15299?utm_source=chatgpt.com))

**다섯째**, 일반 MCP, Channels, Remote Control, Claude Code in Slack을 서로 다른 기능으로 분리했습니다. Anthropic 공식 문서에서도 이 네 방식을 별도로 비교합니다. ([Claude](https://code.claude.com/docs/en/channels))

**여섯째**, 실제 설치 여부뿐 아니라 Allowlist, Permission Relay, Prompt Injection, Preview 상태, 현재 공개 Issue까지 확인했습니다. 특히 Channel은 외부에서 현재 Claude 세션으로 직접 입력을 넣을 수 있으므로 **Sender-ID Allowlist가 선택사항이 아니라 핵심 보안 요구사항**입니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

따라서 **2026년 8월 9일 현재 가장 안정적인 시작점은 `공식 Telegram Channel` 또는 `공식 Discord Channel`**, **Claude Code와 Codex를 동시에 메신저에서 다루려면 `Claude-to-IM-skill`**, **Claude와 Codex 자체를 서로 연결하려면 `agent-bridge` 계열**, **사내 시스템·CI·Webhook까지 연결하려면 공식 `Channels Reference + Custom MCP Channel`** 순으로 보는 것이 가장 정확합니다. ([Claude](https://code.claude.com/docs/en/channels))

이 분야는 아직 **Research Preview + 빠르게 바뀌는 Codex/Claude 생태계**라서 “인터넷상 존재하는 프로젝트를 영구적으로 100% 누락 없이 보장”하는 것은 불가능하지만, 이번 답변은 현재 공식 문서·공식 소스·현재 GitHub 디렉터리·Open Issue·주요 실제 구현체를 서로 비교해 **공식 / 준공식·Marketplace / 서드파티 / 실험적 Bridge / 별도 기능**을 분리한 현재 기준 전수형 목록입니다. ([Claude](https://code.claude.com/docs/en/channels))

원하시면 이 분야는 변화가 빨라서 **Claude Code Channels에 Telegram·Discord·iMessage 외 새 공식 채널이 추가되거나 Codex가 Native Channels를 지원하기 시작할 때 확인하도록 모니터링**할 수도 있습니다.
