# DQC Version 30 — Proposed New Rules
**Prepared for:** DQC Rules Committee  
**Date:** March 12, 2026  
**Status:** Initial Proposal — Public Review Pending  
**Prepared by:** XBRL US

---

## Overview

This document presents the initial set of rules proposed for inclusion in Version 30 of the DQC rule set. The rules address data quality issues observed in filer submissions and are designed to improve the consistency, comparability, and machine-readability of XBRL-tagged financial disclosures. The proposals include three US GAAP rules and one IFRS rule.

Additionally, this document identifies a further area of work currently under development: rules governing the consistent tagging of the domestic and foreign jurisdictional members on the income tax rate reconciliation schedule. These rules will be submitted as a separate proposal once drafting is complete.

The committee is asked to review the proposed rules, provide feedback, and approve them for public review.

---

## Summary of Proposed Rules

| Rule | Taxonomy | Rule Element IDs | Subject |
|---|---|---|---|
| DQC.US.0233 | US GAAP | 10926, 10927 | Incorrect use of `ScenarioAdjustmentMember` for accounting standard update adjustments |
| DQC.US.0234 | US GAAP | 10928, 10929, 10930 | Incorrect use of `IncomeTaxAuthorityAxis` with domestic-only tax reconciliation concepts |
| DQC.US.0235 | US GAAP | 10931, 10932 | Incorrect use of `CollateralAxis` with repurchase agreement and securities loaned concepts |
| DQC.IFRS.0232 | IFRS | 10925 | Disallowed combined total concepts in IFRS 18 statement of operating activities or income statement |

---

## Rule 1 — DQC.US.0233: Incorrect Use of ScenarioAdjustmentMember for Accounting Standard Updates

### Background

Filers that adopt new accounting standards are required to tag the cumulative-effect adjustments associated with those adoptions using specific taxonomy members. The US GAAP taxonomy provides `CumulativeEffectPeriodOfAdoptionAdjustmentMember` and `RevisionOfPriorPeriodAccountingStandardsUpdateAdjustmentMember` for this purpose. However, a pattern has been observed where filers use the more generic `ScenarioAdjustmentMember` on the `srt:StatementScenarioAxis` for these adjustments, either alone or in combination with the `AdjustmentsForNewAccountingPronouncementsAxis`. This practice is inconsistent with taxonomy guidance and reduces the ability of data consumers to identify and compare ASU adoption impacts across filings.

### Proposed Rule Logic

The rule consists of two assertions:

**Rule 10926** flags any fact that is simultaneously tagged with:
- `srt:StatementScenarioAxis = ScenarioAdjustmentMember`, **and**
- Any member on `AdjustmentsForNewAccountingPronouncementsAxis`

The check is concept-agnostic — any concept with this dimension combination will trigger the error.

**Rule 10927** flags any fact where:
- The concept is `AccountingStandardsUpdateExtensibleList`, **and**
- The fact carries `srt:StatementScenarioAxis = ScenarioAdjustmentMember`

### Rationale

The adoption-period members (`CumulativeEffectPeriodOfAdoptionAdjustmentMember` and `RevisionOfPriorPeriodAccountingStandardsUpdateAdjustmentMember`) are semantically precise and enable consistent identification of ASU adoption adjustments by downstream consumers. Using `ScenarioAdjustmentMember` conflates the adjustment type with a generic scenario, making it impossible to reliably distinguish ASU adjustments from other types of scenario adjustments in automated processing.

### Example Error Message

> The filer has reported a fact value for the concept **RetainedEarningsAccumulatedDeficit** of 500000 with the scenario of ScenarioAdjustmentMember and a dimension of AdjustmentsForNewAccountingPronouncementsAxis. Adjustments relating to accounting standard updates should use standard Cumulative Effect, Period of Adoption, Adjustment [Member] or Revision of Prior Period, Accounting Standards Update, Adjustment [Member], as opposed to the scenario of ScenarioAdjustmentMember.

### Applicability

Applies to filings using the 2024, 2025, and 2026 US GAAP taxonomy versions.

---

## Rule 2 — DQC.US.0234: Incorrect Use of IncomeTaxAuthorityAxis with Domestic Tax Reconciliation Concepts

### Background

The `IncomeTaxAuthorityAxis` is intended to disaggregate income tax data by the jurisdiction of the relevant tax authority. Several income tax reconciliation concepts in the US GAAP taxonomy are, by definition, domestic (i.e., US federal) in nature — including those related to BEAT (Base Erosion and Anti-Abuse Tax), GILTI (Global Intangible Low-Taxed Income), FDII (Foreign-Derived Intangible Income), cross-border adjustments, and the US federal statutory rate. Breaking these concepts down by tax authority is conceptually incorrect and introduces inconsistency into the data. A further issue is that when `IncomeTaxAuthorityAxis` is used in a filing, the filer's domestic jurisdiction is not always identified, making it impossible for consumers to determine which jurisdiction is treated as the "home" jurisdiction.

