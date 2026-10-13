# Pair Project Proposal: Do Richer Neighborhoods Name Their Dogs Differently?
Shweta Mayur Goradia & Aslan French | Revised discussion draft | October 13, 2026

## Research Question and Motivation
Do the names recorded on dog licenses differ between higher- and lower-income areas of New York City? This playful question involves geographic lookups, messy text, name-reference construction, and aggregation across incompatible schemas. We will explore human-name recognition and generational associations, with uncommonness and diversity as an extension.

Steven D. Levitt and Stephen J. Dubner's *Freakonomics* discusses how baby names associated with higher-income families can spread more broadly over time. This motivates our pet-name question and the generational extension: do areas favor names popular among humans roughly 100 years ago, 50 years ago, or recently? We do not assume this pattern applies to dogs; a snapshot cannot establish diffusion over time.

Income describes the surrounding Census area, not an individual owner's household. Our conclusions will concern associations among licensed-dog naming patterns and area income, rather than causal effects of personal wealth.

## Project Goals and Outputs
- Create a joined dataset connecting license records, area income, and human-name reference measures.
- Compare human-name recognition rates across area income and examine whether matched names differ in generational association.
- Explore whether areas favor uncommon names or have greater name diversity, accounting for unequal numbers of records.
- Produce an area-level summary table, plots of naming measures against income, and illustrative names for interpretation. Document null or inconsistent relationships alongside interesting patterns.
- Implement equivalent workflows in R tidyverse, Python Pandas, SQL, and Excel/Power Query; validate that they yield the same cleaned counts and summary measures. Disclose LLM assistance and understand submitted code.

## Data Sources and Join Structure
1. **NYC Dog Licensing Dataset - NYC Department of Health and Mental Hygiene.** Names, owner ZIP codes, breed, sex, birth year, license dates, and extract year. Each row is an active license period; renewals can create multiple records for a dog. The published fields lack a dog ID. We will select a documented annual extract and explicitly address repeat records rather than assume one row per dog. [Dataset](https://data.cityofnewyork.us/Health/NYC-Dog-Licensing-Dataset/nu7n-tubp).
2. **ACS five-year median household income, B19013 - U.S. Census Bureau.** One estimate and margin of error per ZCTA. Choose a release appropriate to the license period; the 2024 release is a candidate. [2024 table fields](https://api.census.gov/data/2024/acs/acs5/groups/B19013.html).
3. **National baby names by birth year - Social Security Administration.** Name, sex, and count by year since 1880. Build a reference with one row per normalized name, combining counts across sex and deriving recognition and decade-popularity measures. [Downloads](https://www.ssa.gov/oact/babynames/limits.html).
4. **ZIP-to-ZCTA crosswalk - HRSA GeoCare Navigator.** The official 2024 workbook uses TIGER 2023 ZCTA boundaries and June 2023 ZIP data. It has unique ZIP keys, although several ZIPs can map to one ZCTA. [Workbook](https://data.hrsa.gov/DataDownload/GeoCareNavigator/ZIPCodetoZCTACrosswalk2024UDS.xlsx).

**Joins:** license ZIP -> crosswalk ZCTA -> ACS income; cleaned name -> prepared SSA name reference. Aggregate at ZCTA level when using ZCTA income, combining postal ZIPs that map to the same area.

**Feasibility check, October 13, 2026:** 105,941 of 105,974 records in NYC's 2024 extract map to a nonblank ZCTA (99.97%); 33 records across 29 ZIP codes remain unmatched. This verifies postal lookup coverage, not geographic perfection or availability of every income estimate. Census API access currently requires a key; income acquisition and final coverage remain to be checked.

## Operational Definitions
- **Human-name recognition:** match to an SSA reference meeting a declared frequency threshold within a specified period. Compare reasonable thresholds. Report unmatched names as unmatched, not proof that they are non-human names.
- **Generational association:** compare relative name popularity across chosen birth decades, provisionally the 1920s, 1970s, and 2010s. Use proportions or ranks rather than raw counts. Allow shared or unclear associations; historic SSA coverage is incomplete before 1937.
- **Name uncommonness (extension):** measure how infrequent a name is in a citywide reference period, then summarize rarity by area. Define the reference and any rare-name cutoff explicitly.
- **Local name diversity (extension):** measure variety and concentration within an area. Compare expected distinct names in equally sized samples, or the probability that two records have different names. Raw distinct-name counts alone would favor areas with more records.

## Transformations Needed
- **Excluding rows and columns:** select the annual extract; retain dates and identifying attributes needed for repeat-record checks. Flag missing names, placeholders such as UNKNOWN and NAME NOT PROVIDED, invalid postal codes, and unmatched geography. Report exclusions rather than silently drop them.
- **Extracting and recoding:** retain original names and create normalized match keys using case and whitespace rules. Handle qualifiers only under documented rules; do not indiscriminately shorten compound names. Store postal codes as five-character strings, restoring leading zeros where justified.
- **Lookups and joins:** apply the ZIP-to-ZCTA crosswalk; attach income estimates and their margins of error; join the prepared SSA reference. Validate key uniqueness and row counts to prevent accidental multiplication of records.
- **Grouping and aggregation:** sum SSA counts across sex and years; calculate comparable decade measures. Aggregate license records into area counts, recognition rates, and generational profiles. Retain missingness and sample-size indicators.
- **Wider/longer and ranking:** reshape decade measures for comparison; optionally rank names within income groups. Use explicit denominators and distinguish weighting areas equally from weighting by license-record count.
- **Validation:** reconcile counts across stages and all four implementations; inspect ambiguous matches and test sensitivity to name thresholds, repeat-record handling, and low-count areas.

## Entity Resolution, Scope, and Interpretation
ZIP and ZCTA codes refer to related but different geographies; the crosswalk resolves that mismatch. We will confirm whether this meets the course's synonym-handling requirement and, if needed, identify genuine observed aliases or spelling variants for an explicit lookup. Case conversion alone is not synonym handling.

Keep the core project in NYC. Compare annual patterns before pooling years, since the same dogs can recur. Licensed dogs need not represent all dogs, and area income may be associated with other demographic differences. SSA coverage is not a universal inventory of human names. We will describe these limits and avoid interpreting unmatched names as inherently strange. [SSA qualifications](https://www.ssa.gov/oact/babynames/background.html).

Exploration is part of the project: document the measures and comparisons tried, and use another annual extract to assess the consistency of promising patterns where feasible. A null relationship remains an informative result. Exact sampling period, recognition thresholds, income groupings, repeat-record treatment, and extension scope remain joint decisions before implementation. This is a proposed revision for discussion, not a settled specification.

## AI Disclosure
Aslan used OpenAI Codex to help examine public data sources, run ZIP-to-ZCTA feasibility checks, explore operational definitions, and draft this revision of Shweta's proposal. The research interests and preferences came from the partners' ideas and discussion; methodological suggestions and wording were AI-assisted. This remains a discussion draft for both partners to review and agree on before submission. No final project workflow has been implemented.
