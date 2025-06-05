# Retail Data Analysis


### Project Overview


This project analyzes a fictional retail dataset to uncover business insights across sales, customer behavior, product performance, and geographic trends. It showcases the end-to-end data analysis process using Python and includes visualizations to support data-driven decision making.


### Data Source 

The primary dataset used in this project is Retail_data.csv, which contains detailed transaction-level records from a fictional retail company.Each row represents a unique sale and designed for analysis and visualization tasks in a real-world business context.
| Column Name         | Description                                                  |
|---------------------|--------------------------------------------------------------|
| Transaction_ID      | Unique identifier for each transaction                      |
| Customer_ID         | Unique customer identifier                                  |
| Email               | Customer email (simulated)                                  |
| Phone               | Customer phone number                                       |
| State / Country     | Location of the customer                                    |
| Gender              | Gender of the customer                                      |
| Income              | Income level: Low / Medium / High                           |
| Year, Month, Time   | Timestamp details for the purchase                          |
| Total_Purchases     | Number of items bought in the transaction                   |
| Amount              | Average item price                                          |
| Total_Amount        | Total transaction value                                     |
| Product_Category    | General product category (e.g., Electronics, Grocery)       |
| Product_Brand       | Brand of the product                                        |
| Product_Type        | Specific type of product                                    |
| Shipping_Method     | Delivery speed/type                                         |
| Payment_Method      | Payment type (e.g., Credit Card, PayPal)                    |
| Order_Status        | Order completion status (e.g., Shipped, Delivered, etc.)    |

### Tools Used
* Python (Pandas, Matplotlib, Seaborn) : Main data cleaning, transformation, and visualizations
* Excel: Initial inspection and basic summarization
* Jupyter Notebook
* Data cleaning and transformation
* Exploratory data analysis (EDA)

### Data Cleaning / Preparation
In the initial phase of this retail analytics project, I focused on transforming raw retail data into a clean, analysis-ready dataset. The key steps included:

🔍 Data Inspection
* Reviewed data structure and types (.info(), .describe()).
* Identified missing, duplicate, and inconsistent entries.

🧽 Cleaning Process
* Standardized column names: converted to lowercase and replaced spaces with underscores.
* Handled missing values: used imputation and conditional filtering based on context (e.g. mean fill for numeric, "Unknown" for categories).
* Corrected data types: e.g. converted date fields to datetime, numeric fields to float.
* Normalized categorical data: trimmed spaces, fixed casing issues (e.g., Income, Product_Type).
* Removed or flagged outliers: based on domain knowledge or visual inspection.

🛠️ Feature Engineering
* Extracted Month, Year, Hour from Date column for time-based analysis.
* Created new features like High_Value_Customer, Multiword_Product_Type and Order_Returned.
* Applied regex to identify product names with multiple words.


### Exploratory Data Analysis (EDA)
The EDA phase focused on uncovering patterns in customer behavior, product performance, and overall sales performance. The goal was to support strategic decisions using insights derived from the data.

🔎 Key Questions Explored
* What is the overall sales trend over time?
* Which product categories or brands are top performers?
* When do sales peak—by month, weekday, or time of day?
* Which customer segments drive the most revenue?
* Are there patterns by geographic region or income group?

### Interesting Python Code Used
1. 📅 Time-Based Feature Engineering
Extracted components for sales trends and temporal KPIs
``` python
df['Month'] = pd.to_datetime(df['Month'], format='%B', errors='coerce').dt.month
df['Date'] = pd.to_datetime(df[['Year', 'Month']].assign(Day=1))
df['Day_of_Week'] = df['Date'].dt.day_name()
df['Hour'] = pd.to_datetime(df['Time'], format='%H:%M:%S', errors='coerce').dt.hour
```

2. 🔍 Multi-Word Product Type Detection (Regex)
Flagged detailed product names with 2+ words for segmentation
``` python
df['Multiword_Product'] = df['Product_Type'].str.contains(r'\b\w+\s+\w+', na=False)
multiword_kpi = df['Multiword_Product'].mean() * 100
```

3. 📈 Monthly Sales Trend with Growth Rate
Calculated total sales and MoM growth:
``` python
Sales_by_Monthly = df.groupby(['Year', 'Month_Num'])['Total_Amount'].sum().reset_index()
Sales_by_Monthly['Date'] = pd.to_datetime(Sales_by_Monthly['Year'].astype(str) + '-' + Sales_by_Monthly['Month_Num'].astype(str) + '-01')
Sales_by_Monthly['MoM_Growth_Rate'] = Sales_by_Monthly['Total_Amount'].pct_change() * 100
```

4. 🎯 Customer Segmentation: High-Value Customers
Identified top-spending customers for targeting:
``` python
customer_totals = df.groupby('Customer_ID')['Total_Amount'].sum().reset_index()
high_value_customers = customer_totals[customer_totals['Total_Amount'] > 2000]
```

5. 📊 AOV by Income Group (Box Plot)
Analyzed customer spending across income levels:
``` python
sns.boxplot(x='Income', y='AOV', data=aov, order=['Low', 'Medium', 'High'])
plt.title('AOV by Income Group')
```
6. 🗺️ Geographic Sales Visualization
Used Plotly to create an interactive choropleth map:
``` python
fig = px.choropleth(
    country_sales,
    locations='Country',
    locationmode='country names',
    color='Total_Amount',
    color_continuous_scale='Blues',
    title='Total Sales by Country'
)
fig.show()
``` 
7. 🧮 Customer Lifetime Value (CLTV)
Calculated how much each customer contributed to total revenue:
``` python
# Aggregate total amount spent by each customer
Customer_Lifetime_Value = df.groupby('Customer_ID', observed=True)['Total_Amount'].sum().reset_index()
Customer_Lifetime_Value.columns = ['Customer_ID', 'Customer_Lifetime_Value']
``` 
8. 💸 Income-Based Revenue Distribution
Visualized which income groups drive the most revenue:
``` python
# Group revenue by income level
revenue_by_income = df.groupby('Income', observed=True)['Total_Amount'].sum().reset_index()

# Create pie chart
plt.figure(figsize=(8, 8))
plt.pie(
    revenue_by_income['Total_Amount'],
    labels=revenue_by_income['Income'],
    autopct='%1.1f%%',
    startangle=140
)
plt.title('Revenue by Income Level')
plt.axis('equal')
plt.tight_layout()
plt.show()
``` 
### The analysis 
#### rResults are summarized as follows:
* Overall sales showed a steady upward trend throughout the year, with significant spikes during the holiday season (November–December).
* Product Category A consistently outperformed others in both total sales and revenue generation.
* Customers with high income levels contributed the most to overall revenue, particularly in premium product segments.
* High Lifetime Value (LTV) customers represent a key segment that should be prioritized in marketing and retention strategies.
* Repeat purchases were higher among certain customer demographics, indicating opportunities for loyalty programs.
* The majority of purchases occurred during evening hours and weekends, suggesting optimal times for promotions.
* A small percentage of products were frequently returned, pointing to potential quality or expectation issues.
* Products with multi-word names (e.g., “Smart Watch”) were more likely to be higher value and associated with premium categories.
