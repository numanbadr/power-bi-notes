# Introduction to Data Normalization

Data normalization is a systematic process of organizing data in a database or data model to reduce data redundancy and improve data integrity. Think of it as tidying up your data, ensuring each piece of information has a proper home and doesn't appear in multiple, unnecessary places.

The process involves breaking down a large table into smaller, related tables and defining relationships between them. This approach helps prevent anomalies (inconsistencies) that can arise when data is updated, deleted, or inserted in unnormalized tables.

To guide this process, database theory defines "normal forms." We'll look at the first three, which are most commonly applied:

---

## Normal Forms (1NF, 2NF, 3NF)

### a. First Normal Form (1NF)

**Definition**: A table is in 1NF if:  

1. Each column contains atomic (indivisible) values.  
2. There are no repeating groups of columns.  
3. Each row is unique, typically ensured by a primary key.  

---

**Unnormalized (Violating 1NF):**

| OrderID | Customer | Full Address           | Product1 | Qty1 | Product2 | Qty2 |
|---------|----------|------------------------|----------|------|----------|------|
| 101     | Alice    | 123 Main St, Anytown   | Laptop   | 1    | Mouse    | 2    |
| 102     | Bob      | 456 Oak Ave, Cityville | Keyboard | 1    | NULL     | NULL |

---

**In 1NF (split into two tables):**

Orders Table

| OrderID | Customer | Street        | City      |
|---------|----------|---------------|-----------|
| 101     | Alice    | 123 Main St   | Anytown   |
| 102     | Bob      | 456 Oak Ave   | Cityville |

OrderDetails Table

| OrderID | Product   | Quantity |
|---------|-----------|----------|
| 101     | Laptop    | 1        |
| 101     | Mouse     | 2        |
| 102     | Keyboard  | 1        |

---

### b. Second Normal Form (2NF)

**Definition**: A table is in 2NF if it is already in 1NF, **AND** all non-key attributes are fully dependent on the **entire primary key**. This mainly applies when there is a composite key.

---

**Unnormalized (Violating 2NF):**

| OrderID | ProductID | ProductName | ProductPrice | Quantity |
|---------|-----------|-------------|--------------|----------|
| 101     | 1         | Laptop      | 1200         | 1        |
| 101     | 2         | Mouse       | 25           | 2        |
| 102     | 1         | Laptop      | 1200         | 1        |

(Redundancy: *Laptop* and *1200* are repeated.)

---

**In 2NF (split into two tables):**

OrderDetails Table

| OrderID | ProductID | Quantity |
|---------|-----------|----------|
| 101     | 1         | 1        |
| 101     | 2         | 2        |
| 102     | 1         | 1        |

Products Table

| ProductID | ProductName | ProductPrice |
|-----------|-------------|--------------|
| 1         | Laptop      | 1200         |
| 2         | Mouse       | 25           |

---

### c. Third Normal Form (3NF)

**Definition**: A table is in 3NF if it is already in 2NF, **AND** there are no transitive dependencies. (No non-key attribute depends on another non-key attribute.)

---

**Unnormalized (Violating 3NF):**

| CustomerID | CustomerName | CustomerCity  | CitySalesRegion |
|------------|--------------|---------------|-----------------|
| C001       | Alice Smith  | New York      | East            |
| C002       | Bob Johnson  | Los Angeles   | West            |
| C003       | Charlie B.   | New York      | East            |

(Problem: *CitySalesRegion* depends on *CustomerCity*, not directly on *CustomerID* → transitive dependency.)

---

**In 3NF (split into two tables):**

Customers Table

| CustomerID | CustomerName | CustomerCity |
|------------|--------------|--------------|
| C001       | Alice Smith  | New York     |
| C002       | Bob Johnson  | Los Angeles  |
| C003       | Charlie B.   | New York     |

Cities Table

| City        | SalesRegion |
|-------------|-------------|
| New York    | East        |
| Los Angeles | West        |

---

## 2. Why and When to Normalize Data in Power BI

While databases often aim for higher normal forms, for analytical tools like Power BI, achieving 3NF or a well-structured star schema is generally sufficient and highly beneficial.

