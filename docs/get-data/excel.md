# Connect to Excel

Excel workbooks are one of the most common data sources for Power BI.

## Step-by-Step Guide

1. In Power BI Desktop, on the **Home** ribbon, click **Get Data**.
2. Select **Excel Workbook** from the common sources, or click "More..." and find it in the list.
3. Click **Connect**.
4. Navigate to your Excel file and click **Open**.
5. The **Navigator** window will appear. This window shows you all the sheets and defined tables within the workbook.

    ![Excel Navigator Window](images/excel-navigator.png)

6. **Best Practice:** Whenever possible, connect to a **Table** (indicated by the blue header icon) rather than a **Sheet** (grid icon). Tables are more robust and less likely to break if the sheet structure changes.
7. Check the box next to the table(s) or sheet(s) you want to import.
8. Click **Transform Data** to load the data into the Power Query Editor for cleaning and shaping.

!!! note
If you click "Load," the data will be loaded directly into the data model, skipping the Power Query Editor. For the PL-300, you will almost always want to click "Transform Data" first.

## Exercise

- **Goal:** Connect to the provided `ProductSales.xlsx` file.
- **File:** [Link to your practice dataset]
- **Task:** Connect to the `Sales_Data` table within the workbook and load it into the Power Query Editor.
