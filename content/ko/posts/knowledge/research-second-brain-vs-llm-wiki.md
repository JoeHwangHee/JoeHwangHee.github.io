+++
date = '2026-07-18T21:00:00+09:00'
draft = false
title = 'Second Brain vs LLM Wiki — 개발자를 위한 PKM 설계 비교 리포트'
summary = "인간이 다시 쓸 것을 전제한 Second Brain과, 에이전트가 읽고 유지할 것을 전제한 LLM Wiki를 축별로 비교한 리서치 리포트. 공통 병목인 「수집 이후의 재관여」를 누가 맡느냐가 개발자의 실질적 선택 축임을 정리한다."
tags = ['Second Brain']
+++

> **이 글의 근거와 검증** (2026-07-18 조사 · 2026-07-19 보충)
> - 이 리포트는 여러 각도의 웹 검색으로 소스를 모으고, 각 소스를 읽어 요약한 뒤, 그 요약에서 뽑아낸 주장들을 적대적으로 교차 검증하는 자동화 리서치 과정으로 작성했다.
> - 검증은 소스에서 추출한 "반증 가능한 주장" 45건을 대상으로 했고, 그중 강하게 살아남은 것은 2건뿐이다(본문에 "2/3 지지, 신중"으로 표기). 기각된 43건은 대부분 소스가 틀려서가 아니라, 추출 과정에서 주장이 과도하게 일반화됐기 때문이다.
> - 따라서 본문 서술의 주된 근거는 소스별 요약이며, 검증 결과는 문장의 확신 수위 표기와 7절(기각된 통념)에 반영되어 있다.
> - 7절에 나오는 반례 수치(citation omission 91.9%, LinkedIn 63% 개선 등)는 검증 과정에서 인용된 2차 자료로, 이 글에서 원문까지 직접 대조하지는 않았다.

## 1. 한 줄 결론

Second Brain은 "인간이 미래에 다시 쓸 것"을 전제로 actionability와 인간 판단 중심의 큐레이션을 설계한 시스템이고 [1][6][11], LLM Wiki는 "에이전트가 읽고 유지할 것"을 전제로 머신 가독성과 LLM 위임 유지보수를 설계한 markdown 지식베이스이며 [4][7][12], 수집된 실패 사례들이 공통으로 가리키는 병목 — 수집 이후의 지속적 재관여 — 를 인간이 맡을지 에이전트가 맡을지가 개발자의 실질적 선택 축이다 [3][10][15].

## 2. 두 개념 정의

### Second Brain (Tiago Forte, fortelabs.com)

Forte Labs 원전 기준으로 Second Brain은 정보 과부하를 해결하기 위한 외부 디지털 저장소로, 지식을 주제 분류가 아니라 행동 가능성(actionability) 기준으로 조직한다 [1]. 핵심 구성은 다음과 같다.

- **PARA**: Projects / Areas / Resources / Archives의 네 고정 범주로, 추상적 주제 영역이 아니라 현재의 목표와 commitment를 기준으로 정보를 배치한다. 특정 과제에 필요한 자료가 한곳에 모이도록 하는 인간 워크플로 중심 설계다 [6].
- **캡처와 증류**: 공명(resonant)하는 자료를 반복적으로 capture하고, progressive distillation로 층위화된 요약을 만들며, 기회가 될 때마다 다듬는다. 노트는 미래의 나를 위한 도구이자 창작 산출의 재료이지, 포괄적 레퍼런스 백과사전이 아니다 [1].
- **Progressive Summarization**: capture → bolding → highlighting → summarizing → remixing으로 이어지는 층위 압축 기법으로, 인간 중심의 발견가능성(discoverability)과 맥락 보존을 우선한다. PKM을 "지식을 시간을 건너 미래 상황으로 전달(forwarding knowledge through time)"하는 문제로 프레이밍한다 [11].

대표 도구로는 Obsidian과 Notion이 결부되어 언급된다 [5][7][10].

