+++
date = '2026-07-26T15:30:00+09:00'
draft = false
title = '그래프 엔지니어링이라 들어봤나'
summary = "AI 에이전트 설계의 변천사 — 단일 루프에서 그래프 엔지니어링까지. Auto Company 프로젝트와 함께 바라본, 사람의 일하는 구조를 AI에게 주입하는 흐름."
+++

*2026년 7월 26일 작성*

그래프 엔지니어링이란 개념을 들은 적이 있는가?

단순하게 해당 개념을 들은 건 가입한 카카오톡 단체채팅방의 단순 이미지를 통해서다.

아래 이미지인데, 지금까지의 Engineering의 변천사와 Graph Engineering의 개념을 이해할 수 있도록 구조화시켜서 설명한 것이 보인다.

![Engineering 변천사와 Graph Engineering 개념 정리](/images/daily_diary_20260726/image.png)

그리고 아래는 그 개념을 Gemini에게 물어본 결과다.

## AI 에이전트 설계에서의 그래프 엔지니어링 (Graph Engineering)

AI 에이전트 개발 분야에서 **그래프 엔지니어링(Graph Engineering)**은 여러 AI 에이전트들의 작업 흐름과 의존성을 노드(Node)와 연결선(Edge)을 가진 '그래프' 형태로 설계하고 제어하는 방법론입니다.[^1]

과거에는 단일 AI가 스스로 평가하고 수정하며 목표를 달성하는 단일 반복 구조(Loop Engineering)가 주로 사용되었습니다. 하지만 작업이 복잡해질수록 무한 루프에 빠지거나 환각에 취약해지는 한계가 있었습니다. 이를 해결하기 위해 에이전트의 역할을 분리하고 네트워크 구조로 연결하는 그래프 엔지니어링이 표준으로 자리 잡고 있습니다.[^2]

### 핵심 구성 요소

- **노드 (Node):** 작업을 수행하는 독립적인 단위입니다. 특정 역할을 맡은 AI 에이전트, 외부 도구(API) 호출, 또는 사람의 개입(Human-in-the-loop) 단계가 노드가 될 수 있습니다.[^3]
- **엣지 (Edge) 및 조건부 분기:** 노드 간의 실행 순서와 조건 논리를 정의합니다. 예를 들어 "A와 B 에이전트가 병렬로 작업을 마치면 C가 취합한다" 또는 "검수 결과가 기준 미달이면 다시 A로 되돌린다(Cyclic Workflow)"와 같은 복잡한 흐름을 명시적으로 통제합니다.[^3]
- **상태 (State):** 그래프를 통과하며 전체 메시지 기록과 실행 컨텍스트를 유지하는 공유 메모리입니다. 이를 통해 에이전트가 이전 맥락을 잃지 않고 장기적인 작업을 수행할 수 있습니다.[^4]

### 대표 프레임워크

이러한 그래프 기반 오케스트레이션을 구현하는 대표적인 도구로는 **LangGraph**가 있습니다. LangChain에서 개발한 이 프레임워크는 상태 지속성(State Persistence)과 체크포인트를 지원하여, 복잡한 다중 에이전트 시스템을 안정적인 프로그래머블 그래프로 구축할 수 있게 해줍니다.[^4]

---
**참고 자료 (References)**

[^1]: [Graph Engineering Learning Pathway - explainx.ai](https://explainx.ai/pathways/graph-engineering)
[^2]: [Graph Engineering for AI Agents: Beyond Single Feedback Loops - Eigent AI](https://www.eigent.ai/blog/graph-engineering-ai-agents)
[^3]: [LangGraph: Agent Orchestration Framework for Reliable AI Agents - LangChain](https://www.langchain.com/langgraph)
[^4]: [Building Agentic Workflows in Python with LangGraph - Machine Learning Mastery (2026.07.20)](https://machinelearningmastery.com/building-agentic-workflows-in-python-with-langgraph/)

---

그리고 아래는 git에서 한번 붐이 되었던 Auto Company라는 프로젝트의 github 주소다.

https://github.com/MaxMiksa/Auto-Company?utm_source=pytorchkr&ref=pytorchkr

내가 이해한 게 맞다면, Auto Company라는 개념은 어디까지나 LLM을 사용해서 각 서브에이전트를 가동시켜 역할에 따라 업무를 진행하도록 하는 프로세스로 이해된다.

---

결국, 두 가지의 개념은 하나의 방향으로 간다고 생각한다.

첫째, 사람이 일하는 구조를 AI에게 구조화시켜 주입한다.
둘째, AI가 스스로 결과물을 검토하고 서로 네트워킹을 거쳐 사람의 개입을 최대한 줄인다.
셋째, AI가 가지는 컨텍스트의 유실을 DB로 보완한다.

사실 이러한 시도들은 AI를 사용한 시도들과 같은 방향으로 보인다.

AI로 Workflow를 자동화시키는 것이 주목받으며, 사람들은 어떻게든 사람의 개입을 최소화하기 위한 노력을 해왔고 이것이 계속 발전해 온 게 맨 위에 있는 이미지의 결과라고 본다.

아마 이후에는 이 이상의 구조가 나올 테지.

부디 이 자동화가 업계에 들어올 시 내 직무까지 뺏어가지 않길 바랄 뿐이다.
