---
name: translate-and-publish
description: Use when a Korean blog draft (plain text, possibly unformatted, with image paths or pasted images) in this repo's content/posts/ needs to be styled into readable Markdown, translated into English/Japanese, independently reviewed, and pushed to the live site.
---

# Translate and Publish

## Overview

This blog (Hugo + PaperMod, `defaultContentLanguage = "ko"`) ships every post in three
languages using filename suffixes: `slug.md` (ko), `slug.en.md`, `slug.ja.md`. The user
writes the Korean source as plain, unformatted prose. This skill styles it into readable
Markdown, turns it into all three languages, gates the publish behind independent QA for
both the styling and the translations, and pushes only when every QA check passes.

**Core principle:** neither a styling pass nor a translation is trustworthy enough to
publish unless an independent reviewer — who never sees how the output was produced —
confirms the meaning survived. Self-grading doesn't count.

## When to Use

- The user just finished writing/editing a Korean post at `content/posts/<slug>.md` (no
  language suffix), written as plain prose, and asks to clean it up / translate / publish.
- The user says things like "이 글 정리해서 번역하고 push해줘", "다국어로 만들어서 배포해줘".

If `<slug>.en.md` / `<slug>.ja.md` already exist and only the Korean source changed,
re-style, re-translate, and re-review — don't assume old output still matches.

## Workflow

### 0. Check the write gate

If a Write/Edit call is blocked by the loop-engineering audit gate, tell the user and wait
for them to say "진행해" before continuing. Don't work around it.

### 1. Identify the source post

Find the changed/target Korean file:
```bash
git status --short content/posts/ | grep -E '\.md$' | grep -v '\.\(en\|ja\)\.md$'
```
If ambiguous, ask which post. Read the full file (front matter + raw body).

### 2. Style the Korean draft

Rewrite the body in place as readable Markdown — add structure, don't add or remove
content:
- Break run-on prose into paragraphs at natural topic shifts.
- Use `**bold**` and `*italic*` for emphasis the text already implies (a term the writer
  is clearly stressing, a key word) — don't invent emphasis that isn't there.
- Use `---` horizontal rules to separate distinct sections/topic shifts, not between every
  paragraph.
- If a chunk of prose is actually tabular data (comparisons, lists of items with
  attributes), convert it to a Markdown table. Don't force a table where the content is
  narrative.
- Images:
  - A bare path reference in the draft → move the file into
    `static/images/<slug>/` (create the dir if needed) if it isn't already under
    `static/`, then insert `![](/images/<slug>/<filename>)` at that point in the text.
  - A pasted image attached in the conversation → save it to
    `static/images/<slug>/<descriptive-name>.<ext>`, insert the same way.
  - Never leave a reference pointing outside `static/` — it won't resolve on the deployed
    site.
- Don't touch front matter except what's already there (date/draft/title).

### 3. Independent style review — gate before translating

Dispatch a fresh `general-purpose` Agent (not a fork — zero context on how the styling was
done) with this prompt:

```
당신은 10년 이상 경력의 베테랑 한국어 카피에디터(교정·편집자)입니다. 아래
원문(사용자가 쓴 평문 초안)과 편집문(마크다운으로 스타일링된 결과)을 대조
검수하세요.

[중요] 편집 의도나 근거는 제공되지 않습니다. 두 텍스트만 보고 독립적으로
판단하세요.

절차:
1. 편집문에서 마크다운 마크업(**굵게**, *기울임*, 구분선 ---, 표, 이미지
   삽입 등)을 걷어냈다고 가정하고 남는 순수 텍스트를, 원문과 문장 단위로
   대조하세요.
2. 다음 기준으로 평가하세요:
   - 내용 보존: 원문의 문장/정보가 추가·누락·왜곡·순서변경 없이 그대로
     담겼는가 (표로 변환된 내용 포함)
   - 과잉 스타일링 여부: 원문에 없는 강조나 뉘앙스를 스타일링이 만들어내지
     않았는가
   - 마크업 적절성: 굵게/기울임/구분선/표 사용이 맥락상 자연스럽고 과하지
     않은가
3. 반드시 아래 형식으로만 답변하세요:

VERDICT: PASS 또는 FAIL
ISSUES:
- (FAIL이면 문제된 부분을 원문/편집문 병기하고 무엇이 문제인지 한 줄씩.
  PASS면 "없음")

--- 원문 (평문) ---
[raw text]

--- 편집문 (마크다운) ---
[styled markdown text]
```

