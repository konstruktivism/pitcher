---
Date: 2026-11-24
Status: Current
git_commit: 0b1d338
---

# Research: sharing and tracking pitch decks

Vendor figures are vendor claims. The legal points are research, not legal advice.

## Findings

- A GET is not a view: mail scanners and link previews open links first.
- Codes must be typed, because scanners burn one-time links.
- Time per slide needs consent: ePrivacy applies on top of the GDPR.
- Existing tools share a small core. Per-slide tracking of an HTML deck is yours to build.
- Link security comes down to a handful of server rules.

## Existing tools

- **What they log:** a visit (who, when, duration, device, rough location), seconds per page, and the share of pages seen ([DocSend](https://help.dropbox.com/share/dropbox-docsend-documents-activity-tab), [Papermark](https://www.papermark.com/pricing)).
- **Gates:** email capture, password, expiry and a disable switch are standard. Email verification, allow-lists, NDAs and watermarks are paid extras ([Papermark](https://www.papermark.com/help/article/link-settings), [DocSend](https://help.dropbox.com/share/dropbox-docsend-link-settings-in-content-library)).
- **Forwarding:** no tool detects it. DocSend advises one link per recipient ([DocSend](https://help.dropbox.com/share/does-dropbox-docsend-track-visits-from-unintended-visitors)).
- **Papermark:**
  - tracks an HTML deck as a single page, and posts active time every 10 s ([html-viewer.tsx](https://raw.githubusercontent.com/mfts/papermark/main/components/view/viewer/html-viewer.tsx), [tracking-config.ts](https://github.com/papermark/papermark/blob/main/lib/tracking/tracking-config.ts));
  - is AGPLv3, with a separately licensed `ee/` folder ([README](https://github.com/mfts/papermark)).
- **Alerts:** DocSend and Papermark mail the owner on every visit by default ([DocSend](https://help.dropbox.com/account-settings/dropbox-docsend-email-notification-settings), [Papermark](https://www.papermark.com/help/article/email-notifications)).
- **Email gates:** that they cost views is unproven. The "28.5 vs 4.3" figure comes from a vendor blog ([Pitchwise](https://www.pitchwise.se/blog/pitch-deck-attachment-vs-link)).

## Scanners and bots

- **Safe Links** scans before delivery, even without rewriting links. Only the recipient's admin can exempt a sender ([Microsoft](https://learn.microsoft.com/en-us/defender-office-365/safe-links-about), [Microsoft](https://learn.microsoft.com/en-us/defender-office-365/advanced-delivery-policy-configure)).
- **Bot share:** chat previews identify themselves; most security scanners don't. One vendor counts 15–40% of deck views as bots ([HummingDeck](https://hummingdeck.com/blog/why-your-deck-analytics-are-wrong)).
- **JavaScript:** whether Proofpoint, Mimecast or Barracuda run it is undocumented, so assume they do.
- **Filters in use:**
  - Papermark filters on user agent only ([user-agent.ts](https://github.com/papermark/papermark/blob/main/lib/utils/user-agent.ts));
  - DocSend flags visits from data centres ([DocSend](https://help.dropbox.com/security/dropbox-docsend-atypical-source-visits));
  - others flag hits within 2–5 s of sending ([lemlist](https://help.lemlist.com/en/articles/9140285-identifying-legitimate-clicks-and-opens), [CleverTap](https://docs.clevertap.com/docs/bot-activity-filter)).
- **Handling:** keep flagged rows but leave them out of stats and alerts. Don't block IP ranges, which would hit corporate VPNs. No filter proves a human.

## Timing

- `sendBeacon` on `visibilitychange: hidden` is the most reliable moment to send ([MDN](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/sendBeacon)). A Chrome test delivered 95.8% of beacons ([Nic Jansma](https://nicj.net/beaconing-in-practice-an-update-on-reliability-and-the-pending-beacon-api/)).
- Send a heartbeat every 10 s and on each slide change.
- Measure from timestamps, because background tabs throttle timers ([MDN](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API)).
- Key events by a stable slide id and version, not by position ([HummingDeck](https://hummingdeck.com/support/replace-document-without-changing-links)).

## Mail

- **Magic links:** scanners burn them ([Stytch](https://stytch.com/products/email-magic-links), [Descope](https://docs.descope.com/flows/use-cases/email-scanner), [Vanta](https://status.vanta.com/incidents/8zx3jd1kjq5w)). Use a typed code.
- **Codes:** NIST SP 800-63B-4 asks for at least 6 digits, 10 minutes, single use, and a new code must not reset the failure count ([NIST](https://pages.nist.gov/800-63-4/sp800-63b.html)). With 5 tries, a guess succeeds 5 in 1,000,000 times per code, so also count failures per recipient and deck.
- **Authentication:**
  - below 5,000 mails a day, Gmail requires SPF or DKIM and recommends all three ([Google](https://support.google.com/a/answer/81126), [FAQ](https://support.google.com/a/answer/14229414));
  - use SPF, 2048-bit DKIM and DMARC `p=none` with `rua`, on a sending subdomain.
- **Tracking:** click tracking rewrites links and logs clicks, so turn it off ([Postmark](https://postmarkapp.com/developer/user-guide/tracking-links), [Lettermint](https://lettermint.co/docs/production-checklist)).
- **Escaping:** escape user values in notifications ([Twilio](https://www.twilio.com/en-us/blog/dont-get-pwned-via-email-html-injection)).
- **EU providers:**
  - options: Scaleway TEM, Lettermint, and Mailgun's EU region ([Scaleway](https://www.scaleway.com/en/docs/transactional-email/reference-content/data-privacy/), [Lettermint](https://lettermint.co), [Mailgun](https://documentation.mailgun.com/docs/mailgun/api-reference/api-overview));
  - Postmark has no EU servers, and Resend's EU region covers sending only ([Postmark](https://postmarkapp.com/euprivacy), [Resend](https://resend.com/security/gdpr)).
- **Testing:** check delivery at Gmail, Outlook.com and a tenant with Safe Links on.

## Privacy (NL)

- **The law:** Telecommunications Act art. 11.7a. Access to a device needs consent unless strictly necessary ([wetten.overheid.nl](https://wetten.overheid.nl/BWBR0009950/2026-08-15)).
- **Reading time:**
  - EDPB 2/2023 counts data that a script sends back as "gaining access", para 53 ([EDPB](https://edpb.europa.eu/system/files/2024-10/edpb_guidelines_202302_technical_scope_art_53_eprivacydirective_v2_en_0.pdf));
  - reading time serves the sender, so it needs consent;
  - there is no AP or court ruling on this case.
- **Open log:** a log of opens might rest on legitimate interest, with a written balancing test ([EDPB 1/2024](https://www.edpb.europa.eu/our-work-tools/documents/public-consultations/2024/guidelines-12024-processing-personal-data-based_nl?page=5)). The plan asks for consent for both.
- **Walls and pixels:** the AP rejects cookie walls ([Telecompaper](https://www.telecompaper.com/nieuws/ap-cookiemuur-is-overtreding-van-avg--1283730)), and tracking pixels in mail need consent ([CNIL](https://www.cnil.fr/sites/default/files/2026-04/recommandation-pixels_de_suivi.pdf)).
- **Notices:**
  - the gate cookie is exempt, but belongs in the notice ([Ondernemersplein](https://ondernemersplein.overheid.nl/wetten-en-regels/gebruik-van-cookies-op-uw-website/));
  - give the art. 13 notice before any beacon;
  - use art. 14 when the sender supplied the address, and offer an art. 21 objection route ([13](https://gdpr-info.eu/art-13-gdpr/), [14](https://gdpr-info.eu/art-14-gdpr/), [21](https://gdpr-info.eu/art-21-gdpr/)).
- **Self-hosting:** host fonts and scripts yourself. A Munich court ruled that loading Google Fonts without consent was unlawful ([LG München I](https://gwern.net/doc/www/rewis.io/349d376d95f9b44a7e8119b1408f382e6f19938b.html); not binding in NL).
- **Retention and erasure:**
  - the AP sets no retention period, so pick one per table ([AP](https://www.autoriteitpersoonsgegevens.nl/en/themes/basic-gdpr/privacy-and-personal-data/retention-of-personal-data));
  - a hashed email address is still personal data ([recital 26](https://gdpr-info.eu/recitals/no-26/));
  - a soft delete is not an erasure.

## Link security

- Slugs: 128 random bits from a CSPRNG, base64url ([OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)).
- Only a server-side gate protects content, so deny by default ([OWASP A01](https://top10.owasp.org/2021/A01_2021-Broken_Access_Control)).
- Headers on every gated response: `Cache-Control: no-store`, `Referrer-Policy: no-referrer`, `X-Robots-Tag: noindex` ([OWASP](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)).
- Don't block share paths in robots.txt, or Google never sees the `noindex` ([Google](https://developers.google.com/search/docs/crawling-indexing/block-indexing)).
- Serve neutral Open Graph data before the gate, because unfurlers read it.
- Rate-limit per link and address with growing delays, not lockouts ([OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)).
- A gate does not stop a permitted viewer from taking screenshots.

## Decks and runtimes

- **Sequoia's outline:** purpose, problem, solution, why now, market, competition, business model, team, financials, vision ([Sequoia](https://sequoiacap.com/article/writing-a-business-plan)). YC adds early traction and ends on the ask ([YC](https://ycombinator.com/library/2u-how-to-build-your-seed-round-pitch-deck)).
- **Kevin Hale (YC):** legible, simple, obvious. Large type, one idea per slide, the conclusion on the chart, no screenshots ([YC](https://www.ycombinator.com/blog/how-to-design-a-better-pitch-deck)).
- **Kawasaki:** 10 slides, 20 minutes, type of at least 30 pt ([CBS](https://www.cbsnews.com/news/improve-your-powerpoint-presentations-with-guy-kawasakis-10-20-30-rule/)).
- **Hoffman:** add a risk slide ([Hoffman](https://www.reidhoffman.org/linkedin-pitch-to-greylock/)).
- **DocSend:**
  - in 2022, investors spent under 3 minutes per deck, viewed product and business model most, and failed decks lacked financials ([TechCrunch](https://techcrunch.com/2022/09/22/science-of-pitch-decks/amp));
  - in 2019, successful decks averaged 20 pages against 17 for failed ones ([TechCrunch](https://techcrunch.com/2019/04/12/data-tells-us-that-investors-love-a-good-story)).
- **reveal.js (MIT):**
  - keys, swipe, edge controls, progress, slide numbers, `#/id` links, an overview, a speaker view, a print view, a scroll view and events ([config](https://revealjs.com/config/), [speaker view](https://revealjs.com/speaker-view/), [PDF](https://revealjs.com/pdf-export/), [scroll view](https://revealjs.com/scroll-view/), [events](https://revealjs.com/events/));
  - notes sit in the page source, so leave them out of what viewers get.
- **Slidev:** builds one app per deck, with a fixed base path ([hosting](https://sli.dev/guide/hosting)). It suits markdown in git better than an app that serves many links.
- **Pitch:** tracks only after a consent popover ([Pitch](https://help.pitch.com/en/articles/5592127)).

## Untested

- Whether scanners run JavaScript, and how reliable beacons are on iOS Safari. A Microsoft 365 tenant with Safe Links on settles both.
