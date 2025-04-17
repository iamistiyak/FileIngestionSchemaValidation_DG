# 🚀 Large File Ingestion and Processing Benchmark

This project explores efficient techniques for reading and processing a large CSV dataset using Python libraries like **Pandas**, **Dask**, and **Modin**. The file used is a sample of NYC Yellow Taxi trip data.

---

## 📋 Task

- **File Selection**: Take any CSV/text file of 2+ GB of your choice (Google Colab can be used).
- **File Reading**: Present the approach for reading the file.
- **File Reading Methods**: Try different methods (Dask, Modin, Ray, Pandas) and present your findings in terms of computational efficiency.
- **Basic Data Validation**: Perform basic validation on data columns (e.g., remove special characters and white spaces from column names).
- **Define Separator**: Define separator of the read and write files, column names in YAML format.
- **Schema Validation**: Validate the number of columns and column names of the ingested file with YAML.
- **Write File**: Write the file in a pipe-separated text file (`|`) in gzip format.
- **File Summary**: Generate a summary of the file, including:
  - Total number of rows
  - Total number of columns
  - File size

---

## 📌 Objectives

- Compare file reading performance using different Python libraries
- Perform basic data cleaning on column names
- Define schema using YAML and validate against the ingested file
- Export the cleaned data to a compressed format with a custom delimiter
- Generate a file summary, including row count, column count, and size

---

## 📂 Dataset

- **File**: `yellow-tripdata-2025-01.csv`
- **Source**: [NYC TLC Trip Data](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
- **Size**: ~375MB
- **Rows**: 3.47 million+
- **Columns**: 19

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

## 📚 Read the File Using Different Libraries


## Using Pandas:
```python
from google.colab import drive
import pandas as pd
import time
import os

# Mount Google Drive
drive.mount('/content/drive')

# Update this path to match your actual file location in Drive
file_path = '/content/drive/MyDrive/DataAnalysis/Internship/DataGlaciers/Week-6/yellow-tripdata-2025-01.csv'

# Check if file exists (optional but helpful for debugging)
if not os.path.exists(file_path):
  raise FileNotFoundError(f"File not found at: {file_path}")

start = time.time()
df_pandas = pd.read_csv(file_path)
print("Pandas read time:", time.time() - start)

```
---

## Using Dask:

```python

import dask.dataframe as dd
from google.colab import drive
import time
import os

#Mount Google Drive
drive.mount('/content/drive')

#Update this path to match your actual file location in Drive
file_path = '/content/drive/MyDrive/DataAnalysis/Internship/DataGlaciers/Week-6/yellow-tripdata-2025-01.csv'

#Check if file exists (optional but helpful for debugging)
if not os.path.exists(file_path):
    raise FileNotFoundError(f"File not found at: {file_path}")

start = time.time()
df_dask = dd.read_csv(file_path, assume_missing=True, blocksize="64MB")
df_dask.head()

print("Dask read time:", time.time() - start)

```
---

## Using Modin:

```python

import modin.pandas as mpd
import os
os.environ["MODIN_ENGINE"] = "ray"
from google.colab import drive
import time
import os

#Mount Google Drive
drive.mount('/content/drive')

#Update this path to match your actual file location in Drive
file_path = '/content/drive/MyDrive/DataAnalysis/Internship/DataGlaciers/Week-6/yellow-tripdata-2025-01.csv'

start = time.time()
df_modin = mpd.read_csv(file_path)
print("Modin read time:", time.time() - start)

```
---

## 🧹 Column Cleaning


#Column Name Cleaning (Validation Step)

#Stripping leading/trailing whitespace

#Replacing special characters with _ using regex


```python 
import re

def clean_columns(df):
    df.columns = [re.sub(r'\W+', '_', col.strip()) for col in df.columns]
    return df

df_clean = clean_columns(df_pandas.copy())  # Use Pandas version for simplicity

```
---

## 💾 Save Column Names

This snippet saves the column names and CSV separator into a schema.yaml file for reference or reuse.

```python

import yaml

schema = {
    "separator": ",",
    "columns": list(df_clean.columns)
}

with open("schema.yaml", "w") as f:
    yaml.dump(schema, f)

```
---<br>

## ✅ Validate Schema with YAML

```python
with open("schema.yaml") as f:
    schema_yaml = yaml.safe_load(f)

assert schema_yaml["columns"] == list(df_clean.columns), "Column names don't match"
assert df_clean.shape[1] == len(schema_yaml["columns"]), "Column count mismatch"


```
---

## 📦 Write File in Pipe-Separated Gzip Format


output_file = "output_file.txt.gz"
df_clean.to_csv(output_file, sep='|', index=False, compression='gzip')

```


```python 


import os

summary = {
    "total_rows": df_clean.shape[0],
    "total_columns": df_clean.shape[1],
    "file_size_MB": os.path.getsize(output_file) / (1024 * 1024)
}

print(summary)
```
---

## 📊 Summary

```python

{'total_rows': 3475226, 'total_columns': 19, 'file_size_MB': 62.119622230529785}

```



