
---

# **Amazon Stock Data Pipeline**

## **Project Overview**
This project implements an **end-to-end data pipeline** for analyzing **Amazon stock data** using **NiFi, HDFS, Hive, and PySpark**. The goal is to automate data collection, storage, processing, and analysis of **historical stock prices** from **Nasdaq**.

## **Pipeline Components**
The data pipeline follows these steps:
1. **NiFi** → Pulls historical Amazon stock data from **GitHub**.
2. **HDFS** → Stores the **raw data** for processing.
3. **Hive** → Structures data into **tables** for querying.
4. **PySpark** → Performs **data transformation and analysis**.

## **Dataset**
- **Source:** [NASDAQ](https://www.nasdaq.com/market-activity/stocks/amzn/historical)
- **GitHub Repository:** Amazon Stock Data
- **Key Features:**
  - **Trade Date:** The date of stock transactions.
  - **Close Price:** The stock's closing price.
  - **Volume:** The number of shares traded.
  - **Open, High, Low Prices:** Stock price movements within a trading day.

## **Implementation Steps**
### **1. Data Ingestion (NiFi)**
- Configured **NiFi** to pull stock data from the **GitHub repository**.
- Processed and transferred the data to **HDFS** for storage.

### **2. Data Storage (HDFS)**
- Raw stock data was ingested and stored in **HDFS**.

### **3. Data Processing (Hive)**
- Created a **Hive database** and external table for structured storage.
- Enabled **SQL querying** on stock data.

#### **Hive SQL Commands:**
```sql
CREATE DATABASE final_project;

USE final_project;

CREATE EXTERNAL TABLE amazon_stocks (
    trade_date STRING,
    close_last STRING,
    volume STRING,
    open_price STRING,
    high STRING,
    low STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/AmazonStocks'
TBLPROPERTIES ("skip.header.line.count"="1");
```

### **4. Data Analysis (PySpark)**
- Queried the Hive table using **PySpark**.
- Performed **aggregations and statistical calculations**.

#### **PySpark Code:**
```python
from pyspark.sql import SparkSession

# Create a Spark session with Hive support
spark = SparkSession.builder \
    .appName("ReadHiveTable") \
    .enableHiveSupport() \
    .getOrCreate()

# Load stock data from Hive table
df1 = spark.sql("SELECT * FROM final_project.amazon_stocks")
df1.show()

# Calculate sum of volume per trade date
df2 = spark.sql("SELECT trade_date, SUM(volume) FROM final_project.amazon_stocks GROUP BY trade_date")
df2.show()

# Calculate the average volume per trade date
df3 = spark.sql("SELECT trade_date, AVG(volume) FROM final_project.amazon_stocks GROUP BY trade_date")
df3.show()

# Find the maximum traded volume
df4 = spark.sql("SELECT MAX(volume) FROM final_project.amazon_stocks")
df4.show()

# Stop Spark session
spark.stop()
```

## **Challenges and Resolutions**
### **1. Issues with Data Flow Connection**
- **Problem:** NiFi could not connect to HDFS and Hive.
- **Cause:** Missing Hadoop configuration.
- **Solution:** Added correct Hadoop configuration resources in **NiFi**.

### **2. NiFi, Hadoop, and VM Crash**
- **Problem:** System crash due to **unchecked data ingestion**.
- **Cause:** Excessive file ingestion overloaded the system.
- **Solution:** Rebuilt the **VM** and ran the flow **once** to prevent excessive data ingestion.

### **3. Concurrent Execution Issues**
- **Problem:** Errors when running **NiFi** and **Hadoop** together.
- **Cause:** **Resource allocation conflicts** between both systems.
- **Solution:** Ran **one system at a time** to prevent crashes.

### **4. Errors in Table Creation and Queries**
- **Problem:** Hive table creation **syntax errors**.
- **Cause:** Incorrect table schema definitions.
- **Solution:** **Edited and corrected SQL syntax**.

## **Conclusion**
This project successfully implemented an **end-to-end pipeline** for processing **Amazon stock data**. Despite multiple system challenges, they were systematically **resolved** to ensure:
- **Efficient data storage**
- **Structured querying**
- **Data transformation using PySpark**

The pipeline showcases the integration of **NiFi, HDFS, Hive, and PySpark** for automated stock market data processing.

## **Future Improvements**
- **Automate data ingestion** using **stock market APIs**.
- **Implement real-time streaming** for stock price updates.
- **Enhance analytics** using machine learning models for stock trend predictions.

---
