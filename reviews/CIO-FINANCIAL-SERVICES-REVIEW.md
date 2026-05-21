# CIO Stakeholder Review — FullContact Data Marketplace Roadmap

**Date:** 2026-05-08
**Prototype:** `/Users/jakecohen/data-marketplace-prototype/index.html` — 8 phases (1, 1B, 2, 3, 4, 5, 6, 7)
**Reviewer (synthetic):** Daniel R. Marchetti, EVP & Chief Information Officer, **Cascadia Heritage Financial Group** — a publicly-traded regional bank holding company
**Methodology:** AI-simulated stakeholder review by Claude (Anthropic) on Jake Cohen's instruction. The persona, scoring, and verdicts are simulated based on the artifact provided and on publicly available regulatory frameworks; this is a pre-flight stress test of the roadmap before live customer interviews, not a real customer review.

---

## Reviewer Vignette

Daniel Marchetti (51), 4th year as CIO at **Cascadia Heritage Financial Group** — $32B-asset regional holding company HQ Portland, OR. 192 branches across OR/WA/ID/UT/NV/CO, ~$14B AUM wealth arm, small-commercial / SBA book. ~5,200 employees. IT spend tracking to $352M against a $349M plan. App-dev → enterprise architecture → CIO. Hired into Cascadia after the 2022 board-mandated core modernization decision (year 3 of a 4-year FIS Modern Banking Platform migration).

The stack:
- **Salesforce Financial Services Cloud** as system of record for retail and wealth (deployed 2023).
- **Snowflake** as the enterprise analytics platform; data lands via FIS extracts, Salesforce CDC, and ~140 Fivetran connectors.
- **Apigee** as corporate API gateway — every external API call crosses it.
- **Okta** for SSO (OIDC + SAML), **CyberArk** PAM, **Splunk** SIEM, **Symantec/Broadcom** DLP, **OneTrust** for privacy / DSAR.
- Existing data-vendor spend: **LiveRamp**, **Acxiom InfoBase**, **LexisNexis Risk** (KYC/AML), **Equifax/Experian/TransUnion** (FCRA-direct), **Verafin** (BSA/AML), **Plaid**.

Cascadia is examined by the **Federal Reserve** (state-member holding co), **OCC** (lead bank), **CFPB** (above $10B), and **NY DFS** (NY commercial LPO). The board's Risk & Cybersecurity subcommittee meets quarterly on vendor concentration, model risk, and AI policy. CRO, CCO, CISO, CDO are peers — nothing material moves without CRO and CCO in the room. Daniel reports to CEO.

Not vendor-hostile, vendor-skeptical. A 2019 third-party breach at his prior bank cost $11M and 14 months of remediation. Treats third-party risk as board-level. Also knows reflexive "no" gets a CIO replaced in 18 months — his team biases toward "yes, with controls" when the controls are achievable on a defensible timeline.

He got the prototype URL from the CMO and spent ~90 minutes walking the 8 phases, with screenshots forwarded to the CISO and CCO for parallel reaction. This is his consolidated take.

---

## Executive Summary

**Overall composite: 5.4 / 10.**

There is a real product here. The Phase 1 / 1B / 2 surface — file enrichment, self-serve credits, customer recognition tag as input — is the kind of marketing-ops capability my CMO will absolutely want, and the prototype shows craft (the phase explainer, the new/changed pulse markers, the unified credential across batch / API / MCP, the per-dataset enable toggle). I would walk this in front of my CMO and CDO with a recommendation to scope a pilot.

What I cannot do is sign the multi-year platform commitment that Phases 4–7 imply, in the form they're shown. A bank is not a grocery chain. When a marketing tool quietly evolves into a real-time agent-callable enrichment fabric stitched to Experian credit attributes and 24+ outbound destinations, my regulators stop seeing "marketing software" and start seeing **a third-party data ecosystem with FCRA, GLBA Safeguards, SR 11-7 model-input, and SR 13-19 / OCC 2013-29 vendor-management implications, all converging on a single API key**. The roadmap as shown does not have the control layer to absorb that, and the MCP surface in Phase 6 — bearer token in a config file, no per-tool entitlements, no agent budget — is not something I can put in front of my Risk & Cyber subcommittee in 2026 without a substantial redesign.

My adoption posture: **a yes through Phase 1B with controls, a conditional yes through Phase 3 with FCRA carve-outs and audit-trail work, an annual re-evaluation on Phase 4, a likely no on Phase 5 marketplace self-onboarding without vendor-tier classification, a hard no on Phase 6 MCP in its current form, and a patient watch on Phase 7.** What would change that is not exotic: it is the discipline of treating regulated-industry buyers as a tier-one design constraint instead of an afterthought, and shipping a financial-services-specific bundle (KYC / sanctions / BSA-AML / beneficial ownership) that demonstrates FullContact understands the vertical.

---

## Per-Phase Score Table

| Phase | Title | Score | One-line CIO verdict |
|---|---|---:|---|
| **1** | UI Batch MVP | **7.0** | Defensible marketing utility; needs DLP egress story and SSO. |
| **1B** | Self-serve credits | **5.5** | Convenience for marketers; uncontrolled spend surface for me. |
| **2** | CR tag input | **5.0** | High value, high regulatory load — GLBA / Reg P / wiretap exposure on bank web traffic. |
| **3** | 18 Insight bundles | **4.5** | "Employment History" and "Email Risk" in a self-serve UI is FCRA-territory for a bank. |
| **4** | 3rd-party vendors | **3.5** | Hidden vendor royalty fails SR 13-19 / OCC 2013-29 vendor due diligence. |
| **5** | Marketplace catalog | **3.0** | N-th party vendor sprawl; vendor self-classification is unacceptable for a regulated buyer. |
| **6** | API + MCP | **2.5** | Agentic access via bearer-token-in-config-file is a board-level red flag in 2026. |
| **7** | Workflows | **3.5** | Activations to 24+ destinations on a schedule is a GLBA Safeguards data-flow inventory problem. |

**Score-weighted composite:** 5.4. Like the FreshWay panel, the trajectory is monotonically downward from Phase 1 through Phase 6, but the inflection point for a bank is earlier (Phase 2 instead of Phase 3) because GLBA covers website-traffic data the moment a customer or prospect is identifiable, and it's later in severity (Phase 6 is a 2.5 not a 1) because I rate the *technical* MCP work above what FreshWay's CFO did — my issue is governance, not feasibility.

