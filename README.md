# Week-5-Assignment-Power-BI-Business-Intelligence

**Student Name:** Varun Arun Joshi 

**Course/Batch:** Data Analytics 

**Submission Date:** 17/10/2026

**Dataset Used:** Mexico Toy Sales

The objective of this assignment is to develop practical skills in Power BI and Business Intelligence. learn to import and prepare datasets, create data models, build calculated measures using DAX, create interactive visualizations, and develop an interactive dashboard for business analysis.

# Task 1: Power BI Introduction & Data Import

In Data Analytics there are many tools to explore the massive volume data and get insight from the datasets for the business progress. Power Bi is the popular visualization and BI tool developed by Microsoft. 

**Basic purpose of using Power BI is to process the Raw data into clean actionable insight and making the massive information easy to understand using the visualizations.**

### Dataset Details:

**Mexico Toy Sales** Sales & inventory data for a fictitious toy store chain in Mexico called Maven Toys, including information about products, stores, daily sales transactions, and current inventory levels at each location. 

**Dataset is stored in the local folder contains CSV files shown below:** 

<img width="1009" height="260" alt="image" src="https://github.com/user-attachments/assets/fa50d487-7656-47ad-9c7f-1ed5db9d2ad5" />

### Explore the Power BI interface:

<img width="1602" height="759" alt="image" src="https://github.com/user-attachments/assets/8e1b242e-d127-4483-8865-7e875a0b4ec7" />

Select a blank report in Power BI interface and save the PBIX file as **Mexico Toy Sales** at desired path.

<img width="1632" height="701" alt="image" src="https://github.com/user-attachments/assets/5ee5600f-62b1-4fa9-83a0-9f872a5bf015" />

Select the Get Data and choose Text/CSV as CSV File in this datasets cannot be combined using folder because each CSV have separate structure and fields.

<img width="535" height="756" alt="image" src="https://github.com/user-attachments/assets/256af27c-f446-4a5f-8ec1-986b83613a98" />

Data has been imported to Power BI environment using the **ELT** method.

<img width="523" height="570" alt="image" src="https://github.com/user-attachments/assets/666254fa-837a-4e50-bcc5-6412dcca790f" />

# Task 2: Data Cleaning & Preparation

**Inspect the imported dataset.**

### Calendar table

Correct the header using **Mark first row as header** and change **date** data type by using Locate in power query.

<img width="1235" height="634" alt="image" src="https://github.com/user-attachments/assets/267efec0-807b-4fb9-aeae-360088ef89e0" />

### Inventory table

Inventory table all data types are changed to whole number

<img width="1579" height="794" alt="image" src="https://github.com/user-attachments/assets/14303646-6024-431b-b835-643b129a1ec9" />

### products table

The data type of Product_Cost & Product_Price have been changed to fixed decimal using locate, No other issues found.

<img width="1567" height="788" alt="image" src="https://github.com/user-attachments/assets/c6f8a778-2ab8-42f8-b8f7-2c89b9973d18" />

### sales table

<img width="1330" height="854" alt="image" src="https://github.com/user-attachments/assets/023bacd1-801b-4bef-b51e-fc5d5a5c959c" />

### stores table

**Store_Open_Date** data type by using Locate in power query.

<img width="1592" height="980" alt="image" src="https://github.com/user-attachments/assets/d104ea1a-9025-4cda-8643-7aa2e5c2bfbe" />

**Removed duplicates, errors, changed data types**

# Task 3: Data Modeling & Relationships

Lets close & apply the steps applied and move to model view in Power BI Desktop.

<img width="1557" height="840" alt="image" src="https://github.com/user-attachments/assets/b1fc0663-1c3a-48b2-9322-4f4ba2200687" />

### Data Model Architecture
A multi-fact **Star Schema** architecture was established to optimize query performance, support DAX calculations, and enable interactive cross-filtering across visuals.

### Table Classifications

#### Dimension Tables (Lookup & Context)
* **`products`**: Master product catalog containing item metadata, categories, unit costs, and retail prices.  
  * **Primary Key:** `Product_ID`
* **`calendar`**: Dedicated date dimension enabling continuous time-intelligence calculations and hierarchical temporal slicing (`Year`, `Quarter`, `Month`, `YearMonth`).  
  * **Primary Key:** `Date`