If FAIL, fix per the listed ISSUES and re-review with the same prompt until PASS. Only
once styling PASSes, move to step 4 — translate the *styled* Markdown, not the raw prose,
so structure (bold/italic/tables/images) carries into en/ja.

### 4. Draft translations

Write `content/posts/<slug>.en.md` and `content/posts/<slug>.ja.md`:
- Same `date` and `draft = false` as the source.
- `title` translated naturally, not word-for-word.
- Body: preserve paragraph structure, tone (this blog's voice is personal/reflective),
  and all Markdown from step 2 (bold/italic/`---`/tables/image references — image paths
  don't get translated).
- Do this yourself directly — don't delegate drafting to a subagent, since the review step
  needs an independent second opinion, not two agents echoing each other.

### 5. Independent translation review — one subagent per language, run in parallel

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
[ko 본문 전체 - 스타일링된 마크다운]

--- 번역문 ([언어명]) ---
[번역 본문 전체]
```

Send both language reviews as two Agent calls in a single message (independent, parallel).

### 6. Gate — no PASS, no push

The styling review (step 3) AND both translation reviews (step 5) must all return
`VERDICT: PASS` before step 7.

**Absolute rules:**
- Any FAIL → fix that item per the listed ISSUES, then re-review with the same persona
  prompt until it PASSes. Don't touch unrelated languages/sections.
- Never push while anything is un-PASSed.
- A reviewer reply that doesn't follow the `VERDICT:`/`ISSUES:` format is not a PASS —
  re-ask it to answer in the required format. Don't interpret a vague or hedged answer as
  passing.

| Rationalization | What to actually do |
|---|---|
| "이 정도 스타일링/번역이면 거의 맞으니 괜찮다" | FAIL은 FAIL. 지적된 부분을 고치고 재검수한다. |
| "리뷰어가 너무 깐깐하다" | 지적을 원문과 직접 대조해 실제 오류인지 확인한다. 트집이라 판단되면 근거를 들어 같은 리뷰어에게 재질의한다 — 임의로 PASS 처리하지 않는다. |
| "시간이 없으니 나중에 고치자" | 게이트 통과 전 push 금지. 예외 없음. |
| "사용자가 급하다고 했다" | 급함은 품질 게이트를 생략할 이유가 아니다. 속도는 재검수 루프를 빨리 도는 것으로 확보한다. |

### 7. Commit and push

Only after every review PASSes:
```bash
git add content/posts/<slug>.md content/posts/<slug>.en.md content/posts/<slug>.ja.md static/images/<slug>/
git commit -m "Publish post: <title> (ko/en/ja)"
git push -u origin main
```
Capture exit codes directly (`cmd; RC=$?`), don't infer success from piped output.

### 8. Verify deployment

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
| Style ko draft | main agent | — |
| Review styling | fresh subagent, veteran KO copy-editor persona | content preserved, markup not overdone |
| Draft en/ja | main agent | styling review PASS |
| Review en | fresh subagent, EN↔KO veteran persona | back-translation vs. styled ko source |
| Review ja | fresh subagent, JA↔KO veteran persona | back-translation vs. styled ko source |
| Push | main agent | styling PASS + both translation reviews PASS |
| Verify | main agent | Actions run + live curl checks |

## Common Mistakes

- Translating the raw plain-text draft instead of the styled Markdown — loses structure in
  en/ja and makes the three language versions inconsistent.
- Letting the same agent that styled/translated also do its own review — not independent,
  defeats the point of the check.
- Treating "PASS with minor notes" as fail-safe to ignore — if the reviewer's format says
  FAIL, it's FAIL regardless of how minor the listed issues sound to you.
- Pushing before every review PASSes because some passed and you didn't want to wait for
  the rest.
- Forgetting `draft = false` in the new language files (post silently won't build/show).
- Leaving an image reference pointing outside `static/` (breaks on the deployed site).
- Forcing a table onto narrative prose that doesn't actually have tabular structure.
