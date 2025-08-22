# LineairDB MySQL Custom Storage Engine

## Quick Start

### Build

```bash
git clone --recursive https://github.com/Noxy3301/LineairDB-SE.git
cd LineairDB-SE
./build.sh
```

### Initialization

#### Start MySQL Server

```bash
cd build
./runtime_output_directory/mysqld --initialize-insecure --user=$USER --datadir=./data
./runtime_output_directory/mysqld --datadir=./data --socket=/tmp/mysql.sock --port=3306 &
```

#### Connect to MySQL and Install LineairDB Plugin

```bash
./runtime_output_directory/mysql -u root --socket=/tmp/mysql.sock --port=3306
```

Then run the following SQL command:
```sql
INSTALL PLUGIN lineairdb SONAME 'ha_lineairdb_storage_engine.so';
```

#### Restart MySQL with LineairDB as default storage engine
```bash
ps aux | grep "runtime_output_directory/mysqld" | grep -v grep | awk '{print $2}' | xargs -r kill -9
./runtime_output_directory/mysqld --datadir=./data --socket=/tmp/mysql.sock --port=3306 --default-storage-engine=lineairdb &
```

## Benchmark

> [!NOTE]
> For detailed benchmarking, see the `bench/` directory. 
> The commands below are for quick testing.

Setup Benchbase (first time only)
```bash
cd ..
./bench/setup_benchbase.sh
```

Prepare Database
```bash
./build/runtime_output_directory/mysql -u root --socket=/tmp/mysql.sock --port=3306 < bench/reset.sql
```

Create Schema
```bash
cd bench/benchbase/benchbase-mysql
java -jar benchbase.jar -b ycsb -c ../../config/ycsb.xml --create=true --load=false --execute=false
```

Load Data
```bash
java -jar benchbase.jar -b ycsb -c ../../config/ycsb.xml --create=false --load=true --execute=false
```

Execute Benchmark
```bash
java -jar benchbase.jar -b ycsb -c ../../config/ycsb.xml --create=false --load=false --execute=true
```

<details>

<summary>Sample Output</summary>