* **`stores`**: Store master profile containing geographical and store operational attributes (`Store_Name`, `Store_City`, `Store_Location`, `Store_Open_Date`).  
  * **Primary Key:** `Store_ID`

#### Fact Tables (Events & Quantitative Data)
* **`sales`**: Transactional fact table recording historical purchase events, order identifiers (`Sale_ID`), transaction dates, and units sold.
* **`inventory`**: Snapshot fact table tracking periodic stock availability (`Stock_On_Hand`) across stores and items.

---

### Relationship Matrix

| From Table (Dimension / `1`) | To Table (Fact / `*`) | Join Key | Cardinality | Cross-Filter Direction | Purpose / Business Logic |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `products` | `sales` | `Product_ID` | One-to-Many (`1:*`) | Single | Propagates product categories and pricing attributes to individual sales transactions. |
| `products` | `inventory` | `Product_ID` | One-to-Many (`1:*`) | Single | Enables filtering stock levels by product name and category. |
| `calendar` | `sales` | `Date` | One-to-Many (`1:*`) | Single | Drives trend lines and time-intelligence reporting without gaps. |
| `stores` | `sales` | `Store_ID` | One-to-Many (`1:*`) | Single | Enables sales breakdown by city, store location, and store name. |
| `stores` | `inventory` | `Store_ID` | One-to-Many (`1:*`) | Single | Tracks inventory levels across physical retail locations. |

---

### Design Rationale
* **Single Directional Filtering:** Filter context strictly flows downwards from the dimension tables to the fact tables (`1` $\rightarrow$ `*`). This ensures predictable query evaluation and prevents ambiguous circular dependency paths.
* **Master Dimension Sharing (Conformed Dimensions):** Both `products` and `stores` serve as conformed dimensions shared between `sales` and `inventory`, allowing unified cross-analysis of sales performance against current inventory availability.


<img width="1154" height="669" alt="image" src="https://github.com/user-attachments/assets/04b0eb3a-e133-4b2f-92a0-03279483de5c" />

## Task 4: DAX Calculated Columns & Measures 

#### 1. Average Order Value (AOV)

**DAX Formula:**

Average Order Value = 
DIVIDE(
    [Revenue], 
    DISTINCTCOUNT(sales[Sale_ID]), 
    0
)

#### 2. Profit Margin %

**DAX Formula:**

Profit Margin % = 
DIVIDE([Total Profit], [Revenue], 0)

#### 3. Total Revenue

**DAX Formula:**

Revenue = 
SUMX(
    sales, 
    sales[Units] * RELATED(products[Product_Price])
)

#### 4. Total Cost

**DAX Formula:**

Total Cost = 
SUMX(
    sales, 
    sales[Units] * RELATED(products[Product_Cost])
)

#### 5. Total Profit

**DAX Formula:**

Total Profit = 
[Revenue] - [Total Cost]

#### 6. Total Transactions

**DAX Formula:**

Total Transactions = 
COUNTROWS(sales)

## DAX Measures – Mexico Toy Sales Dashboard

### 1. Average Order Value (AOV)
Calculates the average revenue generated per unique transaction by dividing total revenue by the distinct count of `Sale_ID`. `DIVIDE()` safely handles division by zero.

### 2. Profit Margin %
Measures the percentage of revenue retained as profit after deducting product costs. `DIVIDE()` calculates Total Profit divided by Revenue, returning 0 if the denominator is zero.

### 3. Revenue
Uses `SUMX()` to calculate revenue for every sales row by multiplying `Units` by the related product's `Product_Price`, then sums the results.

### 4. Total Cost
Calculates the total product cost by multiplying `Units` by the related product's `Product_Cost` for each sales row and summing the results using `SUMX()`.

### 5. Total Profit
Calculates total profit by subtracting Total Cost from Revenue.

**Formula:** Total Profit = Revenue − Total Cost

### 6. Total Transactions
Counts the total number of rows in the `sales` table using `COUNTROWS()`. Each row represents one sales record, so this measures sales records rather than necessarily unique transactions.

<img width="1593" height="704" alt="image" src="https://github.com/user-attachments/assets/626e35f0-e6d6-4dbe-9280-747e74143824" />

## Task 5: Power BI Visualizations

<img width="1015" height="470" alt="image" src="https://github.com/user-attachments/assets/fda03215-f769-47c2-8f31-90d6a06e3080" />