**자료가 얇은 지점**: 질문에 언급된 CODE(Capture-Organize-Distill-Express) 약어 자체는 수집 자료에 등장하지 않으며, capture·distill 요소만 [1]에서 확인된다. Zettelkasten도 [4]에서 "human-curated PARA/Zettelkasten"으로 스치듯 언급될 뿐 상세 자료가 없다. 또한 수집 자료에는 Forte 원전들의 발표 시점이 명시되어 있지 않다. (2026-07-19 보충: [1] 원문을 재확인한 결과 CODE 약어는 실제로 존재한다 — "CODE, which stands for Capture, Organize, Distill, and Express." 즉 위 '미등장'은 소스의 공백이 아니라 수집 발췌의 공백이었다.)

### LLM Wiki (Andrej Karpathy, GitHub Gist)

Karpathy의 gist가 정의하는 LLM Wiki는 LLM이 소스 문서로부터 요약·entity 페이지·상호참조를 증분적으로 종합·유지하는 영속적 markdown 지식베이스다 [4].

- **동작 구조**: ingest(신규 소스 통합), query(검색·종합), lint(일관성 검사)의 세 핵심 기능으로 운영되며, 인간은 입력을 큐레이션하고 LLM이 유지보수를 담당한다 [4]. DAIR.AI 해설은 같은 패턴을 ingest–compile–query–lint의 4단계 사이클로 기술한다 — 소스 간 단계 구분이 약간 다르다는 점은 그대로 밝혀 둔다 [4][12].
- **원본/위키 분리(신중)**: 불변의 raw sources("LLM이 읽되 절대 수정하지 않는 원본")와 LLM이 능동적으로 갱신하는 wiki 페이지를 구조적으로 분리한다는 주장이 있다. 이 주장은 검증에서 2/3 지지에 그쳤으므로 단정하지 않는다 [4].
- **머신 가독성 우선**: plain markdown과 일관된 템플릿으로 LLM이 분산 파일을 횡단해 답을 종합할 수 있게 하고, 수동 브라우징 대신 자연어 질의를 가능케 하며, 로컬 파일에 대한 직접 파일시스템 접근을 강조한다 [7]. [7]의 제목 자체가 Claude Code로 구축하는 방법을 다룬다.
- **개인 규모 운용**: 개인 규모에서는 vector database 없이 context window와 index 파일만으로 운용했다는 실무 보고가 있으나, 검증 결과 이는 약 100편 규모의 특정 사례에서 "retrieval에 충분"했다는 한정된 진술이다 [12].

**부속 맥락**: Karpathy 본인의 인간용 노트 실천인 append-and-review note는 폴더·태그·무거운 메타데이터를 거부하고 단일 파일 상단에 즉시 캡처, 시간에 따른 자연 침전, 주기적 인간 리뷰로 운영되는 미니멀 시스템이다 — LLM Wiki와는 별개의 실천이지만, 그의 설계 철학(급진적 단순함)을 보여준다 [2].

파생 구현으로 SamurAIGPT의 llm-wiki-agent(ingest 시 지식 추출·모순 감지·상호참조 유지) [9], Obsidian 생태계의 Smart Connections(local embeddings 기반 semantic search) [14]가 수집되었다.

**자료가 얇은 지점**: LLM Wiki 원전([2][4])의 작성 시점도 수집 자료에 명시되어 있지 않다. [13]만 URL 경로상 2026년 3월 게재로 추정 가능하다.

## 3. 핵심 차이 — 축별 비교

