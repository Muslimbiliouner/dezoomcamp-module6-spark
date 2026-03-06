# ❓ FAQ — Module 6: Batch Processing with Apache Spark

Frequently asked questions based on completing the Module 6 homework of the Data Engineering Zoomcamp.

---

## 🔧 Setup & Installation

### Q: Why does `TypeError: 'JavaPackage' object is not callable` appear when creating a SparkSession?

**A:** This error occurs because `JAVA_HOME` is not detected by the Jupyter Notebook kernel, even if Java is already installed on the system.

**Fix:** Set `JAVA_HOME` manually in the **first cell** of your Jupyter Notebook, before importing PySpark:

```python
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-11-openjdk-amd64"
os.environ["PATH"] = os.environ["JAVA_HOME"] + "/bin:" + os.environ["PATH"]
```

For a permanent fix, run this in your terminal:
```bash
echo 'export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64' >> ~/.bashrc
source ~/.bashrc
```

---

### Q: Why does `Method sql([class java.lang.String, class [Ljava.lang.Object;]) does not exist` appear?

**A:** This is caused by an incompatibility between the installed PySpark version and the running Spark version (3.3.2). The newer `spark.sql()` method signature is not available in this version.

**Fix:** Use the **DataFrame API** instead of SQL queries:

```python
# ❌ Avoid this (broken on Spark 3.3.2)
spark.sql("SELECT COUNT(*) FROM yellow_trips WHERE DATE(tpep_pickup_datetime) = '2025-11-15'")

# ✅ Use this instead
from pyspark.sql import functions as F
df.filter(F.to_date(F.col("tpep_pickup_datetime")) == "2025-11-15").count()
```

---

### Q: How do I install PySpark using `uv`?

**A:**
```bash
uv init homework6
cd homework6
uv add pyspark jupyter
uv run jupyter notebook
```

---

### Q: Which Java version is recommended for PySpark?

**A:** Use **Java 11**. Java 17+ may cause compatibility issues with certain versions of Spark.

```bash
sudo apt install openjdk-11-jdk -y
java -version  # Verify installation
```

---

## 🚀 Spark Session

### Q: How do I create a local Spark Session?

**A:**
```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .master("local[*]") \
    .appName("homework6") \
    .getOrCreate()

print(spark.version)  # Output: 3.3.2
```

`local[*]` tells Spark to use all available CPU cores on your machine.

---

### Q: On which port does the Spark UI run?

**A:** Spark UI runs on **port 4040** by default.
Open it in your browser at: `http://localhost:4040`

> ⚠️ **WSL2 users:** The Spark UI is only accessible while the Spark session is active. Make sure your Jupyter Notebook is still running when you open the browser. If `localhost:4040` is unreachable, check your WSL IP with `hostname -I` and use that IP address instead.

---

## 📁 Data & Parquet

### Q: What is the average file size after repartitioning to 4 partitions?

**A:** After reading `yellow_tripdata_2025-11.parquet` (4,181,444 rows) and repartitioning to 4:

```
part-00000: 26.36 MB
part-00001: 26.35 MB
part-00002: 26.36 MB
part-00003: 26.37 MB
Average  : ~26.36 MB → Closest answer: 25MB
```

---

### Q: Why is my trip count slightly different from the answer choices in Q3?

**A:** The actual output is `160,996` trips on November 15th, while the closest answer choice is `162,604`. This small difference is likely due to a dataset version difference from when the homework was created. Always select the **closest matching answer** to your output.

---

## 🔍 Queries & Analysis

### Q: How do I count trips on a specific date?

**A:**
```python
from pyspark.sql import functions as F

count = df.filter(
    F.to_date(F.col("tpep_pickup_datetime")) == "2025-11-15"
).count()

print(f"Total trips: {count:,}")
```

---

### Q: How do I calculate the longest trip duration in hours?

**A:**
```python
result = df.select(
    F.round(
        F.max(
            (F.unix_timestamp("tpep_dropoff_datetime") -
             F.unix_timestamp("tpep_pickup_datetime")) / 3600
        ), 2
    ).alias("max_hours")
)
result.show()  # Output: 90.65 hours
```

---

### Q: How do I JOIN the trip DataFrame with the zone lookup data?

**A:**
```python
zones = spark.read.option("header", "true").csv("taxi_zone_lookup.csv")

result = df.groupBy("PULocationID").count() \
    .join(
        zones.withColumnRenamed("LocationID", "PULocationID"),
        on="PULocationID"
    ) \
    .orderBy("count") \
    .select("Zone", "count")

result.show(truncate=False)
```

---

## 📊 Homework Answer Summary

| # | Question | Answer |
|---|----------|--------|
| Q1 | Spark version | **3.3.2** |
| Q2 | Avg parquet file size | **25MB** |
| Q3 | Trips on November 15 | **162,604** |
| Q4 | Longest trip (hours) | **90.6** |
| Q5 | Spark UI port | **4040** |
| Q6 | Least frequent pickup zone | **Governor's Island/Ellis Island/Liberty Island** |

---

## 🔗 References

- [Data Engineering Zoomcamp](https://github.com/DataTalksClub/data-engineering-zoomcamp/)
- [PySpark Documentation](https://spark.apache.org/docs/latest/api/python/)
- [NYC TLC Trip Record Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
- [uv Package Manager](https://github.com/astral-sh/uv)
