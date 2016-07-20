# HDFS/Spark/Hive Local Development Setup

This repository provides the installation instructions for
* Hadoop 2.7.2,
* Spark 1.6.2 and
* Hive 2.1.0
for development on a local machine. SANSA stack developers use this environment setup for development and debugging. As we run our production code in docker containers, docker-driven CI is a part of our delivery cycle as well.

Our developers use Ubuntu LTS and organize their work inside dedicated ~/Workspace directory. If you do not know where to install your HDFS/Spark/Hive setup, then put it into ~/Workspace/hadoop-spark-hive directory. After the installation the directory will be contains the following:
```
├── derby.log
├── data
├── Makefile
├── metastore_db
├── src
└── tools
    ├── apache-hive-2.1.0-bin
    ├── hadoop-2.7.2
    └── spark-1.6.2-bin-without-hadoop
```
* Makefile. Used for running various tasks such as starting up the hadoop/spark/hive, running interactive shells for spark/hive etc.
* src/ directory. Contains git repository with various spark applications.
* tools/ directory. Contains hadoop/spark/hive binaries.
* derby.log and metastore_db is created by Hive automatically when running the Hive server.
* data/ directory contains HDFS data and spark-rdd data.

## Usage

Clone this repository into the folder where you want to create your HDFS/Spark/Hive setup:
```
mkdir -p ~/Workspace/hadoop-spark-hive && cd ~/Workspace/hadoop-spark-hive
git clone https://github.com/earthquakesan/hdfs-spark-hive-dev-setup ./
```

### Download HDFS/Spark/Hive binaries

```
make download
```

After this step you should have tools/ folder with the following structure:
```
└── tools
    ├── apache-hive-2.1.0-bin
    ├── hadoop-2.7.2
    └── spark-1.6.2-bin-without-hadoop
```

### Configure HDFS/Spark/Hive
```
make configure
```

### Start HDFS
Start hadoop DFS (distributed file system), basically 1 namenode and 1 datanode:
```
make start_hadoop
```

Open your browser and go to localhost:50070. If you can open the page and see 1 datanode registered on your namenode, then hadoop setup is finished.

### Start Spark
Start local Spark cluster:
```
make start_spark
```

Open your browser and go to localhost:8080. If you can open the page and see 1 spark-worker registered with spark-master, then spark setup is finished.

### Start Hive
Run the Hive server (it will occupy the terminal session, providing server logs to it):
```
make start_hive_server
```

Start beeline client to connect to the Hive server (you might not be able to connect if you are too fast, the Hive server takes time to start up):
```
make start_hive_beeline_client
```

Execute some queries to see if the Hive server works properly:
```
CREATE TABLE pokes (foo INT, bar STRING);
LOAD DATA LOCAL INPATH './tools/apache-hive-2.1.0-bin/examples/files/kv1.txt' OVERWRITE INTO TABLE pokes;
DESCRIBE pokes;
```

## Misc

### Adding sample data to Hive

Assuming that you have hadoop/spark/hive_server running, start the beeline client:
```
make start_hive_beeline_client
```

Then load the sample data as follows:
```
CREATE TABLE pokes (foo INT, bar STRING);
LOAD DATA LOCAL INPATH './tools/apache-hive-2.1.0-bin/examples/files/kv1.txt' OVERWRITE INTO TABLE pokes;
```

### Stopping HDFS/Spark/Hive
To stop HDFS:
```
make stop_hadoop
```

To stop Spark:
```
make stop_spark
```

To stop Hive you need to open terminal session, CTRL+Z and then kill the process by its pid:
```
kill -9 pid
```

### How to connect to HIVE with JDBC
* [Hive JDBC Clients](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-JDBC)