| 축 | Second Brain | LLM Wiki |
|---|---|---|
| 설계 철학 | 인간의 actionability·판단·창작 산출 중심. 미래의 나에게 지식을 전달하는 도구 [1][6][11] | 머신 가독성·에이전트 접근 최적화. LLM이 1차 독자이자 유지자라는 전제 [4][7] |
| 조직 단위·구조 | PARA 4범주(목표·commitment 기준) + 노트 단위의 층위 요약 [6][11] | markdown 파일 + entity 페이지 + 상호참조 + 일관 템플릿 [4][7]. 원본/위키 분리 구조는 2/3 지지로 신중히 [4] |
| 유지보수 주체 | 인간(기회주의적 다듬기, progressive distillation) [1][11] | 입력 큐레이션은 인간, 종합·일관성 유지(lint)는 LLM [4][12]. 단, 실구현의 "자율성"은 검증에서 제한됨(7절) [9] |
| 검색·활용 방식 | 사람이 미래 상황에서 찾아 쓰도록 발견가능성을 설계, 수동 브라우징·탐색 전제 [7][11] | 자연어 질의 → LLM이 분산 파일을 종합해 답변 [7]. query 산출을 위키에 재파일링 [12]. 임베딩 semantic search 변형도 존재 [14] |
| 도구 생태계 | Obsidian, Notion 등 노트 앱 [5][7][10] | plain markdown + 파일시스템 + Claude Code류 에이전트 [7], gist 스펙 [4], llm-wiki-agent [9], Smart Connections [14]. 한 저자는 Mem·MemoryOS를 "AI가 1차 독자" 계열로 소개(이해관계 고지 있는 저자, 신중) [8] |

축별 부연:

- **설계 철학**: 두 진영의 대비는 자료 전반에서 일관된다. Forte 계열은 "인간 워크플로 기반 우선순위" [6]와 "인간 판단 주도 큐레이션·맥락 보존" [11]을, Karpathy 계열은 "인간 내비게이션이 아니라 기계 가독성 중심 재구조화" [7]를 내세운다. 단 검증에서 "LLM Wiki는 인간이 읽을 수 없다"는 배타적 해석은 기각됐다 — 우선순위의 차이이지 인간 배제가 아니다(7절).
- **유지보수 주체**: 이 축이 가장 근본적 차이다. [4]는 "인간이 입력을 큐레이션하고 LLM이 유지보수"라는 역할 분담을 명시하고, [9]는 소스 추가 시마다 위키가 축적·갱신된다는 구현 주장을 편다. 반면 Second Brain의 progressive distillation은 인간의 반복 관여 그 자체가 방법론이다 [1][11].
- **검색·활용**: Smart Connections는 흥미로운 중간 지대다 — 노트 retrieval(local embeddings)과 LLM 실행을 의도적으로 분리하고, "connections의 전체 내용을 클립보드로 복사해 임의의 AI chat 컨텍스트로" 넘기는 수동 수출 워크플로를 쓴다는 주장이 있다(2/3 지지, 신중) [14].

## 4. Second Brain 장단점

### 장점

- **행동 연결성**: 자료가 현재 목표·과제 단위로 모여, 특정 작업에 필요한 것이 한곳에 있게 되고 인지 오버헤드 감소를 지향한다 [6].
- **창작 산출 파이프라인**: 공명 기준의 캡처와 층위 증류가 노트를 "미래의 나를 위한 도구·창작 재료"로 만든다 — 백과사전식 완결성을 요구하지 않는 점이 부담을 낮춘다 [1].
- **맥락 보존과 발견가능성의 균형**: Progressive Summarization은 압축하면서도 맥락적 의미를 유지해 미래 회수를 돕는 인간 중심 기법이다 [11].
- **판단 노동의 보존**: 직접 비교 자료는 아니지만, [13]이 경고하는 "판단 노동 없는 앎의 느낌" 리스크의 관점에서 보면, 인간이 직접 증류·조직하는 구조는 인지적 관여를 시스템 안에 남겨두는 설계에 가깝다 [13].

### 단점 (대부분 개인 사례·심리 기제 기반 — 스코프 주의)

