# Power Query Join (Merge) & Append Operations 

## The Sample Datasets

Imagine we are analyzing data for a company. We have three core tables: a sales fact table, an employee dimension table, and a product dimension table.

### Table 1: `Fact_Sales` (24 rows)

This table contains raw sales transactions. It uses `EmployeeName` instead of an ID, which is a common real-world challenge. It also contains a sale for a `ProductSKU` that doesn't exist (`SKU-ERR-01`) and a sale by a temporary contractor ("Frank") who isn't in the employee directory.

| SaleID | SaleDate  | ProductSKU | EmployeeName  | UnitsSold | Revenue |
| :----- | :-------- | :--------- | :------------ | :-------- | :------ |
| T1001  | 1/15/2025 | SKU-101    | Alice Smith   | 1         | 1200    |
| T1002  | 1/16/2025 | SKU-104    | Bob Johnson   | 2         | 150     |
| T1003  | 1/17/2025 | SKU-102    | Carol White   | 1         | 800     |
| T1004  | 1/18/2025 | SKU-107    | David Green   | 5         | 125     |
| T1005  | 1/20/2025 | SKU-101    | Alice Smith   | 1         | 1200    |
| T1006  | 1/21/2025 | SKU-105    | Eve Black     | 3         | 225     |
| T1007  | 2/5/2025  | SKU-108    | Bob Johnson   | 10        | 50      |
| T1008  | 2/6/2025  | SKU-103    | Carol White   | 1         | 1500    |
| T1009  | 2/8/2025  | SKU-106    | David Green   | 2         | 300     |
| T1010  | 2/9/2025  | SKU-102    | Alice Smith   | 1         | 800     |
| T1011  | 2/11/2025 | SKU-109    | Eve Black     | 4         | 100     |
| T1012  | 2/12/2025 | SKU-101    | Bob Johnson   | 1         | 1200    |
| T1013  | 3/1/2025  | SKU-110    | Carol White   | 2         | 180     |
| T1014  | 3/2/2025  | SKU-104    | David Green   | 3         | 225     |
| T1015  | 3/5/2025  | SKU-107    | Alice Smith   | 8         | 200     |
| T1016  | 3/7/2025  | SKU-105    | Eve Black     | 2         | 150     |
| T1017  | 3/10/2025 | SKU-ERR-01 | Bob Johnson   | 1         | 99      |
| T1018  | 3/12/2025 | SKU-102    | David Green   | 1         | 800     |
| T1019  | 4/1/2025  | SKU-103    | Alice Smith   | 1         | 1500    |
| T1020  | 4/3/2025  | SKU-108    | Eve Black     | 20        | 100     |
| T1021  | 4/5/2025  | SKU-101    | Frank         | 1         | 1200    |
| T1022  | 4/6/2025  | SKU-106    | Bob Johnson   | 1         | 150     |
| T1023  | 4/8/2025  | SKU-109    | Carol White   | 3         | 75      |
| T1024  | 4/10/2025 | SKU-104    | David Green   | 1         | 75      |

## Appending Datasets (Union)

It's crucial to distinguish merging (joining) from appending (union).

-   **Merge (Join):** Adds **columns** from different tables based on a matching key. It makes a table **wider**.
-   **Append (Union):** Stacks rows from one table on top of another. It requires the tables to have similar column structures. It makes a table **taller**.

### The Scenario: Combining Data from Multiple Sources

A company's data is rarely in one place. You might have in-store sales from a point-of-sale system (`Fact_Sales`), online sales from a web database, and returns from a legacy system. To get a complete picture, you must combine them.

#### New Table 1: `Online_Sales`
Data from the e-commerce platform. It has a `CustomerID` but no `EmployeeName`.

| SaleID  | SaleDate  | ProductSKU | CustomerID | UnitsSold | Revenue |
| :------ | :-------- | :--------- | :--------- | :-------- | :------ |
| WEB-001 | 1/25/2025 | SKU-101    | CUST-843   | 1         | 1250    |
| WEB-002 | 2/14/2025 | SKU-109    | CUST-129   | 2         | 60      |
| WEB-003 | 3/8/2025  | SKU-106    | CUST-843   | 1         | 160     |
| WEB-004 | 4/11/2025 | SKU-104    | CUST-551   | 3         | 225     |
| WEB-005 | 4/12/2025 | SKU-110    | CUST-129   | 1         | 95      |

