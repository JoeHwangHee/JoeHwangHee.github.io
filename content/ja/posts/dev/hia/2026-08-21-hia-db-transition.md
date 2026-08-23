+++
date = '2026-08-21T23:00:00+09:00'
draft = false
title = '[2026-08-21] DBが原本、Notionはskin'
summary = "元帳の権威をNotionからPostgreSQLに移した。既存データは移行しないことにした。新しい実行からDB、過去のものはNotionにそのまま。"
tags = ['HIA']
+++

8月21日、HIAの元帳の権威をNotionからPostgreSQLに移動した。

---

## なぜ移したのか

HIAの元帳（ledger）はNotionページとして始まった。すべてのエージェントがNotionを読み書きして状態を共有していた。問題は**Notionが障害を起こすとシステム全体が止まる**ことだった。元帳はシステムの心臓であり、その心臓が外部サービスの可用性に依存していた。

決定は明確だった。**「DBが原本、Notionはskin。」** すべての判定はPostgreSQLで行い、Notionは人間が見やすいように表示するだけの層に降格した。Notionが落ちてもシステムは動き続ける。

```mermaid
flowchart TB
    subgraph before["以前"]
        N1[Notion<br/>原本＋表示]:::canon
        A1[エージェント達]:::derived
        A1 -->|読み書き| N1
    end

    subgraph after["以後"]
        DB[PostgreSQL<br/>原本]:::canon
        N2[Notion<br/>表示専用]:::ext
        A2[エージェント達]:::derived
        A2 -->|読み書き| DB
        DB -->|同期| N2
    end

    before -->|移行| after
```

## マイグレーションなし

Notionに蓄積された実行履歴（run history）をDBに移行しないことにした。

理由は単純だ。一人のシステムなので二重ストレージを許容できる。過去の記録はNotionにそのまま残り、新しい実行からDBに蓄積される。マイグレーションは作業量に対して得られるものが少ない——過去の記録を参照する必要があればNotionを直接見ればいい。

## 持ち込んだ原則、先送りしたもの

DBに移行しても**追記専用（append-only）の原則は維持した。** ドメインテーブルはINSERTのみ許可する。Notionで守っていた原則をDBでもそのまま踏襲した。

一方、CAS（Content-Addressable Storage）、監査チェーン（audit chain）、べき等キー（idempotency key）は先送りにした。有用だが今すぐ必要ではない。「エレガンスはコストだ」の原則に従い、必要になった時点で入れることにした。

| 決定 | 理由 |
|---|---|
| DBを原本に | Notion障害＝システム停止を防止 |
| Notionをskinとして維持 | 人間が見るには依然便利 |
| マイグレーションなし | 一人のシステム、二重ストレージ許容 |
| 追記専用を維持 | 原則はストレージが変わっても同じ |
| CAS・監査・べき等は先送り | 今は不要、必要時に追加 |
