# HTML KPI Cards

Collection of DAX-HTML-CSS custom KPI cards. 

*Disclaimer: during the creation of these cards I relied heavily on GitHub Copilot used in VS Code, to manupulate the .pbip files and do some "heavy lifting" such as: repetitive code continuation after setting the standard, deep code commenting, bulk operations.* 

## What is currently available?

**1 - Two Metrics KPI Cards with Tabular Details (Quarterly and Monthly)**

<img width="30%" alt="image" src="https://github.com/user-attachments/assets/fb01dcfc-230b-4196-adbe-65d5bd4ccfbe" /> <img width="30%" alt="image" src="https://github.com/user-attachments/assets/330e01fd-d2af-445b-a141-eedca44e90ce" />

This visual is directly linked to two metrics (Total Sales $ and Total Quantity Sold), it displays a "Current" value, vs. a "Previous Period / Comparison" value. It has Quarterly and Monthly views, and with a click it is possible to display details for each quarter/month in a tabular format.

**2 - One Metrics KPI Card with Tabular Details (Quarterly and Monthly)**

<img width="60%" alt="image" src="https://github.com/user-attachments/assets/2fe74681-dfaf-457f-b893-4a6faf80311f" />

This visual is linked to the standardized measures for measures selector [Actual] and [SPLY], so it is possible dynamically switch between Total Sales $ and Total Quantity Sold. It displays a "Current" value, vs. a "Previous Period / Comparison" value. It has Quarterly and Monthly views, and with a click it is possible to display details for each quarter/month in a tabular format.

**3 - One Metrics KPI Card with Waterfall Mini-Chart Details (Quarterly and Monthly)**

<img width="30%" alt="image" src="https://github.com/user-attachments/assets/1963ea87-c828-4655-892e-d64146e4af75" /> <img width="30%" alt="image" src="https://github.com/user-attachments/assets/eb2a5f92-751d-42b3-ad3f-7fc964f6b272" />

This visual is linked to the standardized measures for measures selector [Actual] and [SPLY], so it is possible dynamically switch between Total Sales $ and Total Quantity Sold. It displays a "Current" value, vs. a "Previous Period / Comparison" value. It has Quarterly and Monthly views, and with a click it is possible to display details for each quarter/month in a waterfall chart. The waterfall chart has a hover effect that shows all the details for each data point.

## How to use it

**⚙️ Step 1: download the template in pbix**

[📥 Download Here](https://github.com/SteCiu01/Power-BI-HTML-Templates/raw/refs/heads/main/HTML-KPI-Cards/HTML%20KPI%20Cards%20Templates.pbix)

**⚙️ Step 2: Create the measures [Actual] and [SPLY] that need to be amended (OPTIONAL, ONLY IF YOU IMPLEMENT THE ONE-METRIC KPI CARDS)**

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

There should be one field parameter for each visual. See below example for the One Metrics KPI Card with Tabular Details:

```
Single Metric KPIs Table Switcher Q-M = {
    ("Q", NAMEOF('MeasuresTable'[HTML KPI Card and Table - Quarterly - Single Metric]), 0),
    ("M", NAMEOF('MeasuresTable'[HTML KPI Card and Table - Monthly - Single Metric]), 1)
}
```

If you have multiple cards visuals in one report you can create a Master M-Q switcher and then create a "mini-model" between switchers:

```
Master Switcher Q-M = 
DATATABLE (
    "TimeFrame", STRING,
    "Order", INTEGER,
    {
        { "M", 1 },
        { "Q", 0 }
    }
)
```

Then the model should be as follows: 

<img width="40%" alt="image" src="https://github.com/user-attachments/assets/8cb2b3bd-e98a-4360-91fc-6705910e6b17" />


**⚙️ Step 4: Create the measures selector (parameter) table (OPTIONAL, ONLY IF YOU IMPLEMENT THE ONE-METRIC KPI CARDS)**

This is a necessary step for those cards that display one measure only and a switch between measures is necessary

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
- (OPTIONAL, ONLY IF YOU IMPLEMENT THE ONE-METRIC KPI CARDS) Create a slicer to swithch between measures using the Measure-Selector column [Measure]

## Final Considerations and limitations

After completing the six steps, your KPI Cards are ready to be used. 

Please consider this approach is based on heavily coded cards and relies on HTML custom Visual. Before using it in any production environment make sure the code is well understood, to guarantee timely maintainance, if needed.
