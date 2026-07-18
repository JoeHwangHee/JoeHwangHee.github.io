---
name: translate-and-publish
description: Use when a Korean blog post in this repo's content/posts/ has been written or edited and needs English/Japanese translations, independent translator review, and a push to the live site.
---

# Translate and Publish

## Overview

This blog (Hugo + PaperMod, `defaultContentLanguage = "ko"`) ships every post in three
languages using filename suffixes: `slug.md` (ko), `slug.en.md`, `slug.ja.md`. This skill
turns a Korean draft into all three, gates the publish behind independent translation QA,
and pushes only when QA passes.

**Core principle:** a translation that isn't verified by back-translation against the
original is not trustworthy enough to publish. The review subagents never see how the
translation was produced — they only see the original and the translated text — so the
check is genuinely independent, not a self-grade.

## When to Use

- The user just finished writing/editing a Korean post at `content/posts/<slug>.md` (no
  language suffix) and asks to translate + publish it.
- The user says things like "이 글 번역해서 push해줘", "다국어로 만들어서 배포해줘".

If `<slug>.en.md` / `<slug>.ja.md` already exist and only the Korean source changed,
re-translate and re-review — don't assume old translations still match.

## Workflow

### 0. Check the write gate

If a Write/Edit call is blocked by the loop-engineering audit gate, tell the user and wait
for them to say "진행해" before continuing. Don't work around it.

### 1. Identify the source post

Find the changed/target Korean file:
```bash
git status --short content/posts/ | grep -E '\.md$' | grep -v '\.\(en\|ja\)\.md$'
```
If ambiguous, ask which post. Read the full file (front matter + body).

### 2. Draft translations

Write `content/posts/<slug>.en.md` and `content/posts/<slug>.ja.md`:
- Same `date` and `draft = false` as the source.
- `title` translated naturally, not word-for-word.
- Body: preserve paragraph structure, tone (this blog's voice is personal/reflective),
  and markdown/code blocks (leave code untranslated).
- Do this yourself directly — don't delegate drafting to a subagent, since the review step
  needs an independent second opinion, not two agents echoing each other.

### 3. Independent review — one subagent per language, run in parallel

For each of `en` and `ja`, dispatch a fresh `general-purpose` Agent (not a fork — it must
have zero context on how the translation was produced) with this prompt, filling in the
bracketed parts:

```
당신은 한국어-[언어명] 전문 통번역사로, 10년 이상 출판·에세이·블로그 번역 경력을
가진 베테랑입니다. 아래 원문(한국어)과 번역문([언어명])을 검수하세요.

[중요] 번역 의도나 근거는 제공되지 않습니다. 번역문만 보고 독립적으로 판단하세요.

절차:
1. 번역문을 한국어로 역번역(back-translation)하세요. 이 역번역 결과를 근거로
   원문과 의미가 일치하는지 비교하세요.
2. 다음 기준으로 평가하세요:
   - 의미 충실도: 원문의 내용이 추가/누락/왜곡 없이 전달되었는가
   - 문법/자연스러움: [언어명] 원어민이 쓴 것처럼 자연스럽고 문법적으로
     올바른가 (직역투, 어색한 표현 여부)
   - 문맥/톤: 개인 블로그 에세이의 담담하고 성찰적인 톤이 유지되었는가
3. 반드시 아래 형식으로만 답변하세요:

VERDICT: PASS 또는 FAIL
ISSUES:
- (FAIL이면 문제 원문 표현과 번역 표현을 병기하고 무엇이 문제인지 한 줄씩.
  PASS면 "없음")

--- 원문 (한국어) ---
[ko 본문 전체]

--- 번역문 ([언어명]) ---
[번역 본문 전체]
```

Send both language reviews as two Agent calls in a single message (independent, parallel).

### 4. Gate — no PASS, no push

Both reviews must return `VERDICT: PASS` before step 5.

**Absolute rules:**
- Any FAIL → fix that language's translation per the listed ISSUES, then re-review with
  the same persona prompt until it PASSes. Don't touch the other language's file.
- Never push while a language is un-PASSed.
- A reviewer reply that doesn't follow the `VERDICT:`/`ISSUES:` format is not a PASS —
  re-ask it to answer in the required format. Don't interpret a vague or hedged answer as
  passing.

| Rationalization | What to actually do |
|---|---|
| "번역이 거의 맞으니 이 정도는 괜찮다" | FAIL은 FAIL. 지적된 부분을 고치고 재검수한다. |
| "리뷰어가 너무 깐깐하다" | 지적을 원문과 직접 대조해 실제 오류인지 확인한다. 트집이라 판단되면 근거를 들어 같은 리뷰어에게 재질의한다 — 임의로 PASS 처리하지 않는다. |
| "시간이 없으니 나중에 고치자" | 게이트 통과 전 push 금지. 예외 없음. |
| "사용자가 급하다고 했다" | 급함은 품질 게이트를 생략할 이유가 아니다. 속도는 재검수 루프를 빨리 도는 것으로 확보한다. |

### 5. Commit and push

Only after both PASS:
```bash
git add content/posts/<slug>.md content/posts/<slug>.en.md content/posts/<slug>.ja.md
git commit -m "Publish post: <title> (ko/en/ja)"
git push -u origin main
```
Capture exit codes directly (`cmd; RC=$?`), don't infer success from piped output.

### 6. Verify deployment

Watch the triggered Actions run to completion, then confirm all three language URLs
return 200:
```bash
gh run watch "$(gh run list --limit 1 --json databaseId --jq '.[0].databaseId')" --exit-status
curl -s -o /dev/null -w "%{http_code}\n" https://joehwanghee.github.io/posts/<slug>/
curl -s -o /dev/null -w "%{http_code}\n" https://joehwanghee.github.io/en/posts/<slug>/
curl -s -o /dev/null -w "%{http_code}\n" https://joehwanghee.github.io/ja/posts/<slug>/
```

## Quick Reference

| Step | Who does it | Blocks on |
|---|---|---|
| Draft en/ja | main agent | — |
| Review en | fresh subagent, EN↔KO veteran persona | back-translation vs. ko source |
| Review ja | fresh subagent, JA↔KO veteran persona | back-translation vs. ko source |
| Push | main agent | both reviews PASS |
| Verify | main agent | Actions run + live curl checks |

## Common Mistakes

- Letting the same agent that translated also do the review — not independent, defeats
  the point of back-translation QA.
- Treating "PASS with minor notes" as fail-safe to ignore — if the reviewer's format says
  FAIL, it's FAIL regardless of how minor the listed issues sound to you.
- Pushing before both languages PASS because one language passed and you didn't want to
  wait for the other.
- Forgetting `draft = false` in the new language files (post silently won't build/show).
