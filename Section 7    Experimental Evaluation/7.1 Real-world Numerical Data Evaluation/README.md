# Replication of Figures 9, 10–11 & 13 – Encoding and Compression Performance in Apache IoTDB

This repository contains a partial reproduction of experimental results from the paper:  
***Time Series Data Encoding for Efficient Storage: A Comparative Analysis in Apache IoTDB***  
(https://www.vldb.org/pvldb/vol15/p2148-song.pdf)

#### Target Figures: Figure 9, Figure 10–11, and Figure 13

The reproduction uses Apache IoTDB v1.0.0 (based on the authors' encoding-exp branch: https://github.com/apache/iotdb/tree/research/encoding-exp) along with slightly modified versions of the original experiment scripts from the authors’ repository (https://github.com/xjz17/iotdb/tree/TSEncoding). These modifications were necessary to ensure compatibility with the current runtime environment and folder structure.
This reproduction was performed using Python 3.11 and Java 11.

Due to implementation updates in newer IoTDB versions and the unavailability of the full original datasets used in the paper (only partial versions were provided in the authors’ repository), the reproduced results differ from those reported in the publication — this is further discussed in the accompanying report.

## Repository Structure
```
📁 iotdb         			  # IoTDB v1.0.0 binaries used for Python experiments
📁 Section 7 Experimental Evaluation/7.1 Real-world Numerical Data Evaluation/
   ├── 📁 Ingestion/          # Numerical datasets provided in the authors repository (INT32/INT64/FLOAT/DOUBLE)/n
   ├── Ratio_expr.py          # Compression-ratio experiment script
   ├── result_ratio_vis.py    # Boxplot visualization for Figure 9
   ├── expr.py                # Ingestion/Select-time experiment script
   ├── result_time_vis.py     # Boxplot visualization for Figures 10–11
   ├── 📁 Datasets/           # Reorganized folder structure with the same datasets required for Java tests (for Figure 13)
   ├── new_average.py         # Aggregation + normalization for radar plot (for Figure 13)
   ├── radar_draw.py          # Radar chart generation for Figure 13
   ├── result csv Files       # Generated intermediate & final results data files
   └── png Files              # Generated graphs/plots
```

## Execution Workflow (Step-by-Step)

Start IoTDB by running the iotdb/sib/start-standalone.sh

### Figure 9 — Compression ratio over real-world datasets
Dataset Source: 7.1 Real-world Numerical Data Evaluation/Ingestion/<DATA_TYPE>/*.csv

1. Run compression ratio script: *Ratio_expr.py*  
→ Produces *result_compression_ratio.csv*  
2. Plot results using the script: *result_ratio_vis.py*  
→ Output: Boxplot for Figure 9  

### Figures 10–11 — Insert Time & Select Time Evaluation
Same dataset directory as above.

1. Run ingestion timing script: *expr.py*  
→ Produces *result_ingestion.csv*  
2. Plot results using the script: *result_time_vis.py*  
→ Output: Two boxplots replicating Figures 10 & 11  

### Figure 13 — Trade-off Between Time and Compression Ratio
This part uses Java benchmarks from the research encoding branch of iotdb repository: https://github.com/apache/iotdb/tree/research/encoding-exp
Clone this branch and build the project using Maven.
In the instructions below, **<iotdb-repo>** refers to the local path of this cloned repository.

1. Dataset:
Copy the included Datasets folder to IoTDB’s expected location:
```
<iotdb-repo>/tsfile/src/test/java/org/apache/iotdb/tsfile/encoding/decoder/
```

Ensure folder structure is:
```
tsfile/
└── Datasets/
    └── Real-World/
        └── Numerical/
            ├── INT32/data/*.csv
            ├── INT64/data/*.csv
            ├── FLOAT/data/*.csv
            └── DOUBLE/data/*.csv
```
2. Java Benchmark Execution:
Replace EncodeTest.java in:
```
<iotdb-repo>/tsfile/src/test/java/org/apache/iotdb/tsfile/encoding/decoder/
```
with the modified/updated version included in this repository.

3. Run benchmark by using the command:
```
mvn -pl tsfile -Dtest=EncodeTest test
```
4. Copy generated file: *Real_Numerical_result.csv* in 
```
<iotdb-repo>/tsfile/
```
back into this repository.

6. Run *new_average.py* to create the processed csv data files required for plotting the Radar charts.

7. Run *radar_draw.py* to generate the Radar charts replicating Figure 13, this script uses the csv files created in the previous steps.