```bash
noxy@XG6326-2U:~/work/LineairDB-SE/bench/benchbase/benchbase-mysql$ java -jar benchbase.jar -b ycsb -c ../../config/ycsb.xml --create=false --load=false --execute=true
[INFO ] 2025-08-22 18:29:18,129 [main]  com.oltpbenchmark.DBWorkload main - ======================================================================

Benchmark:              YCSB {com.oltpbenchmark.benchmarks.ycsb.YCSBBenchmark}
Configuration:          ../../config/ycsb.xml
Type:                   MYSQL
Driver:                 com.mysql.cj.jdbc.Driver
URL:                    jdbc:mysql://localhost:3306/benchbase?rewriteBatchedStatements=true&sslMode=DISABLED
Isolation:              TRANSACTION_SERIALIZABLE
Batch Size:             128
Scale Factor:           1.0
Terminals:              1
New Connection Per Txn: false

[INFO ] 2025-08-22 18:29:18,131 [main]  com.oltpbenchmark.DBWorkload main - ======================================================================
[INFO ] 2025-08-22 18:29:18,407 [main]  com.oltpbenchmark.DBWorkload runWorkload - Creating 1 virtual terminals...
[INFO ] 2025-08-22 18:29:18,560 [main]  com.oltpbenchmark.DBWorkload runWorkload - Launching the YCSB Benchmark with 1 Phase...
[INFO ] 2025-08-22 18:29:18,567 [main]  com.oltpbenchmark.ThreadBench runRateLimitedMultiPhase - PHASE START :: [Workload=YCSB] [Serial=false] [Time=60] [WarmupTime=0] [Rate=10000.0] [Arrival=REGULAR] [Ratios=[50.0, 5.0, 15.0, 10.0, 10.0, 10.0]] [ActiveWorkers=1]
[INFO ] 2025-08-22 18:29:18,567 [main]  com.oltpbenchmark.ThreadBench runRateLimitedMultiPhase - MEASURE :: Warmup complete, starting measurements.
[INFO ] 2025-08-22 18:30:18,570 [main]  com.oltpbenchmark.ThreadBench runRateLimitedMultiPhase - TERMINATE :: Waiting for all terminals to finish ..
[INFO ] 2025-08-22 18:30:18,594 [Thread-1]  com.oltpbenchmark.ThreadBench run - Starting WatchDogThread
[INFO ] 2025-08-22 18:30:18,600 [main]  com.oltpbenchmark.DBWorkload runWorkload - ======================================================================
[INFO ] 2025-08-22 18:30:18,600 [main]  com.oltpbenchmark.DBWorkload runWorkload - Rate limited reqs/s: Results(nanoSeconds=60000043004, measuredRequests=736) = 12.26665787474408 requests/sec (throughput), 12.299991184186318 requests/sec (goodput)
[INFO ] 2025-08-22 18:30:18,717 [main]  com.oltpbenchmark.DBWorkload writeOutputs - Output Raw data into file: ycsb_2025-08-22_18-30-18.raw.csv
[INFO ] 2025-08-22 18:30:18,772 [main]  com.oltpbenchmark.DBWorkload writeOutputs - Output samples into file: ycsb_2025-08-22_18-30-18.samples.csv
[INFO ] 2025-08-22 18:30:18,785 [main]  com.oltpbenchmark.DBWorkload writeOutputs - Output summary data into file: ycsb_2025-08-22_18-30-18.summary.json
[INFO ] 2025-08-22 18:30:18,800 [main]  com.oltpbenchmark.DBWorkload writeOutputs - Output DBMS parameters into file: ycsb_2025-08-22_18-30-18.params.json
[INFO ] 2025-08-22 18:30:18,818 [main]  com.oltpbenchmark.DBWorkload writeOutputs - Output DBMS metrics into file: ycsb_2025-08-22_18-30-18.metrics.json
[INFO ] 2025-08-22 18:30:18,824 [main]  com.oltpbenchmark.DBWorkload writeOutputs - Output benchmark config into file: ycsb_2025-08-22_18-30-18.config.xml
[INFO ] 2025-08-22 18:30:18,861 [main]  com.oltpbenchmark.DBWorkload writeOutputs - Output results into file: ycsb_2025-08-22_18-30-18.results.csv with window size 5
[INFO ] 2025-08-22 18:30:18,875 [main]  com.oltpbenchmark.DBWorkload writeHistograms - ======================================================================
[INFO ] 2025-08-22 18:30:18,875 [main]  com.oltpbenchmark.DBWorkload writeHistograms - Workload Histograms:

Completed Transactions:
com.oltpbenchmark.benchmarks.ycsb.procedures.ReadRecord/01                       [ 362] ********************************************************************************
com.oltpbenchmark.benchmarks.ycsb.procedures.InsertRecord/02                     [  34] *******
com.oltpbenchmark.benchmarks.ycsb.procedures.ScanRecord/03                       [ 111] ************************
com.oltpbenchmark.benchmarks.ycsb.procedures.UpdateRecord/04                     [  68] ***************
com.oltpbenchmark.benchmarks.ycsb.procedures.DeleteRecord/05                     [  88] *******************
com.oltpbenchmark.benchmarks.ycsb.procedures.ReadModifyWriteRecord/06            [  75] ****************

Aborted Transactions:
<EMPTY>

Rejected Transactions (Server Retry):
<EMPTY>

Rejected Transactions (Retry Different):
<EMPTY>

Unexpected SQL Errors:
<EMPTY>

Unknown Status Transactions:
<EMPTY>


[INFO ] 2025-08-22 18:30:18,876 [main]  com.oltpbenchmark.DBWorkload writeHistograms - ======================================================================
```

</details>