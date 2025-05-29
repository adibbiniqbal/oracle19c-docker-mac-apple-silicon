# 🐘 Oracle 19c on Mac with Docker (Apple Silicon Compatible)

This repository provides a clean and fast way to run **Oracle Database 19c** on your **Mac** using **Docker**, with full support for **Apple Silicon** (M1, M2, M3, M4). Perfect for developers who want Oracle locally without the complexity of a native install.

By using a containerized setup, you get a fully functional Oracle 19c database on your macOS machine — optimized for performance and compatibility with Apple’s ARM architecture.

---

## 📋 Table of Contents

- [Prerequisites](#prerequisites)
- [Installation Steps](#installation-steps)
  - [1. Download Oracle 19c Installer](#1-download-oracle-19c-installer)
  - [2. Clone Oracle Docker Images Repository](#2-clone-oracle-docker-images-repository)
  - [3. Prepare the Docker Build Environment](#3-prepare-the-docker-build-environment)
  - [4. Build the Oracle 19c Docker Image](#4-build-the-oracle-19c-docker-image)
  - [5. Set Up Oracle Data Directory](#5-set-up-oracle-data-directory)
  - [6. Run the Oracle 19c Container](#6-run-the-oracle-19c-container)
  - [7. Create a New Oracle User](#7-create-a-new-oracle-user)
- [Environment Configuration](#environment-configuration)
- [References](#references)

---

## ✅ Prerequisites

- **Mac** or **macOS** device with Apple Silicon (M1/M2/M3/M4)
- **[Docker Desktop](https://www.docker.com/products/docker-desktop/)** or **[OrbStack](https://orbstack.dev/)** installed and running
- An active **Oracle account** to download the installer

---

## 🛠️ Installation Steps

### 1. Download Oracle 19c Installer

Visit the [Oracle Database Software Downloads](https://www.oracle.com/database/technologies/oracle19c-linux-arm64-downloads.html) page and download:

- **File:** `LINUX.ARM64_1919000_db_home.zip`

> Ensure you have an Oracle account to access the download.

### 2. Clone Oracle Docker Images Repository

```bash
git clone https://github.com/oracle/docker-images.git
```

### 3. Prepare the Docker Build Environment

```bash
cd docker-images/OracleDatabase/SingleInstance/dockerfiles/19.3.0
mv ~/Downloads/LINUX.ARM64_1919000_db_home.zip .
```

### 4. Build the Oracle 19c Docker Image

```bash
cd ..
./buildContainerImage.sh -v 19.3.0 -e
```

> This builds the `oracle/database:19.3.0-ee` image.

### 5. Set Up Oracle Data Directory on macOS

```bash
sudo mkdir -p ~/Documents/opt/oracle-19c/oradata
sudo chmod -R 777 ~/Documents/opt/
sudo chown -R 54321:54321 ~/Documents/opt/
```

### 6. Run the Oracle 19c Container

```bash
docker run --name oracle19c \
  -p 1521:1521 \
  -e ORACLE_SID=orcl \
  -e ORACLE_PWD=OracleHomeUser1 \
  -v ~/Documents/opt/oracle-19c/oradata/:/opt/oracle/oradata \
  oracle/database:19.3.0-ee
```

### 7. Create a New Oracle User and Schema

Using [Oracle SQL Developer](https://www.oracle.com/database/sqldeveloper/) or any Oracle client (I would suggest [VS Code](https://code.visualstudio.com/) with the [Oracle SQL Developer Extension for VS Code](https://marketplace.visualstudio.com/items?itemName=Oracle.sql-developer)), connect to the database and execute the following SQL commands to create a new **user** (which also creates a schema with the same name):

> In Oracle, a **user** is the account you log in with, and a **schema** is the collection of database objects (like tables and views) owned by that user.


```sql
CREATE USER {USERNAME} IDENTIFIED BY {PASSWORD};
GRANT CREATE SESSION TO {USERNAME};
GRANT CREATE TABLE TO {USERNAME};
GRANT CREATE SEQUENCE TO {USERNAME};
GRANT UNLIMITED TABLESPACE TO {USERNAME};
GRANT CONNECT, RESOURCE TO {USERNAME};
GRANT CREATE VIEW TO {USERNAME};
ALTER USER {USERNAME} ACCOUNT UNLOCK;
ALTER USER {USERNAME} IDENTIFIED BY {PASSWORD};
```

> Replace `{USERNAME}` and `{PASSWORD}` with your preferred username and password.

> If you’d like to install Oracle’s official HR sample schema (for learning or testing), use the companion repository:
[oracle-hr-sample-schema](https://github.com/adibbiniqbal/oracle-hr-sample-schema)
This includes all SQL scripts needed to create the HR user, tables, sample data, and indexes.

---

## ⚙️ Environment Configuration

Set up your `.env` file like this:

```env
DB_CONNECTION=oracle
DB_HOST=localhost
DB_PORT=1521
DB_DATABASE=orclpdb1
DB_SERVICE_NAME=orclpdb1
DB_USERNAME=localuser
DB_PASSWORD=OracleHomeUser1
```

---

## 🧠 Why This Matters

- ✅ No need to install Oracle directly on macOS
- ✅ Fully works with Apple Silicon (ARM64)
- ✅ Perfect for testing, learning, and development

---

## 📚 References

- [Oracle Docker Images GitHub](https://github.com/oracle/docker-images)
- [Oracle 19c Software Download](https://www.oracle.com/database/technologies/oracle-database-software-downloads.html)
- [Running Oracle on M1 Macs with Docker](https://levelup.gitconnected.com/running-oracle-19c-on-docker-for-mac-with-apple-chip-262ba83ff26d)
