# lab-dataops
LAB : DataOps

## Lab 0 : ออกแบบ Data pipeline และ test



## Lab 1 : Upload files

upload files ชื่อ track_small.csv และ track_large.csv เข้าสู่ Workspace เพื่อเป็น Data source ของ Data pipeline

โดย track_small.csv สำหรับการ test และ track_large.csv สำหรับ production

## Lab 2 : สร้าง Data Pipeline อย่างง่าย

สร้าง Notebook ชื่อ pipeline ซึ่งเป็น ETL pipeline ที่ทำหน้าที่เปลี่ยนข้อมูลจาก UnitPrice ปัดค่าทศนิยมขึ้นเป็นจำนวนเต็ม โดยมี code ดังนี้
```
# Databricks notebook source
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

## Lab 3 : 