### Benefits of Normalization in Power BI

1. **Reducing Data Redundancy:**
    * Storing unique information only once (e.g., customer details in a `DimCustomer` table, product details in `DimProduct`).
    * **Impact:** Smaller file sizes, faster data refreshes, and more efficient use of memory.

2. **Improving Data Integrity:**
    * Less chance of inconsistencies when data changes. If a customer's address changes, you update it in one place (`DimCustomer`), not potentially across thousands of transaction rows.
    * **Impact:** More reliable and accurate reports.

3. **Optimizing Model Performance:**
    * Power BI's VertiPaq engine thrives on highly compressed, normalized data. Dimension tables (often containing low cardinality columns) can be compressed very efficiently.
    * **Impact:** Faster report interactions, quicker DAX calculations, and snappier dashboards.

4. **Facilitating Easier Analysis with DAX:**
    * Well-defined relationships between normalized tables simplify DAX formulas. You can easily aggregate measures from a fact table and slice them by attributes from related dimension tables without complex `LOOKUPVALUE` or `SUMMARIZE` functions.
    * **Impact:** Simpler to write and debug DAX, leading to more robust and flexible analytical models.

### Scenarios and Indicators for When Normalization is Necessary

You should consider normalizing your data in Power BI when you encounter any of the following:

* **"Flat" Wide Tables:** A single table that contains all transactional data along with all descriptive details (e.g., order lines, customer details, product details, employee details all in one massive table).
* **Repeating Groups of Data:** Columns with a numeric suffix indicating multiple instances of the same attribute (e.g., `Product1_Name`, `Product1_Quantity`, `Product2_Name`, `Product2_Quantity`). This is a clear violation of 1NF.
* **Unpivot Scenarios:** When attributes are stored as column headers rather than values in a single column (e.g., monthly sales data where each month is a column: `Jan_Sales`, `Feb_Sales`, `Mar_Sales`).
* **Duplicate Information:** When you see the same descriptive text (like a customer's full address or an employee's region) repeated across many rows, especially in transactional data.
* **Single Column Holding Multiple Attributes:** A column like "Product Code and Name" or "City-State-Zip" that needs to be broken down. This is a 1NF violation.

---

## 3. Practical Example: Unnormalized Data Scenario

Let's consider a scenario for an electronics retail store. We have an export of "Monthly Sales Transactions" that lumps all information into a single table. This is typical of raw data extracts. We'll use a conceptual table of approximately 50 rows, but for demonstration purposes, a representative snippet is shown below.

Our goal is to take this unnormalized table and transform it into a more efficient and analytical-friendly star schema using Power Query.

### Unnormalized Data Example: `MonthlySales_Flat`

This table represents sales transactions over a few months. Notice how customer, employee, and especially product details are repeated or grouped within a single row.

