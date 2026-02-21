# HTML Waterfall Visual

Interactive IBCS-style waterfall chart for Power BI built entirely in DAX as an HTML measure. Displays year-over-year variance breakdown by quarter or month (switchable via button), with hover tooltips, color-coded positive/negative bars, and dynamic K/M/B formatting. Supports dual-metric toggling (Revenue vs. Quantity) through a slicer-connected measure selector. 

<img width="45%" alt="image" src="https://github.com/user-attachments/assets/bb815544-b06e-467f-93f8-58ce290dff8d" /> <img width="45%" alt="image" src="https://github.com/user-attachments/assets/8ae21932-62d0-40cb-ae25-7f1c34802c7e" />



*Disclaimer: during the creation of this visual I relied heavily on GitHub Copilot used in VS Code, to manupulate the .pbip files and do some "heavy lifting" such as: repetitive code continuation after setting the standard, deep code commenting, bulk operations.* 

## How to use it

**⚙️ Step 1: download the template in pbix**

[📥 Download Here](https://github.com/SteCiu01/Power-BI-HTML-Templates/raw/refs/heads/main/HTML-Waterfall-Visual/HTML%20Waterfall%20Visual%20Template.pbix)

**⚙️ Step 2: Create the measures [Actual] and [SPLY] that need to be amended**

**⚠ IMPORTANT**

Before running these DAX Query View code below, make sure the 'MeasuresTable' in the code has the same name of your table where you want to save the measures. 

‼️ This is crucial to make everything work smoothly: if your measures table is called 'Tables for Measures', for example, follow these steps:

- Rename the 'MeasuresTable' of the template you just downloaded into 'Tables for Measures'
- Change the codes below replacing 'MeasuresTable' with 'Tables for Measures'
- Run the below code in your model to add the measures.

Following these steps you avoid the mismatch between the template and your model. 


```
DEFINE

// Metrics Measures to modify, see guidelines

    MEASURE 'MeasuresTable'[Actual] = SWITCH(
    TRUE(),
    SELECTEDVALUE('Measures-Selector'[Order]) = 0, [Total Sales $],
    SELECTEDVALUE('Measures-Selector'[Order]) = 1, [Total Sales Qty]
)
    MEASURE 'MeasuresTable'[SPLY] = SWITCH(
    TRUE(),
    SELECTEDVALUE('Measures-Selector'[Order]) = 0, [SPLY Sales $],
    SELECTEDVALUE('Measures-Selector'[Order]) = 1, [SPLY Sales Qty]
)
```
Guidelines
- These measures are based on the parameter table we created earlier.
- The structure is set so that when the parameter is used in a slicer, the correct measure is displayed based on the selection.
- In our template we set in the parameter the possibility to switch between Total Sales ($) and Total Qty Sold, therefore for both metrics we need Actual and SPLY.
- Replace the 4 sample measures of the template ([Total Sales $], [Total Sales Qty], [SPLY Sales $], [SPLY Sales Qty]) with your measures that you have referenced in the parameter table.

**⚙️ Step 3: Amend the HTML measures in the .pbix file template you downloaded**

In each measure there are 2 sections to be amended:

**Formatting Section**

This section allows to customise the visual appearence, choosing font size, dimension, visual size, etc. There are in-measures comments to guide you in this process, to know exactly what to change, if needed.

**Data Section**

This section allows to bring in your data into the visual. You can do it in 2 ways:
- Paste the measure in your model, then oing through the code and manually change the reference to your tables/measures.
- Change the name of the tables/measures in the .pbix template you downloaded before copying and pasting the html measures in your data model.

**⚙️ Step 3: Create a field parameter, to switch between monthly and quarterly views**

There should be a field parameter for the waterfall visual so that you can switch between monthly and quarterly views. See below example:

```
Waterfall Visual Switcher Q-M = {
    ("M", NAMEOF('MeasuresTable'[HTML Waterfall Chart Monthly Visual]), 1),
    ("Q", NAMEOF('MeasuresTable'[HTML Waterfall Chart Quarterly Visual]), 0)
}
```

**⚙️ Step 4: Create the measures selector (parameter) table**

This is a necessary step for you being able to sswitch between metricss to display in the visual (e.g., switch between Total Sales ($) and Quantity Sold)

Create a calculated table in your model that will act as a measure selector parameter, using the following DAX code:

```
Measures-Selector = 
DATATABLE (
    "Measure", STRING,
    "Order", INTEGER,
    {
        { "Total Sales ($)", 0 },
        { "Total Qty Sold", 1 }
    }
)
```
Guidelines
- Use user-friendly names for the measures
- For example, if your actual measure is called [tot_sales_usd], expose it as "Total Sales ($)"
- Create as many rows as the measures you need to use in this visual

**⚙️ Step 5: Ready to go!**

A this point you just need to: 
- Drop the field parameter's column [Time Frame] in the HTML Visual -> Values
- Create a button SLicer with the as Value the parameter's column [Time Frame]
- Create a slicer to swithch between measures using the Measure-Selector column [Measure]

## Final Considerations and limitations

After completing the six steps, your Waterfall Visual is ready to be used. 

Please consider this approach is based on a heavily visual and relies on HTML custom Visual. Before using it in any production environment make sure the code is well understood, to guarantee timely maintainance, if needed.