## Task 6: Interactive Dashboard & Filters

<img width="1197" height="665" alt="image" src="https://github.com/user-attachments/assets/a17c4fe2-6a42-48e2-bf7f-c1b5a56e4f59" />

## Task 7: Business Insights & Recommendations

### 1. Business Insights

#### Insight 1: Overall Business Profitability

The business generated $563.44 million in revenue and $172.31 million in total profit, with a reported profit margin of 30.58%. This indicates that the business retains approximately $30.58 in profit for every $100 of revenue, based on the dashboard's calculations.

#### Insight 2: Toys Is the Highest Revenue-Generating Category

Toys is the leading product category by revenue, contributing approximately $38 million, followed by Art & Crafts at around $34 million. This makes Toys the largest revenue contributor among the five displayed categories.

#### Insight 3: Art & Crafts Leads in Units Sold

Art & Crafts records the highest sales volume, at approximately 0.33 million units, followed by Toys at around 0.26 million units. Despite selling fewer units, Toys generates more revenue, suggesting differences in average selling prices or product mix.

#### Insight 4: Ciudad de México Is the Top Profit-Generating City

Ciudad de México ranks first in total profit at approximately $20 million, followed by Guadalajara and Monterrey. The leading cities contribute substantially to the displayed profit totals, making geographical performance an important area for further analysis.

#### Insight 5: Electronics Is the Lowest-Performing Category by Revenue and Volume

Electronics records the lowest revenue, at approximately $8 million, and the lowest unit sales among the five categories. Further analysis of its costs, profit margin, inventory, and monthly sales is required to determine whether the category is underperforming.

#### Insight 6: Monthly Revenue Shows Significant Fluctuations

Monthly revenue increases from approximately $19–20 million in early 2022 to a peak of around $35–36 million in early 2023. Revenue subsequently declines to approximately $26–27 million in the later months displayed. This pattern warrants investigation to identify the categories, cities, or periods contributing to the decline.

### 2. Important Trends and Patterns

Revenue concentration: Toys and Art & Crafts are the two largest revenue-generating categories.

Sales volume versus revenue: Art & Crafts sells more units, while Toys generates more revenue.

Geographical variation: Profit differs across cities, with Ciudad de México leading the displayed results.

Changing monthly performance: Revenue rises significantly during late 2022 and early 2023 before declining later in 2023.

Category performance gap: Electronics contributes substantially less revenue and volume than the other categories.

### 3. Business Recommendations

#### Recommendation 1: Strengthen the Top-Performing Categories

Prioritize the availability of popular Toys and Art & Crafts products by monitoring stock levels, replenishment requirements, and sales trends. Evaluate opportunities for targeted promotions and product assortment improvements while monitoring their impact on profit margins.

Expected benefit: Help maintain revenue from the strongest categories while reducing the risk of stockouts and missed sales.

#### Recommendation 2: Investigate the Decline in Monthly Revenue

Analyze revenue by product category, store city, and store location for the months before and after the decline. Compare sales volumes and average selling prices to determine whether the decrease is associated with particular products, locations, or changes in demand.

Expected benefit: Identify the main contributors to declining revenue and develop targeted corrective actions rather than applying broad discounts across the business.

#### Recommendation 3: Review Electronics Category Performance

Examine Electronics' profit margin, inventory levels, product-level sales, and stock turnover before deciding whether to adjust prices, promotions, or product assortment.

Expected benefit: Identify opportunities to improve category performance while avoiding unnecessary inventory accumulation or unprofitable discounting.

#### Recommendation 4: Replicate Successful City-Level Practices

Compare Ciudad de México, Guadalajara, and Monterrey with lower-profit cities using revenue, costs, units sold, and profit margin. Identify differences in product mix and sales performance before applying successful practices to other locations.

Expected benefit: Improve decision-making around local sales strategies and identify opportunities for more balanced geographical performance.

### 4. Conclusion

The dashboard indicates that the business is profitable overall, with Toys leading in revenue, Art & Crafts leading in unit sales, and Ciudad de México ranking first in total profit. However, the decline in monthly revenue and the comparatively low performance of Electronics warrant further investigation. By strengthening leading categories, reviewing underperforming segments, and analyzing geographical and monthly trends, the business can make more informed decisions to support sustainable growth and profitability.
























































