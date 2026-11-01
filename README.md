# tax-review-pro: 세무 검토 스킬

Claude에게 세무 질문을 하면 **걸리는 세목부터 판정하고, 법령·예규 원문을 직접 열어, 문장마다 클릭 가능한 근거 링크를 붙여** 답하게 하는 업무 지시서(Claude 스킬)입니다. 초안을 쓴 뒤 **독립 검토자 1명**이 원문과 다시 대조합니다.

> **반드시 읽어 주세요.** 이 스킬이 내는 답변은 AI가 공개된 법령·예규를 조회해 만든 **검토 참고자료**입니다. 틀릴 수 있습니다. 그대로 믿고 신고하거나 의사결정에 쓰지 마시고, 실제 적용 전에 반드시 세무 전문가(공인회계사·세무사)와 상담하세요. 모든 답변 끝에 같은 문구가 붙어 나옵니다.

만든 사람: NuttyCPA · 라이선스: [CC BY-NC 4.0](LICENSE) (출처 표시, 비영리 목적만 재배포 가능)

## 답변은 이렇게 나옵니다

1. 사실관계
2. 쟁점사항
3. 검토결과: 문장마다 `(근거: [법인세법 제18조의4 제1항](https://www.law.go.kr/법령/법인세법/제18조의4))`처럼 클릭하면 조문이 열리는 근거
4. 검토내용: 원문 문구 인용과 근거
5. 확인 필요한 사항
6. 근거 URL 표

맨 끝에 `검토자 점검: 지적 N건 중 반영 N건`과 단서 문구가 붙습니다.

## 먼저 알아 두실 것: 세 가지 도구

이 스킬은 Claude가 법령과 예규 **원문**을 직접 열어 보게 합니다. 원문을 여는 데 아래 세 가지가 쓰입니다.

