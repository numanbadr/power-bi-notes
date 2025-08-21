# Data Transformation in Power BI (PL-300)

---

## 1. Introduction to Data Transformation

### Purpose and Importance of Data Transformation

**Definition**
Data transformation is the process of cleaning, shaping, and restructuring raw data into a format suitable for analysis and reporting in Power BI.

**Use Cases**
- Raw sales data with inconsistent formatting needs standardization
- Multiple data sources require uniform structure for integration
- Text data contains unnecessary spaces and formatting issues
- Date formats vary across different systems
- Numerical data stored as text needs conversion

**Examples (Before & After)**

| Scenario | Before | After |
|----------|--------|--------|
| Customer Names | "  john SMITH  " | "John Smith" |
| Sales Amount | "$1,250.00" (Text) | 1250 (Number) |
| Order Date | "2024-12-25" (Text) | 12/25/2024 (Date) |

**Step-by-step in Power BI/Power Query**
1. Open Power BI Desktop
2. Click "Get Data" → Select data source
3. Click "Transform Data" to open Power Query Editor
4. Apply necessary transformations in the editor
5. Click "Close & Apply" to load transformed data

**Cautions/Pitfalls**
- Always preview data before applying transformations
- Keep original data source intact
- Document transformation steps for future reference
- Test with sample data first

**Pro Tips**
- Create a data profiling checklist before starting transformations: check for nulls, duplicates, data types, and outliers
- Use "Column Profile" and "Column Quality" features in the View ribbon to quickly assess data health
- Always work with a subset of data during development, then apply to full dataset
- Keep a transformation log document outside of Power BI to track business decisions and rationale
- Set up a staging area in your data model for raw data before transformation

---

### Overview of Power Query Editor

**Definition**
Power Query Editor is the data transformation interface in Power BI where you clean, shape, and combine data before loading it into your data model.

**Use Cases**
- Cleaning imported Excel files with formatting issues
- Combining data from multiple CSV files
- Restructuring database query results
- Creating calculated columns based on existing data

**Examples (Before & After)**

| Component | Purpose | Example Usage |
|-----------|---------|---------------|
| Applied Steps | Track transformation history | "Removed Columns", "Changed Type" |
| Data Preview | Show current data state | Live preview of 200 rows |
| Formula Bar | Edit M language code | `= Table.RemoveColumns(Source,{"Column1"})` |

**Step-by-step in Power BI/Power Query**
1. From Power BI, click "Transform Data"
2. Familiarize with ribbon tabs: Home, Transform, Add Column, View
3. Notice Applied Steps pane on the right
4. Observe data preview in center panel
5. Use Formula Bar for advanced editing

**Cautions/Pitfalls**
- Changes are not applied until "Close & Apply"
- Deleting Applied Steps can break subsequent steps
- Large datasets may slow down preview performance
- Always validate data types after transformations

**Pro Tips**
- Use Ctrl+Z to quickly undo mistakes instead of deleting Applied Steps
- Pin frequently used queries by right-clicking and selecting "Enable Load" to keep them easily accessible
- Use View → "Go To Column" to quickly navigate to specific columns in wide datasets
- Right-click column headers to see contextual transformation options specific to that data type
- Use the status bar at the bottom to monitor row and column counts as you transform data
- Enable "Show whitespace" in View options to visualize spaces and tabs in text data

---

## 2. Connecting to Data Sources

### Common Data Source Types

**Definition**
Data sources are the origins of data that Power BI can connect to, including files, databases, web services, and cloud platforms.

**Use Cases**
- Excel files from finance department
- SQL Server database for sales transactions
- SharePoint lists for project tracking
- Web APIs for real-time data feeds
- Azure SQL Database for cloud data

**Examples (Before & After)**

| Source Type | Connection Method | Data Access |
|-------------|------------------|-------------|
| Excel File | File → Excel Workbook | Import sheets and tables |
| SQL Server | Database → SQL Server | Query tables and views |
| Web API | Web → From Web | JSON/XML data consumption |
| SharePoint | Online Services → SharePoint | Lists and document libraries |

**Step-by-step in Power BI/Power Query**
1. Click "Get Data" in Power BI Desktop
2. Browse or search for desired connector
3. Enter connection details (server, file path, credentials)
4. Select specific tables/sheets to import
5. Choose connection mode (Import/DirectQuery)
6. Preview and transform data as needed

**Cautions/Pitfalls**
- Verify permissions before connecting
- Consider data refresh requirements
- Check for connection dependencies
- Validate data source stability

**Pro Tips**
- Use "Recent Sources" to quickly reconnect to frequently used data sources
- Save connection strings as parameters for easy environment switching (Dev/Test/Prod)
- Test connections with a small subset of data first to validate structure
- Use folder connections for processing multiple files with identical structure
- Create a data source inventory document with connection details and refresh schedules
- For web sources, use browser developer tools (F12) to inspect API calls and understand data structure

---

### Understanding Connection Modes

**Definition**
Connection modes determine how Power BI accesses and stores data: Import mode loads data into memory, while DirectQuery queries data source directly.

**Use Cases**
- **Import**: Small to medium datasets, historical reporting, offline access needed
- **DirectQuery**: Large datasets, real-time requirements, data governance restrictions
- **Composite**: Mix of imported and DirectQuery sources

**Examples (Before & After)**

| Mode | Data Size | Performance | Real-time | Storage |
|------|-----------|-------------|-----------|---------|
| Import | < 1GB recommended | Fast queries | No | Local cache |
| DirectQuery | Any size | Depends on source | Yes | No local storage |
| Composite | Mixed | Balanced | Partial | Hybrid approach |

**Step-by-step in Power BI/Power Query**
1. During data source connection, select connection mode
2. For Import: Data loads during refresh
3. For DirectQuery: Configure query optimization
4. Monitor performance and adjust as needed
5. Set appropriate refresh schedules

**Cautions/Pitfalls**
- Import mode requires regular refreshes
- DirectQuery performance depends on source system
- Not all transformations work with DirectQuery
- Consider licensing implications

**Pro Tips**
- Use DirectQuery for frequently changing transaction data and Import for slowly changing dimensions
- Monitor DirectQuery performance with SQL Profiler or database monitoring tools
- Create aggregated tables in DirectQuery scenarios to improve performance
- Consider using Power BI Premium's incremental refresh for large imported datasets
- Test report performance with realistic user concurrency before deploying DirectQuery solutions
- Use Composite mode strategically: Import small lookup tables, DirectQuery large fact tables

---

### Introduction to Query Folding

**Definition**
Query folding is Power Query's ability to push transformation logic back to the data source, improving performance by processing data at the source rather than in Power BI.

**Use Cases**
- Filtering large SQL Server tables
- Aggregating data at the database level
- Joining tables within the same database
- Converting data types using database functions

**Examples (Before & After)**

| Transformation | Foldable | Result |
|----------------|----------|--------|
| Filter Rows | Yes | WHERE clause in SQL |
| Remove Columns | Yes | SELECT specific columns |
| Custom Functions | No | Processed in Power Query |
| Complex Text Operations | No | May break folding |

**Step-by-step in Power BI/Power Query**
1. Right-click on Applied Steps
2. Look for "View Native Query" option
3. If available, folding is occurring
4. Optimize transformations to maintain folding
5. Use Query Diagnostics to monitor

**Cautions/Pitfalls**
- Not all data sources support folding
- Complex transformations may break folding
- Custom functions typically prevent folding
- Monitor performance impact when folding breaks

