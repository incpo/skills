# The 32 Principles — full rulebook

Reference for the `viral-product` skill. Each entry has the **rule**, the
**why** behind it, and **how to apply** it during an audit or a generate. These
are opinionated heuristics for small-team products fighting for scarce
attention — see SKILL.md on when they don't apply.

Jump to a group:
- [Pricing & business model](#pricing--business-model) — 1, 8, 12, 16, 25, 27, 32
- [Copy & messaging](#copy--messaging) — 3, 7, 9, 14, 17, 18, 23, 24, 26, 30
- [Page structure & experience](#page-structure--experience) — 2, 4, 5, 6, 10, 15, 20, 21, 22, 28, 29, 31
- [Product strategy](#product-strategy) — 11, 13, 19

---

## Pricing & business model

### 1. No free plan
**Rule:** Drop the free tier. **Why:** free users raise support load and server
cost, pull the roadmap toward features paying users don't want, and under ~3%
convert. **Apply:** if a page leads with "Free forever," flag it — unless a free
tier is the actual growth loop (see the caveat in SKILL.md). Suggest a free
*trial* or a cheap entry tier instead of a free *plan*.

### 8. Hard paywall
**Rule:** Ask for payment before you ask for data. **Why:** a signup is not
validation; a credit card is. **Apply:** flag long signup flows that collect
data before showing value or asking for money. The exception: products that
genuinely need onboarding data to work at all.

### 12. Popcorn pricing
**Rule:** Three tiers — good, better, best. **Why:** visitors came to buy, not
to study a spreadsheet; every extra tier is another decision and another exit.
**Apply:** count the tiers. More than three → recommend which three to keep and
why. One tier can be fine for a single simple product; the sin is five.

### 16. Pricing in the header
**Rule:** Put "Pricing" in the top nav. **Why:** the pricing section is one of
the first places visitors look — they use it to understand the product, not
only the price. **Apply:** if pricing is buried or missing from the nav, flag
it. Hidden "contact us for pricing" is a red flag for this product type.

### 25. Let them try before they pay
**Rule:** Put the best features on the page; don't hide everything behind login.
**Why:** playing beats reading. **Apply:** look for an interactive demo, a live
example, or sample output on the page itself. This can coexist with a hard
paywall (8): let them *experience* value, charge before they *keep* it.

### 27. No subscription
**Rule:** Prefer one-time payment; add a subscription only if you truly can't
ship without recurring cost. **Why:** people are subscription-fatigued;
one-time offers sell ~10× easier. **Apply:** if the product has no real ongoing
server/API cost, question the monthly plan. If it does (hosting, API calls),
a subscription is defensible — say so.

### 32. Priced above competitors
**Rule:** Charge more than the alternatives. **Why:** nobody talks about the
second-cheapest option; price signals quality and funds the product. **Apply:**
if the page competes on being cheapest, flag it. Higher price needs the proof
and positioning to back it (testimonials, comparison table, clear outcome).

---

## Copy & messaging

### 3. Numbers, not adjectives
**Rule:** Replace vague adjectives with concrete numbers. **Why:** "fast" is
forgettable; "save 4 hours every week" is not. **Apply:** hunt for "fast,"
"easy," "powerful," "seamless" and rewrite each with a number or a specific
outcome. This is one of the highest-leverage copy fixes.

### 7. Fifth-grader headline
**Rule:** The headline uses simple words a child could read. **Why:** complexity
kills curiosity; jargon makes people bounce. **Apply:** flag headlines with
industry jargon, abstractions, or clever constructions. Rewrite plain.

### 9. Copy only you could write
**Rule:** The copy should be impossible for a competitor to paste onto their
site. **Why:** generic copy proves nothing and differentiates nothing.
**Apply:** ask "could any rival use this exact sentence?" If yes, it's too
generic — push for specifics from the founder's real experience and the
product's real behavior.

### 14. Steal copy from customers
**Rule:** Write the way customers already describe the product. **Why:** they
describe it better and more believably than the founder does. **Apply:** in a
generate, ask the user for real quotes, reviews, or support messages and mine
them for phrasing. In an audit, flag marketing-speak that no real user would say.

### 17. Headline they remember tomorrow
**Rule:** Write five headlines, show friends, ask 24h later which stuck; keep
that one. **Why:** memorability is the test, not cleverness. **Apply:** when
generating, actually offer 3–5 headline options and note which is most likely to
stick and why, rather than committing to one silently.

### 18. Emotional headline
**Rule:** The headline should make people feel something — laugh, "wow," or
"what is this?!" **Why:** people remember feelings, not features. **Apply:** flag
flat, purely-descriptive headlines. Pair with 7 (simple) and 3 (numbers): the
best headlines are simple, concrete, *and* emotional.

### 23. A name people remember
**Rule:** Use words people already know; avoid made-up words, wordplay, and
names needing explanation. **Why:** a name you must spell out or explain leaks
attention. **Apply:** if asked about naming, favor real, spellable, sayable
words. Flag names that need a "it's like X but with a Y" explanation.

### 24. Sell a human desire, not a feature
**Rule:** Sell the outcome — more money, more time, better health, more status,
less pain. Features are just the vehicle. **Why:** people buy the destination,
not the engine. **Apply:** for each feature the page lists, ask "so what does
the buyer *get*?" and lead with that. This reframes most feature lists.

### 26. No weak words
**Rule:** Cut "most," "many," "rarely," "often," "usually." **Why:** nobody knows
what they mean; they hedge the claim. **Apply:** replace with a clear, pictur-
able claim the reader can challenge. "Works with most tools" → "Works with
Slack, Notion, and Figma."

### 30. Describe it in under 10 words
**Rule:** One sentence, under ten words, and a stranger gets it. **Why:** if the
founder can't, the users won't be able to either — and can't spread it.
**Apply:** try to write the sub-10-word description. If you can't, the product
or its positioning is unfocused (often a symptom of violating principle 11).

---

## Page structure & experience

### 2. Three colors
**Rule:** Black text, white background, one accent color for the buy button.
**Why:** every added color competes for attention and dilutes what matters.
**Apply:** flag busy, multi-color pages. The accent color should appear almost
only on the primary CTA, so the eye goes straight there.

### 4. A footer worth sharing
**Rule:** End with something people want to share. **Why:** 97% won't buy, but
they might share; people remember what they see last. **Apply:** flag dead,
boilerplate footers. Suggest a memorable closing line, a bold restatement of
the promise, or a shareable hook.

### 5. OG image = YouTube thumbnail
**Rule:** Design the social-share (Open Graph) image like a YouTube thumbnail —
made to earn the click. **Why:** the OG image is often seen more than the site
itself; "if they don't click, they don't watch." **Apply:** check for a
deliberate OG image, not a default screenshot. Big, legible, curiosity-driving.

### 6. One idea per screen
**Rule:** Each screen communicates exactly one idea. **Why:** saying everything
at once means nothing lands — like the Instagram feed, one thing at a time.
**Apply:** flag cluttered sections that juggle multiple messages. Split them so
each scroll-height delivers a single point.

### 10. Show before you explain
**Rule:** Lead with a demo, not paragraphs. **Why:** a demo communicates more
than any wall of text. **Apply:** flag pages that open with dense description
and no visual. Recommend a product shot, GIF, or short clip up top. Pairs with
25 (let them try).

### 15. A visible founder
**Rule:** Put a real founder on the page — face and voice. **Why:** people buy
from people; a founder screen-recording beats a corporate promo or a wall of
features. **Apply:** flag faceless pages. Suggest a short founder video or a
signed note. Especially strong for small/indie products.

### 20. Sellable from the hero alone
**Rule:** The hero must sell on its own. **Why:** ~80% of visitors never scroll
past it; if they don't get it and want it in seconds, it's already lost.
**Apply:** read *only* the hero and ask: do I understand what this is, who it's
for, and why I'd want it? If not, fix the hero before anything else. This is
usually the single highest-impact audit finding.

### 21. Empathy before selling
**Rule:** Show you understand the problem before you pitch the solution.
**Why:** people trust a solution only after they believe you get their pain.
**Apply:** look for a problem/empathy section early. If the page jumps straight
to features, recommend describing the problem "better than they can" first.

### 22. One call to action
**Rule:** One CTA, one next step. **Why:** multiple paths cause hesitation, and
hesitation causes people to choose none. **Apply:** count distinct CTAs
competing in the hero and throughout. Consolidate to a single primary action;
demote the rest to secondary links at most.

### 28. CTA says what happens next
**Rule:** The button text names the action. **Why:** "Get Started" means
nothing; "Analyze my website" removes uncertainty. **Apply:** flag generic
button labels ("Get Started," "Learn More," "Sign Up") and rewrite them to
describe the exact next thing the user will do.

### 29. Testimonials before traffic
**Rule:** Don't launch without testimonials. **Why:** a page with none asks
strangers to trust you blindly. **Apply:** flag the absence of social proof. In
a generate, tell the user to collect a few quotes from early users/friends/beta
testers first, and leave clearly-marked slots for them.

### 31. Comparison table
**Rule:** Show a simple table comparing you to the obvious alternative on the
features customers care about. **Why:** people don't care what you do; they care
why they should switch. **Apply:** if there's a clear incumbent, recommend a
focused comparison table (few rows, the ones that matter) that makes the choice
obvious.

---

## Product strategy

### 11. Do one thing
**Rule:** Be known for one thing. **Why:** the more it does, the less people
remember; nobody remembers the Swiss Army knife, they remember the tool that
solved their problem. **Apply:** if the page lists many unrelated capabilities,
flag the lack of focus. Ask what the *one* thing is. This is often the root
cause when principle 30 (describe in <10 words) fails.

### 13. Ride a wave
**Rule:** Build around a trend, technology, or problem people already discuss.
**Why:** the wave does half the marketing — you borrow existing attention.
**Apply:** in strategy conversations, connect the product to a current wave the
audience is already talking about. Don't manufacture a fake trend; find the real
one the product sits on.

### 19. Do something new
**Rule:** Do something people haven't seen. **Why:** nobody shares another clone;
surprise is what gets talked about. **Apply:** if the product is a
me-too clone, flag that virality will be hard and push for the one surprising,
novel angle. Pairs with 9 (copy only you could write) and 11 (one thing).