| OrderID | OrderDate  | CustomerName   | CustomerEmail        | CustomerCity | EmployeeName | EmployeeRegion | Product1_Name    | Product1_Qty | Product1_Price | Product2_Name    | Product2_Qty | Product2_Price |
| :------ | :--------- | :------------- | :------------------- | :----------- | :----------- | :------------- | :--------------- | :----------- | :------------- | :--------------- | :----------- | :------------- |
| 1001    | 2025-01-15 | Alice Smith    | <alice@email.com>      | New York     | John Doe     | North          | Laptop           | 1            | 1200           | Mouse            | 2            | 25             |
| 1002    | 2025-01-15 | Bob Johnson    | <bob@email.com>        | Los Angeles  | Jane Doe     | West           | Keyboard         | 1            | 75             | NULL             | NULL         | NULL           |
| 1003    | 2025-01-16 | Alice Smith    | <alice@email.com>      | New York     | John Doe     | North          | Monitor          | 1            | 300            | Webcam           | 1            | 50             |
| 1004    | 2025-01-16 | Charlie Brown  | <charlie@email.com>    | Chicago      | Sam Spade    | Central        | Printer          | 1            | 250            | Ink Cartridge    | 4            | 30             |
| 1005    | 2025-01-17 | David Lee      | <david@email.com>      | New York     | John Doe     | North          | USB Drive        | 5            | 15             | NULL             | NULL         | NULL           |
| 1006    | 2025-02-01 | Bob Johnson    | <bob@email.com>        | Los Angeles  | Jane Doe     | West           | Headphones       | 1            | 100            | Microphone       | 1            | 60             |
| 1007    | 2025-02-02 | Alice Smith    | <alice@email.com>      | New York     | John Doe     | North          | Laptop           | 1            | 1200           | External HDD     | 1            | 80             |
| 1008    | 2025-02-03 | Charlie Brown  | <charlie@email.com>    | Chicago      | Sam Spade    | Central        | Keyboard         | 1            | 75             | Mouse            | 1            | 25             |
| 1009    | 2025-02-04 | Emily White    | <emily@email.com>      | Houston      | Jane Doe     | West           | Monitor          | 1            | 300            | NULL             | NULL         | NULL           |
| 1010    | 2025-02-05 | Frank Green    | <frank@email.com>      | Miami        | John Doe     | North          | Printer          | 1            | 250            | Paper            | 2            | 10             |
| 1011    | 2025-02-06 | George Black   | <george@email.com>     | Seattle      | Sam Spade    | Central        | Scanner          | 1            | 150            | NULL             | NULL         | NULL           |
| 1012    | 2025-02-07 | Alice Smith    | <alice@email.com>      | New York     | John Doe     | North          | SSD Drive        | 2            | 60             | External Monitor | 1            | 200            |
| ...     | ...        | ...            | ...                  | ...          | ...          | ...            | ...              | ...          | ...            | ...              | ...          | ...            |

**Issues in this table:**

* **1NF Violation (Repeating Groups):** `Product1_Name`, `Product1_Qty`, `Product1_Price`, `Product2_Name`, `Product2_Qty`, `Product2_Price` are repeating groups. Each order can have multiple products, but they are stored horizontally across columns instead of vertically in rows. This means adding a `Product3` would require adding new columns.
* **Redundancy (2NF/3NF Violation):**
  * `CustomerName`, `CustomerEmail`, `CustomerCity` are repeated for every order placed by the same customer (e.g., Alice Smith).
  * `EmployeeName` and `EmployeeRegion` are repeated for every order handled by the same employee (e.g., John Doe).
  * `ProductX_Price` is repeated for every order where the same product is sold.

---

## 4. Step-by-Step Normalization in Power Query

We will use the Power Query Editor in Power BI Desktop to transform `MonthlySales_Flat` into a normalized schema, creating a `FactSales` table and dimension tables for `DimCustomer`, `DimEmployee`, and `DimProduct`.

**Start in Power BI Desktop:**

1. **Load the data:** Imagine you've loaded this `MonthlySales_Flat` data from an Excel file or CSV. It appears as a query in the Power Query Editor.

### Step 1: Create the `DimCustomer` Table

This table will hold unique customer details.

1. **Duplicate the original query:**
    * In the Power Query Editor, right-click on the `MonthlySales_Flat` query in the "Queries" pane (left side).
    * Select "Duplicate".
    * Rename the new query to `DimCustomer`.

2. **Select relevant columns:**
    * In the `DimCustomer` query, select the columns: `CustomerName`, `CustomerEmail`, `CustomerCity`.
    * Right-click on any of the selected column headers and choose "Remove Other Columns".

3. **Remove duplicate rows:**
    * With `CustomerName`, `CustomerEmail`, `CustomerCity` columns still selected, go to the "Home" tab on the ribbon.
    * Click "Remove Rows" -> "Remove Duplicates". This ensures each customer appears only once.

4. **Add a `CustomerID`:**
    * Go to the "Add Column" tab.
    * Click "Index Column" -> "From 1". This creates a unique ID for each customer.
    * Drag the `CustomerID` column to the far left, making it the first column.
    * **Reasoning:** We are creating a unique dimension table for customers, ensuring each customer's details are stored only once (reducing redundancy and improving 3NF). The `CustomerID` will be our primary key for this dimension.

**Resulting `DimCustomer` Table:**