**Pro Tips**
- Place transformations that break folding (like custom functions) as late as possible in the transformation sequence
- Use database-specific functions when available (e.g., SQL Server's DATEPART) instead of Power Query equivalents
- Create views or stored procedures in your database for complex logic that doesn't fold well
- Test folding by checking the "View Native Query" option after each transformation step
- Keep a "folding-friendly" transformation library for common operations
- Use SQL Profiler to see exactly what queries are being sent to your database

---

## 3. Navigating the Power Query Editor Interface

### Editor Layout

**Definition**
The Power Query Editor interface consists of multiple panes and components that allow you to transform data: Ribbon, Query Pane, Data View, Applied Steps, and Formula Bar.

**Use Cases**
- Navigate between multiple queries efficiently
- Track transformation history with Applied Steps
- Edit advanced transformations using Formula Bar
- Preview data changes in real-time
- Access transformation functions through Ribbon

**Examples (Before & After)**

| Component | Location | Function |
|-----------|----------|----------|
| Ribbon | Top | Access to transformation commands |
| Queries Pane | Left | List of all queries and data sources |
| Data Preview | Center | Shows current state of selected query |
| Applied Steps | Right | History of transformations applied |
| Formula Bar | Below Ribbon | M code for current step |

**Step-by-step in Power BI/Power Query**
1. Open Power Query Editor from "Transform Data"
2. Identify the five main interface components
3. Select different queries in Queries Pane
4. Click on different Applied Steps to see changes
5. Use Formula Bar to view/edit M code
6. Explore Ribbon tabs for available functions

**Cautions/Pitfalls**
- Don't modify M code without understanding syntax
- Applied Steps are interdependent - deleting early steps may break later ones
- Large datasets may cause slow preview performance
- Some operations may not show immediate preview

**Pro Tips**
- Use Ctrl+1, Ctrl+2, Ctrl+3 to quickly switch between ribbon tabs
- Double-click the border between panes to auto-resize for optimal viewing
- Use Ctrl+G to quickly navigate to a specific row number in large datasets
- Right-click in empty space of the Queries pane to access global options
- Use F2 to quickly rename queries without right-clicking
- Dock the Power Query Editor window to a second monitor for better workspace management

---

### Understanding Applied Steps

**Definition**
Applied Steps is a sequential list of transformations performed on a query, allowing you to track, modify, or delete individual transformation steps.

**Use Cases**
- Troubleshoot data transformation issues
- Modify previous transformations without starting over
- Document the transformation process
- Revert changes by deleting steps
- Understand the order of operations

**Examples (Before & After)**

| Step Name | Action | M Code Example |
|-----------|--------|----------------|
| Source | Connect to data | `Excel.Workbook(File.Contents("C:\data.xlsx"))` |
| Navigation | Select worksheet | `Source{[Item="Sheet1"]}[Data]` |
| Promoted Headers | First row as headers | `Table.PromoteHeaders(Navigation)` |
| Changed Type | Set column data types | `Table.TransformColumnTypes(#"Promoted Headers",{{"Date", type date}})` |

**Step-by-step in Power BI/Power Query**
1. In Power Query Editor, locate Applied Steps pane
2. Click on any step to see data state at that point
3. Right-click on step for options (Delete, Rename, Edit)
4. Use gear icon next to step for settings
5. Add new steps using Ribbon commands
6. Reorder steps by careful deletion and recreation

**Cautions/Pitfalls**
- Deleting early steps may break dependent steps
- Renaming steps doesn't change references in M code
- Complex steps may not show settings gear icon
- Order matters - some transformations must occur before others

**Pro Tips**
- Insert comments in M code using /* comment */ to document complex business logic
- Use descriptive names for steps that reflect business meaning, not technical action
- Create "checkpoint" steps by adding custom columns with row counts to validate data at key points
- Use the step settings gear icon whenever available - it's easier than editing M code
- Copy step M code to notepad before making changes as a quick backup
- Group related steps by using consistent naming conventions (e.g., "Clean_", "Transform_", "Enrich_")

---

### Basic M Language Concepts

**Definition**
M is the functional programming language used by Power Query to define data transformation operations, visible and editable in the Formula Bar.

**Use Cases**
- Create custom transformations not available in UI
- Fix broken step references
- Optimize query performance
- Create reusable custom functions
- Understand and modify existing transformations

**Examples (Before & After)**

| Operation | UI Method | M Language |
|-----------|-----------|------------|
| Remove Column | Right-click → Remove | `Table.RemoveColumns(PreviousStep, {"ColumnName"})` |
| Filter Rows | Column filter | `Table.SelectRows(PreviousStep, each [Column] = "Value")` |
| Add Custom Column | Add Column → Custom | `Table.AddColumn(PreviousStep, "NewColumn", each [A] + [B])` |
| Change Data Type | Transform → Data Type | `Table.TransformColumnTypes(PreviousStep, {{"Column", type text}})` |

**Step-by-step in Power BI/Power Query**
1. Click on any Applied Step
2. Observe M code in Formula Bar
3. Try simple edits like changing column names
4. Use intellisense for function suggestions
5. Test changes and observe results
6. Practice with basic functions before complex operations

**Cautions/Pitfalls**
- Syntax errors will break the query
- Function names and syntax are case-sensitive
- Always test modifications with sample data
- Keep backups of working queries
- Reference correct step names in formulas

**Pro Tips**
- Use #"Step Name" syntax when step names contain spaces or special characters
- Learn the "each" keyword - it's shorthand for creating a function that operates on each row
- Use let...in statements to break complex operations into readable chunks
- Bookmark the official M function reference documentation for quick syntax lookup
- Use try...otherwise for error handling in M expressions
- Comment your M code with // for single lines or /* */ for blocks

---

## 4. Basic Data Cleaning Operations

### Choosing and Removing Columns

**Definition**
Column selection involves keeping only necessary columns and removing unwanted ones to optimize performance and simplify the data model.

**Use Cases**
- Remove personal identifiable information (PII)
- Eliminate empty or irrelevant columns
- Reduce data model size for better performance
- Focus analysis on specific business metrics
- Clean imported data with unnecessary formatting columns

**Examples (Before & After)**

| Scenario | Before Columns | After Columns | Reason |
|----------|----------------|---------------|--------|
| Customer Data | CustomerID, FirstName, LastName, SSN, Phone, Email | CustomerID, FirstName, LastName, Phone, Email | Remove PII (SSN) |
| Sales Data | OrderID, Date, Product, Price, Tax, Column1, Column2 | OrderID, Date, Product, Price, Tax | Remove empty Excel columns |
| Employee Data | EmpID, Name, Dept, Salary, Notes, TempCol | EmpID, Name, Dept, Salary | Remove unnecessary columns |

**Step-by-step in Power BI/Power Query**
1. In Power Query Editor, select columns to remove
2. Right-click → "Remove Columns" OR
3. Use Home tab → "Remove Columns" → "Remove Columns"
4. Alternatively: Select columns to keep → "Remove Other Columns"
5. Use "Choose Columns" for selective column picker
6. Verify remaining columns meet requirements

**Cautions/Pitfalls**
- Don't remove columns that might be needed later in transformations
- Consider downstream report requirements
- Removing columns early can improve query performance
- Can't undo after closing Power Query Editor
- Some columns may be needed for relationships

**Pro Tips**
- Use "Choose Columns" instead of manually removing many columns - it's faster and clearer
- Remove columns as early as possible to improve query folding and performance
- Create a "column map" document for complex datasets to track which source columns map to which business concepts
- Use Shift+Click to select ranges of columns for bulk removal
- Check column quality and distribution before removing - sometimes "empty" columns have hidden data
- Keep audit columns (like created_date, modified_by) even if not used in reports - they're helpful for troubleshooting

---

### Filtering Rows

**Definition**
Row filtering removes unwanted records from the dataset based on specific criteria, keeping only data relevant to analysis requirements.

**Use Cases**
- Remove test records or invalid transactions
- Filter by date ranges (current year, last quarter)
- Exclude cancelled or returned orders
- Keep only active customers or employees
- Remove rows with missing critical information

**Examples (Before & After)**

| Filter Criteria | Before (Rows) | After (Rows) | Business Logic |
|------------------|---------------|--------------|----------------|
| OrderStatus ≠ "Cancelled" | 1000 orders (including 50 cancelled) | 950 valid orders | Exclude cancelled transactions |
| OrderDate >= 2024-01-01 | 5000 historical orders | 800 current year orders | Focus on current year analysis |
| CustomerType = "Premium" | 10000 all customers | 500 premium customers | Premium customer analysis |
| Quantity > 0 | 1200 orders (including returns) | 1150 positive quantities | Exclude return transactions |

**Step-by-step in Power BI/Power Query**
1. Click dropdown arrow on column header
2. Uncheck items to exclude OR use search box
3. For advanced filters: Click "Text Filters" or "Number Filters"
4. Select condition (equals, contains, greater than, etc.)
5. Enter filter values
6. Apply multiple filters by repeating on different columns
7. Use "Remove Rows" → "Remove Top/Bottom Rows" for position-based filtering

**Cautions/Pitfalls**
- Filtering reduces data for analysis - ensure it's intentional
- Date filters may exclude recent data if not set properly
- Text filters are case-sensitive by default
- Multiple filters create AND conditions
- Consider impact on aggregations and calculations

**Pro Tips**
- Apply filters as early as possible in your transformation sequence for better performance
- Use parameters for dynamic filters (like date ranges) that change regularly
- Document filter logic in step names or comments for business user understanding
- Use "Keep Rows" operations for positive logic instead of "Remove Rows" when it makes business sense clearer
- Check for null values before filtering - they might disappear unexpectedly
- Use wildcard characters (* and ?) in text filters for pattern matching
- Combine multiple filter conditions in a single step using "Advanced Editor" for complex AND/OR logic

---

### Removing Duplicates

**Definition**
Duplicate removal identifies and eliminates rows with identical values across selected columns, ensuring data uniqueness.

**Use Cases**
- Clean customer lists with multiple entries
- Remove duplicate product records
- Eliminate repeated transaction entries
- Clean email lists for marketing campaigns
- Ensure unique employee records

**Examples (Before & After)**

| Scenario | Before Data | After Data | Duplicate Criteria |
|----------|-------------|------------|-------------------|
| Customer List | John Smith, john.smith@email.com<br>John Smith, john.smith@email.com<br>Jane Doe, jane@email.com | John Smith, john.smith@email.com<br>Jane Doe, jane@email.com | Full row match |
| Product Catalog | ProductID: 101, Name: Widget<br>ProductID: 101, Name: Widget<br>ProductID: 102, Name: Gadget | ProductID: 101, Name: Widget<br>ProductID: 102, Name: Gadget | ProductID column |
| Sales Transactions | Order: 1001, Date: 2024-01-15, Amount: $100<br>Order: 1001, Date: 2024-01-15, Amount: $100 | Order: 1001, Date: 2024-01-15, Amount: $100 | Order number basis |

**Step-by-step in Power BI/Power Query**
1. Select columns that define uniqueness (or select all for full row duplicates)
2. Go to Home tab → "Remove Rows" → "Remove Duplicates"
3. Power Query keeps the first occurrence of each duplicate set
4. For column-specific duplicates:
   - Select specific columns first
   - Then apply Remove Duplicates
5. Review results to ensure correct records were kept

**Cautions/Pitfalls**
- Always verify which record is kept when duplicates are removed
- Consider if all columns should be used for duplicate detection
- Duplicates based on key columns might have different detail information
- May need to aggregate data instead of removing duplicates
- Test with sample data to understand behavior

**Pro Tips**
- Use "Group By" with "All Rows" aggregation to see duplicate groups before removing them
- Add an index column before duplicate removal to track which occurrence was kept
- Consider using "Keep Last" logic by sorting before duplicate removal
- Create a separate query to analyze duplicates before removing them
- For complex duplicate rules, use custom M code with Table.Distinct and custom comparison logic
- Document your duplicate removal strategy - business users often ask "which record was kept?"

---

### Handling Missing Values (Nulls)

**Definition**
Managing null values involves identifying, replacing, or removing empty cells to ensure data quality and prevent analysis errors.

**Use Cases**
- Replace null sales amounts with zero
- Remove rows with missing critical identifiers
- Fill missing dates with default values
- Handle incomplete survey responses
- Clean imported data with empty cells

**Examples (Before & After)**

| Strategy | Before | After | Use Case |
|----------|--------|-------|----------|
| Replace with Value | CustomerName: [null]<br>OrderAmount: [null] | CustomerName: "Unknown"<br>OrderAmount: 0 | Default values for analysis |
| Remove Rows | CustomerID: 123<br>CustomerID: [null]<br>CustomerID: 456 | CustomerID: 123<br>CustomerID: 456 | Critical field cannot be null |
| Fill Down | Date: 2024-01-01<br>Date: [null]<br>Date: [null]<br>Date: 2024-01-02 | Date: 2024-01-01<br>Date: 2024-01-01<br>Date: 2024-01-01<br>Date: 2024-01-02 | Grouped data with repeated values |

**Step-by-step in Power BI/Power Query**
1. **Replace Values Method:**
   - Select column with nulls
   - Home tab → "Replace Values"
   - Leave "Value to Find" empty (for nulls)
   - Enter replacement value
   
2. **Remove Rows Method:**
   - Select column
   - Click dropdown → uncheck "(null)"
   
3. **Fill Down Method:**
   - Select column
   - Transform tab → "Fill" → "Down"

**Cautions/Pitfalls**
- Consider business meaning before replacing nulls
- Zero and null have different meanings in calculations
- Fill Down only works if there's a pattern
- Removing null rows may eliminate important records
- Document null handling decisions for stakeholders

**Pro Tips**
- Use Column Quality feature to quickly see null percentages across all columns
- Create a "null handling strategy" document for your organization's standards
- Consider using conditional columns for complex null replacement logic
- Use Table.ReplaceErrorValues for handling errors vs. nulls differently
- Test null handling logic with edge cases (all nulls, mixed nulls and blanks)
- Use "Keep/Remove Empty Rows" for better performance when dealing with entirely empty rows
- Replace nulls with meaningful business values, not just technical defaults

---

### Changing Data Types

**Definition**
Data type conversion ensures columns have appropriate data types (text, number, date, boolean) for calculations, filtering, and visualization.

**Use Cases**
- Convert text numbers to numeric for calculations
- Change text dates to proper date format
- Convert boolean text ("True"/"False") to boolean type
- Fix imported data with incorrect auto-detected types
- Ensure proper sorting and filtering behavior

**Examples (Before & After)**

| Column | Before Type | Before Value | After Type | After Value | Reason |
|--------|-------------|--------------|------------|-------------|--------|
| OrderDate | Text | "2024-01-15" | Date | 1/15/2024 | Enable date calculations |
| SalesAmount | Text | "$1,234.56" | Number | 1234.56 | Enable mathematical operations |
| IsActive | Text | "True" | Boolean | True | Proper true/false logic |
| ProductCode | Number | 12345 | Text | "12345" | Prevent mathematical operations |
| CustomerAge | Text | "25" | Number | 25 | Enable age-based calculations |

**Step-by-step in Power BI/Power Query**
1. **Using Column Header:**
   - Click data type icon next to column name
   - Select appropriate data type from dropdown
   
2. **Using Transform Tab:**
   - Select column(s)
   - Transform tab → "Data Type" → Choose type
   
3. **Using Detect Data Type:**
   - Transform tab → "Detect Data Type" (auto-detect all columns)
   
4. **Handle Conversion Errors:**
   - Right-click column → "Change Type" → "Using Locale" for regional formats

**Cautions/Pitfalls**
- Data type changes may cause conversion errors
- Regional settings affect date and number parsing
- Some text values may not convert properly
- Always validate data after type changes
- Currency symbols need removal before converting to numbers
- Consider locale-specific formatting (US vs European dates)

**Pro Tips**
- Always use "Using Locale" for date and number conversions when dealing with international data
- Set data types as early as possible to catch conversion errors quickly
- Use "Detect Data Type" cautiously - review all changes before applying
- Create custom functions for common data type conversions with error handling
- Use Column Profile to identify problematic values before type conversion
- Keep a data type reference chart for your organization's standards
- Use try...otherwise in custom columns to handle conversion errors gracefully

---

### Sorting Data

**Definition**
Sorting arranges data rows in ascending or descending order based on one or more columns, improving data readability and analysis.

**Use Cases**
- Arrange sales data by date for trend analysis
- Sort customers alphabetically for easier lookup
- Order products by price for comparison
- Arrange employees by hire date
- Sort survey responses by rating

**Examples (Before & After)**

| Sort Criteria | Before (Sample Rows) | After (Sample Rows) | Purpose |
|---------------|---------------------|-------------------|---------|
| OrderDate (Ascending) | 2024-03-15, 2024-01-10, 2024-02-20 | 2024-01-10, 2024-02-20, 2024-03-15 | Chronological analysis |
| SalesAmount (Descending) | $100, $500, $250 | $500, $250, $100 | Identify top performers |
| CustomerName (Ascending) | Smith, Adams, Jones | Adams, Jones, Smith | Alphabetical lookup |
| Multiple: Region (Asc), Sales (Desc) | East-$100, West-$300, East-$200 | East-$200, East-$100, West-$300 | Regional performance analysis |

**Step-by-step in Power BI/Power Query**
1. **Single Column Sort:**
   - Click column header dropdown
   - Choose "Sort Ascending" or "Sort Descending"
   
2. **Multiple Column Sort:**
   - Home tab → "Sort Rows"
   - Add columns and specify sort direction
   - Order of columns determines sort priority
   
3. **Remove Sort:**
   - Home tab → "Sort Rows" → Remove sort criteria

**Cautions/Pitfalls**
- Sorting doesn't persist in final data model (sort in visuals instead)
- Multiple column sorts: first column has highest priority
- Text sorting is case-sensitive
- Date columns must be proper date type for chronological sorting
- Null values typically appear first or last depending on sort direction
- Consider performance impact on large datasets

**Pro Tips**
- Remember that sorting in Power Query doesn't affect the final report - sort in visuals instead
- Use sorting during development to quickly identify data quality issues (outliers appear at top/bottom)
- Sort before removing duplicates to control which record is kept
- Use multiple column sorting to create logical groupings for easier data review
- Apply temporary sorting to validate transformations, then remove to improve performance
- Sort by unique identifier columns to create reproducible query results for testing

---

## 5. Data Shaping Techniques

### Splitting Columns

**Definition**
Column splitting divides a single column into multiple columns based on delimiters, character positions, or patterns to separate combined data elements.

**Use Cases**
- Separate first and last names from full name column
- Split address into street, city, state components
- Extract area code from phone numbers
- Separate product code components
- Parse email addresses into username and domain

**Examples (Before & After)**

| Split Type | Before Column | After Columns | Delimiter/Rule |
|------------|---------------|---------------|----------------|
| By Delimiter | "John Smith" | "John" \| "Smith" | Space character |
| By Position | "ABC12345" | "ABC" \| "12345" | After 3 characters |
| By Pattern | "john.doe@email.com" | "john.doe" \| "email.com" | @ symbol |
| By Rows | "Product A\nProduct B" | Row 1: "Product A"<br>Row 2: "Product B" | Line break |

**Step-by-step in Power BI/Power Query**
1. **Split by Delimiter:**
   - Select column to split
   - Transform tab → "Split Column" → "By Delimiter"
   - Choose or enter delimiter
   - Select split options (each occurrence, leftmost, rightmost)
   - Choose number of columns to create

2. **Split by Character Position:**
   - Transform tab → "Split Column" → "By Number of Characters"
   - Enter character positions
   - Choose split direction (left-to-right or right-to-left)

3. **Split by Pattern:**
   - Transform tab → "Split Column" → "By Delimiter"
   - Use Advanced options for regex patterns

**Cautions/Pitfalls**
- Delimiter might appear in data unexpectedly
- Consider what happens with missing delimiters
- Extra delimiters create additional columns
- Data may not split evenly across all rows
- Review all rows after splitting, not just preview
- Splitting reduces original column (backup if needed)

**Pro Tips**
- Test split operations on a small sample before applying to full dataset
- Use "Advanced Options" to control how many splits occur (split at first occurrence vs. all occurrences)
- Consider using "Extract" functions for specific patterns instead of splitting everything
- Use regex patterns in advanced splitting for complex scenarios
- Add error handling for inconsistent data formats
- Create a backup query before major splitting operations
- Use "Split Column by Positions" for fixed-width data formats

---

### Merging Columns

**Definition**
Column merging combines multiple columns into a single column using specified separators or custom formats to create consolidated data fields.

**Use Cases**
- Combine first and last names into full name
- Create complete addresses from separate components
- Merge date and time columns
- Concatenate product details into descriptions
- Build unique identifiers from multiple fields

**Examples (Before & After)**

| Merge Purpose | Before Columns | After Column | Separator |
|---------------|----------------|--------------|-----------|
| Full Name | "John" \| "Smith" | "John Smith" | Space |
| Address | "123 Main St" \| "City" \| "CA" | "123 Main St, City, CA" | Comma-space |
| Product Code | "CAT" \| "001" \| "A" | "CAT-001-A" | Hyphen |
| Date-Time | "2024-01-15" \| "14:30:00" | "2024-01-15 14:30:00" | Space |

**Step-by-step in Power BI/Power Query**
1. **Basic Merge:**
   - Select multiple columns (Ctrl+click)
   - Transform tab → "Merge Columns"
   - Choose separator (Tab, Comma, Space, Custom)
   - Enter new column name
   - Original columns are removed

2. **Advanced Merge:**
   - Add Column tab → "Custom Column"
   - Use formula: `[Column1] & " " & [Column2]`
   - Keeps original columns

**Cautions/Pitfalls**
- Original columns are deleted in basic merge
- Null values may cause unexpected results
- Choose separators that don't appear in data
- Consider formatting needs (spaces, punctuation)
- Test with various data combinations
- Empty fields may create double separators

**Pro Tips**
- Use custom columns instead of merge columns when you need to keep original columns
- Handle null values explicitly in merge formulas: `if [Col1] = null then "" else [Col1]`
- Consider using Text.Combine() function for more control over null handling
- Test merged results with edge cases (null values, empty strings, special characters)
- Use consistent separators across your organization for similar data patterns
- Create custom functions for frequently used merge patterns
- Validate merged data by spot-checking against source columns

---

### Transposing Data

**Definition**
Transposing swaps rows and columns, turning horizontal data into vertical format or vice versa, useful for restructuring summary data or cross-tabulated information.

**Use Cases**
- Convert monthly sales columns to rows for time-series analysis
- Restructure survey responses from wide to long format
- Transform pivot table outputs for further analysis
- Convert configuration settings from horizontal to vertical
- Reshape financial statements for consistent reporting

**Examples (Before & After)**

| Before (Wide Format) | | | |
|---------------------|---|---|---|
| Product | Q1 | Q2 | Q3 |
| Widget | 100 | 120 | 140 |
| Gadget | 80 | 90 | 95 |

| After (Transposed) | |
|--------------------|---|
| Attribute | Value |
| Product | Widget |
| Q1 | 100 |
| Q2 | 120 |
| Q3 | 140 |
| Product | Gadget |
| Q1 | 80 |
| Q2 | 90 |
| Q3 | 95 |

**Step-by-step in Power BI/Power Query**
1. Select the query to transpose
2. Transform tab → "Transpose"
3. Data rows become columns and columns become rows
4. First row often needs to be promoted to headers
5. Consider using "Use First Row as Headers" if appropriate

**Cautions/Pitfalls**
- All data must be compatible types after transposing
- Column headers become data values
- May need additional unpivoting after transpose
- Large datasets may have performance issues
- Review data types after transposing
- Consider if unpivoting is better alternative

**Pro Tips**
- Transpose is often followed by "Use First Row as Headers" operation
- Consider whether unpivoting might be more appropriate for your scenario
- Test transpose operations with small datasets first due to performance impact
- Document the business reason for transposing in your query steps
- Consider creating a backup query before transposing
- Use transpose for converting configuration tables to more analysis-friendly formats
- Combine transpose with other operations like filtering to handle specific data layouts

---

### Pivoting Columns

**Definition**
Pivoting transforms unique values from a column into multiple columns, creating a cross-tabulated structure similar to Excel pivot tables.

**Use Cases**
- Convert transaction data to monthly columns
- Transform survey responses to question columns
- Create product sales matrix by region
- Convert time-series data to period columns
- Build comparison tables from normalized data

**Examples (Before & After)**

| Before (Long Format) | | |
|---------------------|---|---|
| Region | Month | Sales |
| East | Jan | 100 |
| East | Feb | 120 |
| West | Jan | 80 |
| West | Feb | 90 |

| After (Pivoted on Month) | | |
|-------------------------|---|---|
| Region | Jan | Feb |
| East | 100 | 120 |
| West | 80 | 90 |

**Step-by-step in Power BI/Power Query**
1. Select the column containing values to become new column headers
2. Transform tab → "Pivot Column"
3. Choose the values column (data to populate the matrix)
4. Select aggregation function if needed (Sum, Count, Average, etc.)
5. Review resulting cross-tabulated structure

**Cautions/Pitfalls**
- Creates columns for every unique value (can be many)
- May create sparse data with many nulls
- Aggregation function affects final values
- Column names come from data values
- Performance impact with high cardinality
- Consider filtering unique values first

**Pro Tips**
- Filter your pivot column values before pivoting to control the number of columns created
- Use "Don't Aggregate" option when you know there's only one value per combination
- Consider using parameters to dynamically filter pivot values
- Pivot operations work well with Group By operations as a preliminary step
- Test with a subset of data first to see how many columns will be created
- Document aggregation function choice for business user understanding
- Consider unpivoting as the inverse operation if you need to reverse the transformation

---

### Unpivoting Columns

**Definition**
Unpivoting transforms multiple columns into key-value pairs, converting wide format data into long format suitable for analysis and visualization.

**Use Cases**
- Convert monthly sales columns into time-series format
- Transform wide survey data into analyzable format
- Normalize cross-tabulated financial data
- Convert Excel pivot table outputs for Power BI
- Prepare data for trend analysis and forecasting

**Examples (Before & After)**

| Before (Wide Format) | | | |
|---------------------|---|---|---|
| Product | Jan | Feb | Mar |
| Widget | 100 | 120 | 140 |
| Gadget | 80 | 90 | 95 |

| After (Unpivoted) | | |
|------------------|---|---|
| Product | Attribute | Value |
| Widget | Jan | 100 |
| Widget | Feb | 120 |
| Widget | Mar | 140 |
| Gadget | Jan | 80 |
| Gadget | Feb | 90 |
| Gadget | Mar | 95 |

**Step-by-step in Power BI/Power Query**
1. **Unpivot Selected Columns:**
   - Select columns to unpivot (Ctrl+click)
   - Transform tab → "Unpivot Columns"

2. **Unpivot Other Columns:**
   - Select columns to keep as-is
   - Transform tab → "Unpivot Other Columns"

3. **Rename Generated Columns:**
   - Rename "Attribute" to meaningful name (e.g., "Month")
   - Rename "Value" to appropriate name (e.g., "Sales")

**Cautions/Pitfalls**
- Choose correct columns to unpivot vs. keep
- Generated column names may need customization
- Data types should be consistent across unpivoted columns
- May significantly increase row count
- Consider performance with large datasets
- Null values in original columns become null rows

**Pro Tips**
- Use "Unpivot Other Columns" when you have many columns to unpivot - it's more maintainable
- Always rename the "Attribute" and "Value" columns to business-meaningful names
- Filter out null values after unpivoting to reduce row count
- Use unpivoting to prepare data for time-series analysis in Power BI
- Consider data types of unpivoted columns - they should be compatible
- Document the business logic behind column selection for unpivoting
- Combine unpivoting with date parsing when column names contain date information

---

### Filling Up/Down

**Definition**
Fill operations populate empty cells by copying values from adjacent cells either upward or downward, useful for handling grouped or hierarchical data structures.

**Use Cases**
- Fill missing group headers in imported reports
- Complete hierarchical data with missing parent values
- Handle Excel data with merged cells (imported as nulls)
- Fill category names in product listings
- Complete time-series data with missing intervals

**Examples (Before & After)**

| Before (Fill Down) | After |
|-------------------|-------|
| Category: Electronics | Category: Electronics |
| Category: [null] | Category: Electronics |
| Category: [null] | Category: Electronics |
| Category: Clothing | Category: Clothing |
| Category: [null] | Category: Clothing |

| Before (Fill Up) | After |
|-----------------|-------|
| Product: [null] | Product: Widget |
| Product: [null] | Product: Widget |
| Product: Widget | Product: Widget |
| Product: [null] | Product: Gadget |
| Product: Gadget | Product: Gadget |

**Step-by-step in Power BI/Power Query**
1. Select column with missing values to fill
2. Transform tab → "Fill" → choose direction:
   - "Down": Copy values downward to fill nulls below
   - "Up": Copy values upward to fill nulls above

**Cautions/Pitfalls**
- Only fills null/empty values, doesn't overwrite existing data
- Direction matters - choose based on data structure
- May propagate incorrect values if source data is wrong
- Consider business logic before filling
- Works best with grouped or hierarchical data
- Verify results with sample of filled data

**Pro Tips**
- Sort data appropriately before filling to ensure logical groupings
- Use fill operations carefully with time-series data - verify the business logic
- Combine with grouping operations to fill within logical boundaries
- Consider using conditional columns for more complex fill logic
- Test fill operations on a sample to understand the pattern before applying to full dataset
- Document the business justification for filling missing values
- Use in combination with other cleaning operations for best results

---

## 6. Adding New Columns

### Adding Custom Columns (using M language)

**Definition**
Custom columns allow creation of new calculated columns using M language expressions, providing flexibility for complex transformations and business logic implementation.

**Use Cases**
- Calculate profit margins from cost and price
- Create age categories from birth dates
- Build compound identifiers from multiple fields
- Implement complex business rules
- Generate flags based on multiple conditions

**Examples (Before & After)**

| Business Logic | Source Columns | Custom Column | M Expression |
|----------------|----------------|---------------|--------------|
| Profit Margin | Price: $100, Cost: $70 | Margin: 30% | `([Price] - [Cost]) / [Price]` |
| Full Name | FirstName: "John", LastName: "Smith" | FullName: "John Smith" | `[FirstName] & " " & [LastName]` |
| Age Category | Age: 35 | AgeGroup: "Adult" | `if [Age] < 18 then "Minor" else "Adult"` |
| Days Since Order | OrderDate: 1/15/2024 | DaysSince: 45 | `Duration.Days(DateTime.LocalNow() - [OrderDate])` |

**Step-by-step in Power BI/Power Query**
1. Add Column tab → "Custom Column"
2. Enter new column name
3. Write M expression in formula box
4. Use "Available columns" list for reference
5. Validate syntax (green checkmark appears)
6. Click OK to create column
7. Verify results and data type

**Cautions/Pitfalls**
- M language is case-sensitive
- Handle null values in expressions (use `try...otherwise`)
- Test expressions with various data scenarios
- Consider performance impact of complex calculations
- Data types may need adjustment after creation
- Use parentheses for proper operation order

**Pro Tips**
- Use the "Available columns" list to avoid typing errors in column names
- Start with simple expressions and build complexity gradually
- Use let...in statements for complex calculations to improve readability
- Test custom columns with edge cases including nulls and extreme values
- Create a library of common M expressions for reuse across projects
- Use meaningful column names that reflect business purpose, not technical implementation
- Comment complex M code using /* */ for future maintenance

---

### Adding Conditional Columns

**Definition**
Conditional columns create new columns based on if-then-else logic using a user-friendly interface, eliminating the need to write M language code for simple conditions.

**Use Cases**
- Categorize customers by sales volume (High, Medium, Low)
- Create performance ratings based on scores
- Generate region codes based on state/country
- Set priority levels based on urgency and impact
- Flag records based on business criteria

**Examples (Before & After)**

| Condition Logic | Source Column | Condition | Result | New Column Values |
|-----------------|---------------|-----------|--------|-------------------|
| Sales Performance | SalesAmount | ≥ $10,000 = "High"<br>≥ $5,000 = "Medium"<br>Else = "Low" | Performance: "High", "Medium", "Low" |
| Age Classification | Age | < 18 = "Minor"<br>< 65 = "Adult"<br>Else = "Senior" | AgeCategory: "Minor", "Adult", "Senior" |
| Region Assignment | State | "CA", "OR", "WA" = "West"<br>"NY", "NJ", "CT" = "East"<br>Else = "Other" | Region: "West", "East", "Other" |

**Step-by-step in Power BI/Power Query**
1. Add Column tab → "Conditional Column"
2. Enter new column name
3. Set up conditions:
   - Choose column to evaluate
   - Select operator (equals, greater than, etc.)
   - Enter comparison value
   - Enter result value
4. Add multiple conditions using "Add Clause"
5. Set "Else" value for unmatched conditions
6. Click OK to create column

**Cautions/Pitfalls**
- Conditions are evaluated in order (top to bottom)
- Make sure conditions don't overlap unexpectedly
- "Else" clause catches all remaining cases
- Use appropriate data types for comparisons
- Consider null values in source columns
- Test with edge cases and boundary values

**Pro Tips**
- Order conditions from most specific to most general (narrow to broad)
- Use the "Else" clause to handle unexpected values gracefully
- Test conditional logic with boundary values to ensure correct classification
- Document the business rules behind conditions for future reference
- Consider using parameters for threshold values that might change
- Use meaningful output values that business users will understand
- Validate conditional results by creating temporary grouping analysis

---

### Adding Index Columns

**Definition**
Index columns add sequential numbers to each row, providing unique row identifiers or maintaining original row order for later reference.

**Use Cases**
- Create unique row identifiers for duplicate data
- Maintain original sort order before other transformations
- Generate sequence numbers for reporting
- Create primary keys for tables without them
- Track row positions for audit purposes

**Examples (Before & After)**

| Index Type | Before | After | Use Case |
|------------|--------|--------|----------|
| From 1 | Name: "John"<br>Name: "Jane" | Index: 1, Name: "John"<br>Index: 2, Name: "Jane" | Standard numbering |
| From 0 | Product: "A"<br>Product: "B" | Index: 0, Product: "A"<br>Index: 1, Product: "B" | Programming-style indexing |
| Custom Start | Order: "ABC"<br>Order: "DEF" | ID: 1001, Order: "ABC"<br>ID: 1002, Order: "DEF" | Custom numbering scheme |

**Step-by-step in Power BI/Power Query**
1. Add Column tab → "Index Column"
2. Choose index type:
   - "From 1": Standard numbering starting at 1
   - "From 0": Programming-style starting at 0
   - "Custom": Specify starting number and increment
3. For custom index:
   - Enter starting value
   - Enter increment value (usually 1)
4. New index column appears as first column
5. Rename column if needed

**Cautions/Pitfalls**
- Index reflects current row order, not original data order
- Filtering or sorting changes index values
- Index column is typically integer data type
- May want to move index column to desired position
- Consider if index should be preserved through transformations
- Large datasets may have performance considerations

**Pro Tips**
- Add index columns early in transformations to preserve original row order
- Use custom starting values to align with existing numbering systems
- Consider whether you need the index in the final data model or just for processing
- Use index columns to create unique identifiers when natural keys don't exist
- Remember that index values will change if rows are filtered or reordered
- Use descriptive names for index columns (OriginalRowOrder, SequenceNumber, etc.)
- Consider removing index columns before final load if not needed for analysis

---

### Adding Column From Examples

**Definition**
Column from Examples uses AI to detect patterns from sample outputs you provide, automatically generating the necessary transformation logic to create the new column.

**Use Cases**
- Extract patterns from complex text fields
- Parse inconsistent date formats
- Create formatted output from multiple fields
- Generate codes or abbreviations
- Transform data without writing formulas

**Examples (Before & After)**

| Pattern to Learn | Source Data | Example Input | Generated Column | AI-Detected Pattern |
|------------------|-------------|---------------|------------------|-------------------|
| Extract First Name | "John Smith" | Type "John" | FirstName: "John" | Extract text before space |
| Format Phone | "1234567890" | Type "(123) 456-7890" | FormattedPhone | Phone number formatting |
| Create Initials | "John Smith" | Type "JS" | Initials: "JS" | First letter of each word |
| Extract Domain | "user@domain.com" | Type "domain.com" | Domain: "domain.com" | Extract text after @ |

**Step-by-step in Power BI/Power Query**
1. Add Column tab → "Column from Examples"
2. Choose source columns (or use "From All Columns")
3. In the new column, type examples of desired output
4. Provide 2-3 examples showing the pattern
5. Power Query detects pattern and fills remaining rows
6. Review generated results
7. Click OK if pattern is correct
8. Edit examples and retry if pattern is wrong

**Cautions/Pitfalls**
- AI may not detect complex patterns correctly
- Provide diverse examples to improve accuracy
- Review all generated results, not just visible ones
- May need manual M code editing for edge cases
- Works best with consistent, logical patterns
- Some patterns may require traditional custom columns instead

**Pro Tips**
- Provide examples that represent edge cases in your data
- Use diverse examples to help the AI understand the full pattern
- Start with 2-3 examples, then add more if the pattern isn't detected correctly
- Review the generated M code to understand what logic was created
- Test with a larger sample of data before applying to the full dataset
- Save successful patterns as custom functions for reuse
- Combine with other column operations when the AI gets close but not perfect

---

## 7. Specific Transformation Types

### Text Transformations

**Definition**
Text transformations modify string data through various operations including cleaning, formatting, case changes, and extraction to standardize and improve data quality.

**Use Cases**
- Standardize customer names and addresses
- Clean imported data with extra spaces
- Extract specific information from text fields
- Format text for consistent presentation
- Parse unstructured text data

**Examples (Before & After)**

| Transformation | Before | After | Purpose |
|----------------|--------|-------|---------|
| Trim | "  John Smith  " | "John Smith" | Remove extra spaces |
| Upper Case | "john smith" | "JOHN SMITH" | Consistent formatting |
| Title Case | "JOHN SMITH" | "John Smith" | Proper name formatting |
| Clean | "John\tSmith\n" | "John Smith" | Remove non-printable characters |
| Extract Length | "Hello World" | 11 | Count characters |
| Extract First Characters | "Hello World" | "Hello" (first 5) | Get substring |

**Step-by-step in Power BI/Power Query**
1. **Basic Text Operations:**
   - Select text column
   - Transform tab → "Format" → Choose operation:
     - "Trim": Remove leading/trailing spaces
     - "Clean": Remove non-printable characters
     - "Lower Case/Upper Case/Title Case/Capitalize Each Word"

2. **Extract Operations:**
   - Transform tab → "Extract" → Choose:
     - "Length": Character count
     - "First Characters": Substring from beginning
     - "Last Characters": Substring from end
     - "Range": Substring from position
     - "Text Before/After Delimiter"

3. **Parse Operations:**
   - Transform tab → "Parse" → Choose format:
     - "JSON", "XML" for structured text

**Cautions/Pitfalls**
- Trim only removes spaces, not other whitespace characters
- Case changes are permanent - backup original if needed
- Extract operations create new columns, original remains
- Consider locale-specific text rules (accented characters)
- Some operations may not work with null values
- Test with international characters and special symbols

**Pro Tips**
- Use "Clean" transformation to remove non-printable characters from imported data
- Apply text transformations early in the process for consistent results
- Use "Text Before Delimiter" and "Text After Delimiter" for precise extractions
- Consider creating custom functions for complex text parsing patterns
- Test text operations with international characters and special symbols
- Use Text.Combine() function for joining text with null handling
- Document text transformation rules for business user understanding

---

### Number Transformations

**Definition**
Number transformations perform mathematical operations, formatting changes, and data type conversions on numerical data to support calculations and analysis requirements.

**Use Cases**
- Round financial amounts to appropriate precision
- Convert between different number formats
- Perform mathematical calculations
- Standardize scientific notation
- Apply statistical functions

**Examples (Before & After)**

| Transformation | Before | After | Business Context |
|----------------|--------|-------|------------------|
| Round | 123.456789 | 123.46 | Currency formatting (2 decimals) |
| Absolute Value | -150 | 150 | Distance or magnitude calculations |
| Power | 2 | 8 (power of 3) | Compound growth calculations |
| Square Root | 25 | 5 | Standard deviation calculations |
| Modulo | 17 | 2 (mod 5) | Grouping or cycling calculations |
| Scientific | 0.000123 | 1.23E-04 | Very large or small numbers |

**Step-by-step in Power BI/Power Query**
1. **Standard Operations:**
   - Select numeric column
   - Transform tab → "Number Column" → Choose:
     - "Rounding": Round, Round Up, Round Down
     - "Information": Sign, Absolute Value
     - "Operations": Add, Subtract, Multiply, Divide, Power, Modulo
     - "Trigonometry": Sine, Cosine, Tangent, etc.

2. **Scientific Operations:**
   - Transform tab → "Scientific" → Choose:
     - "Logarithm", "Factorial", "Square Root"

3. **Custom Calculations:**
   - Add Column tab → "Custom Column"
   - Use mathematical operators (+, -, *, /, ^)

**Cautions/Pitfalls**
- Division by zero creates errors - handle with try/otherwise
- Rounding can accumulate precision errors in calculations
- Consider significant digits for scientific calculations
- Some operations may change data type
- Null values in calculations typically result in null
- Verify precision requirements for financial calculations

**Pro Tips**
- Use Number.Round() with specified decimal places for financial data
- Handle division by zero with try...otherwise or conditional logic
- Consider using Number.IsNaN() to detect invalid number operations
- Apply number transformations after ensuring proper data types
- Use statistical functions (like percentiles) for data quality analysis
- Document rounding rules for financial compliance
- Test mathematical operations with edge cases (very large/small numbers, negatives)

---

### Date & Time Transformations

**Definition**
Date and time transformations extract components, calculate durations, and perform temporal operations to support time-based analysis and reporting.

**Use Cases**
- Extract year, month, day for grouping and filtering
- Calculate age from birth dates
- Determine business days between dates
- Extract time components for scheduling analysis
- Create fiscal year and quarter calculations

**Examples (Before & After)**

| Transformation | Source Date/Time | Result | Use Case |
|----------------|------------------|--------|----------|
| Extract Year | 2024-03-15 | 2024 | Annual reporting |
| Extract Month Name | 2024-03-15 | "March" | Monthly analysis |
| Extract Day of Week | 2024-03-15 | "Friday" | Weekday patterns |
| Age Calculation | DOB: 1985-06-15 | 39 (years) | Customer demographics |
| Duration | Start: 09:00, End: 17:30 | 8.5 (hours) | Work time calculation |
| Add Days | 2024-03-15 | 2024-03-22 (+7 days) | Due date calculation |

**Step-by-step in Power BI/Power Query**
1. **Extract Date Components:**
   - Select date column
   - Transform tab → "Date" → Choose:
     - "Year", "Month", "Day"
     - "Day of Week", "Day Name"
     - "Month Name", "Quarter"
     - "Week of Year"

2. **Extract Time Components:**
   - Transform tab → "Time" → Choose:
     - "Hour", "Minute", "Second"

3. **Calculate Duration:**
   - Transform tab → "Duration" → Choose:
     - "Days", "Hours", "Minutes", "Seconds"
     - "Total Years", "Total Days"

4. **Date Arithmetic:**
   - Add Column tab → "Custom Column"
   - Use functions like `Date.AddDays([Date], 30)`

**Cautions/Pitfalls**
- Ensure proper date data type before transformations
- Time zones can affect calculations
- Leap years impact date calculations
- Duration calculations may need business day logic
- Consider regional date formats
- Null dates will result in null calculations

**Pro Tips**
- Create fiscal year calculations using Date.Year() with conditional logic for fiscal calendar alignment
- Use Date.IsInCurrentWeek(), Date.IsInCurrentMonth() for dynamic date filtering
- Extract date parts early in transformations for better query folding
- Use Duration.Days() for calculating differences between dates
- Consider business day calculations for accurate turnaround time analysis
- Create date dimension tables for consistent date-based reporting
- Handle time zones explicitly when working with datetime data from multiple regions

---

## 8. Combining Queries

### Appending Queries

**Definition**
Appending queries combines rows from multiple queries with similar structures into a single query, essentially stacking datasets vertically.

**Use Cases**
- Combine monthly sales files into yearly dataset
- Merge data from multiple regional offices
- Consolidate historical data with current data
- Unite survey responses from different periods
- Aggregate product data from various sources

**Examples (Before & After)**

| Before - Query 1 (Q1 Sales) | Before - Query 2 (Q2 Sales) |
|------------------------------|------------------------------|
| Date: 2024-01-15, Product: Widget, Amount: $100 | Date: 2024-04-15, Product: Widget, Amount: $120 |
| Date: 2024-02-20, Product: Gadget, Amount: $200 | Date: 2024-05-20, Product: Gadget, Amount: $180 |

| After - Appended Query |
|------------------------|
| Date: 2024-01-15, Product: Widget, Amount: $100 |
| Date: 2024-02-20, Product: Gadget, Amount: $200 |
| Date: 2024-04-15, Product: Widget, Amount: $120 |
| Date: 2024-05-20, Product: Gadget, Amount: $180 |

**Step-by-step in Power BI/Power Query**
1. **Two Queries Method:**
   - In Queries pane, right-click first query
   - Select "Append Queries"
   - Choose second query to append
   - Select append option (create new query or append to first)

2. **Multiple Queries Method:**
   - Home tab → "Append Queries" → "Append Queries as New"
   - Select "Three or more tables"
   - Add queries to append list
   - Arrange order if needed

3. **Review Results:**
   - Check column alignment
   - Verify data types match
   - Handle any schema differences

**Cautions/Pitfalls**
- Column names must match exactly (case-sensitive)
- Data types should be compatible across queries
- Extra columns in one query will be null in others
- Order of rows in result follows order of queries
- Performance considerations with very large datasets
- Consider creating a function for recurring appends

**Pro Tips**
- Create a standardized column order and naming convention before appending
- Use "Append Queries as New" to preserve original queries
- Add a source identifier column before appending to track data origin
- Validate data types are consistent across all queries before appending
- Consider using folder connections for multiple files with identical structure
- Test append operations with sample data from each source first
- Document the business logic for combining different data sources

---

### Merging Queries (Join Operations)

**Definition**
Merging queries combines columns from multiple queries based on matching values in specified key columns, similar to SQL JOIN operations.

**Use Cases**
- Add customer details to sales transactions
- Enrich product sales with inventory information
- Combine employee records with department details
- Link survey responses with participant demographics
- Match transactions with account information

**Examples (Before & After)**

| Before - Sales Query | Before - Customer Query |
|---------------------|-------------------------|
| OrderID: 1001, CustomerID: C123, Amount: $100 | CustomerID: C123, Name: "John Smith", City: "New York" |
| OrderID: 1002, CustomerID: C456, Amount: $200 | CustomerID: C456, Name: "Jane Doe", City: "Chicago" |

| After - Merged Query (Inner Join) |
|-----------------------------------|
| OrderID: 1001, CustomerID: C123, Amount: $100, Name: "John Smith", City: "New York" |
| OrderID: 1002, CustomerID: C456, Amount: $200, Name: "Jane Doe", City: "Chicago" |

**Join Types Comparison:**

| Join Type | Description | Use Case | Result |
|-----------|-------------|----------|--------|
| Inner Join | Only matching records | Standard data enrichment | Records exist in both tables |
| Left Outer | All records from left table | Keep all primary records | All left + matching right |
| Right Outer | All records from right table | Rarely used in BI | All right + matching left |
| Full Outer | All records from both tables | Complete data comparison | All records, nulls where no match |
| Left Anti | Records only in left table | Find missing relationships | Left records without right match |
| Right Anti | Records only in right table | Find orphaned records | Right records without left match |

**Step-by-step in Power BI/Power Query**
1. **Basic Merge:**
   - Select primary query (left table)
   - Home tab → "Merge Queries"
   - Select second query (right table)
   - Choose key columns from both tables
   - Select join kind from dropdown
   - Click OK

2. **Expand Columns:**
   - Click expand icon in new column header
   - Select columns to include from merged table
   - Choose whether to use original column names as prefix

3. **Advanced Options:**
   - Use "Merge Queries as New" to create separate result
   - Enable "Fuzzy matching" for approximate matches
   - Set matching tolerance for fuzzy joins

**Cautions/Pitfalls**
- Key columns must have compatible data types
- Case sensitivity affects text matches
- Null values in keys may cause unexpected results
- One-to-many relationships multiply rows
- Performance impact with large datasets
- Consider indexing in source systems
- Fuzzy matching requires careful tolerance setting

**Pro Tips**
- Always validate key columns have compatible data types before merging
- Use "Merge Queries as New" to preserve original queries for troubleshooting
- Check for null values in key columns before merging
- Use fuzzy matching cautiously and with appropriate tolerance settings (80-90%)
- Consider data quality: clean and standardize key columns before merging
- Document join logic and expected cardinality for business users
- Test merges with a subset of data to verify expected results
- Use anti-joins to identify data quality issues (orphaned records)

---

## 9. Advanced Transformation Concepts

### Creating and Using Query Parameters

**Definition**
Query parameters are dynamic values that can be referenced throughout queries, enabling flexible and reusable transformations that adapt to changing requirements without code modification.

**Use Cases**
- Create date range filters that can be easily updated
- Build reusable file path references
- Set threshold values for business rules
- Configure database connection strings
- Enable dynamic filtering based on user input

**Examples (Before & After)**

| Parameter Use | Static Approach | Parameter Approach | Benefit |
|---------------|-----------------|-------------------|---------|
| Date Filter | `Table.SelectRows(Source, each [OrderDate] >= #date(2024,1,1))` | `Table.SelectRows(Source, each [OrderDate] >= StartDate)` | Easy date updates |
| File Path | `Excel.Workbook(File.Contents("C:\Data\Sales.xlsx"))` | `Excel.Workbook(File.Contents(FilePath))` | Environment flexibility |
| Sales Threshold | `Table.SelectRows(Source, each [Amount] >= 1000)` | `Table.SelectRows(Source, each [Amount] >= MinSales)` | Business rule changes |

**Step-by-step in Power BI/Power Query**
1. **Create Parameter:**
   - Home tab → "Manage Parameters" → "New Parameter"
   - Enter parameter name (e.g., "StartDate")
   - Select data type (Text, Number, Date, etc.)
   - Set current value
   - Add description for documentation

2. **Use Parameter in Query:**
   - In formula bar, reference parameter name
   - Example: `Table.SelectRows(Source, each [Date] >= StartDate)`
   - Parameter appears in "Queries" pane under "Parameters" section

3. **Update Parameter Values:**
   - Right-click parameter in Queries pane
   - Select "Edit Parameter"
   - Update current value
   - Refresh queries to apply changes

**Cautions/Pitfalls**
- Parameter data types must match usage context
- Parameters are query-scoped, not global across all queries
- Changes to parameters affect all referencing queries
- Consider default values for reliability
- Document parameter purpose and expected values
- Test parameter changes with various values

**Pro Tips**
- Use parameters for values that change regularly (date ranges, thresholds, file paths)
- Create parameter documentation with expected value ranges and business context
- Use meaningful parameter names that reflect business purpose
- Set up parameter validation in queries using conditional logic
- Consider using parameter lists for dropdown-style selections
- Group related parameters with consistent naming conventions
- Test parameter changes across all dependent queries before deploying

---

### Creating and Invoking Custom Functions

**Definition**
Custom functions are reusable M language code blocks that accept parameters and return processed results, enabling standardized transformations across multiple queries.

**Use Cases**
- Standardize data cleaning routines across multiple files
- Create complex business calculations for reuse
- Build custom data parsing logic
- Implement organization-specific data transformations
- Centralize common formatting operations

**Examples (Before & After)**

| Function Purpose | Repetitive Code | Custom Function | Usage |
|------------------|-----------------|-----------------|-------|
| Clean Name | `Text.Trim(Text.Proper([Name]))` in multiple queries | `CleanName = (name) => Text.Trim(Text.Proper(name))` | `CleanName([CustomerName])` |
| Calculate Age | `Number.RoundDown(Duration.Days(DateTime.LocalNow() - [BirthDate]) / 365)` | `CalculateAge = (birthDate) => Number.RoundDown(Duration.Days(DateTime.LocalNow() - birthDate) / 365)` | `CalculateAge([DOB])` |
| Format Phone | Complex text manipulation repeated | `FormatPhone = (phoneNumber) => ...` | `FormatPhone([Phone])` |

**Step-by-step in Power BI/Power Query**
1. **Create Custom Function:**
   - Home tab → "New Source" → "Blank Query"
   - In formula bar, write function:
     ```
     (parameter1, parameter2) => 
     let
         // transformation steps
         result = // final calculation
     in
         result
     ```
   - Rename query to function name
   - Function appears in Queries pane

2. **Invoke Function:**
   - In target query, Add Column tab → "Invoke Custom Function"
   - Select function name
   - Map source columns to function parameters
   - New column created with function results

3. **Alternative Invocation:**
   - Use in custom column: `FunctionName([Column1], [Column2])`

**Cautions/Pitfalls**
- Functions must handle null values appropriately
- Parameter data types should be validated
- Test functions with edge cases
- Document function parameters and expected outputs
- Functions can impact query folding
- Consider performance with complex functions

**Pro Tips**
- Start with simple functions and build complexity gradually
- Include error handling (try...otherwise) within functions
- Document functions with comments explaining parameters and return values
- Test functions with edge cases including null values and extreme scenarios
- Create a function library with consistent naming conventions
- Use meaningful parameter names in function definitions
- Consider function performance impact on large datasets

---

### Referencing and Duplicating Queries

**Definition**
Query referencing creates a new query that dynamically points to another query's result, while duplicating creates an independent copy. Both enable query reuse and branching transformations.

**Use Cases**
- Create summary queries from detailed data
- Branch queries for different analysis paths
- Build data marts from single source
- Create backup copies before major changes
- Implement multi-step data processing pipelines

**Examples (Before & After)**

| Scenario | Original Query | Reference Query | Duplicate Query |
|----------|----------------|-----------------|-----------------|
| Sales Analysis | RawSales (all transactions) | SummaryByProduct (references RawSales) | SalesBackup (independent copy) |
| Customer Data | CustomerMaster | ActiveCustomers (filtered reference) | CustomerArchive (historical copy) |
| Multi-step Process | Step1_Import | Step2_Clean (references Step1) | Step1_Backup (safety copy) |

**Key Differences:**

| Aspect | Reference | Duplicate |
|--------|-----------|-----------|
| Data Source | Points to original query | Independent copy of original |
| Updates | Auto-updates when original changes | Independent, no auto-updates |
| Performance | More efficient (single source) | Potentially slower (separate processing) |
| Use Case | Branching transformations | Backup or variant analysis |

**Step-by-step in Power BI/Power Query**
1. **Create Reference:**
   - Right-click source query in Queries pane
   - Select "Reference"
   - New query appears with same data
   - Apply different transformations to reference
   - Original query changes affect reference automatically

2. **Create Duplicate:**
   - Right-click source query in Queries pane
   - Select "Duplicate"
   - New independent query created
   - Changes to original don't affect duplicate
   - Rename duplicate appropriately

3. **Manage Query Dependencies:**
   - View → "Query Dependencies" shows relationships
   - Understand impact of changes to source queries

**Cautions/Pitfalls**
- References create dependencies - deleting source breaks references
- Circular references are not allowed
- References can create performance bottlenecks
- Consider query folding impact with references
- Document query relationships for maintenance
- Be careful when modifying source queries with references

**Pro Tips**
- Use references for branching logic from a common cleaned dataset
- Create duplicates for backup purposes before major query modifications
- Use meaningful naming conventions to identify reference relationships
- Document query dependencies for team collaboration
- Consider performance implications of deep reference chains
- Use the Query Dependencies view to visualize query relationships
- Plan query architecture before creating complex reference structures

---

### Organizing Queries (Groups)

**Definition**
Query groups provide organizational structure in the Queries pane, allowing logical categorization of related queries for better navigation and maintenance.

**Use Cases**
- Group queries by data source (Sales, Finance, HR)
- Organize by processing stage (Raw, Cleaned, Aggregated)
- Separate production queries from development/testing
- Group related functions and parameters
- Create logical data pipeline sections

**Examples (Before & After)**

| Before (Unorganized) | After (Organized Groups) |
|---------------------|-------------------------|
| CustomerData<br>SalesQ1<br>SalesQ2<br>ProductInfo<br>CleanCustomer<br>SalesSummary | **Data Sources**<br>├── CustomerData<br>├── ProductInfo<br>**Sales Queries**<br>├── SalesQ1<br>├── SalesQ2<br>├── SalesSummary<br>**Processed Data**<br>├── CleanCustomer |

**Step-by-step in Power BI/Power Query**
1. **Create New Group:**
   - Right-click in Queries pane empty area
   - Select "New Group"
   - Enter group name (e.g., "Data Sources")
   - Group folder appears in Queries pane

2. **Move Queries to Group:**
   - Drag and drop queries into group folders
   - Or right-click query → "Move to Group" → Select group

3. **Create Nested Groups:**
   - Right-click existing group → "New Group"
   - Creates subgroup within parent group

4. **Group Management:**
   - Rename groups by right-clicking
   - Delete empty groups
   - Expand/collapse groups for navigation

**Cautions/Pitfalls**
- Groups are purely organizational - don't affect functionality
- Moving queries doesn't change dependencies
- Empty groups may be automatically removed
- Group names should be descriptive and consistent
- Consider standardizing group naming conventions
- Groups don't affect query execution order

**Pro Tips**
- Establish group naming conventions early in project development
- Group by business function rather than technical implementation
- Use nested groups for complex projects with many queries
- Color-code or use prefixes for different types of queries within groups
- Document group structure and purpose for team members
- Regularly review and reorganize groups as projects evolve
- Use groups to separate development/testing queries from production queries

---

### Error Handling in Power Query

**Definition**
Error handling in Power Query involves anticipating and managing data quality issues, connection problems, and transformation errors to create robust and reliable data processes.

**Use Cases**
- Handle missing files or connection failures gracefully
- Manage data type conversion errors
- Deal with divide-by-zero calculations
- Handle null values in complex expressions
- Provide meaningful error messages for debugging

**Examples (Before & After)**

| Error Scenario | Without Error Handling | With Error Handling |
|----------------|------------------------|-------------------|
| Division by Zero | `[Sales] / [Units]` → Error | `try [Sales] / [Units] otherwise 0` → 0 |
| Missing File | Query fails completely | `try Excel.Workbook(...) otherwise #table(...)` → Empty table |
| Data Type Conversion | `Number.FromText([TextNumber])` → Error | `try Number.FromText([TextNumber]) otherwise null` → null |
| Null Value Operations | `[FirstName] & " " & [LastName]` → Error if null | `try [FirstName] & " " & [LastName] otherwise "Unknown"` → "Unknown" |

**Error Handling Techniques:**

| Method | Syntax | Use Case |
|--------|--------|----------|
| Try-Otherwise | `try [operation] otherwise [fallback]` | Handle specific operation errors |
| If-Null Check | `if [Column] = null then [default] else [Column]` | Null value handling |
| Error.Record | `Error.Record("Type", "Reason", "Detail")` | Custom error creation |
| Table.ReplaceErrorValues | `Table.ReplaceErrorValues(table, {{"Column", "Replacement"}})` | Replace errors in entire columns |

**Step-by-step in Power BI/Power Query**
1. **Basic Try-Otherwise:**
   - In custom column or formula bar
   - Wrap risky operation: `try [RiskyOperation] otherwise [SafeValue]`

2. **Column-Level Error Replacement:**
   - Transform tab → "Replace Errors"
   - Enter replacement value for errors
   - Applies to entire selected column

3. **Conditional Error Handling:**
   - Add Column → Custom Column
   - Use if-then logic: `if [Column] = null then "Default" else [Operation]`

4. **Query-Level Error Handling:**
   - Wrap entire query steps in try-otherwise
   - Provide fallback data sources or empty tables

**Cautions/Pitfalls**
- Error handling can mask data quality issues
- Performance impact of extensive error checking
- Document why error handling is needed
- Test error scenarios thoroughly
- Don't hide errors that indicate data problems
- Consider logging errors for monitoring
- Balance robustness with data accuracy

**Pro Tips**
- Implement error handling proactively for known problem areas
- Use descriptive error replacement values that indicate the source of the problem
- Log errors to a separate table for monitoring and analysis
- Test error handling by deliberately introducing errors in test data
- Consider business impact when choosing error handling strategies
- Document error handling decisions for audit and compliance purposes
- Use conditional logic to handle different error scenarios appropriately
- Monitor error patterns to identify systemic data quality issues

---

## 10. Performance Considerations and Best Practices

### Optimizing Query Folding

**Definition**
Query folding optimization ensures that transformation operations are pushed back to the data source whenever possible, leveraging the source system's processing power and reducing data transfer.

**Use Cases**
- Improve performance with large SQL Server datasets
- Reduce network traffic from cloud data sources
- Leverage database indexing and optimization
- Minimize memory usage in Power BI
- Speed up refresh operations

**Examples (Before & After)**

| Transformation | Folding Status | Performance Impact |
|----------------|-----------------|-------------------|
| Filter rows on indexed column | ✅ Folds to WHERE clause | Fast - uses database index |
| Remove unnecessary columns | ✅ Folds to SELECT list | Reduced network transfer |
| Sort by database column | ✅ Folds to ORDER BY | Fast - database optimization |
| Custom function on text | ❌ Breaks folding | Slow - processes in Power Query |
| Complex calculated column | ❌ Breaks folding | Slow - all data transferred |

**Folding-Friendly Operations:**

| Operation Category | Foldable Examples | Non-Foldable Examples |
|-------------------|-------------------|----------------------|
| Filtering | Column filters, date ranges | Custom function filters |
| Column Operations | Remove columns, rename | Complex text transformations |
| Sorting | Standard sort operations | Sort by calculated column |
| Aggregation | Group by with standard functions | Custom aggregation functions |
| Joins | Inner/outer joins on key columns | Fuzzy matching joins |

**Step-by-step in Power BI/Power Query**
1. **Check Folding Status:**
   - Right-click on Applied Steps
   - Look for "View Native Query" option
   - If available, step is folding
   - Review generated SQL/query

2. **Optimize for Folding:**
   - Apply filters early in transformation sequence
   - Remove unnecessary columns before complex operations
   - Use standard transformations when possible
   - Avoid custom functions in early steps

3. **Monitor Folding Impact:**
   - View → "Query Diagnostics"
   - Start diagnostics before refresh
   - Review performance metrics
   - Identify folding breaks

**Cautions/Pitfalls**
- Not all data sources support folding
- Custom functions typically break folding
- Complex text operations may prevent folding
- One broken step affects all subsequent steps
- Balance functionality with performance
- Test folding with realistic data volumes

**Pro Tips**
- Structure transformations to maximize folding: filters and column removal first, complex operations last
- Use database views or stored procedures for complex logic that doesn't fold well
- Monitor folding status regularly, especially after making changes
- Create "folding-friendly" versions of common transformations
- Use SQL Profiler or database monitoring tools to see actual queries generated
- Consider creating summary tables in the database for complex aggregations
- Document which operations break folding for your team's reference

---

### Disabling Query Load and Refresh

**Definition**
Query load and refresh settings control whether queries load data into the Power BI model and participate in refresh operations, optimizing performance by excluding intermediate or reference queries.

**Use Cases**
- Exclude intermediate transformation steps from data model
- Prevent loading of lookup tables used only for enrichment
- Disable loading of backup or archive queries
- Control which queries consume memory
- Optimize refresh performance

**Examples (Before & After)**

| Query Type | Load Setting | Refresh Setting | Rationale |
|------------|--------------|-----------------|-----------|
| Raw data import | Enabled | Enabled | Final query for reports |
| Data cleaning steps | Disabled | Enabled | Intermediate processing |
| Lookup table (small) | Disabled | Enabled | Used for enrichment only |
| Backup query | Disabled | Disabled | Archival purposes only |
| Custom functions | N/A | N/A | Functions don't load/refresh |

**Load vs. Refresh Settings:**

| Setting | Enabled | Disabled | Impact |
|---------|---------|----------|---------|
| Enable Load | Loads into data model | Excluded from model | Memory usage, model size |
| Include in Report Refresh | Refreshes with dataset | Skips during refresh | Refresh time, data freshness |

**Step-by-step in Power BI/Power Query**
1. **Configure Load Settings:**
   - Right-click query in Queries pane
   - Uncheck "Enable load" to exclude from model
   - Query still executes for dependencies

2. **Configure Refresh Settings:**
   - Right-click query in Queries pane
   - Uncheck "Include in report refresh"
   - Query won't update during refresh operations

3. **Bulk Configuration:**
   - Home tab → "Close & Apply" dropdown
   - "Close & Apply" vs. "Close & Apply Later"
   - Set options before applying

**Cautions/Pitfalls**
- Disabled queries may still be needed by references
- Consider data freshness requirements
- Refresh dependencies must be maintained
- Disabled refresh can lead to stale data
- Document load/refresh decisions
- Review settings periodically

**Pro Tips**
- Disable loading for intermediate transformation queries to reduce memory usage
- Keep refresh enabled for queries that feed into loaded queries
- Use load/refresh settings strategically for development vs. production environments
- Document the purpose of each query and its load/refresh requirements
- Review query dependencies before changing load/refresh settings
- Consider creating separate development queries with loading disabled
- Monitor model size and refresh performance after changing settings

---

### Using Query Diagnostics

**Definition**
Query Diagnostics provides detailed performance metrics and execution information for Power Query operations, enabling identification of bottlenecks and optimization opportunities.

**Use Cases**
- Identify slow-performing transformation steps
- Analyze data source connection performance
- Monitor query folding effectiveness
- Troubleshoot refresh failures
- Optimize complex query sequences

**Examples (Before & After)**

| Diagnostic Metric | Example Reading | Interpretation | Action |
|-------------------|-----------------|----------------|--------|
| Evaluation Duration | 15 seconds | Step took 15s to complete | Investigate optimization |
| Folding Status | "DataSource.Contents" | Query folded to source | Good performance |
| Row Count | 1,000,000 → 50,000 | Filter reduced 95% of data | Effective filtering |
| Memory Usage | 150 MB | High memory consumption | Consider chunking |

**Diagnostic Information Available:**

| Category | Metrics | Use Case |
|----------|---------|----------|
| Performance | Duration, CPU time | Identify bottlenecks |
| Data Flow | Row counts, column counts | Understand data volume |
| Query Folding | Native query text | Verify source optimization |
| Errors | Error messages, stack traces | Troubleshoot issues |

**Step-by-step in Power BI/Power Query**
1. **Start Diagnostics:**
   - View tab → "Query Diagnostics"
   - Choose "Start Diagnostics"
   - Diagnostics begin recording

2. **Perform Operations:**
   - Execute query refresh or transformations
   - All operations are monitored and recorded

3. **Stop and Review:**
   - View tab → "Query Diagnostics" → "Stop Diagnostics"
   - New queries appear in Queries pane:
     - "Diagnostics" folder
     - Individual step diagnostics
     - Aggregated diagnostics

4. **Analyze Results:**
   - Review duration and performance metrics
   - Check for query folding indicators
   - Identify optimization opportunities

**Cautions/Pitfalls**
- Diagnostics add overhead to query execution
- Enable only when troubleshooting performance
- Large diagnostic datasets may impact performance
- Focus on queries with longest execution times
- Consider data source performance vs. transformation performance
- Use representative data volumes for testing

**Pro Tips**
- Run diagnostics on production-like data volumes for accurate results
- Focus on the longest-running operations for optimization efforts
- Compare diagnostics before and after optimization changes
- Use diagnostics to validate query folding assumptions
- Save diagnostic results for performance baseline documentation
- Analyze diagnostic patterns to identify systematic performance issues
- Use diagnostics to justify infrastructure or design changes

---

### Documentation and Readability of Queries

**Definition**
Query documentation involves adding meaningful names, descriptions, and comments to queries and transformation steps, making the data transformation process maintainable and understandable.

**Use Cases**
- Enable team collaboration on complex transformations
- Document business logic for audit and compliance
- Facilitate knowledge transfer and training
- Support troubleshooting and maintenance
- Meet regulatory documentation requirements

**Examples (Before & After)**

| Documentation Element | Before | After | Impact |
|----------------------|--------|-------|---------|
| Query Names | Query1, Query2 | SalesData_Cleaned, CustomerMaster_Current | Clear purpose |
| Step Names | Changed Type, Filtered Rows | Set_Data_Types, Filter_Active_Customers | Business context |
| Step Descriptions | (none) | "Remove cancelled orders and returns" | Clear business logic |
| Parameter Names | Parameter1 | ReportStartDate | Self-documenting |

**Documentation Best Practices:**

| Element | Naming Convention | Example | Purpose |
|---------|-------------------|---------|---------|
| Queries | Purpose_Status | Sales_Raw, Sales_Cleaned | Clear data state |
| Steps | Verb_Object | Remove_Test_Records | Action description |
| Parameters | Descriptive_Name | MinSalesThreshold | Clear parameter purpose |
| Groups | Logical_Category | Data_Sources, Processed_Data | Organizational clarity |
| Functions | Action_Description | CleanPhoneNumber | Function purpose |

**Step-by-step in Power BI/Power Query**
1. **Rename Queries:**
   - Right-click query name in Queries pane
   - Select "Rename"
   - Use descriptive, consistent naming

2. **Rename Steps:**
   - Right-click step in Applied Steps
   - Select "Rename"
   - Use action-oriented names

3. **Add Step Descriptions:**
   - Right-click step in Applied Steps
   - Select "Properties"
   - Add detailed description of business logic

4. **Document Parameters:**
   - Edit parameter properties
   - Add comprehensive description
   - Include expected values and format

5. **Create Documentation Queries:**
   - Create blank queries with documentation text
   - Use as readme or instruction queries
   - Include change logs and version information

**Cautions/Pitfalls**
- Consistent naming conventions across team/organization
- Keep documentation up-to-date with changes
- Don't over-document simple, obvious operations
- Focus on business logic, not technical implementation
- Consider creating documentation templates
- Review and update documentation regularly

**Pro Tips**
- Establish naming conventions early and enforce them consistently
- Create documentation templates for complex projects
- Use version control principles: document what changed and why
- Include business context in documentation, not just technical details
- Create a data dictionary alongside query documentation
- Use consistent prefixes or suffixes to indicate query status (Raw_, Clean_, Final_)
- Document data quality assumptions and business rule implementations
- Include contact information for subject matter experts in documentation
- Regular documentation reviews should be part of the development process

---