- **Collector's fallacy**: 저장이 재방문을 압도하는 불균형이 생산성·진전의 착각을 만들고, 축적된 노트가 잠들어 있게 된다는 심리 기제가 Second Brain의 핵심 실패 모드로 지목된다 [15].
- **조직화의 자기목적화(개인 사례)**: 한 저자는 Obsidian/Notion으로 6개월·103시간을 들였으나 자체 평가 ROI -68%를 보고했다. 태깅·재구조화에 77시간, 실제 캡처에 14시간이 쓰였고, 본인 감사 기준 노트의 83%가 재방문되지 않았으며, "생산성으로 위장한 미루기 시스템"이 됐다고 결론지었다 [10]. 검증 결과 이 수치는 일반 연구가 아닌 저자 개인 노트 847개의 자체 감사이므로 일반화할 수 없다(7절).
- **거짓 인지 안도와 유지 부담(개인 비판)**: 다른 저자는 처리 없는 캡처가 거짓 인지 안도를 주고, auto-linking이 의미가 아닌 텍스트를 기계적으로 매칭하며("apophenia"라 명명), 유지보수 부담이 인지부하 감소라는 원래 목적과 모순됐다고 주장한다 — 저자 경험 기반 비판이다 [5].
- **수동 아카이브화(개인 경험)**: 6개의 second brain을 실패시킨 저자는 노트가 능동적 재관여 없이 쌓이며 수동적 아카이브가 되는 것을 공통 사인(死因)으로 꼽는다 [3].

이 단점들의 보편화 시도는 검증 단계에서 반복적으로 기각되었다는 점이 중요하다 — 실패담은 실재하지만 "시스템의 본질적 속성"으로 승격되지는 못했다(7절).

## 5. LLM Wiki 장단점

### 장점

- **유지보수 위임**: 종합·상호참조·일관성 검사(lint)를 LLM이 수행하는 설계로, 인간은 입력 큐레이션에 집중한다 [4][12].
- **질의 중심 활용**: 위치를 기억해 브라우징하는 대신 자연어로 질의하면 LLM이 분산 파일을 횡단해 종합한다 [7].
- **개발자 친화 스택**: plain markdown, 로컬 파일 통제, 직접 파일시스템 접근, 일관 템플릿 — 코드 다루듯 다룰 수 있는 구조다 [7].
- **낮은 시작 비용(한정 사례)**: 개인 규모(~100편 사례)에서 vector DB 없이 context window + index 파일로 retrieval이 충분했다는 보고가 있다 — 이 스코프를 넘는 일반화는 검증에서 기각됐다 [12].
- **축적 구조**: query 산출을 위키로 재파일링하고 [12], 소스가 추가될수록 상호참조가 풍부해진다는 것이 구현 측 주장이다 — 도구 자체의 주장이므로 신중히 받아야 한다 [9].
- **재관여 병목의 해법 후보**: [3]의 저자는 기존 폴더 구조를 대체하지 않고 그 위에 LLM의 능동적 큐레이션·검색을 얹었을 때 비로소 시스템이 유지됐다고 보고한다 — 개인 경험 기반이다 [3].

### 단점·리스크

- **인지 위임 리스크**: 자동화된 지식 시스템은 "판단이라는 노동 없이 알고 있다는 느낌"을 만들 수 있고, 지식 유지에 필요한 인지적 관여를 줄일 위험이 있다는 경고가 있다 — 직접적인 PKM 대 LLM Wiki 비교는 아니며, 구체 주장들은 검증을 완전히 통과하지 못했다 [13].
- **자율성 과장 주의**: "hands-off maintenance"를 표방하는 구현 [9]조차 검증에서는 인간 지시형 반자율로 확인됐다(7절).
- **유지보수 신뢰성 미검증**: LLM이 상호참조·일관성·decay 방지를 실제로 감당한다는 주장은 검증에서 기각되거나 제한됐다(7절) [4][12].
- **실패 사례 데이터 부재(자료 얇음)**: 이번 수집에서 failure-modes 각도의 자료([5][10][15])는 전부 Second Brain 대상이다. LLM Wiki의 장기 운영 실패담·정량 데이터는 수집되지 않았다. LLM Wiki가 실패하지 않는다는 뜻이 아니라, 패턴이 신생이라 검증된 실패 문헌 자체가 얇다는 뜻으로 읽어야 한다.

