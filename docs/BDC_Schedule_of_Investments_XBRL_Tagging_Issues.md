# Schedule of Investments XBRL Tagging Issues and Proposed Solutions: Business Development Corporations (BDCs) and Employee Benefit Plans (Form 11-K)

**Prepared for:** U.S. Securities and Exchange Commission  
**Prepared by:** Data Quality Committee (DQC)  
**Date:** March 20, 2026  
**Status:** Draft for Discussion

---

## Executive Summary

Business Development Corporations (BDCs) and employee benefit plans filing on Form 11-K are required to file detailed Schedule of Investments (SOI) disclosures with the SEC that identify each investment holding and its associated attributes. The Financial Accounting Standards Board (FASB) has issued guidance requiring that these attributes be tagged using Extensible Enumeration concepts within the XBRL filing. In practice, however, widespread non-compliance with this guidance is resulting in XBRL data that is incomplete, inconsistent, and largely unusable by investors and data consumers.

This report documents the nature of the problem, its causes, its impact, and a proposed interim solution that would make investment attribute data accessible to data users while addressing the immediate practical barriers to full compliance. Although the issues are most visible in BDC filings due to the volume and complexity of their portfolios, the same structural tagging problems affect employee benefit plan filings on Form 11-K, which also require a Schedule of Investments disclosing the plan's holdings. The proposed solutions apply equally to both filing types.

---

## 1. Background

### 1.1 Affected Filing Types

This report addresses two categories of SEC filer that are required to include a Schedule of Investments in their XBRL-tagged filings:

**Business Development Corporations (BDCs):** BDCs are closed-end investment companies regulated under the Investment Company Act of 1940 that lend to and invest in small and mid-sized companies. As registered investment companies, BDCs file annual reports on Form 10-K and semi-annual reports on Form N-2, and are required to include a complete Schedule of Investments disclosing all portfolio holdings.

**Employee Benefit Plans (Form 11-K):** Certain employee benefit plans — including defined contribution plans such as 401(k) plans that hold employer securities or participant-directed investments — are required to file annual reports on Form 11-K with the SEC. These filings include financial statements prepared in accordance with ERISA, which typically contain a Schedule of Assets Held for Investment Purposes (a form of Schedule of Investments) disclosing each investment held by the plan.

The XBRL tagging issues described in this report affect both filing types. The structural complexity of their respective Schedules of Investments, the volume of individual holdings, and the required attribute disclosures for each holding create the same practical barriers described below.

### 1.2 Schedule of Investments: Required Attributes

For both BDC and Form 11-K filers, the Schedule of Investments must identify every investment holding and, for each holding, report a range of required attributes including:

- **Investment name / issuer**
- **Security type** (e.g., senior secured loan, subordinated debt, equity warrant)
- **Industry classification** of the portfolio company
- **Country** of domicile of the investee
- **Maturity date** (for debt instruments)
- **Interest rate / coupon** (for debt instruments, including reference rate and spread)
- **Affiliation status** between the BDC and the investee (e.g., non-affiliated, affiliated, controlled)
- **Non-accrual status**
- **Currency denomination**
- **Cost basis and fair value**
- **Percentage of net assets**

### 1.3 FASB XBRL Implementation Guidance

