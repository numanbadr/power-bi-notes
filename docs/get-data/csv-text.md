# Connect to CSV/Text

CSV (Comma-Separated Values) and Text files are simple, plain-text data sources that are very common.

## Step-by-Step Guide

1. On the **Home** ribbon, click **Get Data**.
2. Select **Text/CSV**.
3. Navigate to your `.csv` or `.txt` file and click **Open**.
4. Power Query will show a preview window where it attempts to detect the file settings.

    ![CSV Import Settings](images/csv-settings.png)

5. **Key Settings to Check:**
    - **File Origin:** Usually fine as is, but important for files with special characters from different systems.
    - **Delimiter:** Power Query is good at detecting this (comma, tab, semicolon, etc.), but you can change it if it's wrong.
    - **Data Type Detection:** You can base this on the first 200 rows, the entire dataset, or not at all. "Based on first 200 rows" is usually sufficient.
6. Once you are happy with the preview, click **Transform Data** to open the Power Query Editor.

## Exercise

- **Goal:** Connect to the provided `CustomerData.csv` file.
- **File:** [Link to your practice dataset]
- **Task:** Connect to the file, ensure the delimiter is correctly identified as a comma, and load it into the Power Query Editor.