## 6. 직접 구축 중인 개발자 관점 시사점

**핵심 변수는 조직 구조가 아니라 재관여다.** [3]의 저자 통찰(개인 경험 기반)에 따르면 성공은 정교한 폴더/태그 체계보다 "누군가 — 인간이든 AI든 — 가 축적된 지식에 지속적으로 관여하고 처리하는가"에 달렸다 [3]. Second Brain 실패담들의 공통 기제도 재방문 부재였다 [10][15]. 따라서 시스템 선택 전에 "이 노트를 누가, 언제, 왜 다시 읽는가"에 먼저 답해야 한다.

**Second Brain 계열을 택할 조건**: 목적이 글·창작 산출이고, 증류 과정 자체(bolding·요약·리믹스)에서 사고 가치를 얻으며, 인간 판단을 시스템 밖으로 내보내고 싶지 않은 경우 [1][11][13]. 단 조직화가 산출을 잠식한 사례([10]의 77시간 대 14시간)를 반면교사로, 조직에 쓰는 시간을 계측하는 것이 안전하다 [10].

**LLM Wiki 계열을 택할 조건**: 활용이 질의 중심이고, 유지보수에 시간을 쓸 수 없으며, 로컬 markdown·파일시스템 기반 워크플로가 이미 몸에 밴 경우 [4][7][12]. 단 "LLM이 알아서 유지한다"는 기대는 검증에서 지지받지 못했으므로, lint 결과를 사람이 점검하는 루프를 남겨야 한다(7절).

**하이브리드 여지** (자료가 실제로 가리키는 조합):

1. **기존 구조 + LLM 레이어**: 수동 조직을 버리지 않고 그 위에 LLM 큐레이션·검색을 얹는 방식 — [3] 저자가 실제로 정착한 형태다 [3].
2. **retrieval과 LLM 실행의 분리**: Smart Connections처럼 local embeddings로 검색하고, 선별된 컨텍스트를 임의의 외부 LLM에 수출하는 구조는 특정 에이전트에 지식 접근을 묶지 않는다는 주장이 있다(2/3 지지, 신중) [14]. 벤더 종속을 피하려는 개발자에게 시사적이다.
3. **원본/파생 분리**: 불변 raw sources와 LLM이 갱신하는 wiki를 구조적으로 분리한다는 설계(2/3 지지, 신중)는, LLM이 원본을 오염시키지 않게 하는 안전장치로 읽을 수 있다 [4].
4. **인간용 채널과 기계용 저장소의 분리**: 같은 Karpathy가 인간용으로는 단일 파일 + 주기적 인간 리뷰라는 극단적 단순함을 [2], 에이전트용으로는 템플릿·상호참조를 갖춘 구조화 위키를 [4] 설계했다는 사실 자체가 시사적이다. 인간 최적화와 기계 최적화는 다른 설계를 낳으므로, 캡처는 마찰 없는 인간용 채널로, 종합·유지는 기계용 저장소로 나누는 2-tier 구성을 검토할 만하다 [2][4].
5. **판단 노동의 접점 유지**: [13]의 경고를 설계 제약으로 삼는다면, 자동 종합을 쓰되 인간의 리뷰 지점([2]의 주기적 리뷰, [11]의 층위 압축 같은)을 의도적으로 남기는 것이 절충이다 [2][11][13].

**얇은 지점 최종 명시**: 두 접근의 정량 비교, 장기 운영 데이터, LLM Wiki 실패 사례는 이번 자료에 없다. "전통 PKM은 죽었다"는 방향의 전면 전환론 [8]은 요약 수준에서만 수집됐고 세부 주장이 전량 기각된 데다 저자의 이해상충 고지가 있어, 근거로 삼기 어렵다 [8].