| CustomerID | CustomerName  | CustomerEmail     | CustomerCity |
| :--------- | :------------ | :---------------- | :----------- |
| 1          | Alice Smith   | <alice@email.com>   | New York     |
| 2          | Bob Johnson   | <bob@email.com>     | Los Angeles  |
| 3          | Charlie Brown | <charlie@email.com> | Chicago      |
| 4          | David Lee     | <david@email.com>   | New York     |
| 5          | Emily White   | <emily@email.com>   | Houston      |
| 6          | Frank Green   | <frank@email.com>   | Miami        |
| 7          | George Black  | <george@email.com>  | Seattle      |
| ...        | ...           | ...               | ...          |

### Step 2: Create the `DimEmployee` Table

This table will hold unique employee details.

1. **Duplicate the original query again:**
    * Right-click on `MonthlySales_Flat`.
    * Select "Duplicate".
    * Rename the new query to `DimEmployee`.

2. **Select relevant columns:**
    * In the `DimEmployee` query, select `EmployeeName`, `EmployeeRegion`.
    * Right-click and "Remove Other Columns".

3. **Remove duplicate rows:**
    * With `EmployeeName`, `EmployeeRegion` selected, go to "Home" tab.
    * Click "Remove Rows" -> "Remove Duplicates".

4. **Add an `EmployeeID`:**
    * Go to the "Add Column" tab.
    * Click "Index Column" -> "From 1".
    * Drag `EmployeeID` to the far left.
    * **Reasoning:** Similar to customers, we create a dedicated dimension for employees to remove redundant employee information from the sales transaction table (3NF).

**Resulting `DimEmployee` Table:**

| EmployeeID | EmployeeName | EmployeeRegion |
| :--------- | :----------- | :------------- |
| 1          | John Doe     | North          |
| 2          | Jane Doe     | West           |
| 3          | Sam Spade    | Central        |

### Step 3: Create the `DimProduct` Table

This table will hold unique product details. This step involves unpivoting to address the 1NF violation.

