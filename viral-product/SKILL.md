---
name: viral-product
description: >-
  Audit or generate landing pages, product pages, pricing, and marketing copy
  using 32 battle-tested principles of viral products (from the indie-hacker /
  build-in-public playbook). MODE A critiques an existing page or copy against
  the principles and returns a score plus a ranked list of concrete fixes;
  MODE B generates a new page or copy that follows them. Use this whenever the
  user is working on a landing page, hero section, headline, subheadline, value
  proposition, pricing table, OG/social-share image, call-to-action button,
  testimonials section, comparison table, or product positioning — or asks why
  their page or product "isn't converting," "feels generic," or "won't take
  off." Trigger it even when the user never says the word "viral" or names
  these principles; if the task is "make this page better at selling," this is
  the skill.
---

# Viral Product

A set of 32 opinionated principles for building products and pages that spread
and sell. This skill uses them two ways.

- **MODE A — Audit**: the user has a page, hero, pricing, or copy and wants it
  critiqued. Return a score, the misses ranked by impact, and a concrete fix
  for each.
- **MODE B — Generate**: the user has a product (or an idea) and wants a page or
  copy built. Produce it so it already satisfies the principles.

Both modes lean on the same rulebook in `references/principles.md`. That file
holds the full reasoning and examples for all 32 — read it when you need the
"why" behind a rule or a worked example. The compact checklist below is enough
to run an audit without loading it.

## These are heuristics, not laws

Treat the 32 as a sharp founder's opinions, not commandments. They are tuned
for a specific target: a one-person or small-team product sold to consumers or
solo buyers, where attention is scarce and the goal is a fast "yes." That is
where they shine.

They fit less well for enterprise SaaS, regulated markets, free/open-source
tools, or products with a genuine free-tier growth loop. When a principle
clashes with the user's real context, say so plainly and explain the trade-off
— do not force the product into the mold. Blindly applying "no free plan" to a
product whose whole distribution is a free tier is worse than useless. Your job
is to bring the sharp opinion *and* the judgment about when it applies.

## Picking the mode

- User shares a URL, screenshot, or existing copy → **Audit**.
- User describes a product and wants a page/headline/pricing built → **Generate**.
- User shares something AND asks you to rewrite it → **Audit first, then
  generate** the fixed version of the worst offenders.

If it is ambiguous, ask one short question. Do not guess and burn a long output
on the wrong mode.

## MODE A — Audit

1. Read the input (page, copy, or description). If it is a URL, fetch it. If
   parts are missing (e.g. no pricing shown), note that as unknown rather than
   assuming a violation.
2. Walk the 32 principles. For each, decide: **pass**, **miss**, or **N/A**
   (with a one-line reason for N/A — this is where context judgment lives).
3. Score = passes ÷ (passes + misses), ignoring N/A. Report as `X/Y`.
4. Rank the misses by impact on the specific product, not by principle number.
   The hero, headline, pricing clarity, and single CTA usually move the needle
   most — a weak footer rarely does.

Use this exact structure so the output is skimmable:

```markdown
# Viral audit — [what was reviewed]

**Score: X/Y** — [one-sentence verdict]

## Critical misses (fix these first)
1. **[Principle name]** — what's wrong, in one line.
   → **Fix:** the concrete change, ideally with rewritten copy.

## Quick wins
- **[Principle name]** — problem → fix.

## Already working
- [Principle name] — one line on why it passes.

## Not applicable
- [Principle name] — why it doesn't fit this product.
```

Rules that make an audit useful:

- **Show the fix, don't just name the flaw.** "Headline is weak" helps no one.
  Rewrite it. If pricing has five tiers, show the three you'd keep.
- **Quote the user's own text** when you flag it, so they know exactly what you
  mean.
- **Be specific with numbers and copy.** You are auditing against principles
  that themselves demand specificity (numbers over adjectives, no weak words) —
  hold your own output to the same bar.

## MODE B — Generate

1. Pull out the essentials first: what the product does (in your own words),
   who it is for, the one core desire it serves, and any proof (users,
   results, testimonials). If the core desire or the "one thing" is unclear,
   ask — everything downstream depends on it.
2. Build the page top-down, because 80% of visitors never leave the hero
   (principle 20). Get the hero right before anything else.
3. Deliver the copy/structure ready to paste, then a short note on which
   principles each choice serves so the user can push back.

Default page skeleton (adapt to the product — omit sections that don't earn
their place):

```markdown
1. HERO       headline (emotional, <10 words, a number if you have one)
              + subhead (the outcome, in the customer's words)
              + one CTA that says what happens next
              + product shot / demo (show before you explain)
2. PROBLEM    empathy — describe their pain better than they can
3. DEMO       show the one thing it does
4. PROOF      testimonials, real names/faces
5. COMPARISON simple table vs the obvious alternative
6. PRICING    three tiers (good / better / best), one-time if possible
7. FOUNDER    a human — face, voice, why you built it
8. FOOTER     a line worth sharing; finish strong
```

For copy specifically, run every line through the copy principles: numbers not
adjectives (3), a fifth-grader gets it (7), no weak words (26), sell the desire
not the feature (24), and copy only this founder could write (9).

## The 32 principles — checklist

Grouped for scanning. Full rationale + examples: `references/principles.md`.

**Pricing & business model**
1. No free plan — free users cost more than they convert.
8. Hard paywall — ask for payment before data; that's real validation.
12. Popcorn pricing — three tiers only: good, better, best.
16. Pricing in the header — visitors read price to understand the product.
25. Let them try before they pay — best features on the page, not behind login.
27. No subscription — one-time payments sell ~10× easier.
32. Priced above competitors — nobody talks about the second-cheapest option.

**Copy & messaging**
3. Numbers, not adjectives — "save 4 hours/week," not "fast."
7. Fifth-grader headline — simple words; your mum should get it.
9. Copy only you could write — if a rival can paste it, it's too generic.
14. Steal copy from customers — write the way they already describe it.
17. Headline they recall next day — test 5, keep the one that sticks.
18. Emotional headline — make them laugh, say wow, or "what is this."
23. A name people remember — known words, no wordplay to explain.
24. Sell a human desire — money, time, health, status, less pain.
26. No weak words — cut "most / many / rarely"; make clear claims.
30. Describe it in under 10 words — if you can't, users can't either.

**Page structure & experience**
2. Three colors — black text, white background, one accent for the CTA.
4. A footer worth sharing — most won't buy but might share; finish strong.
5. OG image = YouTube thumbnail — often seen more than the site; design it.
6. One idea per screen — one screen, one message.
10. Show before you explain — a demo beats paragraphs.
15. A visible founder — a founder screen-recording beats a corporate promo.
20. Sellable from the hero alone — 80% never scroll; fix the hero first.
21. Empathy before selling — prove you understand the problem first.
22. One call to action — every extra button adds hesitation.
28. CTA says what happens next — "Analyze my website," not "Get started."
29. Testimonials before traffic — don't ask strangers to trust blindly.
31. Comparison table — show why to switch, not just what you do.

**Product strategy**
11. Do one thing — be the tool, not the Swiss Army knife.
13. Ride a wave — build on a trend people already discuss.
19. Do something new — nobody shares another clone.

## After an audit or a generate

Offer the natural next step, don't force it: after an audit, offer to rewrite
the worst offenders (switch to Generate). After generating, offer to pressure-
test it back through the audit. Keep momentum without padding the output.
