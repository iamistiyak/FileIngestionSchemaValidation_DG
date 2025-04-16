# 🚀 Large File Ingestion and Processing Benchmark

This project explores efficient techniques for reading and processing a large CSV dataset using Python libraries like **Pandas**, **Dask**, and **Modin**. The file used is a sample of NYC Yellow Taxi trip data.

---
<b>Task:</b> <br>
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
## Read the File Using Different Libraries

```python
# Using Pandas:

from google.colab import drive
import pandas as pd
import time
import os

# Mount Google Drive

# Read CSV and

drive.mount('/content/drive')

# Update this path to match your actual file location in Drive
file_path = '/content/drive/MyDrive/DataAnalysis/Internship/DataGlaciers/Week-6/yellow-tripdata-2025-01.csv'

# Check if file exists (optional but helpful for debugging)
if not os.path.exists(file_path):
  raise FileNotFoundError(f"File not found at: {file_path}")

start = time.time()
df_pandas = pd.read_csv(file_path)
print("Pandas read time:", time.time() - start)

---

```python
#Using Dask
import dask.dataframe as dd
from google.colab import drive
import time
import os

# Mount Google Drive

# Read CSV and time itdrive.mount('/content/drive')

# Update this path to match your actual file location in Drive
file_path = '/content/drive/MyDrive/DataAnalysis/Internship/DataGlaciers/Week-6/yellow-tripdata-2025-01.csv'

# Check if file exists (optional but helpful for debugging)
if not os.path.exists(file_path):
    raise FileNotFoundError(f"File not found at: {file_path}")


start = time.time()
df_dask = dd.read_csv(file_path, assume_missing=True, blocksize="64MB")
df_dask.head()

print("Dask read time:", time.time() - start)


---

```python
#Using Modin
import modin.pandas as mpd
import os
os.environ["MODIN_ENGINE"] = "ray"
from google.colab import drive
import time
import os

# Mount Google Drive

# Read CSV and time itdrive.mount('/content/drive')

# Update this path to match your actual file location in Drive
file_path = '/content/drive/MyDrive/DataAnalysis/Internship/DataGlaciers/Week-6/yellow-tripdata-2025-01.csv'


start = time.time()
df_modin = mpd.read_csv(file_path)
print("Modin read time:", time.time() - start)

## 🧹 Column Cleaning

#Clean Column Names (Validation Step)

- Stripping leading/trailing whitespace
- Replacing special characters with `_` using regex

```python
import re

def clean_columns(df):
    df.columns = [re.sub(r'\W+', '_', col.strip()) for col in df.columns]
    return df

df_clean = clean_columns(df_pandas.copy())  # use Pandas version for simplicity

## Save column name 

#This snippet saves the column names and CSV separator into a schema.yaml file for reference or reuse

```python
import yaml

schema = {
    "separator": ",",
    "columns": list(df_clean.columns)
}

with open("schema.yaml", "w") as f:
    yaml.dump(schema, f)

## Validate Schema with YAML

```python

with open("schema.yaml") as f:
    schema_yaml = yaml.safe_load(f)

assert schema_yaml["columns"] == list(df_clean.columns), "Column names don't match"
assert df_clean.shape[1] == len(schema_yaml["columns"]), "Column count mismatch"

## Loads the saved schema and checks if the dataframe's columns match

```python

with open("schema.yaml") as f:
    schema_yaml = yaml.safe_load(f)

assert schema_yaml["columns"] == list(df_clean.columns), "Column names don't match"
assert df_clean.shape[1] == len(schema_yaml["columns"]), "Column count mismatch"

## Write File in Pipe-Separated Gz Format

```python
output_file = "output_file.txt.gz"
df_clean.to_csv(output_file, sep='|', index=False, compression='gzip')