1. **Duplicate the original query again:**
    * Right-click on `MonthlySales_Flat`.
    * Select "Duplicate".
    * Rename the new query to `DimProduct_Temp`. (We'll clean this up later).

2. **Unpivot the Product Groups:**
    * In `DimProduct_Temp`, select all columns *except* the product-related ones: `OrderID`, `OrderDate`, `CustomerName`, `CustomerEmail`, `CustomerCity`, `EmployeeName`, `EmployeeRegion`.
    * Now, select all the product-related columns: `Product1_Name`, `Product1_Qty`, `Product1_Price`, `Product2_Name`, `Product2_Qty`, `Product2_Price`.
    * Go to the "Transform" tab.
    * Click "Unpivot Columns" -> "Unpivot Selected Columns".
        * **Reasoning:** This is crucial for 1NF. It transforms our horizontal "repeating groups" (Product1, Product2) into vertical rows. Now, instead of `Product1_Name`, `Product1_Qty` on one row, we'll have separate rows for each distinct product sold in an order.

3. **Filter out NULLs and unnecessary rows:**
    * You'll now have an `Attribute` column (e.g., "Product1_Name", "Product1_Qty") and a `Value` column (the actual product name, quantity, or price).
    * Click the filter arrow on the `Value` column header.
    * Uncheck "(null)" to remove rows where no product was sold in a slot.
    * Now, only keep the columns that represent product details. Select `Attribute` and `Value` columns. Right-click and "Remove Other Columns".

4. **Extract Product Number and Detail Type:**
    * Select the `Attribute` column.
    * Go to "Transform" tab.
    * Click "Split Column" -> "By Delimiter".
    * Choose "Custom" delimiter `_`. Select "At the first occurrence of the delimiter". This splits "Product1_Name" into "Product1" and "Name".
    * Rename the first split column `Attribute.1` to `ProductIdentifier`.
    * Rename the second split column `Attribute.2` to `DetailType`.
    * **Reasoning:** We need to separate the unique product identification (e.g., "Product1") from what kind of detail it is (Name, Qty, Price) to pivot effectively.

5. **Pivot to get `ProductName`, `Quantity`, `Price` columns:**
    * Select the `DetailType` column.
    * Go to "Transform" tab.
    * Click "Pivot Column".
    * In the Pivot Column dialog:
        * "Values Column": Select `Value`.
        * "Advanced options": Select "Don't Aggregate".
    * Click "OK".
        * **Reasoning:** This transforms the `DetailType` values (Name, Qty, Price) back into columns, but now each `ProductIdentifier` (e.g., Product1 from Order 1001) is on its own row, with its `Name`, `Qty`, and `Price` in respective columns. This effectively brings the original `ProductX_Name`, `ProductX_Qty`, `ProductX_Price` into a normalized format (`ProductName`, `Quantity`, `UnitPrice`).

6. **Clean up and identify unique products:**
    * Rename the `Name` column to `ProductName`.
    * Rename the `Qty` column to `ProductQuantity` (we'll remove this in the final `DimProduct` but keep it for identifying unique combinations here).
    * Rename the `Price` column to `UnitPrice`.
    * Change data types: `ProductQuantity` to Whole Number, `UnitPrice` to Decimal Number.
    * Select `ProductName` and `UnitPrice` columns. Right-click -> "Remove Other Columns".
    * Select `ProductName` and `UnitPrice` columns. Go to "Home" tab -> "Remove Rows" -> "Remove Duplicates".
        * **Reasoning:** We only want unique products and their base price for the dimension table.

7. **Add a `ProductID`:**
    * Go to "Add Column" tab.
    * Click "Index Column" -> "From 1".
    * Rename this new column `ProductID`. Drag it to the far left.
    * Rename the query from `DimProduct_Temp` to `DimProduct`.

**Resulting `DimProduct` Table:**

| ProductID | ProductName        | UnitPrice |
| :-------- | :----------------- | :-------- |
| 1         | Laptop             | 1200      |
| 2         | Mouse              | 25        |
| 3         | Keyboard           | 75        |
| 4         | Monitor            | 300       |
| 5         | Webcam             | 50        |
| 6         | Printer            | 250       |
| 7         | Ink Cartridge      | 30        |
| 8         | USB Drive          | 15        |
| 9         | Headphones         | 100       |
| 10        | Microphone         | 60        |
| 11        | External HDD       | 80        |
| 12        | Paper              | 10        |
| 13        | Scanner            | 150       |
| 14        | SSD Drive          | 60        |
| 15        | External Monitor   | 200       |
| ...       | ...                | ...       |

### Step 4: Create the `FactSales` Table

This will be our central fact table, containing transactional details and foreign keys to our new dimension tables.

1. **Duplicate the original query again:**
    * Right-click on `MonthlySales_Flat`.
    * Select "Duplicate".
    * Rename the new query to `FactSales_Temp`. (We will clean this up).

2. **Unpivot the Product Groups (similar to DimProduct, but keeping order context):**
    * In `FactSales_Temp`, select all columns *except* the product-related ones: `OrderID`, `OrderDate`, `CustomerName`, `CustomerEmail`, `CustomerCity`, `EmployeeName`, `EmployeeRegion`.
    * Select the product-related columns: `Product1_Name`, `Product1_Qty`, `Product1_Price`, `Product2_Name`, `Product2_Qty`, `Product2_Price`.
    * Go to "Transform" tab.
    * Click "Unpivot Columns" -> "Unpivot Selected Columns".
        * **Reasoning:** Again, converting horizontal product groups to vertical rows for each order line item. This is crucial for analyzing individual products within an order.

3. **Filter out NULL products:**
    * Filter the `Value` column to remove "(null)" values, ensuring we only keep actual sales items.

4. **Extract `ProductIdentifier` and `DetailType`:**
    * Select the `Attribute` column.
    * Go to "Transform" tab.
    * Click "Split Column" -> "By Delimiter".
    * Choose "Custom" delimiter `_`. Select "At the first occurrence of the delimiter".
    * Rename `Attribute.1` to `ProductIdentifier` and `Attribute.2` to `DetailType`.

5. **Pivot to get `ProductName`, `Quantity`, `Price` columns for each line item:**
    * Select the `DetailType` column.
    * Go to "Transform" tab.
    * Click "Pivot Column".
    * In the Pivot Column dialog:
        * "Values Column": Select `Value`.
        * "Advanced options": Select "Don't Aggregate".
    * Click "OK".
    * Rename the new columns: `Name` to `ProductName`, `Qty` to `Quantity`, `Price` to `UnitPrice`.
    * Change data types: `Quantity` to Whole Number, `UnitPrice` to Decimal Number.

6. **Merge `FactSales_Temp` with `DimCustomer` to get `CustomerID`:**
    * Go to the "Home" tab.
    * Click "Merge Queries" -> "Merge Queries as New".
    * Select `FactSales_Temp` as the first table.
    * Select `DimCustomer` as the second table.
    * Select `CustomerName` and `CustomerEmail` in `FactSales_Temp`, then `CustomerName` and `CustomerEmail` in `DimCustomer`. (This composite key ensures accurate matching, especially if customer names aren't unique without email).
    * Choose "Left Outer" Join Kind.
    * Click "OK".
    * In the new merged column (e.g., `DimCustomer` or `NewColumn`), click the expand icon.
    * Uncheck "Select All Columns". Only select `CustomerID`.
    * Uncheck "Use original column name as prefix".
    * Click "OK".
    * **Reasoning:** We replace the repetitive `CustomerName`, `CustomerEmail`, `CustomerCity` with a single `CustomerID` foreign key, adhering to 2NF/3NF.

7. **Merge `FactSales_Temp` with `DimEmployee` to get `EmployeeID`:**
    * Click "Merge Queries" -> "Merge Queries". (This time we merge into the current `FactSales_Temp` table).
    * Select `FactSales_Temp` as the first table.
    * Select `DimEmployee` as the second table.
    * Select `EmployeeName` in `FactSales_Temp`, then `EmployeeName` in `DimEmployee`.
    * Choose "Left Outer" Join Kind.
    * Click "OK".
    * Expand the new merged column, selecting only `EmployeeID`. Uncheck "Use original column name as prefix".
    * **Reasoning:** Replacing redundant employee details with an `EmployeeID` foreign key.

8. **Merge `FactSales_Temp` with `DimProduct` to get `ProductID`:**
    * Click "Merge Queries" -> "Merge Queries".
    * Select `FactSales_Temp` as the first table.
    * Select `DimProduct` as the second table.
    * Select `ProductName` in `FactSales_Temp`, then `ProductName` in `DimProduct`.
    * Choose "Left Outer" Join Kind.
    * Click "OK".
    * Expand the new merged column, selecting only `ProductID`. Uncheck "Use original column name as prefix".
    * **Reasoning:** Replacing repetitive product details (name, price) with a `ProductID` foreign key.

9. **Remove original descriptive columns and unnecessary interim columns:**
    * Now that we have `CustomerID`, `EmployeeID`, and `ProductID`, remove the original `CustomerName`, `CustomerEmail`, `CustomerCity`, `EmployeeName`, `EmployeeRegion`, `ProductName` (the original one from the source table), `ProductIdentifier` columns.
    * Keep: `OrderID`, `OrderDate`, `Quantity`, `UnitPrice`, `CustomerID`, `EmployeeID`, `ProductID`.
    * **Correction for `FactSales` (`UnitPrice`):** When we pivoted in step 5, we got `UnitPrice` for each line item. We will use this `UnitPrice` (which represents the sales price *at the time of the transaction*) to calculate `LineTotal`.
        * Select `Quantity` and `UnitPrice` columns.
        * Go to "Add Column" tab -> "Standard" -> "Multiply".
        * Rename the new column `Multiplication` to `LineTotal`.

10. **Clean up and finalize `FactSales`:**
    * Ensure data types are correct (e.g., `OrderID` as Text or Whole Number, `OrderDate` as Date, `Quantity` as Whole Number, `UnitPrice` as Decimal Number, `LineTotal` as Decimal Number).
    * Remove any remaining temporary columns like `ProductQuantity` (from step 6. and any intermediate `Attribute`/`Value` columns from pivoting).
    * Rename the query from `FactSales_Temp` to `FactSales`.

**Resulting `FactSales` Table:**

| OrderID | OrderDate  | Quantity | UnitPrice | LineTotal | CustomerID | EmployeeID | ProductID |
| :------ | :--------- | :------- | :-------- | :-------- | :--------- | :--------- | :-------- |
| 1001    | 2025-01-15 | 1        | 1200      | 1200      | 1          | 1          | 1         |
| 1001    | 2025-01-15 | 2        | 25        | 50        | 1          | 1          | 2         |
| 1002    | 2025-01-15 | 1        | 75        | 75        | 2          | 2          | 3         |
| 1003    | 2025-01-16 | 1        | 300       | 300       | 1          | 1          | 4         |
| 1003    | 2025-01-16 | 1        | 50        | 50        | 1          | 1          | 5         |
| 1004    | 2025-01-16 | 1        | 250       | 250       | 3          | 3          | 6         |
| 1004    | 2025-01-16 | 4        | 30        | 120       | 3          | 3          | 7         |
| 1005    | 2025-01-17 | 5        | 15        | 75        | 4          | 1          | 8         |
| 1006    | 2025-02-01 | 1        | 100       | 100       | 2          | 2          | 9         |
| 1006    | 2025-02-01 | 1        | 60        | 60        | 2          | 2          | 10        |
| 1007    | 2025-02-02 | 1        | 1200      | 1200      | 1          | 1          | 1         |
| 1007    | 2025-02-02 | 1        | 80        | 80        | 1          | 1          | 11        |
| 1008    | 2025-02-03 | 1        | 75        | 75        | 3          | 3          | 3         |
| 1008    | 2025-02-03 | 1        | 25        | 25        | 3          | 3          | 2         |
| ...     | ...        | ...      | ...       | ...       | ...        | ...        | ...       |

---

## 5. Final Normalized Tables and Relationships

After performing these transformations in Power Query, you would click "Close & Apply" to load these tables into your Power BI Desktop model.

Your data model would now consist of:

1. **`FactSales`:** Your central fact table containing `OrderID`, `OrderDate`, `Quantity`, `UnitPrice`, `LineTotal`, and foreign keys (`CustomerID`, `EmployeeID`, `ProductID`).
2. **`DimCustomer`:** A dimension table with `CustomerID` (Primary Key), `CustomerName`, `CustomerEmail`, `CustomerCity`.
3. **`DimEmployee`:** A dimension table with `EmployeeID` (Primary Key), `EmployeeName`, `EmployeeRegion`.
4. **`DimProduct`:** A dimension table with `ProductID` (Primary Key), `ProductName`, `UnitPrice`.
    * *Note on UnitPrice in DimProduct:* This `UnitPrice` typically represents the standard or current list price. The `UnitPrice` in `FactSales` (which was derived from the raw data) represents the price *at the time of sale*, which can sometimes differ due to discounts or price changes. Both are valuable depending on the analysis.

### Relationships in Power BI Model View

In Power BI's "Model View," you would establish the following relationships:

* **`FactSales[CustomerID]`** Many-to-one \(\Leftarrow\) **`DimCustomer[CustomerID]`**
* **`FactSales[EmployeeID]`** Many-to-one \(\Leftarrow\) **`DimEmployee[EmployeeID]`**
* **`FactSales[ProductID]`** Many-to-one \(\Leftarrow\) **`DimProduct[ProductID]`**

These relationships connect your transactional `FactSales` table to the descriptive details in your dimension tables, forming a robust star schema.

---

## Conclusion

Data normalization is a fundamental step in preparing your data for effective analysis in Power BI. By systematically organizing your data into smaller, related tables using Power Query, you achieve:

* **Reduced data redundancy:** Storing information once, saving space and improving refresh times.
* **Enhanced data integrity:** Ensuring consistency and accuracy across your model.
* **Optimized performance:** Allowing Power BI's engine to work more efficiently, leading to faster reports.
* **Simplified DAX calculations:** Making it easier to build powerful measures and insights.

While the process might seem intricate at first, especially with techniques like unpivoting and merging, mastering these Power Query transformations will significantly elevate your Power BI data modeling skills, enabling you to build truly impactful and scalable solutions. Always aim for a well-normalized, star-schema-like structure for your Power BI datasets.
