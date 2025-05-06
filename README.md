📌 Project Title:
Retail Sales Data Processing using Microsoft Fabric Lakehouse

👩‍💻 Objective:
To build a structured data pipeline in Microsoft Fabric using Lakehouse and PySpark notebooks that processes sales data through bronze, silver, and gold layers.

✅ Progress Summary
1. Lakehouse Environment Setup
Created a Lakehouse named RetailLakehouse.

Organized the file system into the following folders:

raw/

bronze/

silver/

gold/

2. Raw to Bronze Layer
Task Performed:

Uploaded a CSV file containing retail sales data into the raw/ folder.

Used a PySpark notebook to read the CSV file and convert it to Parquet format, then saved it in the bronze/ layer.

Code Used:

python
Copy
Edit
df_raw = spark.read.option("header", True).csv("/lakehouse/raw/sales.csv")
df_raw.write.mode("overwrite").format("parquet").save("/lakehouse/bronze/sales")
3. Bronze to Silver Layer
Task Performed:

Loaded data from the bronze/ layer.

Added a calculated column sales_amount using the formula:

sales_amount = quantity * price

Saved the transformed data into the silver/ layer as a clean dataset.

Code Used:

python
Copy
Edit
df_bronze = spark.read.format("parquet").load("/lakehouse/bronze/sales")
df_transformed = df_bronze.withColumn("sales_amount", df_bronze.quantity * df_bronze.price)
df_transformed.write.mode("overwrite").format("parquet").save("/lakehouse/silver/sales_transformed")
4. Silver to Gold Layer
Task Performed:

Performed aggregations on the sales_transformed data.

Grouped by order_date and calculated:

total_sales = SUM of sales_amount

total_quantity = SUM of quantity

Saved the result into the gold/ layer.

Code Used:

python
Copy
Edit
from pyspark.sql.functions import sum

df_silver = spark.read.format("parquet").load("/lakehouse/silver/sales_transformed")
df_gold = df_silver.groupBy("order_date").agg(
    sum("sales_amount").alias("total_sales"),
    sum("quantity").alias("total_quantity")
)
df_gold.write.mode("overwrite").format("parquet").save("/lakehouse/gold/sales_enriched")
📊 Current Output Files
raw/sales.csv (Original CSV file)

bronze/sales (Parquet file)

silver/sales_transformed (Cleaned and enriched Parquet with sales_amount)

gold/sales_enriched (Aggregated output: total sales & quantity by date)
