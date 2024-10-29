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
inputPath = "/Workspace/Users/singhanat.rer@kmutt.ac.th/track_small.csv"
outputPath = "/Workspace/Users/singhanat.rer@kmutt.ac.th/output_small.csv"
testResultPath = "/Workspace/Users/singhanat.rer@kmutt.ac.th/test_result.txt"

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

## Lab 3 : สร้าง github repository สำหรับ data pipeline