## 7. 기각되거나 주의가 필요한 통념

검증(적대 검증) 단계에서 기각된 주장 중 개발자 판단에 특히 유용한 것들이다. 아래 내용은 본문 근거가 아니라 "믿기 쉽지만 검증을 통과하지 못한 통념"의 기록이다.

**클러스터 A — LLM Wiki에 대한 과대 기대**

- *"LLM이 지식 decay와 유지보수 문제를 해결한다"* ([4] 관련 기각): 검증 과정에서 LLM의 상호참조 유지 실패를 보여주는 연구(citation omission 91.9%, node fabrication 94.3%, 환각 억제에 외부 knowledge graph 필요)가 인용되었고, Karpathy의 제안 자체가 추상적·선택적·스케일 미검증으로 프레임되어 있다는 점이 지적됐다.
- *"LLM wiki agent는 자율적으로 돈다"* ([9] 기각): 해당 저장소는 스스로 "독립 에이전트 시스템이 아니며 인간 지시 명령이 필요하다"고 명시한다. 자동화되는 것은 인간이 개시한 워크플로 내부의 추출·상호참조 작업이다.
- *"query 산출 재파일링은 자동으로 누적 가치를 만든다"* ([12] 기각): lint 없이는 모순과 낡은 데이터가 쌓이며 관리 오버헤드가 상존한다는 구현 증거가 지적됐다. 유지 비용은 사라지는 게 아니라 형태가 바뀔 수 있다.
- *"개인 규모엔 vector DB가 불필요하다"* ([12] 기각): 원문은 ~100편 규모에서 "retrieval에 충분"이라고만 했다. 규모·기능 일반화는 지지되지 않았다.
- *"LLM Wiki는 인간 브라우징용이 아니다"* ([7] 기각): 위키 레이어는 "인간이 읽을 수 있고 에이전트가 편집"하는 것으로, 배제가 아니라 우선순위의 문제라는 점이 확인됐다. 템플릿이 "LLM의 문서 파싱을 건너뛰게 한다"는 주장도 LLM이 모든 토큰을 처리한다는 이유로 기각됐다.

**클러스터 B — Second Brain 실패담의 과잉일반화**

- *"83%의 노트는 재방문되지 않는다(연구 결과)"* ([10] 기각): 83%는 저자 개인의 Notion 노트 847개 자체 감사이고, 함께 인용된 "Evernote 연구 14%"는 출처 확인에 실패했다. 게다가 원문 자체가 "누구에게는 여전히 완벽히 유효한가"라는 절을 두어 학술·글쓰기 용례에서의 유효성을 인정한다.
- *"캡처는 거짓 안도만 만든다 / 재방문 없는 수집은 무가치하다"* ([5][15] 기각): 능동적으로 리뷰·종합하는 사용자는 다른 결과를 얻으며(수동 캡처는 오용 패턴이지 시스템 본질이 아님), 노트 작성 행위 자체의 인지 이득(encoding effect)과 검색형 저장소의 가치를 보여주는 연구가 반례로 제시됐다.
- *"Second Brain은 정기 재방문 없이는 실패한다(보편 법칙)"* ([3] 기각): 잘 설계된 시스템은 필요 시점에 정보를 밀어줄 수 있고, 아카이브·레퍼런스 용도로 성공하는 사용자도 있다는 반론으로, 개인 경험의 보편화가 차단됐다.

**클러스터 C — "전통 PKM 무용론"과 미니멀리즘의 과장**

