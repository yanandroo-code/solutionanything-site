# Solution Anything — Setup Plan

## Overview

Solution Anything (`solutionanything.co.uk`) is the business domain for Yan's Salesforce consultancy and the Any+ suite of AppExchange apps. This document covers the domain, email, and AppExchange setup plan.

---

## 1. Domain & DNS — Cloudflare Migration

The domain `solutionanything.co.uk` is currently registered with a third-party registrar. We are migrating DNS management to Cloudflare (free plan) for email routing and CDN benefits.

### Steps

1. **Sign up at cloudflare.com**, add `solutionanything.co.uk`, select the Free plan
2. Cloudflare scans existing DNS and imports records — verify the GitHub Pages A records are present:
   - `A @ 185.199.108.153`
   - `A @ 185.199.109.153`
   - `A @ 185.199.110.153`
   - `A @ 185.199.111.153`
   - `CNAME www yanandroo-code.github.io`
3. Cloudflare provides two nameservers (e.g. `anna.ns.cloudflare.com`, `bob.ns.cloudflare.com`)
4. At the domain registrar, **replace the existing nameservers** with the two Cloudflare nameservers
5. Wait for propagation (15 min to a few hours). Cloudflare emails when active.
6. After propagation, verify `solutionanything.co.uk` resolves to GitHub's IPs: `dig solutionanything.co.uk +short`
7. In GitHub repo Settings > Pages, re-add `solutionanything.co.uk` as custom domain and enable **Enforce HTTPS**

### Status

- [ ] Cloudflare account created
- [ ] Domain added to Cloudflare
- [ ] DNS records verified (A records + www CNAME)
- [ ] Nameservers updated at registrar
- [ ] Propagation confirmed
- [ ] GitHub Pages HTTPS enforced

---

## 2. Email — Cloudflare Email Routing

Free email forwarding from `yan@solutionanything.co.uk` to Gmail, with the ability to send from the business address via Gmail.

### Inbound (receiving)

1. In Cloudflare dashboard: domain > **Email** > **Email Routing** > Get started
2. Create route: `yan@solutionanything.co.uk` → `yanandroo@gmail.com`
3. Click the verification link that Cloudflare sends to Gmail
4. Cloudflare auto-adds the required MX and TXT records

### Outbound (sending as the business address)

1. In Gmail: **Settings** > **Accounts** > **Send mail as** > **Add another email address**
2. Name: `Yan`, email: `yan@solutionanything.co.uk`, untick "Treat as an alias"
3. SMTP server: `smtp.gmail.com`, port 587, username is your Gmail address
4. Password: a **Google App Password** (generate at myaccount.google.com > Security > App passwords; requires 2FA enabled)
5. Gmail sends a verification code to `yan@solutionanything.co.uk` — it forwards to your inbox via Cloudflare — enter the code
6. Optionally set as default "Send from" address in Gmail settings

### Status

- [ ] Cloudflare email routing enabled
- [ ] Inbound forwarding verified
- [ ] Gmail "Send mail as" configured
- [ ] App password generated and stored securely
- [ ] Test email sent and received both directions

---

## 3. AppExchange — Any+ Suite

### Business model decision

Salesforce allows only one free AppExchange listing per ISV. The four apps (AnyCal+, AnyKanban+, AnyGantt+, AnyRecordHierarchy+) are packaged as a single 2GP managed package under the `anysuite` namespace.

**Chosen strategy: Option E (hybrid)** — Free combined suite listed on AppExchange with a lightweight Pro tier, consulting as the primary revenue channel.

- **Free tier**: all four components, unlimited usage, no feature gates (pending confirmation that a ConfigLimit is allowed on a free listing — check with Salesforce Partner Community after ISV approval)
- **Pro tier** (if permitted): unlimited configurations per component, $200–490/org/year. Enforced via `ConfigLimit` feature parameter in the License Management App (LMA)
- **Consulting**: the real revenue. The suite acts as a business card and lead generator for custom Salesforce development

**Important**: before committing to the Pro tier, confirm with the Salesforce Partner Community whether a configuration limit counts as "freemium" (which requires a paid listing at $999 security review fee) or is acceptable on a free listing ($0 review fee). This determines the entire pricing model.

### Package details

| App | Namespace (standalone) | Combined namespace |
|-----|----------------------|-------------------|
| AnyCal+ | `anycalplus` | `anysuite` |
| AnyKanban+ | `anykanban` | `anysuite` |
| AnyGantt+ | `anygantt` | `anysuite` |
| AnyRecordHierarchy+ | `anyhierarchy` | `anysuite` |

The standalone namespaces and packages were used for development. The combined `anysuite` package is the AppExchange listing.

### Security review

- Free listing: $0 review fee (waiver code available)
- Paid/freemium listing: $999 per submission
- All apps must pass the same security review regardless of pricing
- Expect 4–9 weeks from submission to outcome
- Pre-review checklist: PMD clean, `WITH USER_MODE` on all SOQL/DML, no hardcoded IDs, FLS checks on any field-level operations

