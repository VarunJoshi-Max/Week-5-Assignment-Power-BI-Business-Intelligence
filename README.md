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

![Data Model Schema](screenshots/data_model.png)

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




