---

## P0 Issues — Must Fix Before Adoption

These are framed against specific regulatory or governance frameworks. Each one corresponds to a control my own ITGC, vendor-management, or model-risk auditors will ask about by name.

### P0-1 · Vendor management does not survive SR 13-19 / OCC 2013-29 / FFIEC IT Booklet review at Phase 4 and beyond

**Frameworks:** SR 13-19 (Federal Reserve guidance on managing outsourcing risk), OCC Bulletin 2013-29 / 2020-10 (third-party relationships), FFIEC IT Examination Handbook — Outsourcing Technology Services Booklet, and the 2023 Interagency Guidance on Third-Party Relationships (OCC / FRB / FDIC).

**Gap:** The roadmap stacks vendors — FullContact, Experian (Phase 4), TruAudience (Phase 4), PlaceIQ (Phase 5), MountainRiver (Phase 5), and an open vendor self-onboarding pipeline (Phase 5) — and the prototype explicitly states "vendor royalty tracked in back-end billing — not surfaced to customers." For a bank, every one of those vendors is a separate **fourth-party** relationship under interagency guidance. That requires:

- A documented inventory of every fourth party touching customer data or NPI.
- Subservicer SOC 2 Type II reports — or attestation that FullContact has them and is contractually able to share findings.
- A right-to-audit clause that flows down through FullContact to the underlying vendor.
- Concentration risk analysis if a single vendor (Experian) appears in multiple FullContact bundles.
- Notice and consent before FullContact materially changes its subservicer list, with right to terminate without penalty if the bank's risk team rejects a new fourth party.

**What's absent:** no mechanism in the prototype, no contract-language hooks, no subservicer page, no per-dataset due diligence artifacts in the catalog modal. The "Become a vendor" CTA on the Phase 5 marketplace card, in particular, signals an intention to grow the fourth-party population without bank-customer ratification — that is a non-starter.

**Fix:** Per-vendor due-diligence pack accessible from the dataset modal (SOC 2 Type II date, ISO 27001 status, SIG questionnaire, BCP/DR test results, breach history); contractual flow-down of right-to-audit; 60-day prior notice on subservicer additions; tenant-level "approved vendors" allowlist that requires bank Risk approval to extend.

---

### P0-2 · Phase 4 Experian flow triggers FCRA permissible-purpose obligations that the prototype does not surface

**Frameworks:** Fair Credit Reporting Act, **15 USC § 1681b** (permissible purposes), § 1681e (compliance procedures by CRAs and resellers), § 1681m (adverse-action notice obligations on users), Regulation V (12 CFR Part 1022). For a bank, FCRA applies *literally* — we are a "user of consumer reports" by statute the moment we touch a credit attribute for a credit, employment, insurance, or housing eligibility decision.

**Gap:** Phase 4 introduces "Experian Demographics+" with **net worth tier, credit range, and homeowner status** at +$32 CPM, marketed as a marketing-enrichment add-on, with no per-pull permissible-purpose attestation, no per-row audit trail tied to a named user, no purpose-specific gating on credit-range fields, and no written FCRA non-applicability opinion. The two consent checkboxes at the Confirm screen reference GDPR / CCPA / CPRA — they do not reference FCRA, and they do not capture the *user's* declared purpose.

This is more dangerous for a bank than for a grocery chain. A grocer who pulls credit_range and uses it to decide who gets a mailer probably gets a class action eventually. A bank that pulls credit_range and uses it — even unintentionally — to influence whether a marketing offer for a HELOC is sent triggers regulatory referrals from the OCC's compliance examination, possible CFPB enforcement under UDAAP, and an FCRA private right of action under § 1681n / o.

The "FullContact already operates under FCRA gray zone" framing acknowledged in the materials provided is precisely the problem. A bank does not get to adopt a tool that operates in a gray zone — our regulators read the tool's terms and judge it by its capabilities, not its marketing copy.

**Fix:** (a) Per-pull permissible-purpose attestation as a required dropdown at the Confirm screen and as a required header on every API/MCP call, with the FCRA permissible-purpose enumeration as an option set when credit attributes are selected; (b) tenant-level field blocklist that, by default, disables all FCRA-adjacent fields for any tenant on a "regulated financial services" plan; (c) per-row immutable audit log including initiator, timestamp, dataset, fields returned, and the declared purpose, retained 7 years and exportable in a SOX-friendly format; (d) a written FCRA non-applicability legal opinion from FullContact's General Counsel specific to each plan, with indemnification language that survives the contract.

---

### P0-3 · GLBA Safeguards Rule data-flow inventory cannot be built against this product as designed

**Frameworks:** Gramm-Leach-Bliley Act, **16 CFR Part 314** (FTC Safeguards Rule, as amended Dec 2021 / effective June 2023), Regulation P (12 CFR 1016, financial privacy notices), and the FFIEC Information Security Booklet.

**Gap:** GLBA Safeguards § 314.4(c)(2) requires the bank to "know where customer information is collected, stored, or transmitted" and to maintain a written information security program based on that inventory. Phase 7's Activations write enriched data to "Salesforce, HubSpot, Google Ads, Meta, Snowflake, SFTP/S3, and 24+ connectors." Phase 2 ingests Customer Recognition tag traffic from the bank's website — which, if the visitor is a customer or prospect, is **non-public personal information** the moment FullContact resolves the visitor to a person identifier.

