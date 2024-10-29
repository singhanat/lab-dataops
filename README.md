# lab-dataops
LAB : DataOps

## Lab 0 : ออกแบบ Data pipeline และ test



## Lab 1 : Upload files

upload files ชื่อ track_small.csv และ track_large.csv เข้าสู่ Workspace เพื่อเป็น Data source ของ Data pipeline

โดย track_small.csv สำหรับการ test และ track_large.csv สำหรับ production

## Lab 2 : สร้าง Data Pipeline อย่างง่าย

สร้าง notebook ชื่อ pipeline ซึ่งเป็น ETL pipeline ที่ทำหน้าที่เปลี่ยนข้อมูลจาก UnitPrice ปัดค่าทศนิยมขึ้นเป็นจำนวนเต็ม โดยมี code ดังนี้
```python
import pandas as pd
import math

# file path
inputPath = "<YOUR_WORKSPACE_PATH>/track_small.csv"
outputPath = "<YOUR_WORKSPACE_PATH>/output_small.csv"

# Extract
tracks = pd.read_csv(inputPath)

# Transform
tracks["UnitPrice"] = tracks["UnitPrice"].apply(lambda x: math.ceil(x))
                             
# Load
tracks.to_csv(outputPath, index=False)
```
และสร้าง notebook ชื่อ test สำหรับทดสอบผลลัพธ์ของ pipeline ว่าถูกต้องตามหลักเกณฑ์ที่กำหนดไว้ในขั้นตอนการออกแบบหรือไม่ โดยมี code ดังนี้
```python
import pandas as pd
from datetime import datetime

# file path
inputPath = "<YOUR_WORKSPACE_PATH>/track_small.csv"
outputPath = "<YOUR_WORKSPACE_PATH>/output_small.csv"
testResultPath = "<YOUR_WORKSPACE_PATH>/test_result.txt"

# read files
tracksInput = pd.read_csv(inputPath)
tracksOutput = pd.read_csv(outputPath)

# open test result file
f = open(testResultPath, "a")

# write datetime
f.write(datetime.now().strftime("%d-%m-%Y %H:%M:%S") + '\n')

# Case 1
unitPriceType = tracksOutput.dtypes['UnitPrice']
if unitPriceType == 'int64':
    f.write("Case 1: Pass\n")
else:
    f.write("Case 1: Fail\n")

# Case 2
mergedTracks = pd.merge(tracksInput, tracksOutput, on='TrackId', suffixes=('_input', '_output'))
if (mergedTracks['UnitPrice_output'] - mergedTracks['UnitPrice_input'] < 1).all():
    f.write("Case 2: Pass\n")
else:
    f.write("Case 2: Fail\n")

# close test result file
f.close()
```

## Lab 3 : สร้าง github repository ชื่อ chinook-datapipeline สำหรับ data pipeline

## Lab 4 : เชื่อม databricks account กับ github account 

## Lab 5 : clone github repositoy ชื่อ chinook-datapipeline มาเป็น git folder ใน databricks workspace

## Lab 6 : ย้ายไฟล์ notebook เข้ามายัง git folder

## Lab 7 : commit และ push ไฟล์ใน git folder เข้ามายัง github repository

## Lab 8 : Configuration Management

เพื่อให้สามารถเปลี่ยน data source ใน notebook ชื่อ pipeline และ test ได้โดยไม่ต้อง hard code ที่อยู่ของไฟล์ไว้ใน notebook จึงแก้ไข code ให้ผ่านที่อยู่ไฟล์ (file path) จากไฟล์ pipeline.conf ที่อยู่ใน folder เดียวกับ notebook เสมอ

1. สร้างไฟล์ pipeline.conf
```conf
[DEFAULT]
INPUT_PATH=<YOUR_WORKSPACE_PATH>/track_small.csv
OUTPUT_PATH=<YOUR_WORKSPACE_PATH>/output_small.csv
TEST_RESULT_PATH=<YOUR_WORKSPACE_PATH>/test_result.csv
```
  
2. แก้ไข notebook ชื่อ pipeline
```python
import pandas as pd
import math
import os
import configparser

# Get the parent path of the notebook
notebook_path = dbutils.entry_point.getDbutils().notebook().getContext().notebookPath().get()
parent_path = os.path.dirname('/Workspace' + notebook_path)
os.chdir(parent_path)

# read config file
config = configparser.ConfigParser()
config.read('./pipeline.conf')
inputPath = config.get('DEFAULT', 'INPUT_PATH')
outputPath = config.get('DEFAULT', 'OUTPUT_PATH')

# Extract
tracks = pd.read_csv(inputPath)

# Transform
tracks["UnitPrice"] = tracks["UnitPrice"].apply(lambda x: math.ceil(x))
                             
# Load
tracks.to_csv(outputPath, index=False)
```

3. แก้ไข notebook ชื่อ test 
```python
import pandas as pd
import configparser
import os
from datetime import datetime

# Get the parent path of the notebook
notebook_path = dbutils.entry_point.getDbutils().notebook().getContext().notebookPath().get()
parent_path = os.path.dirname('/Workspace' + notebook_path)
os.chdir(parent_path)

# read config file
config = configparser.ConfigParser()
config.read('./pipeline.conf')
inputPath = config.get('DEFAULT', 'INPUT_PATH')
outputPath = config.get('DEFAULT', 'OUTPUT_PATH')
testResultPath = config.get('DEFAULT', 'TEST_RESULT_PATH')

# read files
tracksInput = pd.read_csv(inputPath)
tracksOutput = pd.read_csv(outputPath)

# open test result file
f = open(testResultPath, "a")

# write datetime
f.write(datetime.now().strftime("%d-%m-%Y %H:%M:%S") + '\n')

# Case 1
unitPriceType = tracksOutput.dtypes['UnitPrice']
if unitPriceType == 'int64':
    f.write("Case 1: Pass\n")
else:
    f.write("Case 1: Fail\n")

# Case 2
mergedTracks = pd.merge(tracksInput, tracksOutput, on='TrackId', suffixes=('_input', '_output'))
if (mergedTracks['UnitPrice_output'] - mergedTracks['UnitPrice_input'] < 1).all():
    f.write("Case 2: Pass\n")
else:
    f.write("Case 2: Fail\n")

# close test result file
f.close()
```