#### New Table 2: `Legacy_System_Returns`
Data from an older system for product returns. Note the different column names and negative values.

| ReturnID | TransactionDate | Product_Code | Quantity | Amount |
| :--- | :--- | :--- | :--- | :--- |
| RTN-501 | 1/28/2025 | SKU-101 | -1 | -1200 |
| RTN-502 | 2/18/2025 | SKU-104 | -1 | -75 |
| RTN-503 | 3/15/2025 | SKU-102 | -1 | -800 |
| RTN-504 | 4/20/2025 | SKU-109 | -2 | -50 |

### The Append Operation

-   **The Situation:** To get a complete picture of all sales and return activities, we need to combine `Fact_Sales`, `Online_Sales`, and `Legacy_System_Returns` into a single, unified table.
-   **The Challenge:** The tables have different column names (`ProductSKU` vs. `Product_Code`) and some tables have columns that others don't (`CustomerID`, `EmployeeName`).
-   **How to:**
    1.  In the Power Query Editor, on the **Home** tab, click the dropdown for **Append Queries** and select **Append Queries as New**.
    2.  In the dialog box, select **Three or more tables**.
    3.  Add `Fact_Sales`, `Online_Sales`, and `Legacy_System_Returns` to the list of tables to append and click OK.

#### Before Append Snippets:
**`Fact_Sales`**
| SaleID | ProductSKU | EmployeeName | UnitsSold |
| :--- | :--- | :--- | :--- |
| T1024 | SKU-104 | David Green | 1 |

**`Online_Sales`**
| SaleID | ProductSKU | CustomerID | UnitsSold |
| :--- | :--- | :--- | :--- |
| WEB-001 | SKU-101 | CUST-843 | 1 |

**`Legacy_System_Returns`**
| ReturnID | Product_Code | Quantity |
| :--- | :--- | :--- |
| RTN-501 | SKU-101 | -1 |

#### After Append (Initial Result):
Power Query stacks the tables and creates a complete set of columns. Where a table didn't have a certain column, the value is `null`. **Crucially, notice that because the column names were different (`ProductSKU` vs. `Product_Code`), Power Query created two separate columns.**

| SaleID | ProductSKU | EmployeeName | UnitsSold | CustomerID | ReturnID | Product_Code | Quantity | Amount |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| T1024 | SKU-104 | David Green | 1 | _null_ | _null_ | _null_ | _null_ | _null_ |
| WEB-001 | SKU-101 | _null_ | 1 | CUST-843 | _null_ | _null_ | _null_ | _null_ |
| _null_ | _null_ | _null_ | _null_ | _null_ | RTN-501 | SKU-101 | -1 | -1200 |

### Post-Append Cleanup: Unifying Columns

The job isn't finished. You must clean up the appended table to make it usable. The most important step is to combine the mismatched columns.

-   **The Goal:** Create a single, unified `ProductSKU` column from the `ProductSKU` and `Product_Code` columns.
-   **How to (Coalesce Pattern):**
    1.  With the new appended query selected, go to the **Add Column** tab and click **Custom Column**.
    2.  Name the new column `UnifiedSKU`.
    3.  Enter the following formula. This says: if the `ProductSKU` column is not empty, use it; otherwise, use the value from the `Product_Code` column.
        ```powerquery
        if [ProductSKU] <> null then [ProductSKU] else [Product_Code]
        ```
    4.  Click OK. You will now have a new column that is correctly populated.
    5.  You can now right-click and **Remove** the original `ProductSKU` and `Product_Code` columns.
    6.  Repeat this process for other mismatched columns (e.g., `SaleID`/`ReturnID`, `UnitsSold`/`Quantity`, etc.).

#### After Append (Final Cleaned-Up Result Snippet):

| TransactionID | TransactionDate | EmployeeName | UnifiedSKU | Units | Revenue | CustomerID |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| T1024 | 4/10/2025 | David Green | SKU-104 | 1 | 75 | _null_ |
| WEB-001 | 1/25/2025 | _null_ | SKU-101 | 1 | 1250 | CUST-843 |
| RTN-501 | 1/28/2025 | _null_ | SKU-101 | -1 | -1200 | _null_ |

This final, clean table combines all transactions into a single source of truth, ready for analysis and modeling.