**1. 법제처 Open API (인증키)**
- 법제처가 법령·판례·해석례 데이터를 프로그램으로 꺼내 쓰도록 여는 공식 창구입니다. [국가법령정보 공동활용](https://open.law.go.kr/LSO/openApi/guideList.do)에서 무료로 신청합니다.
- 신청하면 **인증키(OC)**를 받습니다. 아래 두 도구가 모두 이 키로 법제처에 접속합니다. 키 하나로 둘 다 씁니다.

**2. korean-law MCP (국가법령 커넥터)**
- 법제처 Open API를 Claude가 쓸 수 있게 이어 주는 공개 오픈소스입니다([GitHub](https://github.com/chrisryugj/korean-law-mcp)).
- 법령 검색, 조문 원문, 판례·조세심판례·법령해석례, 별표·서식, 국세청 예규 목록을 조회합니다.
- claude.ai·Claude 데스크톱 앱에서는 **커넥터**로, Claude Code에서는 **플러그인**으로 붙입니다. 이 스킬의 **필수** 준비물입니다.

**3. lawkit MCP (선택, Claude Code 전용)**
- korean-law MCP를 보완하는 도구입니다([GitHub](https://github.com/mastercpakim-ui/lawkit)).
- 국세청 예규를 제목뿐 아니라 **본문까지** 검색해 질의 사실관계와 회신을 읽어 옵니다. 또 법·시행령·시행규칙을 각각 키워드로 훑어, 조문 번호를 몰랐던 가산세·서식 의무까지 찾습니다.
- 내 PC에서 도는 도구라 **Claude Code(또는 데스크톱 앱 Code 탭)에서만** 붙일 수 있습니다. Python 3.10 이상이 필요하고, 인증키는 korean-law 설정의 키를 그대로 씁니다.

**어떤 조합이 필요한가요?**
- claude.ai·데스크톱 앱 채팅에서 쓰는 분: 1(인증키) + 2(커넥터). 아래 「claude.ai·데스크톱 앱에서 설치」를 따르세요.
  - **알아 두세요**: 이 스킬의 핵심은 초안을 쓴 뒤 **독립 검토자(별도 에이전트) 1명**이 원문과 대조하는 단계입니다. claude.ai 채팅 앱에서는 독립 에이전트를 띄울 수 없어, 같은 대화 안에서 역할만 바꿔 점검합니다. 초안을 쓴 맥락을 그대로 가진 채 검토하므로 **검토 성능이 떨어질 수 있습니다.** 답변 첫 줄에 `검토자: 같은 대화에서 수행`이 보이면 이 경우입니다. 중요한 건은 Claude Code에서 다시 돌리거나 근거 링크를 직접 열어 확인하세요.
- Claude Code에서 쓰는 분: 1 + 2(플러그인) + 3(lawkit, 권장). 아래 「Claude Code에서 설치」를 따르세요.

---

## claude.ai·데스크톱 앱에서 설치

무료 플랜에서도 쓸 수 있습니다. 무료 플랜은 직접 추가하는 커넥터가 1개까지라, 국가법령 커넥터를 그 1개로 쓰시면 됩니다.

### 1. 법제처 Open API 인증키 받기

1. [법제처 Open API 신청 페이지](https://open.law.go.kr/LSO/openApi/guideList.do)에 들어갑니다.
2. 회원가입 후 로그인합니다.
3. **Open API 사용 신청**을 누르고 신청서를 작성합니다.
4. 발급된 **인증키(OC)**를 메모해 둡니다. (예: `honggildong`)

> 발급까지 걸리는 시간은 신청 시점마다 다를 수 있습니다. 미리 신청해 두세요.

### 2. Claude 앱에 국가법령 커넥터 연결

1. [claude.ai](https://claude.ai) 또는 Claude 데스크톱 앱에 로그인합니다.
2. **Customize > Connectors**로 갑니다.
3. **+ Add** → **Add custom connector**를 누릅니다.
4. 이름은 `korean-law`, 주소는 아래를 붙여 넣고 `honggildong` 자리에 **1단계에서 받은 본인 인증키**를 넣습니다.

```
https://mcp.gomdori.app/law?oc=honggildong
```

5. **Continue** → **Add**를 눌러 마칩니다.
6. 채팅 화면의 **+** 버튼 → **Connectors**에서 `korean-law`가 켜져 있는지 확인합니다.

> 주소를 잘못 넣었으면 커넥터를 지우고 다시 추가해야 합니다.

### 3. 스킬 받기

아래 링크를 누르면 최신판 `tax-review-pro.zip`이 바로 받아집니다.

**[tax-review-pro.zip 받기](https://github.com/mastercpakim-ui/tax-review-pro/releases/latest/download/tax-review-pro.zip)**

> **주의**: 이 페이지 위쪽의 초록색 **Code → Download ZIP** 버튼으로 받은 파일은 폴더 구조가 달라 스킬로 올라가지 않습니다. 반드시 위 링크로 받으세요.

### 4. 스킬 올리기

1. **Settings > Capabilities**에서 **Code execution and file creation**을 켭니다.
2. **Customize > Skills**로 갑니다.
3. **+** → **Create skill** → **Upload a skill**을 누르고 받은 `tax-review-pro.zip`을 선택합니다.
   - zip을 풀지 말고 그대로 올립니다.

### 5. 써 보기

새 채팅에서 이렇게 물어보세요.

```
내국법인이 2026년에 해외 자회사(지분 30%, 5년 보유)로부터 배당을 받았습니다.
익금불산입률은 얼마인가요? 세무 검토해 주세요.
```

답변 첫 줄에 `국가법령 커넥터: 사용함`이 보이고, 검토결과·검토내용의 근거를 클릭해 조문 원문이 열리면 제대로 연결된 것입니다. `미연결`이 보이면 2단계를 다시 확인하세요.

### 업데이트

위 링크로 새로 받은 뒤, **Customize > Skills**에서 기존 `tax-review-pro`를 지우고 다시 올립니다.

> 예전 이름 `tax-review-lite`로 설치하셨다면, 그 스킬을 지우고 `tax-review-pro.zip`을 새로 올리세요. 내용은 이어지는 같은 스킬이고 이름만 바뀌었습니다.

---

## Claude Code에서 설치

1. 국가법령 플러그인을 설치합니다. 설치 중에 법제처 인증키를 묻습니다.

```
/plugin marketplace add chrisryugj/korean-law-mcp
/plugin install korean-law@korean-law-marketplace
```

2. Claude Code(또는 데스크톱 앱 Code 탭의 로컬 세션)에 아래 한 줄을 붙여 넣습니다. 설치는 Claude가 이 README의 「Claude가 할 일」대로 처리합니다.

```
https://github.com/mastercpakim-ui/tax-review-pro 설치해줘
```

3. (권장) 국세청 예규를 본문까지 검색하려면 lawkit도 붙입니다. Python 3.10 이상이 필요합니다.

```
https://github.com/mastercpakim-ui/lawkit 설치해줘
```

4. Claude Code를 완전히 종료했다가 다시 켜고, `/tax-review-pro`로 부르거나 세무 질문을 하면 됩니다.

### 업데이트

```
tax-review-pro 업데이트해줘
```

---

## 꼭 지켜 주세요

- **결과는 검토 참고자료입니다.** 최종 판단과 고객 안내는 직접 하세요. 답변의 `확인 필요` 표시는 원문을 끝까지 확인하지 못했다는 뜻입니다.
- **채팅 앱에서는 독립 검토자가 없습니다.** 같은 대화에서 점검하므로 검토 성능이 떨어질 수 있습니다. 결론을 떠받치는 근거 링크는 직접 열어 보세요.
- **고객 이름, 주민등록번호, 계좌번호는 넣지 마세요.** 가명이나 "A법인", "대표이사 갑"처럼 바꿔서 물어보세요.
- 국가법령 커넥터는 법제처 Open API를 쓰는 공개 오픈소스입니다(korean-law-mcp). 법제처 쪽 사정으로 조회가 안 되는 날이 있을 수 있습니다.

## 참고한 공식 안내

- [Claude 스킬 사용법 (Claude Help Center)](https://support.claude.com/en/articles/12512180)
- [커스텀 커넥터 추가 (Claude Help Center)](https://support.claude.com/en/articles/11175166)
- [korean-law-mcp (GitHub)](https://github.com/chrisryugj/korean-law-mcp)

---

## Claude가 할 일 (설치·업데이트 절차)

> **Claude에게**: 각 Step은 진단 → 부족할 때만 조치 → 검증 순서로 한다. 무엇을 바꾸는지 한 줄로 알리고 진행한다. 실패하면 에러 원문과 함께 보고한다.

**Step 1. 스킬 폴더 위치 확인**
- 사용자 스킬 폴더: Windows `%USERPROFILE%\.claude\skills\`, macOS·Linux `~/.claude/skills/`. 없으면 만든다.

**Step 2. 받기**
- 이 저장소의 최신 Release 자산 `tax-review-pro.zip`을 받아 임시 폴더에 푼다. 받을 수 없으면 저장소를 `git clone`하고 그 안의 `tax-review-pro/` 폴더를 쓴다.

**Step 3. 설치·교체**
- 스킬 폴더에 예전 이름 `tax-review-lite`가 있으면, 이름만 바뀐 같은 스킬이라고 알리고, 스킬 폴더 밖 `.claude/skill-backups/tax-review-lite.bak-<날짜>`로 옮겨 둔다고 말한 뒤 사용자가 동의하면 옮긴다. 스킬 폴더 안에서 이름만 바꾸면 두 스킬이 함께 불릴 수 있다.
- 스킬 폴더에 `tax-review-pro`가 이미 있으면, 그 `SKILL.md` 머리말의 `metadata.version`과 새 판을 비교해 사용자에게 알린다. 교체 전에 기존 폴더를 스킬 폴더 밖 `.claude/skill-backups/tax-review-pro.bak-<날짜>`로 옮겨 둔다고 말하고, 사용자가 동의하면 교체한다.
- 없으면 `tax-review-pro/` 폴더를 그대로 복사한다. 폴더 이름은 바꾸지 않는다(스킬 이름과 같아야 한다).

**Step 4. 검증**
- `<스킬 폴더>/tax-review-pro/SKILL.md`가 있고, 머리말의 `name`이 `tax-review-pro`인지 확인한다.
- korean-law MCP(플러그인 또는 MCP 등록)가 있는지 확인한다. 없으면 README 「Claude Code에서 설치」 1번을 안내한다.
- 사용자에게 Claude Code를 완전히 종료했다가 다시 켜라고 안내한다.
