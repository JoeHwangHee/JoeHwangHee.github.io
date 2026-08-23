+++
date = '2026-08-20T23:00:00+09:00'
draft = false
title = 'The 50% Rule'
summary = "Why AI coding agents stumble in brownfield codebases — when 'billing' and 'payment' do the same thing, the agent flips a coin."
+++

*Written August 20, 2026*

After all, I work on developer-facing services. So I've been thinking about what people mean when they say AI coding is untrustworthy — what exactly they're talking about. When they call an agent a hopeless tool, it's because the agent produces ridiculous code. It's like this: you can say "billing" for something, or "payment" for something, and the AI might say "you can use your dollars for it." These are all the same thing — just different vocabulary. But in their codebase, there's already a `billing` function, and the agent creates a separate `using_dollars` function to let customers pay for the service. I think these problems stem from brownfield codebases.

**Brownfield** refers to codebases for services that are over 5 to 10 years old — so-called "legacy codebases." These codebases have too many functions for any single developer to fully grasp, and for various reasons — corporate or personal, though ultimately it all comes down to money — they've been handled by many different developers over the years. I don't think there's a single developer who can say, "I know this codebase well enough to answer your questions about this service." As a result, there are inevitably functions that do the same thing — like `billing` and `payment`. That's what I'm getting at.

---

**The 50% Rule** is about the probability of an agent choosing wrong in a brownfield codebase.

First, there could be duplicated code that performs the same function. The agent cannot tell these apart — not at all. For example, when your codebase has both `payment` and `billing` doing the same thing and you say, "do something with the payment code," the agent has to decide: which code is the user referring to? It picks at random — like a coin toss, gambling on whether it hits what you want. So your agent becomes an untrustworthy tool, when it should be your reliable partner doing the job properly.