### ISV partner program

- [ ] ISV application submitted
- [ ] ISV application approved
- [ ] Business domain and website live (solutionanything.co.uk) — required for approval
- [ ] PADA (Partner Application Development Agreement) signed
- [ ] `anysuite` namespace registered in Dev Hub
- [ ] Package created and version built
- [ ] Security review submitted
- [ ] Listing published

---

## 4. Website — solutionanything.co.uk

Hosted on GitHub Pages via the `yanandroo-code/solutionanything-site` repository.

### Structure

```
solutionanything-site/
├── CNAME                  # solutionanything.co.uk
├── index.html             # Holding page → will become main landing page
└── SETUP.md               # This file
```

### Current state

The site has a holding page showing the Any+ suite with links to each app's individual site. The individual app sites are hosted separately:

| App | Repo | URL (pre-Cloudflare) |
|-----|------|---------------------|
| AnyCal+ | `anycalplus-site` | yanandroo-code.github.io/anycalplus-site |
| AnyKanban+ | `anykanbanplus-site` | yanandroo-code.github.io/anykanbanplus-site |
| AnyGantt+ | `anyganttplus-site` | yanandroo-code.github.io/anyganttplus-site |
| AnyRecordHierarchy+ | `anyrecordhierarchyplus-site` | yanandroo-code.github.io/anyrecordhierarchyplus-site |

### Future state

Once the domain is live, consider mapping subdomains for each app:

- `solutionanything.co.uk` — main business site
- `anycalplus.solutionanything.co.uk` — AnyCal+ (CNAME to GitHub Pages)
- `anykanbanplus.solutionanything.co.uk` — AnyKanban+
- `anyganttplus.solutionanything.co.uk` — AnyGantt+
- `anyrecordhierarchyplus.solutionanything.co.uk` — AnyRecordHierarchy+

Each subdomain needs a CNAME record in Cloudflare pointing to `yanandroo-code.github.io` and the custom domain set in the GitHub repo's Pages settings.

### Landing page plan

The main site should include:
- Hero section: Solution Anything brand, tagline, what we do
- Any+ Suite section: the four apps with descriptions and links
- Consulting section: services offered, call to action
- Contact: form or email link to `yan@solutionanything.co.uk`
- About: brief background

---

## 5. Brand

- **Business name**: Solution Anything
- **Domain**: solutionanything.co.uk
- **Email**: yan@solutionanything.co.uk
- **GitHub org**: yanandroo-code
- **Product family**: Any+ Suite
- **Typefaces**: DM Sans (headings/body), IBM Plex Mono (code/labels)
- **Brand palette**: blue gradient primary (#0B6FCC → #0958A5), inherited from the Any+ family
- **Mascots**: Otto (otter, AnyCal+), Pim (red panda, AnyKanban+), Penny (penguin, AnyGantt+), Sage (squirrel, AnyRecordHierarchy+)

---

## Instructions for AI assistants

If you are an AI helping with this project, here is the context you need:

1. **Yan is a solo Salesforce developer** building a suite of four Lightning Web Components for the AppExchange, sold under the business name Solution Anything.

2. **The four apps** share the same architecture: a dynamic LWC placed on a Lightning record page that queries child records and renders them (as a calendar, kanban board, gantt timeline, or hierarchy tree). No hardcoded object or field references. Configuration is stored in a `Config__c` custom object with a JSON field.

3. **Package structure**: each app was developed in its own 2GP package with its own namespace for isolation during development. They are combined into a single `anysuite` package for the AppExchange listing.

4. **Code repos** are under `/Users/yan/Documents/`:
   - `AnyCalPlus/calendar-viewer/` — most mature, phases 1–12 done
   - `AnyKanbanPlus/kanban-viewer/` — phases 2–10, 12 done, package built
   - `AnyGanttPlus/gantt-viewer/` — phases 1–10 done, needs security review prep and packaging
   - `AnyRecordHierarchyPlus/hierarchy-viewer/` — phases 1–10 done, needs security review prep and packaging

5. **Site repos** are sibling directories named `*-site/` with brand assets, holding pages, and brand briefs for AI-generated mascot illustrations.

6. **Business model** is still being finalised. The key open question is whether Salesforce allows a configuration limit (ConfigLimit feature parameter) on a free AppExchange listing, or whether that counts as freemium (requiring a $999 review fee). This must be confirmed in the Salesforce Partner Community after ISV approval.

7. **The site** at solutionanything.co.uk is hosted on GitHub Pages. DNS is being migrated to Cloudflare for free email routing. Email is forwarded from `yan@solutionanything.co.uk` to Gmail via Cloudflare Email Routing.

8. **Conventions**: DM Sans + IBM Plex Mono typefaces, Any+ brand palette, no emoji in code or docs unless asked, terse commit messages, separate 2GP packages per app during dev.
