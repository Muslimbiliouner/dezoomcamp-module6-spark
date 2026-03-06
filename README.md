# 🚕 Module 6 Homework — Apache Spark & PySpark

Homework submission for **Module 6: Batch Processing with Apache Spark** using NYC Yellow Taxi data for November 2025.

## 📦 Tech Stack

- Python 3.11
- PySpark 3.3.2
- Jupyter Notebook
- uv (package manager)
- Ubuntu / WSL2

## 📁 Project Structure
```
homework6/
├── homework6.ipynb          # Main notebook with all answers
├── taxi_zone_lookup.csv     # Zone lookup data
├── pyproject.toml
└── README.md
```

## 🚀 Setup & Run

### Prerequisites
```bash
sudo apt install openjdk-11-jdk -y
echo 'export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64' >> ~/.bashrc
source ~/.bashrc
```

### Install & Run
```bash
uv add pyspark jupyter
wget https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2025-11.parquet
wget https://d37ci6vzurychx.cloudfront.net/misc/taxi_zone_lookup.csv
uv run jupyter notebook
```

## 📊 Answers

| # | Question | Answer |
|---|----------|--------|
| Q1 | Spark version | **3.3.2** |
| Q2 | Avg parquet file size | **25MB** |
| Q3 | Trips on November 15 | **162,604** |
| Q4 | Longest trip (hours) | **90.6** |
| Q5 | Spark UI port | **4040** |
| Q6 | Least frequent pickup zone | **Governor's Island/Ellis Island/Liberty Island** |