The prototype provides no:
- Standing data-flow diagram artifact for the customer's CISO / privacy office to ingest into the OneTrust data map;
- Tenant-level retention / expiry policy on resolved data;
- DSAR (data subject access request) hook that would let the bank fulfill a CCPA / Reg P / state-law deletion request that propagates through the marketplace;
- Egress controls or DLP integration points (bank Symantec/Broadcom DLP needs to inspect anything leaving the customer's tenant);
- Annual board reporting artifact (Safeguards § 314.4(i) requires the qualified individual to report annually to the board).

**Fix:** Ship a GLBA Safeguards artifact pack — data-flow diagram, retention schedule, breach-notification timeline, subservicer list, annual board report template — as a standard customer-deliverable. Wire DSAR fulfillment into the Activations engine so a deletion in OneTrust propagates to FullContact and downstream destinations. Provide DLP policy hooks (CASB-friendly egress endpoints, file-format manifests) so DLP can classify outbound data.

---

### P0-4 · Phase 6 MCP surface fails NYDFS 23 NYCRR 500 third-party access controls and SR 11-7 / SR 23-4 model risk obligations

**Frameworks:** **NYDFS 23 NYCRR 500.11** (third-party service provider security policy) and **500.12** (multi-factor authentication, including for third-party access), **SR 11-7** (Federal Reserve / OCC supervisory guidance on model risk management), and the 2023 Interagency Statement on AI / **SR 23-4 framing** for AI model governance, plus the **NIST AI Risk Management Framework** the OCC has adopted as informal expectation.

**Gap:** The prototype's MCP configuration shows a bearer token pasted into `~/Library/Application Support/Claude/claude_desktop_config.json`. That violates 500.12 in plain language: a bearer token in a config file is single-factor, long-lived, and impersonates the human without ever authenticating them. There is no SSO, no OIDC / SAML federation, no PIV / FIDO2 step-up, no per-tool entitlement scoping, no human-in-the-loop on sensitive attributes, no agent budget, no per-call provenance back to a named user, and no rate limit / circuit breaker on a runaway agent loop.

Beyond authentication, the MCP server exposes `enrich_person`, `enrich_company`, `search_marketplace`, `list_datasets`, `list_audiences`, and `run_recipe` as undifferentiated tools. `run_recipe` is the most dangerous tool listed and the least specified — a recipe is, by Phase 7's own definition, a chained transformation that may write to "Salesforce, HubSpot, Google Ads, Meta, Snowflake, SFTP/S3, and 24+ connectors." Letting an agent invoke `run_recipe` with no dry-run, no cost estimate, no human approval, and no destination allowlist is a board-reportable incident waiting to happen.

On the model-risk side: any data sourced from the marketplace that flows into a model — credit decisioning, fraud, marketing propensity, customer segmentation — drags **SR 11-7** model-risk obligations onto FullContact-sourced features. That requires data-quality SLAs, lineage, version pinning, methodology documentation per attribute, and ongoing performance monitoring. The prototype's vendor self-attestation pattern in Phase 5 is the opposite of what SR 11-7 contemplates.

**Fix:** OAuth 2.1 / OIDC with refresh-token rotation for MCP, federated through customer's IdP (Okta, Azure AD); per-tool MCP entitlements that the bank's IAM team can configure (read-only / cost-estimate / write-and-billed / destructive); separate credential set for MCP from REST so they can be rotated and revoked independently; mandatory human-in-the-loop approval for sensitive attributes (credit, employment, household income); per-agent monthly budget that returns 402 when exhausted; runaway-loop detection with auto-pause and Splunk-friendly webhook; published per-attribute methodology and version pin support; SOC 2 Type II coverage of the MCP server as a separate trust-boundary system.

---

### P0-5 · Phase 2 (CR tag) on a bank website triggers GLBA, Reg P privacy-notice obligations, and active state wiretap-statute exposure

**Frameworks:** GLBA / Reg P (12 CFR 1016) opt-out requirements for sharing NPI with non-affiliated third parties; California Invasion of Privacy Act (CIPA, Cal. Penal Code § 631-632.7) — actively litigated against website pixel and chat-replay tooling in 2024-2025; Florida Security of Communications Act; Massachusetts Wiretap Act (Ch. 272 § 99); Pennsylvania Wiretap Act (18 Pa. C.S. § 5701 et seq.).

**Gap:** Bank websites are *high-traffic* and *almost-all-customer-or-prospect*. A tag pulling unique visitors → enriching with PII implicates GLBA the moment an identifiable customer relationship is inferred, requires a **Reg P privacy notice update** and an opt-out mechanism, and in CIPA-style states is the exact fact pattern plaintiffs have used to sue retailers, healthcare providers, and now banks (multiple 2024 settlements >$1M against regional banks for chat-replay and pixel tooling).

The prototype offers no GPC honoring, no Do-Not-Sell list intersection, no URL-path exclusions (banks have `/login`, `/apply`, `/account-recovery`, `/careers`, `/loan-application` — categorically off-limits), no documented data-subject notice mechanism, and no integration with the bank's existing Reg P privacy notice / OneTrust consent string.

**Fix:** Tag-level GPC and DNT honoring, mandatory URL-path exclusion list shipped pre-populated for financial services (`/login`, `/apply`, `/loan-application`, `/account`, `/careers`, `/jobs`), CCPA/CPRA opt-out list intersection at the tag layer, OneTrust / TrustArc consent-string passthrough, and a per-pull purpose attestation that records why the bank's marketing team is enriching this cohort *today*.

---

### P0-6 · SOX 404 ITGC change management and access controls are absent from the marketplace surface

**Frameworks:** Sarbanes-Oxley § 404 ITGC (information technology general controls), specifically the COBIT-aligned control families: change management (CC8.1 in SOC 2 mapping), logical access (CC6.1-CC6.8), and computer operations. PCAOB AS 2201.

**Gap:** Anything that touches financial-reporting-adjacent data — and a marketplace producing customer cohorts that feed Salesforce FSC opportunities feeding revenue-pipeline reporting can absolutely fall in scope — needs ITGC coverage. The prototype shows no:

- Change-control surface — when FullContact adds a vendor, changes a CPM, or modifies a bundle's output schema, where does the customer get notified, and is there a customer-side approval queue?
- Role-based access control — every screenshot in the prototype shows a single user with full access to Marketplace, API & MCP, Workflows, and Credits & Billing. There is no Viewer / Operator / Purchaser / Admin separation visible.
- Segregation of duties — the same user who configures a workflow can also approve its spend cap and run it.
- Immutable audit trail with named-user attribution.

This is not a uniquely-banking concern, but for a SOX-covered issuer it is gating. My internal audit team will document a deficiency the first time they walk this product, and that deficiency will land in the next 10-K controls discussion if not remediated.

**Fix:** RBAC with at minimum four roles (Viewer, Operator, Purchaser, Admin); SoD enforcement (purchaser ≠ approver above threshold); change-control feed (webhook + email + in-app) for any customer-affecting catalog change with mandatory acknowledgement; immutable audit log with cryptographic chaining or write-once storage, exportable in CSV + JSON for SOX cycle.

---

### P0-7 · Spending controls are not present at the API gateway pattern banks actually use

**Frameworks:** OCC Heightened Standards (12 CFR Part 30 Appendix D) on operational risk; internal IT operational-risk policy; bank's own API gateway and FinOps practice.

**Gap:** The prototype expects customers to call `api.fullcontact.com` and `mcp.fullcontact.com` directly with a bearer token. My security architecture does not allow that. **Every** outbound call from a bank application to a third-party API crosses Apigee. Every cost-metered third-party API has a per-environment budget enforced at the gateway. The product as designed assumes a SaaS shape that bypasses this: the developer takes a key, drops it into Claude Desktop or a Python script, and calls FullContact direct. That bypasses Apigee, bypasses Splunk SIEM logging, bypasses CyberArk PAM rotation, and bypasses the FinOps spend cap.

**Fix:** Treat the bank's API gateway as a first-class deployment target. Publish OpenAPI 3.1 with rate-limit and idempotency semantics that are gateway-friendly. Provide a server-enforced spend cap that the bank can set (account-level, per-environment, per-key, per-tool), returning 402 with a structured `cap_exceeded` error so Apigee policies can act on it. Provide signed webhooks (HMAC-SHA256 with rotating secret) for spend, anomaly, breach, and catalog-change events that we land in Splunk. Provide a private-link / customer-tenanted MCP endpoint option (think AWS PrivateLink or Azure Private Endpoint) for banks that cannot allow direct internet egress to `mcp.fullcontact.com`.

---

## P1 Issues — Strong Concerns

| # | Issue | Phase impact | Framework / control |
|---|---|---|---|
| P1-1 | Single bearer-token credential drives Batch + REST + MCP — cannot be scoped, rotated independently, or revoked granularly | 6, also 1B/1 | NYDFS 500.7, NIST 800-53 IA-5 |
| P1-2 | No documented data residency option — bank has US-only and US/EU contractual obligations with several commercial customers | 1, 4, 5, 6, 7 | State data localization (NY, IL biometric), customer contracts, OCC HS |
| P1-3 | Vendor self-classification of fields as PII / Demographic / Derived (Phase 5) is the opposite of what a regulated buyer needs — FullContact must classify, vendors must not | 5 | GLBA Safeguards § 314.4(c) data classification |
| P1-4 | "Employment History" bundle in a self-serve marketing UI is FCRA bear trap regardless of contract — investigative consumer report territory | 3 | FCRA § 1681a(d)(1)(B), § 1681d |
| P1-5 | No methodology / lineage / fill-rate / accuracy SLA per attribute — model-risk teams cannot use these data without provenance | 3, 4, 5 | SR 11-7, NIST AI RMF MAP-2.3 |
| P1-6 | Phase 4's "vendor royalty tracked in back-end billing — not surfaced to customers" is incompatible with SR 13-19 vendor due diligence and SOX vendor-payment audit | 4, 5 | SR 13-19, SOX, COSO |
| P1-7 | No SCIM / SSO / Okta integration story — every user provisioned in FullContact is a CyberArk-out-of-scope shadow identity | All | NYDFS 500.7, SOX ITGC access |
| P1-8 | Phase 7 Activations to 24+ destinations on a schedule = data-flow inventory and DSAR propagation problem at scale | 7 | GLBA Safeguards § 314.4(c)(2), CCPA § 1798.105 |
| P1-9 | No status page, no published SLA, no incident history, no breach-notification timeline visible — required for vendor risk file | All | OCC 2013-29 due diligence, FFIEC IT Booklet |
| P1-10 | Identity resolution buried as 1 of 18 bundles — bank already buys from LiveRamp / Acxiom; FullContact's differentiator is unclear | 3, 5 | Procurement / TCO |
| P1-11 | No mention of 1033 / Open Banking implications if bank-account-aggregation data ever flows through (it shouldn't, but the catalog is open-ended) | 5, 6 | CFPB 1033, Reg E |
| P1-12 | "Buy More" pill in topbar with card-on-file, no approval gate, no CFO sign-off threshold | 1B, all later | SOX expense controls, internal expenditure policy |

---

## Phase-by-Phase Synthesis

### Phase 1 — UI Batch MVP · 7.0

The flow is clean, the mapping screen is straightforward, and the consent surface at the Confirm step is at least *present*. The Reports list with status states is a sensible operational surface. The "Privacy & compliance" copy is honest enough — though it mentions GDPR / CCPA / CPRA but not FCRA, GLBA, or any financial-services framework, which is a tell about the customer base FullContact has been designing for.

**What works:** File-in / file-out batch with bounded blast radius. CMO can run a one-time enrichment, CISO can review the file before and after, DLP can inspect egress, vendor surface is one (FullContact). I can write an MSA, DPA, and tenanted SOC 2 review around this and have my Vendor Risk team comfortable in 6-8 weeks.

**What breaks:** No SSO — username/password instead of Okta SAML/OIDC is a NYDFS 500.12 control gap. No DLP egress hooks, no data-residency declaration, no documented retention on uploaded files. No per-row audit trail tied to the named bank user. FCRA-adjacent fields (Household Income, Marital Status, Presence of Children, Net Worth Range) need to be tenant-blocklisted by default for a regulated tenant.

**Net:** Scope a Phase 1 pilot. Will not sign a multi-year platform agreement off Phase 1 alone.

---

### Phase 1B — Self-serve credits · 5.5

The Credits & Billing page itself is good — MTD spend, match balance, purchase history, downloadable receipts. Better than the "email the account team" pattern most data vendors still use.

What I cannot ship is the **"Buy More" pill in the topbar of every screen** with a card-on-file and a real-time charge. Corporate-card spend over a threshold (typically $5K-$10K) requires explicit approval workflow tied to delegation of authority. A button anyone with a login can press for any amount violates internal expenditure policy on day one — Internal Audit would write it up as a control gap regardless of whether anyone has pressed it.

Fix is small and well-understood: approval-threshold workflow, role-based purchase permissions (4 roles minimum), spend forecast on the Credits page. Fix that and I'm at 7.5.

---

### Phase 2 — CR tag input · 5.0

This is where the bank-specific risk surface diverges most sharply from the grocery-chain case. For FreshWay, pulling tag visitors is GDPR Art. 14 "indirect collection" and CPRA "sale or share" — significant but navigable.

For Cascadia, **the same operation falls under GLBA**. Visitors to cascadiaheritage.com are overwhelmingly customers, prospects, or (in careers) job applicants. The moment FullContact's tag resolves an anonymous session to a person identifier, that is NPI being shared with a non-affiliated third party. Reg P requires privacy-notice update; CCPA/CPRA opt-out must be honored; GPC must be respected; and the wiretap-statute plaintiffs' bar is *actively* litigating this exact fact pattern against banks in CA, FL, MA, PA — multiple seven-figure 2024-2025 settlements over chat-replay and pixel-tracking tooling.

The prototype offers no GPC honoring, no URL-path exclusion list (a bank has dozens of paths off-limits: `/login`, `/apply`, `/loan-application`, `/account-recovery`, `/careers`), no OneTrust consent-string integration, no DSAR hook, no per-pull purpose attestation.

**Done correctly, this is high-value.** Identify recent HELOC-product-page prospects who didn't apply, suppress current customers and employees, mail a relevant offer — that's a defensible motion *if* the controls layer is present. Reframe from "skip the file prep" (convenience feature) to "compliant tag-to-list pipeline with GPC, DSAR, and path-exclusion controls baked in" and it sells itself. Tie-breaker: ship controls *with* the surface, not in a follow-up.

---

### Phase 3 — 18 Insight bundles · 4.5

The compliance surface starts to outpace the control surface. Two structural problems specific to a bank:

**"Employment History" as a self-serve bundle.** FCRA § 1681a(d)(1)(B) defines "consumer report" to include reports for "employment purposes"; § 1681d treats an investigative consumer report (which is what aggregated employment history *is*, regardless of FullContact's intent) as having heightened obligations. A bank that pulls Employment History on a marketing list — even without intent to use it for employment decisions — has generated a record set that, in deposition five years out, will be characterized as employment-decision-adjacent. The intent argument loses.

**"Email Risk" and "Email Hash Activity."** In financial services, email risk is a fraud-scoring signal. The moment a bank uses it to inform an account-opening decision, adverse-action notice obligations can attach under FCRA / Reg B (ECOA, 12 CFR Part 1002) / Reg V. The regulator examines bank *use*, not vendor *labeling*. SR 11-7 attaches the moment the signal becomes a model input.

**Fix:** Tenant-level field blocklist defaulting FCRA-adjacent and fraud-scoring-adjacent attributes OFF for any regulated tenant — contract amendment plus per-attribute legal opinion required to enable. Per-attribute methodology page in the catalog. Per-attribute purpose-of-use gating. Version-pin commitment per bundle with 180-day deprecation windows.

I also second FreshWay's complaint that 18 separately-priced bundles is a pricing matrix, not a marketing UI — but for me as CIO, the bigger issue is field-level governance.

---

### Phase 4 — 3rd-party vendors · 3.5

The "vendor royalty tracked in back-end billing — not surfaced to customers" decision is the single most consequential failure of regulated-industry alignment in the roadmap.

For SR 13-19 / OCC 2013-29 / 2023 Interagency Guidance compliance, I need answers to: which legal entity is FullContact paying with my dollars; what percentage lands at Experian vs. TruAudience vs. FullContact margin; what is the indemnification chain when Experian data is wrong; has FullContact done subservicer due diligence at bank standard; when Experian raises wholesale, am I locked in without renegotiation right.

The "back-end" framing is meant to simplify procurement; for a bank it does the opposite — it makes the disclosure-and-due-diligence hygiene the interagency guidance requires impossible to satisfy. SOX audit will flag a vendor payment whose ultimate beneficiary is opaque. CFO will not sign against counterparty pricing set by a third party I have no privity with.

Layer the FCRA exposure from P0-2 on top (Experian credit-range data through a "marketing" pipe with no permissible-purpose attestation) and Phase 4 is a hard hold.

**Fix:** Either make the vendor split visible (FullContact margin transparent, vendor royalty disclosed to the dollar), *or* contractually commit to a CPM ceiling per dataset with right-to-decline > 10% changes on 90-day notice. Either way, ship a per-vendor due-diligence pack in the dataset modal.

---

### Phase 5 — Marketplace catalog · 3.0

The catalog browse experience is well-crafted as a marketing-product surface. The "Become a vendor" self-onboarding card is a non-starter for a regulated buyer.

Mechanical reasoning: every vendor is a potential fourth party touching customer data. Each onboarded vendor needs SOC 2, BCP/DR test, breach history, indemnification, right-to-audit. If FullContact onboards faster than my Vendor Risk team can review, the catalog becomes uninspectable — Risk will simply contractually disable vendor self-onboarding for our tenant, killing the network-effect premise.

The "featuring incentive" pattern (search ranking influenced by margin share) is a bigger issue for a bank than for a grocer. Vendor due-diligence ranking and data-quality ranking are regulator-readable concepts; margin-share ranking is not. A marketplace ordering higher-margin vendors above better-SOC-2-posture vendors is exactly the conflict-of-interest interagency guidance describes as "concentration risk" and "incentive alignment."

Vendor self-classification of fields as PII / Demographic / Derived is unacceptable. **FullContact must classify** against a published taxonomy with a customer-facing field page. Vendor self-attestation does not pass customer-side SOC 2 examination.

**Fix:** Vendor tiering visible to customers (Tier 1: SOC 2 Type II + ISO 27001 + clean breach history; Tier 2: SOC 2 Type II only; Tier 3: SIG-Lite) with default tenant filter to Tier 1; FullContact-side field classification; featuring incentive tied to compliance posture not margin; tenant-level approved-vendors allowlist; FullContact-led onboarding (not pure self-serve) for any vendor with credit, employment, health, or government-ID-adjacent attributes.

---

### Phase 6 — API + MCP · 2.5

In 2026, this is the phase that will be the topic of board-level conversation in any bank paying attention. **Agentic access to a metered third-party data backend does not yet have written policy in most banks** — the maturity of FullContact's MCP surface will determine whether those policies get written *for* this product or *against* it.

What I see in the prototype:

- A single bearer token (`fc_live_a4d2_...`) drives Batch, REST, and MCP. Single point of failure, revocation, and compromise. Cannot be scoped per use case, rotated independently per channel, or audited separately.
- The MCP credential is intended to live in `~/Library/Application Support/Claude/claude_desktop_config.json` — on a developer's laptop, in plain text, indexed by Spotlight, syncable by iCloud, exfiltratable by any malicious npm package. NYDFS 500.12 wants this MFA-protected; my CISO would want it SSO-federated and hardware-token-bound.
- No OAuth 2.1 / PKCE / OIDC, no short-lived tokens, no refresh-token rotation, no per-tool entitlements, no human-in-the-loop on sensitive attributes, no agent budget, no rate limits, no idempotency keys, no batch endpoint, no webhooks, no OpenAPI 3.1, no versioned dataset IDs, no SDK with retry/backoff, no sandbox, no status page.
- `run_recipe` — the most dangerous tool listed — is exposed without `dry_run`, `cost_estimate`, or `describe_recipe` companions, and a recipe by Phase 7's definition can write to 24+ destinations including ad networks. An agent typing `run_recipe` against a customer-acquisition recipe with a runaway loop could push enriched prospect data to Meta and Google Ads in volume before any human notices.

What I appreciate (FreshWay's Kevin already cataloged most technical gaps): **the unified-credential idea — same key drives Batch, REST, and MCP, same entitlements, same billing — is architecturally clean and a real choice**. The per-dataset enable toggle is exactly the granular control my security team wants in UI, not via support ticket. The "API & MCP enabled" filter chip is well-thought-out.

But the credential model must be redesigned for agentic 2026: per-use-case scoped credentials on top of the unified entitlement model, OAuth federated through customer IdP, per-tool entitlements (an agent gets `search_marketplace`, `list_datasets`, `cost_estimate` but not `enrich_person` or `run_recipe` without human approval), agent budgets that return 402 when exhausted, and a customer-tenanted MCP endpoint option (PrivateLink-style) for institutions that cannot allow outbound to `mcp.fullcontact.com`.

The MCP surface also needs **agent governance primitives**: `cost_estimate(payload)` free dry-run, `dry_run` mode on `run_recipe`, `describe_recipe(recipe_id)` for introspection, per-tool annotations (`readOnlyHint`, `costHint`, `destructiveHint`), MCP `prompts` resources for canonical templates. Without these, no agent can reason responsibly about budget and no human can review what an agent is about to do.

**Net:** hard hold as designed. The product team has the right architectural instincts — they have not yet reckoned with the *governance* requirements of an agentic world. SR 11-7 attaching to MCP-path model-input data makes this unfixable with cosmetic changes; it needs a credential-model and entitlements rebuild.

---

### Phase 7 — Workflows · 3.5

I score this generously because the prototype labels it "very early concept" and reviewers shouldn't penalize honest framing.

That said, the shape — Schedules, Recipes, Audiences, Activations writing to "Salesforce, HubSpot, Google Ads, Meta, Snowflake, SFTP/S3, and 24+ connectors" — is the **GLBA Safeguards data-flow inventory problem in concentrated form**. Every scheduled recipe pushing enriched PII is: a standing data flow on the bank's data-flow diagram; a retention question; a DSAR-propagation problem (when a customer requests deletion, can we propagate through Activations to 24+ destinations?); and a model-input governance problem if marketplace attributes feed a propensity model (SR 11-7).

There is a CFO-friendly aspect Robert identified in the FreshWay review: **a workflow with a server-enforced cap-per-workflow and a defined cadence is more financially predictable than ad-hoc enrichment**. If retention/expiry is built into Activations from day one, DSAR webhooks propagate, and my CDO can plug destinations into OneTrust, Phase 7 in steady state could be the most controllable surface on the entire roadmap. Lock that down now while the surface is still being drawn.

**Fix:** Per-destination retention/expiry (max age, automatic deletion). DSAR-propagation webhook. Per-destination DLP-friendly manifest. Server-enforced cap-per-workflow. SoD on workflow approval (designer ≠ approver).

---

## Adopt Verdict Matrix

| Through | Verdict | Notes |
|---|:---:|---|
| **Through 1B** | ✅ Conditional yes | Pilot scope; requires SSO, RBAC with 4 roles, per-purchase approval threshold, immutable audit log. |
| **Through 3** | ⚠️ Conditional, with FCRA carve-outs | Tenant-level blocklist on Employment History / Email Risk / Net Worth / Credit Range / Household Income; per-pull purpose attestation; written FCRA non-applicability opinion. |
| **Through 5** | ⚠️ Annual re-eval, no platform commitment | Need vendor tiering (SOC 2 Type II surfaced), FullContact-side field classification (not vendor self-attestation), per-vendor due-diligence pack, tenant-level approved-vendors allowlist. Will not pre-commit. |
| **Through 6** | 🛑 Hard hold | Will not adopt MCP in shown form. OAuth federated through Okta + per-tool entitlements + agent budget + customer-tenanted endpoint or it does not pass Risk & Cyber subcommittee. |
| **Through 7** | ❌ Not in this contract cycle | Watch-and-wait. Re-engage when Activations ships with retention/expiry, DSAR propagation webhooks, and DLP-friendly destination manifests. |

**Translation:** Cascadia signs a 12-month pilot covering Phase 1 / 1B with a stated intent to extend to Phase 3 if the FCRA controls land. Phases 4–7 are explicitly excluded from the initial paper and require separate amendments tied to specific control deliverables. We do not pre-commit to a 30-month platform agreement.

---

## Specific Fix-List, Ranked by Leverage

Items at the top unlock the most reviewers and the most adoption surface.

1. **SSO via OIDC / SAML federated through customer IdP (Okta / Azure AD / Ping)**, plus SCIM provisioning, plus role-based access control with at minimum Viewer / Operator / Purchaser / Admin. *Unlocks: every regulated buyer; gates SOX ITGC and NYDFS 500.12 compliance.*
2. **Per-pull permissible-purpose attestation** as a required dropdown at Confirm and as a required header on every API/MCP call. Logged. With FCRA permissible-purpose enumeration as the option set when credit attributes are involved. *Unlocks: FCRA defensibility for any regulated tenant.*
3. **Per-row immutable audit log** (initiator, timestamp, dataset, fields, declared purpose, downstream destination), 7-year retention, exportable in SOX-friendly format. Same schema across Batch / API / MCP. *Unlocks: SOX, GLBA Safeguards, FCRA dispute response.*
4. **Tenant-level field blocklist** that defaults all FCRA-adjacent / fraud-scoring-adjacent / employment-adjacent attributes OFF for any tenant on a regulated-industry plan. Requires contract amendment + legal opinion to enable. *Unlocks: every CCO at a bank, insurer, healthcare buyer.*
5. **Server-enforced spend caps** at four tiers (account / per-key / per-tool / per-workflow) returning 402 with a structured `cap_exceeded` error. Plus webhooks at 50% / 80% / 100%. Plus anomaly-based auto-pause. *Unlocks: every CFO and every FinOps team.*
6. **Per-vendor due-diligence pack** in the dataset detail modal (SOC 2 Type II date, ISO 27001 status, breach history, BCP/DR test result, SIG questionnaire, indemnification chain). Plus FullContact-side field classification (not vendor self-attestation). *Unlocks: SR 13-19 / OCC 2013-29 / 2023 Interagency Guidance compliance.*
7. **MCP credential-model rebuild:** OAuth 2.1 / OIDC federated through customer IdP; per-tool MCP entitlements; agent budget primitives; `cost_estimate` and `dry_run` tools; runaway-loop detection with auto-pause. *Unlocks: CISO and Risk & Cyber subcommittee approval for Phase 6.*
8. **Apigee-friendly API design:** OpenAPI 3.1, rate-limit headers, idempotency keys, batch endpoint, webhooks (HMAC-SHA256), versioned dataset IDs with deprecation windows, SDK with retry/backoff, sandbox env, public status page. *Unlocks: every enterprise-IT readiness gate.*
9. **GLBA Safeguards artifact pack** as a standard deliverable (data-flow diagram, retention schedule, breach-notification timeline, subservicer list, annual board report template). Plus DSAR fulfillment hook propagating through Activations. *Unlocks: bank CCO, CDO, privacy office.*
10. **Phase 4 vendor pricing transparency** — visible split *or* contractual CPM ceiling with right-to-decline > 10% changes within 90-day notice. *Unlocks: bank CFO, vendor management.*
11. **Phase 2 financial-services tag-controls bundle** — pre-populated URL-path exclusion list (`/login`, `/apply`, `/loan-application`, `/account`, `/careers`, `/jobs`), GPC/DNT honoring, OneTrust consent-string passthrough, DSAR hook. *Unlocks: bank CMO running compliant prospect-acquisition motion.*
12. **Vendor tiering visible to customers** — Tier 1 / Tier 2 / Tier 3 based on compliance posture; default tenant filter to Tier 1; featuring incentive on compliance posture not margin. *Unlocks: defensible Phase 5 marketplace for regulated buyers.*
13. **Customer-tenanted / private-link MCP endpoint** option for institutions that cannot allow outbound to `mcp.fullcontact.com`. *Unlocks: top-tier banks / wealth managers / insurance carriers.*

---

## Financial-Services Vertical Capability Gaps — What FullContact Could Ship

Beyond the control fixes, what would make FullContact differentiated as a financial-services data partner and not "yet another marketing-data vendor on the bank's ROPA":

- **KYC enrichment bundle** — name/DOB/address/SSN-last-4 verification surfaces, reasonably priced, with an explicit FCRA non-applicability or alternative-permissible-purpose declaration. Sold against LexisNexis Risk Solutions and Equifax Workforce Solutions.
- **Beneficial Ownership Information (BOI) bundle** — supports FinCEN's Corporate Transparency Act reporting flow for the bank's commercial book. There is real white space here as the CTA has matured.
- **Sanctions / PEP / Adverse Media** — OFAC SDN, EU consolidated, UN, UK HMT, plus adverse-media negative-news. Sold against Refinitiv World-Check, Dow Jones Risk & Compliance, Sayari, ComplyAdvantage.
- **Small-business firmographics + UCC + secretary-of-state** — lien, judgment, dissolution, owner-officer linkage. Differentiates from MountainRiver's general firmographics for the small-commercial lending use case.
- **Account-opening fraud signal bundle** — synthetic-identity scoring, velocity signals across institutions, with a clear declaration that it is *not* a "consumer report" under FCRA (or an explicit declaration that it *is*, with the corresponding adverse-action notice scaffolding).
- **Wealth-management household and lifestage signals** — net-worth tier, life-event triggers (inheritance, liquidity event, retirement) with explicit non-FCRA-eligibility-decision constraints in the contract.

That is the financial-services bundle catalog. None of it is in the prototype today, which is a tell that FullContact has been designed for retail / e-commerce / B2B SaaS marketing — not for regulated-industry buyers. Filling that gap would change the conversation.

It would also change how Cascadia would price the contract. A general marketing-enrichment tool in the bank's MarTech stack is a $200K-$400K annual line item. A financial-services data fabric stitching identity, KYC, BOI, sanctions, small-commercial firmographics, and consented marketing enrichment is a $1.5M-$3M annual line item — and meaningfully harder to displace.

---

## Quotes for the Stakeholder Deck

> *"There is a real product here. The Phase 1 / 1B / 2 surface is the kind of marketing-ops capability my CMO will absolutely want. I am ready to scope a pilot. I am not ready to sign the multi-year platform commitment that Phases 4 through 7 imply."* — **Daniel Marchetti, CIO**

> *"A bearer token in `claude_desktop_config.json` is not a 2026 control surface. NYDFS 500.12 wants this to be MFA-protected. My CISO would want it to be SSO-federated and hardware-token-bound. The MCP credential model has to be redesigned before it crosses my Risk & Cyber subcommittee."* — **Daniel Marchetti, CIO**

> *"The 'vendor royalty tracked in back-end billing — not surfaced to customers' decision is the single most consequential failure of regulated-industry alignment in this roadmap. SR 13-19 is unambiguous: I have to know who, by legal entity, FullContact is paying with my dollars."* — **Daniel Marchetti, CIO**

> *"The fact that 'Privacy & compliance' copy on the Confirm screen mentions GDPR and CCPA but not FCRA, GLBA, or financial-services-specific frameworks at all is a tell about the customer base FullContact has been designing for. I will need that gap closed before this gets in front of my CCO."* — **Daniel Marchetti, CIO**

> *"For a bank, Phase 2 is GLBA territory the moment a tag resolves an anonymous visitor to a person identifier. The convenience-feature framing — 'skip the file prep' — is the wrong framing for a regulated buyer. Reframe it as 'compliant tag-to-marketing-list pipeline with GPC, DSAR, and path-exclusion controls baked in' and it sells itself."* — **Daniel Marchetti, CIO**

> *"My security architecture does not allow developers to call `api.fullcontact.com` and `mcp.fullcontact.com` directly with a bearer token. Every outbound call from a bank application crosses Apigee. Treat the bank's API gateway as a first-class deployment target or you do not get past my enterprise architecture review."* — **Daniel Marchetti, CIO**

> *"Ship a financial-services bundle — KYC, BOI, sanctions, PEP, small-commercial firmographics, account-opening fraud — and you change the conversation from 'one more marketing data vendor' to 'data fabric I will fight to keep.' That moves the contract from $300K to $2M and makes you genuinely sticky."* — **Daniel Marchetti, CIO**

> *"I am not a vendor-hostile CIO. I am a vendor-skeptical one. The product team has the right architectural instincts — unified credential, per-dataset enable, real-time + batch parity. They have not yet reckoned with the governance requirements of an agentic 2026. That is fixable. My yes is conditional, not absent."* — **Daniel Marchetti, CIO**

---

## What FullContact Got Right

It is easy in a CIO review to pile on the gaps. It would be unfair not to call out the choices that are clearly correct and that will compound positively as the product matures.

- **Unified credential across Batch / API / MCP, same entitlements, same billing.** This is architecturally clean. Most data vendors negotiate a separate API contract with a different price book. The unified-credential decision is the right call and is a real differentiator with vendor management; it makes contract paper simpler and reduces the surface for renegotiation games. The downside (single point of compromise) is fixable by adding scoped credentials per use case on top of the unified entitlement model.
- **Per-dataset enable toggle for API/MCP availability.** Exactly the granular control my security team wants in the UI rather than via a support ticket. Ship this in production.
- **Phase explainer panel + new/changed pulse markers.** The "what's in this phase" affordance is genuinely respectful of a stakeholder's time. I would lift this pattern into the production app as a "what's new this quarter" surface — banks have to keep track of what their data vendors are changing, and a built-in changelog with phase/release framing is the right answer.
- **Honest framing on Phase 7.** Calling out "very early concept" on Workflows is the right move. CIOs do not punish honesty about roadmap maturity; we punish surprises.
- **Credits & Billing page visibility.** MTD spend, balance, purchase history with downloadable receipts is real. Better than the "email the account team" pattern most data vendors still use.
- **Phase-locked sidebar items.** Hiding `Marketplace`, `API & MCP`, `Workflows` until the phases that introduce them is a small thing, but it shows a product team that thinks about progressive disclosure for stakeholder reviews. That instinct, applied to RBAC for end-users, is the right starting point.
- **The customer recognition tag input is a genuinely good idea.** Not in the form shown, but the shape is right. Done with the controls layer described above, this is a capability my CMO will want to call her own.

---

## Closing — What's Missing for the Financial Services Vertical

FullContact gets the bones right and the regulated-industry control layer wrong. Both halves matter.

The bones — unified credential, batch-to-real-time-to-agent transport parity, per-dataset entitlements, marketplace browse, workflow primitives — are an architecturally coherent vision of what an enrichment platform looks like in 2027. There is a thesis here, and it's defensible. I came in prepared to find a marketing tool with delusions of platform; I'm leaving thinking this is a platform with a gap in regulated-industry GTM.

Four things missing for financial services specifically:

1. **The control layer.** SSO, RBAC, per-pull purpose attestation, per-row audit log, tenant field blocklist, server-enforced spend caps, data-flow inventory artifact, DSAR propagation webhooks. None exotic; together the difference between "scope a pilot" and "sign a platform contract."
2. **The agentic governance primitives** the MCP surface implies but doesn't ship: OAuth federation, per-tool entitlements, agent budgets, `cost_estimate` / `dry_run`, customer-tenanted endpoint. Without these, MCP is a 2024 demo, not a 2026 production capability.
3. **The vertical-specific data bundles**: KYC, BOI, sanctions / PEP, small-commercial firmographics, account-opening fraud, wealth-segment lifestage. Shifts FullContact from "marketing tool I might keep" to "data fabric I will fight to keep." 5x multiplier on contract value, real moat against LiveRamp / Acxiom / LexisNexis displacement.
4. **The vendor-management hygiene** interagency guidance contemplates: per-vendor due-diligence pack, tenant approved-vendor allowlist, vendor tiering by compliance posture, subservicer notification with right-to-decline. Without this, every new fourth party in the catalog is a no-vote from my Vendor Risk Committee.

Ship those four and the score I gave today (5.4) becomes a 7.5-8 and Cascadia becomes a multi-year reference. Don't, and my CMO uses Phase 1/1B/2 for one-off enrichments under a modest contract, my CCO blocks extension into Phase 4+, my CISO blocks MCP, and FullContact stays in the same position with my peers at every other regional bank — a useful tool with a ceiling on contract size and a structural inability to expand into the regulated-industry whitespace where the real money is.

That is the conversation worth having. The roadmap as drawn does not survive my Risk & Cyber subcommittee. With the fixes above, it does — and it becomes a defensible, expanding investment for both sides.

---

*Synthetic stakeholder review compiled 2026-05-08. Persona, scoring, and verdicts simulated by Claude (Anthropic) on Jake Cohen's instruction as a pre-flight stress test of the FullContact Data Marketplace roadmap prototype before live customer interviews. Composite score: 5.4 / 10. Posture: conditional yes through Phase 1B, conditional yes through Phase 3, annual re-eval through Phase 5, hard hold on Phase 6 MCP as designed, watch-and-wait on Phase 7.*
