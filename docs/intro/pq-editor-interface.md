# The Power Query Editor Interface

When you select "Transform Data" in Power BI Desktop, you open the Power Query Editor. It's a separate window dedicated entirely to data preparation. Let's familiarize ourselves with its main components.

![Power Query Editor Labeled](images/pq-editor-labeled.png)

## 1. The Ribbon

Similar to other Microsoft products, the ribbon at the top contains all the available transformations, organized into tabs:

- **Home:** Common tasks like getting data, managing queries, and combining data.
- **Transform:** Contains transformations that modify the _existing_ column (e.g., changing a text column to uppercase).
- **Add Column:** Contains transformations that create a _new_ column based on existing ones (e.g., extracting the year from a date column).
- **View:** Tools to control what you see in the editor, like the formula bar and data profiling tools.

## 2. Queries Pane

Located on the left, this pane lists all the data connections (queries) in your report. You can select, rename, group, and manage your queries here.

## 3. Data Preview

The central area shows a preview of your data for the selected query. This is where you see the results of your transformations in real-time.

## 4. Applied Steps Pane

**This is the most important part of the Power Query Editor.** Located on the right, it records every single transformation you make as a distinct step. It's like a recipe for your data. You can:

- See the history of your transformations.
- Select a step to see what the data looked like at that point.
- Rename steps for clarity.
- Delete steps to undo an action.
- Reorder steps (with caution!).

### 5. Formula Bar

Just above the data preview, the formula bar displays the underlying M language code for the selected step. While you can do most things with the user interface, seeing the M code helps you understand what's happening behind the scenes.
