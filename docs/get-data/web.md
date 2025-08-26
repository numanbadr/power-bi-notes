# Connect to Web

You can extract data directly from tables on a web page. This is useful for static data like lists of countries, exchange rates, or public statistics.

## Step-by-Step Guide

1. On the **Home** ribbon, click **Get Data** and select **Web**.
2. A dialog box will appear asking for a URL. Paste the URL of the page containing the data.
    - *Example URL:* `https://en.wikipedia.org/wiki/List_of_countries_and_dependencies_by_population`
3. Click **OK**.
4. Power BI will analyze the page and the **Navigator** window will appear, showing all the HTML tables it found on the page.

    ![Web Navigator Window](images/web-navigator.png)

5. Click on each table in the list to preview it.
6. Check the box next to the table you want to import.
7. Click **Transform Data**.

!!! warning "Dynamic Web Pages"
    This method works best for pages with simple, static HTML tables. It may not work for data that is loaded dynamically with JavaScript or requires you to log in.

## Exercise

- **Goal:** Import a list of countries by population from Wikipedia.
- **URL:** `https://en.wikipedia.org/wiki/List_of_countries_and_dependencies_by_population`
- **Task:** Connect to the URL and import the main population table into the Power Query Editor.
