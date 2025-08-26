# Applied Steps Explained

The **Applied Steps** pane is the heart and soul of Power Query. Every action you take—from removing a column to changing a data type—is recorded as a step in this list.

Think of it as a repeatable recipe. When you refresh your data, Power Query re-applies this exact sequence of steps to the new source data, ensuring your transformations are consistent and automated.

![Applied Steps Pane](images/applied-steps-pane.png)

## Interacting with Steps

- **Select a Step:** Click on any step to see a preview of what your data looked like after that specific transformation was applied. This is incredibly useful for debugging.
- **Rename a Step:** Right-click a step and choose "Rename" to give it a more descriptive name (e.g., "Removed Unnecessary Product Columns").
- **Delete a Step:** Click the 'X' next to a step name to remove it. This is the equivalent of "undo."
- **Edit Step Settings:** Many steps have a gear icon (⚙️) next to them. Clicking this allows you to modify the settings for that step without having to delete and re-create it.

!!! warning "Changing Early Steps"
Be careful when deleting or modifying steps early in the sequence. A later step might depend on a column or a condition created by an earlier step. Changing an early step can cause subsequent steps to break. Power Query will warn you if this is a possibility.

Understanding how to read and manage the Applied Steps is fundamental to becoming proficient in Power Query.
