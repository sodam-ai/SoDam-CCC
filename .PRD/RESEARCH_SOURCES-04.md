# RESEARCH_SOURCES-04

# ✅ 클로드코드 Channels 실제 사용 방법

**설치는 이미 완료했고 Telegram 페어링까지 끝났다는 기준**으로 설명하겠습니다.

핵심은 아주 간단합니다.

```text
PC에서 Claude Code Channels 실행
        ↓
PC 터미널은 켜둠
        ↓
스마트폰 Telegram에서 봇에게 자연어 명령
        ↓
내 PC의 Claude Code가 실제 프로젝트에서 작업
        ↓
결과가 Telegram으로 돌아옴
```

Channels는 새 Claude 세션을 클라우드에서 만드는 기능이 아니라, **현재 내 PC에서 실행 중인 Claude Code 세션으로 Telegram 메시지를 밀어 넣는 기능**입니다. 따라서 내 PC의 실제 파일, 프로젝트, 터미널 도구 등을 그대로 사용하는 구조입니다. ([Claude](https://code.claude.com/docs/en/channels))

------

## 1. 사용할 프로젝트 폴더에서 실행

예를 들어 작업할 프로젝트가:

```text
D:\Projects\my-web
```

이라면 PowerShell에서:

```powershell
cd D:\Projects\my-web
```

그리고 일반적인:

```powershell
claude
```

가 아니라 Telegram Channel을 활성화해서 실행합니다.

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

이 상태로 **PowerShell/Claude Code 창을 계속 켜둬야 합니다.**

Channels 메시지는 실행 중인 세션에만 도착하기 때문에 Claude Code를 종료하면 Telegram에서 메시지를 보내도 해당 세션에서 처리할 수 없습니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 2. 이제 PC에서 입력할 필요 없음

스마트폰에서 아까 만든 **Telegram Claude Bot**을 엽니다.

그리고 그냥 평소 Claude Code에 입력하던 것처럼 자연어로 명령하면 됩니다.

예:

```text
현재 프로젝트 전체 구조 확인해줘.
```

그러면:

```text
Telegram
↓
Channel Plugin
↓
내 PC의 Claude Code
↓
D:\Projects\my-web 분석
↓
Telegram으로 답변
```

이렇게 동작합니다. 공식 Telegram 플러그인은 들어온 메시지를 실행 중인 Claude Code 세션으로 전달하고, Claude가 `reply` 도구를 이용해 같은 Telegram 채팅으로 결과를 돌려보냅니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 3. 개발 작업도 그대로 명령하면 됨

예를 들어 Telegram에서:

```text
현재 프로젝트 전체를 검사하고 오류가 있는지 찾아줘.
```

또는:

```text
로그인 기능이 제대로 작동하는지 확인해줘.
문제가 있으면 원인을 찾고 수정해줘.
```

또는:

```text
npm run build 실행해서 빌드 오류가 있는지 확인해줘.
오류가 있으면 수정하고 다시 빌드해줘.
```

또는:

```text
현재 웹페이지 UI를 확인하고
모바일 반응형 문제가 있으면 수정해줘.
```

처럼 사용할 수 있습니다.

즉 **Telegram용 특별한 명령어 문법을 외울 필요가 없습니다.** Channels의 목적 자체가 Telegram·Discord 같은 외부 채팅을 현재 Claude Code 세션의 입력 통로로 사용하는 것입니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 4. 연속해서 대화해도 됨

예를 들어 Telegram에서 처음:

```text
현재 로그인 오류 원인 찾아줘.
```

Claude가 조사한 후 답하면:

```text
그럼 가장 안전한 방법으로 수정해줘.
```

그리고 다시:

```text
수정했으면 테스트까지 해줘.
```

이런 식으로 **같은 Claude Code 세션을 계속 이어서 사용할 수 있습니다.**

Channels는 이벤트를 이미 열려 있는 세션으로 보내기 때문에, 그 세션에서 진행하던 작업 맥락을 활용할 수 있습니다. ([Claude](https://code.claude.com/docs/en/channels))

------

# 5. 실제로 이런 식으로 쓰면 편함

### 밖에서 프로젝트 상태 확인

Telegram:

```text
현재 프로젝트 상태 알려줘.
최근 변경된 파일과 아직 해결하지 못한 문제도 알려줘.
```

### 오류 수정

```text
현재 발생하고 있는 오류를 찾아줘.
근본 원인을 확인하고 안전하게 수정한 다음 다시 테스트해줘.
```

### 빌드 검사

```text
전체 빌드 실행해줘.
오류가 나오면 원인 분석 → 수정 → 재빌드까지 진행해줘.
```

### Git 상태 확인

```text
현재 git 상태 확인하고
변경된 파일이 무엇인지 쉽게 설명해줘.
```

### 개발 계속하기

```text
아까 작업하던 기능 계속 구현해줘.
```

### 완료 상황 확인

```text
현재까지 완료된 작업과 남은 작업을 알려줘.
```

이런 것이 Channels의 가장 일반적인 사용 방식입니다. Claude Code 자체는 코드베이스 읽기, 파일 수정, 명령 실행 등의 작업을 수행하고, Channels가 그 세션으로 외부 메시지를 전달합니다. ([Claude Platform Docs](https://docs.anthropic.com/en/docs/claude-code/overview?utm_source=chatgpt.com))

------

# 6. 이미지도 Telegram으로 보낼 수 있음

이 기능이 상당히 유용합니다.

예를 들어 스마트폰에서 오류 화면 스크린샷을 Telegram 봇에게 보내고:

```text
이 오류 화면 확인해줘.
현재 프로젝트에서 원인을 찾아서 수정해줘.
```

라고 하면 됩니다.

공식 Telegram 플러그인은 들어온 사진을 PC의:

```text
~/.claude/channels/telegram/inbox/
```

에 다운로드하고, 그 경로를 Claude Code에 전달합니다. 따라서 Claude가 해당 이미지를 읽어서 작업에 사용할 수 있습니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 7. 원본 이미지는 「파일」로 보내는 것이 좋음

Telegram에서 그냥 사진으로 보내면 Telegram이 이미지를 압축할 수 있습니다.

따라서 UI 오류 화면처럼 세부 글자가 중요하면:

```text
사진 전송
```

보다:

```text
파일로 보내기
```

가 더 좋습니다.

Anthropic의 공식 Telegram 플러그인 문서에서도 원본 품질이 필요한 경우 사진 대신 **Send as File** 방식으로 보내도록 안내합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 8. Claude Code가 파일을 Telegram으로 보내는 것도 가능

반대 방향도 됩니다.

예를 들어 Telegram에서:

```text
분석 결과를 markdown 파일로 작성하고
완료되면 이 Telegram으로 파일도 보내줘.
```

라고 요청할 수 있습니다.

공식 Telegram Channel에는 `reply` 도구가 있고 첨부파일을 함께 전송할 수 있습니다.

현재 공식 플러그인 기준:

```text
JPG
PNG
GIF
WebP
→ Telegram 사진으로 전송

그 외 파일
→ Telegram 문서로 전송
```

파일당 최대 **50MB**까지 지원합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

따라서 예를 들어:

```text
검사 결과를 SECURITY_REPORT.md로 만들어서 보내줘.
```

같은 사용이 가능합니다.

------

# 9. 답변 중간 상태도 표시 가능

Telegram 플러그인에는 단순 답장만 있는 게 아니라:

```text
reply
react
edit_message
```

도구가 있습니다.

Claude가 Telegram 메시지에 반응을 달거나, 자신이 보낸 메시지를 수정할 수도 있습니다. 예를 들어 작업 중 메시지를 보낸 뒤 완료 결과로 수정하는 형태가 가능합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

Telegram에서 메시지가 들어오면 봇이 작업하는 동안:

```text
botname is typing...
```

같은 입력 중 표시도 자동으로 나타납니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 10. Telegram 과거 채팅 전체를 읽는 기능은 아님

이 부분이 중요합니다.

예를 들어 어제 Telegram에서:

```text
A 기능 만들어줘.
```

라고 했는데 오늘 새로운 Claude Code 세션을 시작해서:

```text
어제 얘기했던 거 계속해줘.
```

라고 하면 Telegram 봇이 **Telegram의 과거 전체 대화를 검색해서 가져오는 구조는 아닙니다.**

Telegram Bot API를 사용하는 공식 플러그인에는:

```text
fetch_messages
```

같은 과거 메시지 검색 기능이 없습니다.

봇은 **새로 들어오는 메시지를 받을 뿐**입니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

따라서 새 Claude Code 세션이라면:

```text
어제 로그인 기능 작업했어.
현재 프로젝트 확인하고 이어서 진행해줘.
```

처럼 프로젝트 자체를 다시 확인하도록 시키는 것이 좋습니다.

------

# 11. Claude Code 슬래시 명령어와 Telegram 명령은 구분

PC Claude Code에서 사용하는:

```text
/clear
/compact
/plugin
/mcp
/resume
```

같은 것은 **Claude Code 터미널 인터페이스용 명령**입니다.

Telegram Channel의 기본 사용 방식은 이런 슬래시 명령을 원격으로 조작하는 것이 아니라:

```text
현재 프로젝트 검사해줘.
이 오류 수정해줘.
```

같은 **자연어 메시지를 Claude 세션으로 보내는 것**입니다. Channels는 외부 시스템의 메시지를 Claude Code 세션에 이벤트로 주입하는 구조입니다. ([Claude](https://code.claude.com/docs/en/channels-reference))

그래서 Telegram에서:

```text
/compact
```

같은 것을 보내서 PC Claude Code의 `/compact`를 실행한다고 생각하면 안 됩니다.

------

# 12. 작업 중 권한 승인이 나오면?

여기가 Channels 사용할 때 가장 중요한 제한 중 하나입니다.

예를 들어 Claude가:

```text
파일 수정
명령 실행
특정 MCP 도구 실행
```

등을 하려는데 사용자 승인이 필요한 경우가 있습니다.

그러면 Claude Code 세션이 **사용자 승인을 기다리면서 멈출 수 있습니다.** 공식 Channels는 `permission relay` 기능을 지원하는 Channel 서버라면 원격으로 승인/거절 요청을 전달할 수 있도록 설계되어 있습니다. ([Claude](https://code.claude.com/docs/en/channels))

다만 공식 Telegram 플러그인의 현재 README에는 일반 사용자용 원격 permission 승인 절차가 별도로 설명되어 있지 않습니다. 따라서 Telegram에 승인 화면이 오지 않는 경우에는 **PC의 Claude Code 터미널에서 직접 승인해야 한다고 생각하는 것이 안전합니다.** ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/README.md))

------

# 13. 그렇다고 이걸 쓰면 안 됨

원격으로 편하게 쓰겠다고:

```powershell
claude --dangerously-skip-permissions
```

또는 Channels와 함께 강제 무승인 모드로 운영하는 것은 권장하지 않습니다.

공식 문서도 `--dangerously-skip-permissions`가 대부분의 권한 확인을 우회하기 때문에 **신뢰할 수 있는 환경에서만 사용하라고 명시**하고 있습니다. ([Claude](https://code.claude.com/docs/en/channels))

특히 Channels는 Telegram 메시지가 실제 PC의 Claude Code 작업으로 연결되므로 더 조심해야 합니다.

------

# 14. Telegram 그룹에서도 사용 가능

개인 DM뿐 아니라 Telegram 그룹에도 Claude Bot을 넣을 수 있습니다.

하지만 기본적으로 **그룹 사용은 꺼져 있습니다.**

예를 들어 그룹 ID를 허용:

```text
/telegram:access group add -1001654782309
```

기본값에서는 해당 그룹에서:

```text
@내Claude봇 이것 확인해줘
```

처럼 봇을 멘션하거나 봇 메시지에 답장해야 작동합니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/ACCESS.md))

개인용이면 굳이 그룹 설정할 필요 없이 **Telegram Bot과 1:1 DM으로 사용하는 것이 가장 간단합니다.**

------

# 15. 메시지를 받았다는 표시도 설정 가능

Telegram 메시지가 Claude Code에 전달됐는지 바로 알고 싶으면 수신 반응을 설정할 수 있습니다.

Claude Code에서:

```text
/telegram:access set ackReaction 👀
```

설정하면 Telegram 메시지를 받았을 때 봇이 👀 반응을 붙일 수 있습니다. ([GitHub](https://github.com/anthropics/claude-plugins-official/blob/main/external_plugins/telegram/ACCESS.md))

예:

```text
나: 현재 프로젝트 전체 검사해줘.

        👀
```

그러면 최소한:

> “내 메시지가 Claude Code까지 전달됐구나.”

를 알 수 있어서 원격 사용 시 꽤 편리합니다.

------

# 16. 제가 권장하는 실제 사용법

사용자처럼 **비개발자가 Claude Code를 AI로 직접 개발시키는 방식**이라면 Telegram에서 단순히:

```text
버그 고쳐줘.
```

보다는 다음처럼 보내는 게 좋습니다.

```text
현재 프로젝트 상태부터 확인해줘.

현재 구현된 기능에 영향을 주지 않도록 하고,
문제의 근본 원인을 먼저 분석해.

문제가 확인되면
원인 분석 → 수정 → 테스트 → 빌드 → 회귀 테스트까지 진행해줘.

위험하거나 되돌리기 어려운 작업은
실행하기 전에 나에게 알려줘.
```

그러면 PC 앞에 없더라도 Claude Code에게 비교적 안전하고 명확하게 작업을 맡길 수 있습니다.

------

# ⭐ 가장 간단한 사용 흐름

처음 한 번 설치와 페어링을 끝냈다면 **앞으로는 사실 이것뿐입니다.**

### PC

프로젝트 폴더에서:

```powershell
claude --channels plugin:telegram@claude-plugins-official
```

PC와 Claude Code 터미널을 켜둡니다. ([Claude](https://code.claude.com/docs/en/channels))

### 스마트폰

Telegram Bot에게:

```text
현재 프로젝트 상태 확인해줘.
```

↓

```text
로그인 기능 오류 있는지 확인해줘.
```

↓

```text
문제 있으면 수정하고 테스트까지 해줘.
```

↓

```text
빌드도 실행해서 최종 확인해줘.
```

↓

```text
완료한 내용을 쉽게 요약해서 알려줘.
```

**이게 Claude Code Channels의 실제 핵심 사용법입니다.**

```text
Telegram = 원격 명령창
Claude Code = 내 PC에서 실제 작업하는 AI
내 PC 프로젝트 = 실제 작업 대상
```

공식 Anthropic 문서도 Channels의 대표 사용 예를 **휴대폰의 Telegram/Discord에서 Claude에게 작업을 요청하고, 실제 작업은 사용자 PC의 기존 로컬 세션과 파일을 대상으로 수행하며 결과가 같은 채팅으로 돌아오는 방식**으로 설명합니다. ([Claude](https://code.claude.com/docs/en/channels))

참고로 **내가 원하는 것은 “Telegram 봇에게 명령하는 방식”보다 스마트폰에서 PC의 Claude Code 화면 자체를 그대로 이어서 조작하는 것**이라면 Channels보다 **Claude Code Remote Control**이 더 적합합니다. 두 기능은 목적이 다릅니다. ([Claude](https://code.claude.com/docs/en/mobile))

Channels는 아직 **research preview**라 동작과 명령 형식이 바뀔 수 있습니다. ([Claude](https://code.claude.com/docs/en/channels))

원하시면 Claude Code Channels의 공식 변경사항을 주기적으로 확인해서 **Telegram 기능·원격 승인·Windows 관련 기능이 추가되거나 변경될 때 알려드릴 수도 있습니다.**
