# 📝 Data Cleaning and Validation Log

**Project:** Retail Business Sales Data Standardization  
**Analyst:** Business Analyst Team  
**Date:** June 26, 2026  
[cite_start]**Target Output File:** `data/cleaned_orders.xlsx` [cite: 12]

---

## 1. Summary of Issues Found
[cite_start]During the initial inspection of `data/raw_orders.xlsx`, several critical data integrity and quality roadblocks were identified[cite: 5, 10]:
* [cite_start]**Structural Text Inconsistencies:** Extraneous whitespace variations, leading/trailing spaces, and mixed casing across categorical inputs (`customer_name`, `region`, `city`, `state`)[cite: 5, 13, 14, 15, 17, 18, 19, 25, 26, 27, 28].
* [cite_start]**Date Format Corruption:** Mixed regional parameters (interspersed $MM/DD/YYYY$ and $DD-MM-YYYY$ formats) causing severe string misalignment and parsing errors within Excel[cite: 5].
* [cite_start]**Logical Violations:** Occurrences where logical temporal rules failed, specifically shipping dates registering prior to original order placement dates[cite: 5, 45, 65].
* [cite_start]**Redundant Ingestion:** Multi-system extraction caused identical rows to be captured multiple times, artificially inflating overall revenue figures[cite: 4, 5].

---

## 2. Cleaning Actions Performed
[cite_start]The following technical interventions were systematically applied to isolate and resolve systemic structural discrepancies[cite: 119]:
* [cite_start]**Text Field Harmonization:** Implemented a combination of `=PROPER(TRIM())` and `=UPPER(TRIM())` configurations to strip non-standard trailing spaces and unify structural text fields across categorical filters[cite: 31, 32].
* **Surgical Date Normalization:** Resolved stubborn, multi-locale date values using a customized cell-state check formula: `=IF(ISNUMBER(D2), D2, DATE(RIGHT(D2,4), LEFT(D2,2), MID(D2,4,2)))`. This accurately extracted year, month, and day components from lingering text fields and converted them into cohesive date serial numbers.
* [cite_start]**Financial & Margin Re-calculation:** Created calculated columns for `calculated_sales`, `calculated_profit`, and `profit_margin` utilizing strict division error handling (`=IFERROR()`) to bypass `#DIV/0!` disruptions if transactions resulted in zero financial value[cite: 68, 69, 76, 78, 80].

---

## 3. Business Rules Applied
[cite_start]Per the explicit instructions from leadership, the following validation rules were written into the core processing matrices to protect downstream analytical models[cite: 63, 64]:
* [cite_start]**Missing Regional/Logistical Context:** Fields missing geographical markers or shipping designations were populated with `"Unknown"` and categorized accordingly[cite: 65].
* [cite_start]**Invalid Discount Constraints:** Negative discount allocations or percentages outside regular operational windows were evaluated and suppressed[cite: 65].
* [cite_start]**Order Tracking Flag (`data_quality_flag`):** Generated an automated evaluation column using structural logic to separate transactions into distinct states[cite: 68, 70, 88]:
    * [cite_start]`"Clean"`: Fully validated records matching corporate operational guidelines[cite: 90].
    * [cite_start]`"Warning"`: Flagged transaction failures (`payment_status = "Failed"`) or cancellations (`order_status = "Cancelled"`)[cite: 5, 65].
    * [cite_start]`"Invalid"`: Rows containing negative delays or flawed parameter inputs[cite: 45, 65].

---

## 4. Records Removed & Flagged Summary
* [cite_start]**Exact Duplicate Rows Purged:** 20 records completely removed using the Excel data deduplication engine after matching all column headers[cite: 52, 57, 59, 122].
* [cite_start]**Conflicting Order IDs Flagged:** 12 records flagged via a `=COUNTIF(A:A, A2)` count vector to track non-unique order placements without corrupting the historical tracking index[cite: 53, 58, 60, 62, 123].
* [cite_start]**Total Quality Anomalies Flagged:** 90 records identified with active validation flags (`Warning` or `Invalid`)[cite: 60, 123].

---

## 5. Assumptions and Process Limitations
* [cite_start]**Assumption on Blanks:** Assumed that empty discount blocks sitting against verified sales transactions were meant to denote standard $0\%$ discounts[cite: 65].
* [cite_start]**Data Exclusion Boundary:** Cancelled and failed rows are fully preserved inside `cleaned_orders.xlsx` for transparency, but are filtered out of core regional performance matrix sheets to ensure operational evaluations remain unskewed[cite: 11, 12, 65].
* [cite_start]**Limitation:** String-slicing conversions for dates depend entirely on standard delimiters; highly corrupted, arbitrarily typed manual dates may still require human manual entry auditing[cite: 124].
