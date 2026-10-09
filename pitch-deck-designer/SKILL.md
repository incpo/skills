---
name: pitch-deck-designer
description: "Design compelling pitch decks and investor presentations with strong narrative and visual clarity. Use this skill whenever the user asks to create a pitch deck, investor presentation, Demo Day deck, seed round deck, Series A deck, fundraising slides, or startup presentation. Also trigger when the user asks for help writing pitch copy, slide text, taglines for fundraising, or wants feedback on an existing pitch deck. Covers both the visual design principles (legibility, simplicity, obviousness) and the narrative structure (problem, solution, traction, insights, business model, market, team, ask). Even if the user just says 'help me pitch my startup' or 'I need slides for investors', use this skill."
---

# Pitch Deck Designer

A skill for creating investor-grade pitch decks that are legible, simple, and obvious — based on YC's Demo Day presentation principles.

## Core Philosophy

Every slide must pass three tests:

1. **Legible** — Can someone with bad eyesight in the back row read it?
2. **Simple** — Does it express exactly ONE idea?
3. **Obvious** — Can a stranger understand it at a glance?

If a slide fails any of these, it needs rework. Understanding comes before excitement. If investors can't understand you, they can't remember you.

## When to Read References

- For **slide-by-slide structure and narrative arc** → Read [references/seed-deck-structure.md](references/seed-deck-structure.md)
- For **creating the .pptx file itself** → Also read the `pptx` skill at `/mnt/skills/public/pptx/SKILL.md`

## Workflow

### Step 1: Gather Inputs

Before designing anything, extract from the user:

1. **What does your company do?** (one sentence)
2. **What problem are you solving?** (with real-world impact)
3. **What's your solution?** (concrete benefits, not features)
4. **Do you have traction?** (revenue, users, growth rate — any numbers)
5. **What's your unfair advantage / key insight?**
6. **What's the business model?**
7. **How big is the market?**
8. **Who's on the team and why are they the right people?**
9. **What's the ask?** (how much money, what milestones it funds)
10. **Context**: Demo Day (2.5 min, 5-7 slides) vs. Seed Deck (longer, for follow-up meetings) vs. Series A

If the user can't answer all of these, help them figure it out. These are the raw materials — the presentation can't be better than the clarity of the underlying thinking.

### Step 2: Identify the 5-7 Key Ideas

From the inputs, distill the 5-7 most important things an investor should remember. People can only retain a few points from a short presentation. Every slide should map to one of these ideas. If a slide doesn't serve one of your key ideas, cut it.

Present these to the user for confirmation before proceeding.

### Step 3: Write the Slide Copy

For each slide, write the text following these rules:

**Text Rules:**
- **One idea per slide.** If you have two ideas, make two slides.
- **Fewer words wins.** Cut every word that doesn't earn its place. Then cut more.
- **Be explicit, not implicit.** Don't make the audience interpret a graph — write the conclusion ON the slide. ("Revenue grew 5x in 6 months" next to the chart, not just the chart alone.)
- **No jargon unless your audience lives in that jargon.** Even then, simpler is better.
- **No caveats, nuances, or hedge words on slides.** Save those for the spoken pitch or Q&A.
- **Taglines and descriptions should be concrete.** "We do X for Y" beats "We're reimagining the future of Z."

**What to Avoid in Copy:**
- Packing multiple nuances of the business into one slide
- Subtle humor or memes (investors check email when confused)
- Excessive branding per slide
- Treatises on market philosophy — this is a seed deck, not a whitepaper

### Step 4: Design the Slides

When creating the actual .pptx, follow these design principles on top of the pptx skill's guidance:

**Legibility Rules:**
- Use LARGE type — titles 36-44pt bold minimum, body 18-24pt
- Bold text, simple fonts (no thin/light weights)
- High contrast from background (dark text on light, or white text on dark)
- Position key text at the TOP of the slide (easier to read from back of room)
- No small gray text at the bottom of slides — that's where ideas go to die

**Simplicity Rules:**
- One idea per slide, maximum
- 5-7 slides for Demo Day; 10-12 for a seed deck sent to investors
- If an idea needs multiple slides, that's okay — but try hard to do it in one
- Remove everything that doesn't serve the one idea on that slide

**Obviousness Rules:**
- Every slide should pass the "stranger test": show it to someone for 3 seconds, they should get the point
- Make ideas EXPLICIT — add CliffsNotes-style captions to graphs and charts
- Avoid diagrams (they're "little mazes for ideas")
- Avoid screenshots (almost always illegible, complex, and non-obvious)
- If showing how a product works, use a simple numbered list of steps instead of a UI screenshot
- Remove information distractions: unnecessary labels, logos, decorative elements

**The One Exception:**
Showing overwhelming complexity is okay ONLY when your point IS the complexity — e.g., showing a wall of competitor logos to illustrate how fragmented a market is.

### Step 5: Review & Refine

After creating the deck, review each slide against:

| Check | Question |
|-------|----------|
| Legible? | Can I read every word from 20 feet away? |
| Simple? | Does this slide have exactly one idea? |
| Obvious? | Would a stranger get the point in 3 seconds? |
| Explicit? | Are conclusions stated, not implied? |
| Clean? | Is there anything on this slide that could be removed? |

If any slide fails, rework it. Then run the standard pptx QA process.

## Slide-by-Slide Template

For the recommended narrative structure and slide order, read the reference:

→ [references/seed-deck-structure.md](references/seed-deck-structure.md)

## Quick Checklist for the User

Share this with the user as a final sanity check:

- [ ] Every slide expresses ONE clear idea
- [ ] An outsider can understand each slide in under 5 seconds
- [ ] All text is large, bold, high-contrast
- [ ] Graphs and charts have explicit captions stating the takeaway
- [ ] No screenshots of product UI (use step lists instead)
- [ ] No diagrams (unless absolutely unavoidable)
- [ ] No memes, animations, transitions, or subtle humor
- [ ] Total slide count: 5-7 (Demo Day) or 10-12 (seed deck)
- [ ] The ask slide clearly states how much money and what it funds
- [ ] You would be proud to show this to the smartest investor you know
