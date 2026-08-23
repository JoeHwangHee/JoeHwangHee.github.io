+++
date = '2026-07-26T15:30:00+09:00'
draft = false
title = 'グラフエンジニアリングって聞いたことあるか'
summary = "AIエージェント設計の変遷——単一ループからグラフエンジニアリングまで。Auto Companyプロジェクトと共に見る、人の働く構造をAIに注入する流れ。"
+++

*2026年7月26日 作成*

グラフエンジニアリングという概念を聞いたことがあるだろうか。

単純にこの概念を知ったのは、入っているカカオトークのグループチャットに投稿された一枚の画像を通じてだ。

下の画像だが、これまでのEngineeringの変遷とGraph Engineeringの概念を理解できるよう構造化して説明しているのが分かる。

![Engineeringの変遷とGraph Engineeringの概念整理](/images/daily_diary_20260726/image.png)

そして以下は、その概念をGeminiに聞いてみた結果だ。

## AIエージェント設計におけるグラフエンジニアリング（Graph Engineering）

AIエージェント開発の分野で**グラフエンジニアリング（Graph Engineering）**は、複数のAIエージェントの作業フローと依存関係をノード（Node）とエッジ（Edge）を持つ「グラフ」形態で設計・制御する方法論です。[^1]

かつては、単一のAIが自ら評価・修正しながら目標を達成する単一反復構造（Loop Engineering）が主に使われていました。しかし、タスクが複雑になるほど無限ループに陥ったり、ハルシネーションに脆弱になったりする限界がありました。これを解決するために、エージェントの役割を分離しネットワーク構造で接続するグラフエンジニアリングが標準として定着しつつあります。[^2]

### 核心構成要素

- **ノード（Node）:** タスクを実行する独立した単位です。特定の役割を担うAIエージェント、外部ツール（API）呼び出し、または人の介入（Human-in-the-loop）段階がノードになり得ます。[^3]
- **エッジ（Edge）および条件分岐:** ノード間の実行順序と条件ロジックを定義します。例えば「AとBのエージェントが並列で作業を終えたらCが集約する」や「検査結果が基準未達なら再びAに戻す（Cyclic Workflow）」といった複雑なフローを明示的に制御します。[^3]
- **状態（State）:** グラフを通過しながら全メッセージ履歴と実行コンテキストを維持する共有メモリです。これによりエージェントは以前のコンテキストを失わずに長期的なタスクを遂行できます。[^4]

### 代表フレームワーク

このようなグラフベースのオーケストレーションを実装する代表的なツールとして**LangGraph**があります。LangChainが開発したこのフレームワークは、状態永続性（State Persistence）とチェックポイントをサポートし、複雑なマルチエージェントシステムを安定したプログラマブルなグラフとして構築できるようにしてくれます。[^4]

---
**参考資料（References）**

[^1]: [Graph Engineering Learning Pathway - explainx.ai](https://explainx.ai/pathways/graph-engineering)
[^2]: [Graph Engineering for AI Agents: Beyond Single Feedback Loops - Eigent AI](https://www.eigent.ai/blog/graph-engineering-ai-agents)
[^3]: [LangGraph: Agent Orchestration Framework for Reliable AI Agents - LangChain](https://www.langchain.com/langgraph)
[^4]: [Building Agentic Workflows in Python with LangGraph - Machine Learning Mastery (2026.07.20)](https://machinelearningmastery.com/building-agentic-workflows-in-python-with-langgraph/)

---

そして以下は、gitで一時ブームになったAuto Companyというプロジェクトのgithubアドレスだ。

https://github.com/MaxMiksa/Auto-Company?utm_source=pytorchkr&ref=pytorchkr

自分の理解が正しければ、Auto Companyという概念はあくまでLLMを使って各サブエージェントを稼働させ、役割に応じて業務を進行させるプロセスとして理解される。

---

結局、二つの概念は一つの方向に向かっていると思う。

第一に、人が働く構造をAIに構造化して注入する。
第二に、AIが自ら成果物を検討し、互いにネットワーキングを経て人の介入を最大限減らす。
第三に、AIが持つコンテキストの喪失をDBで補完する。

実はこうした試みは、AIを使った他の試みと同じ方向に見える。

AIでWorkflowを自動化することが注目を集め、人々はどうにかして人の介入を最小化するための努力を重ねてきた。それが発展し続けてきた結果が、冒頭の画像だと思う。

おそらくこの先には、これ以上の構造が登場するだろう。

どうかこの自動化が業界に入ってきたとき、自分の職務まで奪っていかないことを願うばかりだ。
