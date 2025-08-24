# Power Query Join (Merge) Operations 


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

### Table 2: `Dim_Employees` (10 rows)

The official HR directory. It has a `ManagerName` column for the self-join example. Note that "Grace Hopper" and "Henry Ford" have no sales in the `Fact_Sales` table.

| EmployeeID | FullName      | Department | HireDate   | ManagerName  |
| :--------- | :------------ | :--------- | :--------- | :----------- |
| E101       | Alice Smith   | Sales      | 6/1/2022   | Carol White  |
| E102       | Bob Johnson   | Sales      | 7/15/2022  | Carol White  |
| E103       | Carol White   | Sales      | 3/1/2020   | null         |
| E104       | David Green   | Sales      | 8/20/2023  | Carol White  |
| E105       | Eve Black     | Support    | 9/1/2021   | Grace Hopper |
| E106       | Grace Hopper  | HR         | 5/10/2019  | null         |
| E107       | Henry Ford    | Operations | 11/12/2023 | Grace Hopper |
| E108       | Isaac Newton  | IT         | 2/28/2022  | Grace Hopper |
| E109       | Jane Austen   | Marketing  | 7/30/2023  | Grace Hopper |
| E110       | Kevin Mitnick | IT         | 4/19/2024  | Isaac Newton |

### Table 3: `Dim_Products` (12 rows)

The official product catalog. Note that the 'Projector' and 'Docking Station' have never been sold.

| SKU     | ProductName         | Category    | Supplier   | UnitCost |
| :------ | :------------------ | :---------- | :--------- | :------- |
| SKU-101 | ProBook Laptop      | Computers   | OmniCorp   | 850      |
| SKU-102 | Gaming Desktop      | Computers   | OmniCorp   | 550      |
| SKU-103 | UltraWide Monitor   | Peripherals | Visionex   | 1100     |
| SKU-104 | Ergonomic Mouse     | Accessories | ClickRight | 35       |
| SKU-105 | Mechanical Keyboard | Accessories | ClickRight | 50       |
| SKU-106 | 4K Webcam           | Peripherals | Visionex   | 110      |
| SKU-107 | USB-C Hub           | Accessories | ConnectAll | 15       |
| SKU-108 | Printer Paper       | Supplies    | PaperCo    | 2        |
| SKU-109 | Wireless Headset    | Accessories | SoundWave  | 18       |
| SKU-110 | Laptop Stand        | Accessories | ConnectAll | 45       |
| SKU-111 | HD Projector        | Peripherals | Visionex   | 400      |
| SKU-112 | Docking Station     | Accessories | ConnectAll | 120      |

---

## Standard Join Types

For these examples, we will merge `Fact_Sales` (Left Table) with `Dim_Employees` (Right Table) on `EmployeeName` and `FullName`.

### 1. Left Outer Join

-   **The Situation:** You are building the main sales performance dashboard. You need to show **every single transaction** and enrich it with employee details like their department and hire date. If a sale was made by someone not in the HR system (like a temp), you still need to see the sale and identify the missing employee info.
-   **Why this Join?** It keeps all rows from your primary table (`Fact_Sales`) and adds matching information from the secondary table (`Dim_Employees`). This is the most common join for enriching a fact table with dimension attributes.
-   **How to:**
    1.  Select the `Fact_Sales` query.
    2.  On the **Home** tab, click **Merge Queries**.
    3.  Select `Dim_Employees` as the second table.
    4.  Click on the `EmployeeName` column in `Fact_Sales`, then the `FullName` column in `Dim_Employees`.
    5.  For **Join Kind**, select **Left Outer**.

#### Before Merge Snippet:
`Fact_Sales` (Left) & `Dim_Employees` (Right)

#### After Merge Snippet (and Expanding):
The sale by "Frank" is included, but his department and hire date are `null` because he's not in the `Dim_Employees` table.

| SaleID | EmployeeName | Revenue | Dim_Employees.Department | Dim_Employees.HireDate |
| :--- | :--- | :--- | :--- | :--- |
| ... | ... | ... | ... | ... |
| T1020 | Eve Black | 100 | Support | 9/1/2021 |
| **T1021** | **Frank** | **1200** | **_null_** | **_null_** |
| T1022 | Bob Johnson | 150 | Sales | 7/15/2022 |
| ... | ... | ... | ... | ... |

---

### 2. Right Outer Join