- *"backlink·그래프 등 수동 구조는 LLM 검색에 무가치하다"* ([8] 기각): GraphRAG/TOBUGraph 계열 연구와 LinkedIn의 그래프 기반 검색 성과(63% 개선)가 구조화의 이점을 입증한다는 반례가 제시됐다. plain text 우월론도 structured HTML이 RAG 벤치마크에서 우수했다는 2025년 연구로 반박됐다.
- *"context window 확장으로 retrieval 스캐폴딩은 경제적으로 불필요하다"* ([8] 기각): vector 질의가 long-context 대비 훨씬 저렴하고 정확도도 개선(35.5%→66.7%)된다는 데이터로 반박됐으며, 해당 저자의 이해상충("I sell one")도 지적됐다.
- *"단일 파일이 인지 부담을 '제거'한다"* ([2] 기각): 부담이 분류에서 검색·스캔으로 이전될 뿐이며, 비구조 축적이 외재적 인지부하를 늘린다는 연구가 반례로 제시됐다. Karpathy 방식은 본인에게 잘 맞는 겸손한 개인 실천으로 원문에 프레임되어 있다.
- 반대 방향의 경고도 무사통과는 아니다: *"AI 도구는 필연적으로 거짓 확신을 만든다"*, *"지식 도구는 판단을 보강해야만 한다"*는 [13] 계열 주장들도 — 거짓 확신은 시스템 속성이 아니라 사용자 행동에 의존하며, 보편적 설계 당위로는 성립하지 않는다는 이유로 — 기각됐다.

종합하면, 검증은 양 진영의 강한 서사(“LLM이 다 해결한다” / “Second Brain은 필연적으로 실패한다” / “전통 PKM은 죽었다”)를 모두 깎아내고, 스코프가 명시된 온건한 결론 — 인간용과 기계용은 다른 설계이며, 어느 쪽이든 재관여가 병목이고, 자동화의 신뢰성은 아직 각자 검증해야 한다 — 만 남겼다.

## 8. 출처

- [1] Building a Second Brain: The Definitive Introductory Guide — https://fortelabs.com/blog/basboverview/
- [2] The append-and-review note — https://karpathy.bearblog.dev/the-append-and-review-note/
- [3] After 6 dead second brains, an LLM finally made one stick — https://blog.stackademic.com/after-6-dead-second-brains-an-llm-finally-made-one-stick-d6e2f680b30a
- [4] Andrej Karpathy's LLM Wiki (GitHub Gist) — https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- [5] Why Building a Second Brain Made Me Stop Thinking Clearly — https://turbulencegains.com/second-brain/
- [6] The PARA Method: The Simple System for Organizing Your Digital Life in Seconds — https://fortelabs.com/blog/para/
- [7] What Is Andrej Karpathy's LLM Wiki? How to Build a Personal Knowledge Base With Claude Code — https://www.mindstudio.ai/blog/andrej-karpathy-llm-wiki-knowledge-base-claude-code
- [8] Why PKM Is Broken in the AI Era (And What Works Now) — https://www.iwoszapar.com/p/why-personal-knowledge-management-is-broken-ai-era
- [9] SamurAIGPT llm-wiki-agent (GitHub) — https://github.com/SamurAIGPT/llm-wiki-agent
- [10] I Spent 6 Months Building a Second Brain. Then I Deleted It. — https://downloadchaos.com/blog/second-brain-productivity-paradox
- [11] Progressive Summarization: A Practical Technique for Designing Discoverable Notes — https://fortelabs.com/blog/progressive-summarization-a-practical-technique-for-designing-discoverable-notes/
- [12] LLM Knowledge Bases | DAIR.AI Academy Blog — https://academy.dair.ai/blog/llm-knowledge-bases-karpathy
- [13] AI is becoming a second brain at the expense of your first one — https://stackoverflow.blog/2026/03/19/ai-is-becoming-a-second-brain-at-the-expense-of-your-first-one/
- [14] Smart Connections Obsidian Plugin (GitHub) — https://github.com/brianpetro/obsidian-smart-connections
- [15] More Notes, Less Value — The Psychology Behind Collector's Fallacy — https://medium.com/curious/more-notes-less-value-the-psychology-behind-collectors-fallacy-b5210b0c5524