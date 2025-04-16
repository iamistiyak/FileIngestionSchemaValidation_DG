# 🚀 Large File Ingestion and Processing Benchmark

This project explores efficient techniques for reading and processing a large CSV dataset using Python libraries like **Pandas**, **Dask**, and **Modin**. The file used is a sample of NYC Yellow Taxi trip data.

---
<b>Task:<b> <br>
• Take any csv/text file of 2+ GB of your choice. --- (You can do this assignment on Google colab)

•  Read the file ( Present approach of reading the file )

•  Try different methods of file reading eg: Dask, Modin, Ray, pandas and present your findings in term of computational efficiency

•  Perform basic validation on data columns : eg: remove special character , white spaces from the col name

•  Define separator of read and write file, column name in YAML

• Validate number of columns and column name of ingested file with YAML.

• Write the file in pipe separated text file (|) in gz format.

• Create a summary of the file:

- Total number of rows,

- total number of columns

- file size

---

## 📌 Objectives

- Compare file reading performance using different Python libraries
- Perform basic data cleaning on column names
- Define schema using YAML and validate against the ingested file
- Export the cleaned data to a compressed format with custom delimiter
- Generate file summary including row count, column count, and size

---

## 📂 Dataset

- 📁 File: `yellow-tripdata-2025-01.csv`
- 📦 Source: [NYC TLC Trip Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
- 🧮 Size: ~375MB
- 🔢 Rows: 3.47 million+
- 📊 Columns: 19

---

## ⚙️ Technologies Used

- `pandas`
- `dask`
- `modin` (with `ray`)
- `pyyaml`
- `gzip`
- `Google Colab` for execution

---

## 🚀 File Reading Benchmark

| **Library** | **Read Time (s)** | **Notes** |
|-------------|-------------------|-----------|
| Pandas      | 16.58             | Simple and reliable, but slower |
| Dask        | 2.67              | Fastest using lazy and parallel evaluation |
| Modin       | 38.72             | Overhead due to Ray + Colab's memory limits |

> ✅ **Dask** outperformed the rest for large-scale ingestion in this setup.

---

## 🧹 Column Cleaning

Cleaned column names by:
- Stripping leading/trailing whitespace
- Replacing special characters with `_` using regex

```python
def clean_columns(df):
    df.columns = [re.sub(r'\W+', '_', col.strip()) for col in df.columns]
    return df
