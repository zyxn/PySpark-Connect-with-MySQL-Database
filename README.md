# PySpark Connect MySQL - Don't Forget to Star this Repo!!!!!

## Requirements

### Azure Virtual Machine (specification and configuration):
- 2 vCPUs and 8 Gig of RAM for PySpark job
- Linux (Ubuntu Server 22.04 LTS x64 Gen2) 
- Auth type: password
- Inbound Port: SSH (22)

### Working Environment
Connect SSH from VM `ssh {username}@{public_ip}` then input the password

## Installation Guides

- **Python Installation Guide:** [Install Python 3.8 on Ubuntu Linux](https://www.linuxcapable.com/install-python-3-8-on-ubuntu-linux/)

## Create Python Virtual Environment
`sudo apt-get install python3-pip`
`sudo pip3 install virtualenv`
`virtualenv name_of_the_project -p python3.8`
`source {your env name}/bin/activate`

## Guide to install PySpark
- **PySpark Installation Guide:** [How to Install Apache Spark and Run PySpark in Ubuntu 22.04](https://dev.to/kinyungu_denis/to-install-apache-spark-and-run-pyspark-in-ubuntu-2204-4i79)

## Install Java
`sudo apt update`
`sudo apt install default-jdk  -y`
`java --version`

## Install Apache Spark
`sudo apt install curl mlocate git scala -y`
`wget https://dlcdn.apache.org/spark/spark-3.5.1/spark-3.5.1-bin-hadoop3.tgz`
`tar xvf spark-3.5.1-bin-hadoop3.tgz`

## Configure Spark Environment
`cd spark-3.5.1-bin-hadoop3 && pwd && cd ..`
copy the path

`sudo nano ~/.bashrc`

paste this configuration into bashrc:
`export SPARK_HOME={paste the path here}`

`export PATH=$PATH:$SPARK_HOME/bin`

`export SPARK_LOCAL_IP=localhost`

`export PYSPARK_PYTHON=/usr/bin/python3.8`

`export PYTHONPATH=$(ZIPS=("$SPARK_HOME"/python/lib/*.zip); IFS=:; echo "${ZIPS[*]}"):$PYTHONPATH`

### Source the bashrc
`source ~/.bashrc`


## Install mysql-connector and Depackage
`wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-j_8.4.0-1ubuntu22.04_all.deb`
`sudo dpkg -i mysql-connector-j_8.4.0-1ubuntu22.04_all.deb`

## Spark Jar Configuration Path
`cd /usr/share/java/ && pwd && ls && cd ~/`
copy the path and copy the mysql-connector-j-{version}.jar, and save to note with this format: `/usr/share/java/mysql-connector-j-{version}.jar`

## Install MySQL
- **MySQL Installation Guide:** [How to Install MySQL on Ubuntu 22.04](https://phoenixnap.com/kb/install-mysql-ubuntu-22-04)

`sudo apt update`
`sudo apt upgrade`
`sudo apt install mysql-server`
`mysqld --version`

## Set up mysql
`sudo mysql -u root`
`CREATE USER '{your_username}'@'localhost' IDENTIFIED BY '{your_password}';`
`GRANT ALL PRIVILEGES ON *.* TO 'your_username'@'localhost' WITH GRANT OPTION;`
`quit`

### Try to access your user just create:
`mysql -u {your_username} -p`
enter the password `{your_password}`

## Your next objectives is: Create Database and create table with dummy data
### Create Database
`CREATE DATABASE {database_name};`

### User Database
`USE {database_name};`

### Create Table
`CREATE TABLE {table_name} (
    id INT AUTO_INCREMENT PRIMARY KEY,
    date DATE,
    temperature FLOAT,
    precipitation FLOAT,
    humidity FLOAT
);`

### Insert Table
`INSERT INTO {table_name} (date, temperature, precipitation, humidity) VALUES
('2024-05-01', 75.2, 0.3, 60),
('2024-05-02', 74.8, 0.5, 62),
('2024-05-03', 76.5, 0.2, 58),
('2024-05-04', 78.3, 0.1, 55),
('2024-05-05', 77.6, 0.4, 63),
('2024-05-06', 76.9, 0.6, 59),
('2024-05-07', 74.5, 0.8, 61),
('2024-05-08', 73.2, 0.3, 57),
('2024-05-09', 72.8, 0.2, 54),
('2024-05-10', 71.4, 0.1, 56),
('2024-05-11', 70.9, 0.4, 60),
('2024-05-12', 69.8, 0.5, 58),
('2024-05-13', 71.5, 0.3, 62),
('2024-05-14', 73.2, 0.2, 59),
('2024-05-15', 74.6, 0.6, 55),
('2024-05-16', 75.3, 0.7, 57),
('2024-05-17', 76.7, 0.5, 61),
('2024-05-18', 77.2, 0.3, 58),
('2024-05-19', 78.4, 0.2, 60),
('2024-05-20', 79.1, 0.4, 63);
`

### Quit mysql
`quit;`

## Create .py Pyspark File:
`nano {your_files}.py`

## Input this script into your .py:
`from pyspark.sql import SparkSession`

`spark = SparkSession.builder.appName("PySpark MySQL Connection").config("spark.jars", "/usr/share/java/mysql-connector-j-{version}.jar").getOrCreate()`

`df = spark.read.format("jdbc").option("driver","com.mysql.cj.jdbc.Driver").option("url", "jdbc:mysql://localhost:3306/{your_database_name}").option("query", "SELECT * FROM {your_table_name}").option("user", "{your_username}").option("password", "{your_password}").load()`

`df.show()`

## Command to spark submit (copy all command, and make sure your jar version and your_files.py)
`spark-submit --master local[*] --jars /usr/share/java/mysql-connector-j-{version}.jar --executor-cores 2 --executor-memory 4g --driver-memory 4g --driver-cores 2 --executor-memory 2g --executor-cores 1 {your_files.py}`


