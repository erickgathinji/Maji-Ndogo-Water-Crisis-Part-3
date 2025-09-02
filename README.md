
# Maji Ndogo Water Project: Data Integrity Audit and Corruption Investigation

## Project Overview

This project details an independent audit conducted on the `md_water_services` database for the Maji Ndogo water project. The audit was initiated by President Naledi following inconsistencies identified in the `water_quality` table by Chidi Kunto and his team. The primary objective was to assess the **integrity and accuracy of the data** stored in the database, ensuring its reliability for decision-making and governance.

The audit involved re-examining a randomly selected subset of 1620 records, re-recording water quality scores, and gathering statements from citizens near the water sources. This README outlines the steps taken to integrate the auditor's report, identify discrepancies, link them to specific employees, and gather evidence of potential misconduct.

## Problem Statement

Anomalous records were identified in the `water_quality` table, raising concerns about the accuracy and integrity of the water source data. This project aimed to investigate these anomalies to determine their origin and impact.

## Objectives

*   **Integrate the Auditor's Report**: Load the `auditor_report.csv` into the existing `md_water_services` database.
*   **Compare Quality Scores**: Analyze differences between the original surveyor-recorded `subjective_quality_score` and the auditor's `true_water_source_score`.
*   **Assess Data Consistency**: Verify the integrity of other crucial data, such as `type_of_water_source`, to ensure prior analyses remain valid.
*   **Identify Responsible Employees**: Link incorrect records to the employees who originally collected the data.
*   **Uncover Patterns of Error**: Determine if errors are random mistakes or indicate more systemic issues, potentially including corruption.
*   **Gather Incriminating Evidence**: Utilize citizen statements from the auditor's report to corroborate findings and identify specific individuals involved in suspicious activities.

## Data Sources

The following tables from the `md_water_services` database were utilized for this audit:

*   **`auditor_report`**: Contains the auditor's re-recorded scores, `location_id`, `type_of_water_source`, and `statements` from citizens.
*   **`water_quality`**: Stores the original `subjective_quality_score` recorded by surveyors.
*   **`visits`**: A central table linking `location_id`, `record_id`, `source_id`, and `assigned_employee_id`. It also includes `visit_count` to filter out duplicate visits.
*   **`employee`**: Contains `employee_name` and `assigned_employee_id` to identify surveyors.
*   **`water_source`**: Provides the `type_of_water_source` from the original survey data.

## Methodology

The audit process involved a series of SQL queries and data integration steps:

1.  **Database Integration**:
    *   The `auditor_report.csv` file was imported into the `md_water_services` database as a new table named `auditor_report`.
    *   The Entity Relationship Diagram (ERD) was reviewed to understand the database structure and relationships between tables, with a specific correction made to establish a one-to-one relationship between `visits` and `water_quality` tables using `record_id`.

2.  **Identifying Score Discrepancies**:
    *   Tables (`auditor_report`, `visits`, `water_quality`) were joined to compare `true_water_source_score` (auditor) with `subjective_quality_score` (surveyor).
    *   Records where `auditor_score != surveyor_score` and `visits.visit_count = 1` were filtered to identify incorrect records, totaling **102 discrepancies** out of 1620 audited sites. This indicated that 94% of the records checked were correct.

3.  **Verifying `type_of_water_source` Integrity**:
    *   The `water_source` table was joined to compare the `type_of_water_source` from the auditor's report and the original survey.
    *   It was confirmed that the `type_of_water_source` data remained consistent between the auditor and surveyor records, meaning previous analyses based on source types are still valid.

4.  **Linking Errors to Employees**:
    *   The `employee` table was joined to the set of incorrect records using `assigned_employee_id` to identify which employees were responsible for the erroneous entries.
    *   A **`VIEW` called `Incorrect_records`** was created to store this joined data, making subsequent queries more readable and manageable.

5.  **Identifying Suspect Employees**:
    *   A Common Table Expression (CTE) named `error_count` was created to calculate the total number of mistakes made by each employee. There were **17 employees** who made incorrect records.
    *   Another CTE, `suspect_list`, was used to identify employees whose `number_of_mistakes` was **above the average number of mistakes** across all employees.
    *   This process identified four key employees: **Bello Azibo (26 mistakes), Malachi Mavuso (21 mistakes), Zuriel Matembo (17 mistakes), and Lalitha Kaburi (7 mistakes)**, all of whom had an above-average number of errors.

6.  **Analyzing Citizen Statements for Evidence**:
    *   The `statements` column (containing citizen remarks) was added to the `Incorrect_records` view.
    *   Records associated with the four suspect employees were filtered from `Incorrect_records`.
    *   A specific search for statements containing the word **"cash"** was performed on these filtered records.
    *   A final verification confirmed that **only the four suspect employees** had allegations of "cash transactions" or other concerning behaviors in the citizen statements.

## Key Findings and Conclusion

The audit revealed significant insights:

*   **High Accuracy Rate**: The vast majority (94%) of the water quality records audited were accurate, which is a positive sign for the overall data collection efforts.
*   **Identified Discrepancies**: 102 records contained discrepancies between surveyor and auditor scores, requiring further investigation.
*   **Water Source Type Integrity**: The recorded `type_of_water_source` data was consistent, ensuring the validity of analyses based on this crucial feature.
*   **Four Suspect Employees**: A small group of four employees – **Bello Azibo, Malachi Mavuso, Zuriel Matembo, and Lalitha Kaburi** – were responsible for a disproportionately high number of incorrect records, exceeding the average mistake count of their peers.
*   **Incriminating Statements**: Critically, these four employees were the *only ones* associated with citizen statements mentioning "cash exchanges," "arrogance," "detachment," "negligence," and "corruption".

While this evidence is **not decisive proof of corruption**, the combination of an above-average error rate and specific, incriminating citizen statements exclusively tied to these four individuals is highly concerning. This information has been flagged and reported to President Naledi for immediate attention and further action to uphold accountability and transparency within the Maji Ndogo water project.

## Technologies Used

*   **SQL (MySQL/PyMySQL)**: For querying, manipulating, and analyzing data within the `md_water_services` database.
*   **Jupyter Notebooks / IPython SQL Magic**: Used for executing SQL queries and presenting results interactively.
