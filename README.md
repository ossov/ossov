# Ethan Ossowski

Software development student working mostly in TypeScript, Shopify/Liquid and browser
extensions. Recent work has been an AI shopping assistant and a storefront rebuild for a
commercial e-commerce site.

Most repositories below are private — they contain client work — so this page is the summary.
Happy to walk through any of it.

---

## AI Shopping Assistant · `essence-chat`

**TypeScript · Vercel serverless · Anthropic API · vanilla JS**

A chat assistant for a flower-essence store. Customers describe how they *feel* — "I'm
overwhelmed and can't sleep" — and it recommends the matching product and renders it as a card
on the storefront.

Deliberately built **without a vector database**: the catalogue fits in the model's context
window, so the model does the feeling→product matching natively. At this catalogue size that's
simpler and more accurate than retrieval.

What I worked on:

- **Cut inference cost ~88% per message.** Measured where the money actually went — the
  catalogue dominates every request, and a low-traffic store pays the prompt-cache *write* on
  most first messages, so payload size matters more than cache strategy. Trimmed the rendering
  and narrowed the catalogue scope: ~70K tokens → ~12.8K.
- **Ran two configurations side by side** behind separate endpoints, so the model and catalogue
  scope could be changed against live traffic without touching the working setup.
- **Closed a live security hole.** The endpoint was gated only by an `Origin` header — which is
  self-reported, so it could be driven directly from a script at real cost per request. Moved it
  behind Shopify's App Proxy with HMAC-SHA256 signature verification, plus rate limiting and
  email alerting on traffic floods.
- **Crisis handling.** Suicide and self-harm disclosures are detected by the model rather than
  by keyword matching — keywords can't read tense, negation or subject, so "I *used to* feel
  that way" and "my friend is struggling" are handled differently from a present-tense
  disclosure. On a genuine disclosure it drops the product recommendation entirely and points to
  crisis resources.
- **Test suites** — 30 adversarial tests (jailbreaks, pressure to make medical claims,
  hallucination bait, malformed input) and 17 auth tests covering signature forgery. The
  adversarial suite gates deploys.

Also debugged some things I'd rather have caught earlier: a bug that permanently broke any
conversation past ~10 exchanges, and product cards that silently stopped appearing mid-chat
because the client was stripping markers out of the history sent back to the model.

---

## Storefront Rebuild · `pacific-essences-theme`

**Shopify · Liquid · CSS · JavaScript**

A Shopify theme built on Dawn, then substantially rebuilt around how these products are
actually chosen — by how someone feels, not by specification. ~70 commits.

- Product page split into distinct bands, with the buy box reworked around size, quantity and
  compare-at pricing behaviour
- Fourteen custom sections — energetics, ingredients, reviews, FAQ, families, directory,
  contact, quiz, journal, and flip-card pickers
- Product gallery showing the botanical source beside the bottle; painting moved off the main
  thread after the redesign introduced jank
- Content pages restructured — a chronological timeline, a grouped FAQ, a unified distributor
  directory, cross-linked policies
- Store CSS consolidated into a single stylesheet with shared design tokens

`pacess-theme-project` is the earlier iteration this one grew out of.

---

## Elem3x — Browser Extension

**TypeScript · webpack · Chrome Manifest V3**

A developer tool for visually selecting elements on any page and inspecting or editing their
CSS, without opening devtools.

`Elem3x` is the original vanilla-JS build; **`Elem3x.ts`** is the TypeScript rewrite — proper
module structure, a webpack build, and persisted settings via the storage API.

---

## Elsewhere

- `bruhtonium-frontend` — JavaScript front-end project
- `SB_W` — HTML/CSS