### Proposed Rule Logic

The rule consists of three assertions:

**Rule 10928** flags any non-nil fact for the following **monetary** concepts when tagged with any member on `IncomeTaxAuthorityAxis`:

- `EffectiveIncomeTaxRateReconciliationFdiiAmount`
- `EffectiveIncomeTaxRateReconciliationBeatAmount`
- `EffectiveIncomeTaxRateReconciliationGiltiAmount`
- `EffectiveIncomeTaxRateReconciliationCrossBorderOtherAmount`
- `EffectiveIncomeTaxRateReconciliationCrossBorderTaxEffectAmount`
- `IncomeTaxReconciliationIncomeTaxExpenseBenefitAtFederalStatutoryIncomeTaxRate`

**Rule 10929** flags any non-nil fact for the following **percentage/rate** concepts when tagged with any member on `IncomeTaxAuthorityAxis`:

- `EffectiveIncomeTaxRateReconciliationAtFederalStatutoryIncomeTaxRate`
- `EffectiveIncomeTaxRateReconciliationBeatPercent`
- `EffectiveIncomeTaxRateReconciliationGiltiPercent`
- `EffectiveIncomeTaxRateReconciliationFdiiPercent`
- `EffectiveIncomeTaxRateReconciliationCrossBorderOtherPercent`
- `EffectiveIncomeTaxRateReconciliationCrossBorderTaxEffectPercent`

**Rule 10930** is a filing-level check that flags any filing that reports facts with any member on `IncomeTaxAuthorityAxis` but does not also report a value for `TaxJurisdictionOfDomicileExtensibleEnumeration`.

### Rationale

The concepts in Rules 10928 and 10929 are defined within the US federal tax framework — they are not disaggregable by jurisdiction in any meaningful way. Applying `IncomeTaxAuthorityAxis` to them creates misleading or redundant data. Rule 10930 complements this by ensuring that when `IncomeTaxAuthorityAxis` is used elsewhere in the filing, the filer's domestic jurisdiction is always disclosed. Without this anchor, data consumers cannot correctly interpret the relative foreign and domestic splits in the tax reconciliation.

### Example Error Messages

> **Rule 10928/10929:** The filer has reported a fact value for the concept **EffectiveIncomeTaxRateReconciliationGiltiAmount** of 5,000,000 with a dimension of IncomeTaxAuthorityAxis. This dimension should not be used for these concepts as they relate to domestic income tax calculations only.

> **Rule 10930:** The filer has reported a fact value with a dimension of IncomeTaxAuthorityAxis but is missing a value for the concept TaxJurisdictionOfDomicileExtensibleEnumeration. When reporting facts with the IncomeTaxAuthorityAxis dimension, a value for TaxJurisdictionOfDomicileExtensibleEnumeration must also be provided to indicate the domestic tax jurisdiction.

### Applicability

Applies to filings using the 2026 US GAAP taxonomy version.

---

## Rule 3 — DQC.US.0235: Incorrect Use of CollateralAxis with Repurchase Agreement and Securities Loaned Concepts

### Background

Financial institutions that engage in repurchase agreement and securities lending transactions are required to disclose gross obligations disaggregated by the class of collateral pledged. The US GAAP taxonomy provides `CollateralPledgedInSecuredBorrowingAxis` specifically for this purpose. However, filers have been observed using the more generic `CollateralAxis` with gross repurchase agreement and securities loaned concepts. This creates ambiguity about the nature of the collateral breakdown and does not align with the taxonomy's intended axis for secured borrowing disclosures.

### Proposed Rule Logic

Both assertions (10931 and 10932) flag any fact for either of the following concepts when tagged with any member on `CollateralAxis`:

- `FinancialAssetsSoldUnderAgreementsToRepurchaseGrossIncludingNotSubjectToMasterNettingArrangement`
- `SecuritiesLoanedIncludingNotSubjectToMasterNettingArrangementAndAssetsOtherThanSecuritiesTransferred`

The rule applies regardless of which member is used on `CollateralAxis`.

### Rationale

`CollateralPledgedInSecuredBorrowingAxis` is the semantically correct axis for disaggregating gross secured borrowing obligations by collateral class. Using the generic `CollateralAxis` with these concepts conflates two different collateral-related axes and can prevent downstream consumers, such as financial regulators and data aggregators, from correctly identifying the collateral breakdown for repo and securities lending exposures. The rule directs filers to use the more precise axis.

### Example Error Message

> The filer has reported a fact value for the concept **FinancialAssetsSoldUnderAgreementsToRepurchaseGrossIncludingNotSubjectToMasterNettingArrangement** of 250,000,000 with the CollateralAxis. This axis should not be used with this concept. The `CollateralPledgedInSecuredBorrowingAxis` should be used for disaggregation of the gross obligation by class of collateral pledged.