-   **The Situation:** An HR manager wants a report of **all employees** and their total sales revenue to evaluate performance. It's critical to include employees who have made zero sales to identify non-sales staff or underperforming sales staff.
-   **Why this Join?** It keeps all rows from the "right" table (`Dim_Employees`) and brings in matching sales data from the "left" (`Fact_Sales`). Employees with no sales will have `null` values for the sales columns.
-   **How to:** Follow the steps above, but for **Join Kind**, select **Right Outer**.

#### Before Merge Snippet:
`Fact_Sales` (Left) & `Dim_Employees` (Right)

#### After Merge Snippet (and Expanding):
Employees like Grace, Henry, Isaac, Jane, and Kevin are listed, but their sales data is `null` as they have no records in `Fact_Sales`.

| FullName | Department | SaleID | Revenue |
| :--- | :--- | :--- | :--- |
| ... | ... | ... | ... |
| Eve Black | Support | T1020 | 100 |
| **Grace Hopper** | **HR** | **_null_** | **_null_** |
| **Henry Ford** | **Operations** | **_null_** | **_null_** |
| **Isaac Newton** | **IT** | **_null_** | **_null_** |
| ... | ... | ... | ... |

---

### 3. Full Outer Join

-   **The Situation:** You are conducting a full data reconciliation between the sales system and the HR system. You need a single view that shows **all sales and all employees**, highlighting all mismatches: sales by people not in HR, and employees who have never made a sale.
-   **Why this Join?** It combines both Left and Right Outer joins, keeping all rows from both tables and filling in `nulls` where matches don't exist on either side.
-   **How to:** Follow the steps above, but for **Join Kind**, select **Full Outer**.

#### Before Merge Snippet:
`Fact_Sales` (Left) & `Dim_Employees` (Right)

#### After Merge Snippet (and Expanding):
This comprehensive view shows both the sale by "Frank" (with no employee details) and employees like "Grace Hopper" (with no sales details).

| SaleID | EmployeeName | Revenue | FullName | Department |
| :--- | :--- | :--- | :--- | :--- |
| ... | ... | ... | ... | ... |
| T1020 | Eve Black | 100 | Eve Black | Support |
| **T1021** | **Frank** | **1200** | **_null_** | **_null_** |
| T1022 | Bob Johnson | 150 | Bob Johnson | Sales |
| **_null_** | **_null_** | **_null_** | **Grace Hopper** | **HR** |
| **_null_** | **_null_** | **_null_** | **Henry Ford** | **Operations** |
| ... | ... | ... | ... | ... |

---

### 4. Inner Join

-   **The Situation:** The finance department needs a report for calculating sales commissions. The report must **only include sales made by official, current employees** listed in the HR directory. Sales from temporary staff or data entry errors must be excluded.
-   **Why this Join?** It returns only the rows that have a matching key in **both** tables. It's the perfect way to create a "clean" dataset based on confirmed matches.
-   **How to:** Follow the steps above, but for **Join Kind**, select **Inner**.

#### Before Merge Snippet:
`Fact_Sales` (Left) & `Dim_Employees` (Right)

#### After Merge Snippet (and Expanding):
The sale by "Frank" is gone. The result is a clean intersection of the two datasets.

| SaleID | EmployeeName | Revenue | Department | HireDate |
| :--- | :--- | :--- | :--- | :--- |
| T1001 | Alice Smith | 1200 | Sales | 6/1/2022 |
| T1002 | Bob Johnson | 150 | Sales | 7/15/2022 |
| T1003 | Carol White | 800 | Sales | 3/1/2020 |
| ... | ... | ... | ... | ... |
| T1024 | David Green | 75 | Sales | 8/20/2023 |

---

### 5. Left Anti Join

-   **The Situation:** The data quality team needs a report of all sales transactions where the `EmployeeName` does not match any `FullName` in the official `Dim_Employees` directory. This helps identify data entry errors or sales attributed to former/temporary staff.
-   **Why this Join?** It's a filtering join that returns only the rows from the left table (`Fact_Sales`) that **do not have a match** in the right table.
-   **How to:** Follow the steps above, but for **Join Kind**, select **Left Anti**.

#### Before Merge Snippet:
`Fact_Sales` (Left) & `Dim_Employees` (Right)

#### After Merge:
The result is a single row: the sale made by "Frank", who is not in the employee list.

| SaleID | SaleDate | ProductSKU | EmployeeName | UnitsSold | Revenue |
| :--- | :--- | :--- | :--- | :--- | :--- |
| T1021 | 4/5/2025 | SKU-101 | Frank | 1 | 1200 |

---

### 6. Right Anti Join

