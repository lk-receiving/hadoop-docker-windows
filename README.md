# Hadoop Lab README - Spring 2026

This project provides a containerized multi-node Hadoop cluster for **Distributed Databases**. It maps your local Windows assignment folder directly into the NameNode for easy development.

---

# Hadoop Distributed Cluster (Docker)

## 📋 Prerequisites & Requirements
This project is designed to run on Windows using **Docker Desktop**. Before running any commands, ensure your machine meets the following requirements:

### 1. Enable WSL2 (Windows Subsystem for Linux)
Docker on Windows performs best when using the WSL2 backend.
- **Check:** Open PowerShell and run `wsl --status`. 
- **Action:** If not installed, run `wsl --install` and restart your computer.

### 2. Install Docker Desktop
- Download and install [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/).
- **Important Setting:** Open Docker Desktop Settings -> **General** -> Ensure "Use the WSL 2 based engine" is checked.
- **Resources:** Ensure Docker is allocated at least **4GB of RAM** (Settings -> Resources) to handle the 5 Hadoop daemons.

### 3. Have Docker Running
The `docker-compose` commands will only work if the Docker Desktop engine is currently active in your system tray (look for the whale icon).

---

## 🛠️ First-Time Setup

Before you run the cluster for the first time, follow these steps to ensure your files are organized correctly.

### 1. Folder Structure
Ensure your lab folder is organized exactly like this. Docker needs all these files in the **same directory** to connect them:

~~~text
/Hadoop-Lab-Folder
  ├── docker-compose.yml   # The cluster definition
  ├── hadoop.env           # Configuration variables
  └── /assignments         # Your Java files and text data
~~~



### 2. Configure Your Environment (`hadoop.env`)
The `hadoop.env` file acts as the "Settings" menu for your cluster. Open it in a text editor (like Notepad++ or VS Code) and ensure it contains the basic Hadoop variables provided in the lab instructions. 

> **Note:** Do not change the variable names, as the containers look for these specific keys to start up.

### 3. Verify Volume Mapping
Open `docker-compose.yml` and find the `volumes:` line under `namenode`. 
- Make sure the path on the left matches your actual Windows folder path.
- **Example:** `"C:/Users/YourName/Documents/Hadoop:/assignments"`

### 4. The "Health Check"
Once you run `docker-compose up -d`, wait about 30-60 seconds. Then run:
~~~powershell
docker-compose ps
~~~
If any service says `Exit` or `Restarting` instead of `Up (healthy)`, check your `hadoop.env` for typos.

---

## 🔍 Troubleshooting Common Issues

| Error / Symptom | Likely Cause | 5-Second Fix |
| :--- | :--- | :--- |
| **"Port already allocated"** | Another program (or an old Docker session) is using port 8088 or 9870. | Run `docker-compose down`, then `docker-compose up -d`. |
| **"Name node is in safe mode"** | HDFS is protecting itself during startup or disk space is low. | Run `hdfs dfsadmin -safemode leave` inside NameNode bash. |
| **"Connection Refused" (Web UI)** | The Java service inside the container is still booting up. | Wait 60 seconds. Hadoop is heavy and takes time to initialize the JVM. |
| **"Output directory already exists"** | MapReduce jobs refuse to overwrite old data to prevent accidental loss. | Run `hdfs dfs -rm -r /output` before running your job again. |
| **Empty "Assignments" folder** | The path in your `docker-compose.yml` volume mapping is incorrect. | Fix the Windows path on the left side of the `:` in your YAML file. |

---

## 🏗️ Architecture
This lab setup mimics a real-world production cluster by separating services into distinct containers (daemons):

| Service | Daemon Type | Function |
| :--- | :--- | :--- |
| **NameNode** | Master (HDFS) | Manages the file system metadata. |
| **DataNode** | Worker (HDFS) | Stores the actual data blocks. |
| **ResourceManager** | Master (YARN) | Orchestrates job scheduling and resources. |
| **NodeManager** | Worker (YARN) | Executes the tasks on the worker node. |
| **HistoryServer** | Archive (YARN) | Keeps a record of finished jobs and logs. |

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
- **Local Sync Folder:** `C:\Users\my_user\Distributed Databases\Hadoop`
- **Internal Container Path:** `/assignments`