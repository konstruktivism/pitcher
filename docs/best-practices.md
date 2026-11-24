# Best practices for a pitch

These rules are for whoever writes a deck: a founder, or an AI agent that drafts one. Each rule names its source:
- **data** means measured;
- **expert** means advice from investors or practitioners;
- **standard** means WCAG;
- **own rule** means a choice of this blueprint, without direct support from a source.

The rules are defaults: a deck may break one for a reason, but not a check that fails publish. The tool checks what it can (see [What publish checks](#what-publish-checks)); the rest is for a person to judge.

## Story

1. **Open with one sentence:** what the company does, and for whom. *Expert:* Sequoia.
2. **Write each headline as the claim, not a label.** Write "Revenue grew 18% a month", not "Traction". Read the headlines in a row: they should tell the whole story. *Expert:* Barbara Minto's Pyramid Principle, YC (Kevin Hale).
3. **Make one point per slide, with the evidence under it.** The evidence is a chart, a figure or an image that proves the headline. *Data:* in a study of 110 engineering students watching a technical presentation, assertion–evidence slides gave better understanding and recall than bullet slides. That was a classroom study, not proof of pitch success. *Expert:* TED.
4. **Choose the outline for the audience.**
   - For investors: problem, solution, why now, market, competition, business model, team, ask. *Expert:* Sequoia, YC.
   - For a customer or partner: the change in their world, what is at stake, the future they can reach, how you get them there, and proof. *Expert:* Andy Raskin's five moves, from Zuora's sales deck. Closing with one next step is an *own rule*.
5. **Answer the 1–3 biggest objections early.** *Expert:* Reid Hoffman's annotated LinkedIn Series B deck.
6. **End with a concrete ask:** the amount, the use of funds, the milestones it buys, and the runway. For a customer, it is one next step. *Expert:* Point Nine. Intercom's first deck ended by asking $600k for 18 months of runway.

## Evidence

7. **Show traction per period, not only cumulatively.** Label actuals and forecast apart, and give every chart a unit, a period and a source. *Expert:* Point Nine, Index Ventures.
8. **Give each chart one message.** Highlight the bar or line that matters and make the rest grey. *Expert:* Cole Nussbaumer Knaflic.
9. **Size the market bottom-up:** number of buyers × price, with the source. *Own rule*; Sequoia asks for the market, not this formula.
10. **Show the team's proof, not their titles:** why these people can solve this problem. *Data:* in a randomised experiment on AngelList, the average investor responded to information about the team, but not to traction or existing investors.
11. **Keep the caveats.** Put assumptions and risks in `detail` or the appendix; never leave them out. *Expert:* Amazon writes narrative memos of at most six pages instead of slides.

## Length and reading

12. **Keep it short.** In DocSend's 2023 pre-seed data, successful decks averaged 16 slides and unsuccessful ones 19. That is a correlation, not proof that shorter wins. Index Ventures advises the essentials in about 12 slides, plus an appendix. Public examples: Airbnb 2008 had 14 slides, Buffer 13, Intercom 8. *Data, expert.*
13. **Expect a short read, often on a phone.**
    - DocSend measured 3:44 per successful deck in an earlier study of 200 companies, and under two minutes at seed in 2023.
    - A vendor reports that about a third of decks are opened on a phone.

    So each slide must make sense alone, and at phone width. *Data.*
14. **A sent deck is not a presented deck.** A sent deck must work without the founder (Index). A presented deck must not repeat the speaker: in Mayer's research, adding text that duplicates the narration hurt learning in 16 of 16 tests, in multimedia teaching. One source serves both:
    - the headline and the visual, everywhere;
    - `detail`, only when the deck is read alone;
    - `notes`, only for the speaker.

    *Data:* Mayer. *Expert:* Duarte's *Slidedocs*.
15. **Cut decoration.** Remove whatever does not support the point. *Data:* Mayer's coherence principle held in 23 of 23 tests, in multimedia teaching.
16. **Plan the talk.** Set the seconds per slide and rehearse against them. PowerPoint's Speaker Coach aims at 100–165 words per minute. *Expert.*

## Design and access

17. **Use large text.** The components set headlines at 48 px or more and body text at 28 px or more on the 1280×720 canvas. *Own rule:* the pixel sizes are this blueprint's choice. TED's speaker guide asks for 42 pt or more on stage.
18. **Meet contrast.** Text needs 4.5:1, and large text and chart elements 3:1. Never use colour alone to carry meaning. *Standard:* WCAG 2.2 AA.
19. **Make no slide depend on animation, and respect reduced motion.** *Own rule:* the deck has no animation at all. WCAG makes switching motion off a AAA criterion.
20. **Give every image alt text.** On a product slide, show the steps, not raw screenshots. *Expert:* YC (Kevin Hale).

## Myths

- **"Exactly 10 slides" or "exactly 3 points":** Kawasaki's 10/20/30 and the rule of three from Jobs's keynotes are useful habits, not proven optimums.
- **"Famous decks are the formula":** the much-shared 2004 Facebook deck was an advertising sales deck. Coinbase's seed deck needed its covering email to state the ask.
- **"Longer reading means interest":** in 2023, investors spent 110% more time than in 2022 on the traction slides of decks that did not raise. Time on a slide can mean doubt.
- **"AI makes a deck investor-ready":** there is no evidence for it. Sequoia stresses clear thinking over slides.

## For AI agents writing a deck

- Write only claims and numbers the founder gave you, and give every number a source.
- Never invent traction, customers or quotes. Leave a field empty and say so.
- Hand the founder the headline outline from publish to check.

## What publish checks

Publish checks rules 2, 3, 4, 6, 7, 9, 12, 14, 16, 18 and 20; the components enforce 17 and 19. The plan's [Publish](plan.md#publish) section holds the exact checks and decides which ones fail and which only warn. Publish also prints the headline outline. Whether the claims are true, the market is real and the story is clear stays for a person to judge.

## Sources

- **Investors**
  - [Sequoia, Writing a business plan](https://sequoiacap.com/article/writing-a-business-plan)
  - [YC, seed deck](https://www.ycombinator.com/library/2u-how-to-build-your-seed-round-pitch-deck)
  - [YC, Kevin Hale on deck design](https://www.ycombinator.com/blog/how-to-design-a-better-pitch-deck)
  - [Reid Hoffman, LinkedIn Series B](https://www.reidhoffman.org/linkedin-pitch-to-greylock/)
  - [Point Nine, a simple pitch deck](https://medium.com/point-nine-news/a-simple-pitch-deck-a43af1f19f8c)
  - [Index Ventures via Pitch](https://pitch.com/blog/index-ventures-pitch-deck-tips)
  - [Guy Kawasaki, 10/20/30](https://guykawasaki.com/the_102030_rule/)
- **Reading data**
  - [DocSend 2023 pre-seed report](https://www.prnewswire.com/news-releases/from-growth-at-all-costs-to-cost-of-missing-out-docsend-pre-seed-report-shows-investor-shift-to-long-term-profitability-and-risk-aversion-301908433.html)
  - [DocSend fundraising research](https://www.docsend.com/startup-fundraising/)
  - [Storydoc deck statistics](https://www.storydoc.com/blog/pitch-deck-statistics) (vendor data)
- **Example decks**
  - [Airbnb 2008](https://www.slideshare.net/slideshow/airbnb-pitch-deck-from-2008/65585661)
  - [Buffer](https://buffer.com/resources/the-slide-deck-we-used-to-raise-half-a-million-dollars/)
  - [Intercom](https://www.slideshare.net/slideshow/intercoms-first-pitch-deck/60630417)
  - [Coinbase](https://barmstrong.medium.com/the-coinbase-seed-round-pitch-deck-50c8ec91d40b)
  - [Front Series A](https://front.com/blog/inside-fronts-series-a-slide-deck)
  - [Facebook 2004 ad deck](https://digiday.com/media/how-eduardo-saverin-sold-facebook-ads-in-2004/)
- **Craft**
  - [Garner and Alley, assertion–evidence study](https://www.writing.engr.psu.edu/ae_comprehension.pdf)
  - [Mayer and Fiorella, multimedia principles](https://doi.org/10.1017/CBO9781139547369.015)
  - [Duarte, Slidedocs](https://www.duarte.com/resources/books/slidedocs/)
  - [Andy Raskin, the greatest sales deck](https://medium.com/the-mission/the-greatest-sales-deck-ive-ever-seen-4f4ef3391ba0)
  - [Storytelling with Data](https://www.storytellingwithdata.com/blog/2015/03/the-biggest-bang-for-your-buck)
  - [TEDx speaker guide](https://storage.ted.com/tedx/manuals/tedxspeakerguide.pdf)
  - [Barbara Minto, Pyramid Principle](https://www.barbaraminto.com/)
  - [Amazon, 2024 shareholder letter](https://www.aboutamazon.com/news/company-news/amazon-ceo-andy-jassy-2024-letter-to-shareholders)
  - [Bernstein, Korteweg and Laws, AngelList experiment](https://onlinelibrary.wiley.com/doi/10.1111/jofi.12470)
- **Standards**
  - [WCAG 2.2 contrast](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
  - [WCAG 2.2 non-text contrast](https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast.html)
  - [WCAG 2.2 animation from interactions](https://www.w3.org/WAI/WCAG22/Understanding/animation-from-interactions.html)
- **Tools**
  - [PowerPoint Speaker Coach](https://support.microsoft.com/en-us/powerpoint/suggestions-from-speaker-coach)