-   **The Situation:** A manager wants a list of all employees who have **zero sales** recorded in the `Fact_Sales` table. This could be used to confirm that non-sales staff are not making sales, or to identify sales staff who need training.
-   **Why this Join?** It's the inverse of Left Anti, returning only the rows from the right table (`Dim_Employees`) that **do not have a match** in the left table.
-   **How to:** Follow the steps above, but for **Join Kind**, select **Right Anti**.

#### Before Merge Snippet:
`Fact_Sales` (Left) & `Dim_Employees` (Right)

#### After Merge:
The result is a list of all employees who have never appeared in the sales data.

| EmployeeID | FullName | Department | HireDate | ManagerName |
| :--- | :--- | :--- | :--- | :--- |
| E106 | Grace Hopper | HR | 5/10/2019 | null |
| E107 | Henry Ford | Operations | 11/12/2023 | Grace Hopper |
| E108 | Isaac Newton | IT | 2/28/2022 | Grace Hopper |
| E109 | Jane Austen | Marketing | 7/30/2023 | Grace Hopper |
| E110 | Kevin Mitnick | IT | 4/19/2024 | Isaac Newton |

---

## Special & Conceptual Join Types

### Self Join

-   **The Situation:** You need to create an organizational chart report that shows each employee and their direct manager in the same row. The hierarchy information (`ManagerName`) is contained within the `Dim_Employees` table itself.
-   **Why this Join?** A Self Join merges a table with a copy of itself, allowing you to relate rows within the same table.
-   **How to:**
    1.  In the Power Query Editor, right-click the `Dim_Employees` query and select **Duplicate**. Rename the new query `Managers`.
    2.  Select the original `Dim_Employees` query.
    3.  Click **Merge Queries**. Select `Managers` as the second table.
    4.  Join `Dim_Employees` on `ManagerName` to `Managers` on `FullName`. Use a **Left Outer Join**.
    5.  Expand the new column and select just the `EmployeeID` and `Department` of the manager. Rename them to `ManagerID` and `ManagerDepartment`.

#### Before Merge Snippet (`Dim_Employees`):

| EmployeeID | FullName | ManagerName |
| :--- | :--- | :--- |
| E101 | Alice Smith | Carol White |
| E102 | Bob Johnson | Carol White |
| E103 | Carol White | _null_ |
| ... | ... | ... |
| E110 | Kevin Mitnick | Isaac Newton |

#### After Self Join:

| FullName | Department | ManagerName | ManagerID | ManagerDepartment |
| :--- | :--- | :--- | :--- | :--- |
| Alice Smith | Sales | Carol White | E103 | Sales |
| Bob Johnson | Sales | Carol White | E103 | Sales |
| Carol White | Sales | _null_ | _null_ | _null_ |
| David Green | Sales | Carol White | E103 | Sales |
| Eve Black | Support | Grace Hopper | E106 | HR |
| ... | ... | ... | ... | ... |
| Kevin Mitnick | IT | Isaac Newton | E108 | IT |

---

## Union or Append (Not a Join)

It's crucial to distinguish merging (joining) from appending (union).

-   **Merge (Join):** Adds **columns** from different tables based on a matching key. It makes a table **wider**.
-   **Append (Union):** Stacks rows from one table on top of another. It requires the tables to have similar column structures. It makes a table **taller**.

-   **The Situation:** The HR department has a separate list of new hires for the current quarter who are not yet in the main `Dim_Employees` directory. You need to create a single, master list of all employees—both current and new—for a company-wide email distribution.
-   **How to:**
    1.  Ensure you have both tables (`Dim_Employees` and `HR_Onboarding`) loaded in Power Query.
    2.  On the **Home** tab, click **Append Queries** (or its dropdown and choose **Append Queries as New**).
    3.  Select the two tables to combine.

#### Before Append:
**`Dim_Employees` (Snippet)**
| EmployeeID | FullName | Department |
| :--- | :--- | :--- |
| E109 | Jane Austen | Marketing |
| E110 | Kevin Mitnick | IT |

**`HR_Onboarding` (New Table)**
| FullName | Department | HireDate |
| :--- | :--- | :--- |
| Leo Tolstoy | Marketing | 8/1/2025 |
| Marie Curie | R&D | 8/5/2025 |

#### After Append:
The new hires are added to the bottom of the list. Power Query automatically handles the columns, placing `null` where data (like `EmployeeID`) doesn't exist in one of the tables.

| EmployeeID | FullName | Department |
| :--- | :--- | :--- |
| ... | ... | ... |
| E109 | Jane Austen | Marketing |
| E110 | Kevin Mitnick | IT |
| **_null_** | **Leo Tolstoy** | **Marketing** |
| **_null_** | **Marie Curie** | **R&D** |