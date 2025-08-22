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
./build/runtime_output_directory/mysql -u root --socket=/tmp/mysql.sock --port=3306
```

Then run the following SQL command:
```sql
INSTALL PLUGIN lineairdb SONAME 'ha_lineairdb_storage_engine.so';
```

## Benchmark

Setup Benchbase (first time only)
```bash
./bench/setup_benchbase.sh
```

Prepare Database
```bash
./build/runtime_output_directory/mysql -u root --socket=/tmp/mysql.sock --port=3306 < bench/reset.sql
```

Create Schema
```bash
java -jar bench/benchbase/benchbase-mysql/benchbase.jar -b ycsb -c bench/config/ycsb.xml --create=true --load=false --execute=false
```

Load Data
```bash
java -jar bench/benchbase/benchbase-mysql/benchbase.jar -b ycsb -c bench/config/ycsb.xml --create=false --load=true --execute=false
```

Execute Benchmark
```bash
java -jar bench/benchbase/benchbase-mysql/benchbase.jar -b ycsb -c bench/config/ycsb.xml --create=false --load=false --execute=true
```