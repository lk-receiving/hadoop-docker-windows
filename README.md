# Hadoop Lab README - Spring 2026

This project provides a containerized multi-node Hadoop cluster for **Distributed Databases**. It maps your local Windows assignment folder directly into the NameNode for easy development.

---

## 🚀 Quick Start Commands

### Cluster Management
* **Start the cluster:**
``` powershell
docker-compose up -d
```

* **Stop the cluster (Pause):**
``` powershell
docker-compose stop
```

* **Shut down and remove containers:**
``` powershell
docker-compose down
```

### Accessing the Environment
* **Open a Bash shell in the NameNode:**
``` powershell
docker exec -it namenode bash
```

---

## 📂 HDFS File Operations
*Run these inside the NameNode bash terminal.*

* **Create Input Directory:**
``` bash
hdfs dfs -mkdir -p /input
```

* **Upload from Synced Windows Folder:**
``` bash
hdfs dfs -put /assignments/hello_world.txt /input/
```

* **View Output Results:**
``` bash
hdfs dfs -cat /output/part-r-00000
```

* **Reset Output (Must do before re-running):**
``` bash
hdfs dfs -rm -r /output
```

---

## 🛠️ Execution & Monitoring

### Running your JAR
``` bash
hadoop jar /assignments/wordcount.jar com.hadoop.example.WordCount /input /output
```

### Web UIs
- **HDFS File Browser:** [http://localhost:9870](http://localhost:9870)
- **YARN Job Monitor:** [http://localhost:8088](http://localhost:8088)

### Monitoring UIs (Access via Windows Browser)
- **HDFS NameNode:** http://localhost:9870
- **YARN ResourceManager:** http://localhost:8088
- **Application History:** http://localhost:8188/applicationhistory

---

## ⚙️ Configuration
- **Local Sync Folder:** `C:\Users\lkim_default\acc_assignments\2026_Spring\Distributed Databases\Hadoop`
- **Internal Container Path:** `/assignments`