### Applicability

Applies to filings using the 2026 US GAAP taxonomy version.

---

## Rule 4 — DQC.IFRS.0232: Disallowed Combined Total Concepts in IFRS 18 Statement of Operating Activities or Income Statement

### Background

IFRS 18 *Presentation and Disclosure in Financial Statements*, effective for annual reporting periods beginning on or after 1 January 2027, requires that the income statement (statement of profit or loss) present line items classified according to the nature or function of expenses by activity type — operating, investing, or financing. Certain IFRS taxonomy concepts, specifically `Depreciation`, `Amortisation`, and `ImpairmentLossReversalOfImpairmentLossRecognisedInProfitOrLoss`, represent aggregated totals that span all activity categories. Under IFRS 18, these combined totals must not appear as distinct standalone line items in the statement of operating activities or income statement. Filers must instead use activity-specific concepts that are scoped to the relevant category.

### Proposed Rule Logic

The rule applies **only to filings whose DTS includes the IFRS 18 entry point** (`https://xbrl.ifrs.org/taxonomy/2025-03-27/ifrs_18_entry_point_2025-03-27.xsd`). For all other IFRS filings the rule is skipped.

For qualifying filings, the rule:

1. Identifies all presentation (`parent-child`) networks whose role description contains `- Statement -`, while excluding parenthetical, equity/capital/changes in equity, convertible, preferred, temporary equity, redeemable, members' interest, net proceeds from all sources, and highlights networks.
2. For each qualifying network, collects all concepts appearing in the presentation hierarchy.
3. Flags any occurrence of the following concepts within those networks:
   - `Depreciation`
   - `Amortisation`
   - `ImpairmentLossReversalOfImpairmentLossRecognisedInProfitOrLoss`

### Rationale

IFRS 18 requires activity-based classification of income statement line items. The three prohibited concepts aggregate depreciation, amortisation, and impairment charges across all activity categories and therefore cannot be meaningfully attributed to a single activity type. Their presence as distinct items in the primary statement is non-compliant with IFRS 18's presentation requirements. Filers must replace these with their activity-scoped equivalents (e.g., `DepreciationAndAmortisationOfNonFinancialAssetsFromOperatingActivities` for the operating component).

### Example Error Message

> The concept **Depreciation** is used in the statement of operating activities or income statement, but is not allowed when using IFRS 18. These items represent the combined totals of operating, investing, or financing activities and should not be included as distinct items. Items specific to the operating, investing or financing activity should be used instead.

### Applicability

Applies to IFRS 18 filings using the 2025 IFRS taxonomy version.

---

## Forthcoming Rules — Income Tax Rate Reconciliation: Domestic and Foreign Member Consistency

In addition to the rules detailed above, XBRL US is developing a further set of rules that will address the consistent tagging of members on the income tax rate reconciliation schedule. Specifically, these rules will focus on ensuring that:

1. **Domestic jurisdiction tagging** — When a filer disaggregates tax reconciliation items using `IncomeTaxAuthorityAxis`, the domestic (domicile) member is tagged consistently and in a manner that aligns with the value reported for `TaxJurisdictionOfDomicileExtensibleEnumeration`. This ensures that the domestic portion of the reconciliation can be reliably identified and compared across filers.

2. **Foreign jurisdictional member tagging** — When individual foreign jurisdictions are reported on the reconciliation schedule, the rule will check that the members used correctly represent distinct foreign tax authorities (e.g., `ForeignCountryMember` or jurisdiction-specific domain members) and are not conflated with domestic-only concepts. The rule will also verify that the sum of domestic and foreign reconciliation items is internally consistent.

3. **Reconciliation completeness** — Where filers use `IncomeTaxAuthorityAxis` to break down their reconciliation, the rules will verify that both a domestic member and at least one foreign member are present, reflecting the requirement to disaggregate the full reconciliation.

These rules are under active development. Draft rule logic and detailed conditions will be presented to the committee in a subsequent proposal document. The intent is to include these rules as part of the Version 30 release alongside the four rules presented above.

---

## Next Steps

The committee is asked to:

1. Review the four proposed rules described in this document and provide feedback on the rule logic, conditions, and example messages.
2. Approve the rules for public review, or identify changes needed prior to public release.
3. Note the forthcoming income tax rate reconciliation member-consistency rules and provide any early directional feedback on the proposed scope.

All comments and feedback should be submitted to the DQC working group no later than **April 4, 2026**.

---

*© Copyright 2017 – 2026 XBRL US, Inc. All rights reserved.*  
*See [License](https://xbrl.us/dqc-license) for license information.*  
*See [Patent Notice](https://xbrl.us/dqc-patent) for patent infringement notice.*