The FASB has published XBRL Implementation guidance for investment companies, including BDCs and employee benefit plans, at:  
[https://xbrl.fasb.org/impdocs/BDC_TIG/financialinvestmentcompanies.htm](https://xbrl.fasb.org/impdocs/BDC_TIG/financialinvestmentcompanies.htm)

This guidance specifies that:

1. Each individual investment is identified using an **Investment Identifier dimension** (`InvestmentIdentifier` axis), with a unique member per holding.
2. Required investment attributes (security type, industry, country, affiliation, etc.) must be tagged using **Extensible Enumeration** concepts (e.g., `InvestmentSecurityTypeDomain`, `InvestmentAffiliatedIssuerDomain`), with facts reported for each attribute of each investment.
3. Numerical values (cost, fair value, interest rate, maturity date) are tagged as standard numeric or date facts dimensioned by the Investment Identifier member.

The intent of this approach is to create a structured, machine-readable representation of the entire portfolio for each reporting period.

---

## 2. Current State of Compliance

### 2.1 Widespread Non-Compliance with Attribute Tagging

In practice, the vast majority of BDC filers are **not tagging the non-numeric attributes** required under the FASB guidance. Filers are generally limiting their XBRL tagging to numerical values (fair value, cost, interest rate, par/principal amount) and omitting the required:

- Extensible Enumeration facts for security type, industry, country, and affiliation
- Date facts for maturity dates
- Textual facts for issuer name and reference rate descriptions

The DQC has implemented validation rules to detect missing required attribute tags. These rules are generating errors in the **hundreds of thousands** across the BDC filing population, confirming that non-compliance is systemic rather than isolated. Comparable patterns of non-compliance have been observed in Form 11-K filings, where plan-level investment schedules similarly omit required attribute tagging.

### 2.2 Root Causes

Several interrelated factors explain why filers are not complying with the FASB guidance:

#### 2.2.1 Cost and Operational Burden

Fully tagging every attribute of every investment using Extensible Enumeration requires creating and maintaining a large number of XBRL facts per investment holding. For a BDC with hundreds or thousands of portfolio companies, or a Form 11-K plan with a large number of fund options and individual holdings, the tagging burden is substantial. Filing agents report that:

- The time and cost required to produce compliant XBRL is significantly higher than clients are willing to pay.
- Many BDC schedules are prepared from unstructured PDF or word processing documents with limited automation support.
- Investment attributes frequently change between periods (new investments, repayments, restructurings), and the effort to maintain a fully-tagged schedule each quarter is not commercially viable under current fee structures.

#### 2.2.2 File Size and Inline XBRL Viewer Instability

Full compliance with the FASB guidance would require generating an extremely large number of XBRL facts — potentially tens of thousands or more for a single filing. This creates two immediate technical problems:

- **File size**: Fully-tagged BDC XBRL filings approach or exceed the practical limits that SEC systems and intermediary data aggregators can reliably process.
- **Inline XBRL viewer crashes**: The SEC's Inline XBRL viewer has been documented to crash or fail to render when loading filings with very large XBRL fact counts. This is a known limitation that has not yet been addressed on the viewer side.

These technical barriers create a disincentive for even well-resourced filers who might otherwise attempt to comply.

### 2.3 Impact on Data Users

The failure to tag investment attributes in structured form has significant negative consequences for investors and data consumers:

- **Data is not usable as filed.** Data aggregators, analytical platforms, and investors attempting to use XBRL data from BDC filings cannot reliably extract investment-level attributes from the XBRL facts because the required structured data is absent.
- **Attribute data must be parsed from member names.** In the absence of properly tagged attributes, data consumers are forced to parse investment attributes from the string label of the Investment Identifier member (e.g., `"AcmeCorp_SeniorSecuredLoan_3M_SOFR_600_12-2027_Technology"`). This is inherently unreliable because:
  - Member name formats are not standardized across filers.
  - Member names are truncated, abbreviated, or inconsistently punctuated.
  - The same investment may be named differently across periods.
  - Parsing logic must be maintained separately for each filer's naming convention.
- **Affiliation and status data is especially difficult to derive.** Affiliation classification (non-affiliated, affiliated, controlled) and non-accrual status are critical to regulatory compliance analysis but are rarely tagged in structured form.

---

## 3. Proposed Interim Solution

### 3.1 Rationale

Given the systemic nature of the compliance failure, the immediate technical barriers to full compliance, and the urgent need for data users to access investment attribute information in a usable form, the DQC proposes an **interim solution** that:

1. Makes investment attribute data accessible in a structured, parseable form without requiring the use of Extensible Enumeration tagging.
2. Does not require the creation of large numbers of additional XBRL facts and therefore does not exacerbate file size or Inline XBRL viewer issues.
3. Can be validated by the DQC so that filers can confirm compliance before submission.
4. Is recognized as a **temporary measure** pending a more comprehensive revision of the guidance.
5. Does **not** require filers who are already fully compliant with the FASB Extensible Enumeration guidance to change their approach.

### 3.2 Proposed Approach: Structured Key-Value Pairs in Investment Member Names

We propose that the SEC require (or strongly encourage) BDC filers to encode investment attribute information as a **standardized string of key-value pairs** embedded in the label of the Investment Identifier member name. This approach leverages the existing XBRL infrastructure (every BDC already creates Investment Identifier members) while adding structured, parseable attribute data in the one location that is already uniformly present across all filings.

#### 3.2.1 Proposed Format

The member label would consist of:

1. A human-readable investment name (as currently used), followed by
2. A delimiter (`|`), followed by
3. A series of structured key-value pairs in a standardized format

**Proposed format:**

```
<Investment Display Name> | <KEY1>=<VALUE1>; <KEY2>=<VALUE2>; ...
```

**QName Naming Constraint:** Both keys and values in the key-value pair block must conform to XML QName naming rules, since Investment Identifier member names are XBRL QNames. Specifically:

- **No spaces are permitted** in any key or value.
- **Only the special characters `-` and `_` are permitted** within keys and values; characters such as `+`, `.`, `%`, `,`, `;`, `(`, `)`, and `&` are not permitted.
- Multi-word values must encode spaces as `_` (e.g., `Business_Services`, `Acme_Corp`).
- Interest rate spreads that would conventionally be written with `+` must instead use `_` as the separator between the reference rate and the spread (e.g., `SOFR_600` for SOFR + 600 bps).
- Fixed interest rates that would conventionally include a decimal point and percent symbol must encode those characters (e.g., `12-50pct` for 12.50%).
- Issuer names must have spaces replaced with `_` and punctuation (`.`, `,`, `'`) removed (e.g., `Acme_Corp`, `Beta_Industries_Ltd`).

**Example:**

```
Acme Corp. Senior Secured Term Loan | ISSUER=Acme_Corp; TYPE=SeniorSecuredLoan; INDUSTRY=Software; COUNTRY=US; AFFIL=NonAffiliated; RATE=SOFR_600; MATURITY=2027-12-31; CURRENCY=USD; ACCRUAL=Accruing
```

#### 3.2.2 Required Key-Value Pairs

> **Naming rule:** All keys and values must be valid QName tokens — no spaces, and only `-` and `_` as special characters. See Section 3.2.1 for encoding conventions.

The following keys would be required where applicable to the investment type:

| Key | Description | Required For |
|---|---|---|
| `ISSUER` | Legal name of the issuer / portfolio company | All investments |
| `TYPE` | Security type (standardized enumeration) | All investments |
| `INDUSTRY` | Industry classification (standardized enumeration) | All investments |
| `COUNTRY` | Country of domicile of investee (ISO 3166 alpha-2) | All investments |
| `AFFIL` | Affiliation status (standardized enumeration) | All investments |
| `CURRENCY` | Currency denomination (ISO 4217) | All investments |
| `RATE` | Interest rate description, QName-encoded (e.g., `SOFR_600` for SOFR + 600 bps; `12-50pct` for a fixed 12.50% rate) | Debt instruments |
| `MATURITY` | Maturity date (ISO 8601: YYYY-MM-DD) | Debt instruments |
| `ACCRUAL` | Accrual status (Accruing or NonAccrual) | Debt instruments |

#### 3.2.3 Standardized Value Enumerations

To ensure consistency across filers, the SEC or DQC would publish a controlled vocabulary for enumerated keys. Proposed standard values include:

**TYPE (Security Type):**
- `SeniorSecuredLoan`, `FirstLienLoan`, `SecondLienLoan`, `SubordinatedDebt`, `UnsecuredDebt`, `Mezzanine`, `Equity`, `PreferredEquity`, `Warrant`, `CLONote`, `RevolvingCreditFacility`, `LetterOfCredit`

**AFFIL (Affiliation Status):**
- `NonAffiliated`, `Affiliated`, `Controlled`

**ACCRUAL (Accrual Status):**
- `Accruing`, `NonAccrual`

**INDUSTRY:** Values drawn from the standard industry classification used in the filing (e.g., GICS sector names or MSCI BICS names, consistently applied). Multi-word sector names must encode spaces as `_` (e.g., `Business_Services`, `Health_Care`, `Information_Technology`).

**COUNTRY:** ISO 3166-1 alpha-2 country codes (e.g., `US`, `GB`, `CA`, `DE`). Two-letter codes are inherently QName-compliant.

**CURRENCY:** ISO 4217 currency codes (e.g., `USD`, `EUR`, `GBP`). Three-letter codes are inherently QName-compliant.

**ISSUER:** The portfolio company's legal name with spaces replaced by `_` and punctuation (`.`, `,`, `'`, `-`) removed or replaced with `_` (e.g., `Acme_Corp`, `Beta_Industries_Ltd`, `Gamma_Holdings_LLC`).

### 3.3 DQC Validation Rules

The DQC would implement validation rules to confirm that:

1. Every Investment Identifier member label contains the required `|` delimiter and key-value pair block.
2. All required keys are present for each investment type.
3. All keys and values conform to QName naming rules: no spaces, only `-` and `_` as special characters.
4. Enumerated values for `TYPE`, `AFFIL`, `ACCRUAL`, and `COUNTRY` match the published controlled vocabulary.
5. `MATURITY` values are valid ISO 8601 dates (format `YYYY-MM-DD`, which uses only digits and `-` and is therefore inherently QName-compliant).
6. `CURRENCY` values are valid ISO 4217 codes.
7. `ISSUER` values contain no spaces or disallowed special characters.

These rules would allow filers to validate compliance before submission and would give the SEC a mechanism to identify and address non-compliant filings.

### 3.4 Compatibility with Existing Compliant Filers

Filers who are currently tagging investment attributes using Extensible Enumeration as per the FASB guidance **would not be required to change their approach**. The key-value pair encoding in member names is proposed as a minimum floor for filers who cannot or do not tag the full Extensible Enumeration attribute facts. Compliant filers may optionally include the key-value pair string in member names for redundancy, but this would not be required.

---

## 4. Footnote Tagging Standardization

### 4.1 Current Problem

In addition to the attribute tagging issues described above, BDC filers are inconsistent in how they tag required disclosures that appear as footnotes on the Schedule of Investments. Attributes about an investment may be disclosed in one of three ways:

1. As a structured XBRL attribute fact using Extensible Enumeration (the FASB-required approach)
2. As a footnote on the investment (e.g., a superscript footnote mark referencing a note that explains the investment is on non-accrual status, is a PIK instrument, has a delayed draw feature, etc.)

Because these disclosure approaches are used interchangeably and without standardization, data consumers cannot reliably determine whether a given attribute has been disclosed for a given investment, or identify what the footnote text means without string parsing.

### 4.2 Impact

- Non-accrual status — a material attribute for credit analysis — is frequently only disclosed as a footnote symbol (e.g., `(a) Investment is on non-accrual status`) with no structured XBRL tag that identifies the investment as non-accrual.
- PIK (Payment-in-Kind) interest features, delayed draw terms, and currency hedging arrangements are similarly disclosed only as footnotes.
- Data consumers must maintain filer-specific parsing logic to interpret footnote text, which is not scalable and is error-prone.

### 4.3 Proposed Approach: Required Key-Value Pairs for Material Footnote Disclosures

We propose that the SEC identify a set of **required footnote disclosures** that must be tagged using the key-value pair approach described in Section 3.2, in addition to (or instead of) the narrative footnote. This would ensure that material investment attributes disclosed via footnote are captured in structured, machine-readable form.

**Proposed required footnote attributes to tag:**

| Attribute | Recommended Key | Standard Values |
|---|---|---|
| Non-accrual status | `ACCRUAL` | `Accruing`, `NonAccrual` |
| PIK (Payment-in-Kind) interest | `ITYPE` | `Cash`, `PIK`, `CashPIK` |
| Delayed draw feature | `DRAW` | `FullyDrawn`, `Undrawn`, `PartiallyDrawn` |
| Unfunded commitment | `FUNDED` | `Funded`, `Unfunded`, `PartiallyFunded` |
| Currency hedge | `HEDGED` | `Yes`, `No` |
| Fixed/floating rate indicator | `RATETYPE` | `Fixed`, `Floating` |

Narrative footnotes would remain permissible for additional qualitative context, but the structured key-value pair would be the authoritative tagged representation of the attribute.

---

## 5. Implementation Considerations

### 5.1 SEC Inline XBRL Viewer

The SEC's Office of Structured Disclosure should be made aware of the Inline XBRL viewer instability associated with large BDC filings so that a parallel technical remediation effort can be undertaken. This issue is independent of the tagging methodology and would affect any approach that increases the number of structured facts in BDC filings.

### 5.2 FASB Coordination

This interim proposal should be communicated to the FASB as a recognized deviation from the current XBRL Implementation Guide for investment companies. The FASB should be invited to participate in Phase 4 to design a revised approach that addresses the practical barriers identified in this report.

### 5.3 Filing Agent Guidance

SEC staff should issue staff guidance or FAQs clarifying:

1. The minimum tagging requirements for BDC Schedule of Investments under this approach.
2. That filers using key-value pair format in member labels satisfy the interim requirement.
3. That filers using full Extensible Enumeration tagging need not adopt the key-value pair format.
4. The controlled vocabularies for enumerated values.

---

## 6. Summary of Recommendations

| # | Recommendation | Owner | Priority |
|---|---|---|---|
| 1 | Establish key-value pair standard for Investment Identifier member names | SEC / DQC | Immediate |
| 2 | Publish controlled vocabulary for TYPE, AFFIL, ACCRUAL, COUNTRY, CURRENCY | SEC / DQC | Immediate |
| 3 | Implement DQC validation rules for key-value pair format | DQC | Immediate |
| 4 | Issue SEC staff guidance on interim tagging approach | SEC | Short-term |
| 5 | Identify required footnote disclosures for structured tagging | SEC / DQC | Short-term |
| 6 | Coordinate with FASB on deviation from XBRL Implementation Guide | SEC | Short-term |
| 7 | Address SEC Inline XBRL viewer file size limitations | SEC OSD | Medium-term |
| 8 | Initiate comprehensive review of BDC XBRL implementation | SEC / FASB / DQC | 24 months |

---

## Appendix A: References

- FASB XBRL Implementation Guide — Financial Investment Companies (BDC):  
  [https://xbrl.fasb.org/impdocs/BDC_TIG/financialinvestmentcompanies.htm](https://xbrl.fasb.org/impdocs/BDC_TIG/financialinvestmentcompanies.htm)
- Investment Company Act of 1940
- ERISA — Employee Retirement Income Security Act of 1974
- SEC Regulation S-X, Article 6 — Investment Companies
- SEC Form 11-K and related rules under the Securities Exchange Act of 1934
- SEC Inline XBRL Technical Specifications
- DQC Validation Rules for BDC Schedule of Investments
- DQC Validation Rules for Form 11-K Schedule of Investments

---

## Appendix B: Example Compliant Member Name Labels

The following examples illustrate the proposed key-value pair format for Investment Identifier member labels across common BDC investment types:

Note: the human-readable display name before the `|` delimiter may contain any characters (it is the member label used for presentation purposes). Only the key-value pairs after the `|` delimiter must conform to QName naming rules.

**Senior Secured Term Loan (US, Non-Affiliated):**
```
Acme Corp. — First Lien Senior Secured Term Loan | ISSUER=Acme_Corp; TYPE=FirstLienLoan; INDUSTRY=Software; COUNTRY=US; AFFIL=NonAffiliated; RATE=SOFR_600; MATURITY=2027-12-31; CURRENCY=USD; ACCRUAL=Accruing; RATETYPE=Floating; ITYPE=Cash
```
*(RATE encodes "SOFR + 600 bps": `+` replaced with `_`)*

**Non-Accrual Subordinated Debt (Canada):**
```
Beta Industries Ltd. — Subordinated Note | ISSUER=Beta_Industries_Ltd; TYPE=SubordinatedDebt; INDUSTRY=Manufacturing; COUNTRY=CA; AFFIL=NonAffiliated; RATE=12-50pct; MATURITY=2026-06-30; CURRENCY=USD; ACCRUAL=NonAccrual; RATETYPE=Fixed; ITYPE=PIK
```
*(RATE encodes "12.50% fixed": `.` replaced with `-` and `%` replaced with `pct`)*

**Equity Investment (Controlled Affiliate):**
```
Gamma Holdings LLC — Common Equity | ISSUER=Gamma_Holdings_LLC; TYPE=Equity; INDUSTRY=Healthcare; COUNTRY=US; AFFIL=Controlled; CURRENCY=USD
```

**Revolving Credit Facility (Partially Drawn):**
```
Delta Services Inc. — Senior Secured Revolving Credit Facility | ISSUER=Delta_Services_Inc; TYPE=RevolvingCreditFacility; INDUSTRY=Business_Services; COUNTRY=US; AFFIL=NonAffiliated; RATE=SOFR_550; MATURITY=2028-03-15; CURRENCY=USD; ACCRUAL=Accruing; RATETYPE=Floating; DRAW=PartiallyDrawn; FUNDED=PartiallyFunded
```
*(RATE encodes "SOFR + 550 bps"; INDUSTRY encodes "Business Services" with space replaced by `_`)*

---

*This report is a draft prepared by the Data Quality Committee for discussion with the Securities and Exchange Commission. It does not represent a final position of the DQC or any regulatory body.*
