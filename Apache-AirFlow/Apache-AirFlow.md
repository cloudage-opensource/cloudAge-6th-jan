Apache Airflow
--------------

* [Refer Here](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/overview.html) Apache Airflow Architecture 



### Step 1: Install Airflow

Now, follow these steps to reinstall Airflow.

#### 1. Create the user and directory for Airflow

```bash
sudo adduser airflow 
sudo mkdir -p /opt/apache-airflow/airflow
sudo chown $USER:$USER /opt/apache-airflow
```

#### 2. Set up the Python environment

1. **Create a virtual environment**
   ```bash
   python3 -m venv /opt/apache-airflow/evn_airflow
   ```

2. **Activate the virtual environment**
   ```bash
   source /opt/apache-airflow/evn_airflow/bin/activate
   ```

#### 3. Install Airflow

1. **Upgrade pip and install Apache Airflow**
   * Create bash script to install airflow `setup-airflow.sh`
   * Give executable permission to airflow `chomd +x /opt/apache-airflow/setup-airflow.sh`

   ```bash
   pip3 install --upgrade pip setuptools wheel
   export AIRFLOW_HOME=/opt/apache-airflow/airflow
   AIRFLOW_VERSION=2.9.2 # replace with the desired version
   PYTHON_VERSION="$(python3 --version | cut -d " " -f 2 | cut -d "." -f 1-2)"
   CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"
   pip3 install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"
   ```

2. **Install MySQL client for Airflow**
   ```bash
   pip install apache-airflow-providers-mysql
   ```

#### 4. Configure Airflow

1. **Initialize the database**
   ```bash
   airflow db init
   ```

2. **Configure Airflow to use MySQL**
   Open the `airflow.cfg` file in the `$AIRFLOW_HOME` directory and set the following:
   ```ini
   sql_alchemy_conn = mysql+mysqldb://airflow:your_password@localhost/airflow
   ```

#### 5. Create Airflow User

1. **Create an admin user**
   ```bash
   airflow users create \
       --username admin \
       --firstname FIRST_NAME \
       --lastname LAST_NAME \
       --role Admin \
       --email admin@example.com
   ```

#### 6. Running Airflow

1. **Start the Airflow web server**
   ```bash
   airflow webserver -D --port 8080
   ```

2. **Start the Airflow scheduler**
   ```bash
   source /opt/apache-airflow/venv/bin/activate
   airflow scheduler -D 
   ```

#### 7. Systemd Service

To run Airflow as a service, create systemd service files for the web server and scheduler.

1. **Airflow Webserver Service**
   Create `/etc/systemd/system/airflow-webserver.service`:
   ```ini
   [Unit]
   Description=Airflow webserver daemon
   After=network.target
   
   [Service]
   Environment="AIRFLOW_HOME=/opt/apache-airflow/airflow"
   User=airflow
   Group=airflow
   ExecStart=/opt/apache-airflow/evn_airflow/bin/airflow webserver --port 8080
   Restart=on-failure
   
   [Install]
   WantedBy=multi-user.target
   ```

2. **Airflow Scheduler Service**
   Create `/etc/systemd/system/airflow-scheduler.service`:
   ```ini
   [Unit]
   Description=Airflow scheduler daemon
   After=network.target
   
   [Service]
   Environment="AIRFLOW_HOME=/opt/apache-airflow"
   User=airflow
   Group=airflow
   ExecStart=/opt/apache-airflow/evn_airflow/bin/airflow scheduler
   Restart=on-failure
   
   [Install]
   WantedBy=multi-user.target
   ```

3. **Enable and start the services**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable airflow-webserver
   sudo systemctl enable airflow-scheduler
   sudo systemctl start airflow-webserver
   sudo systemctl start airflow-scheduler
   ```



* To configure LocalExecutor we need external db Supported db [Refer Here](https://airflow.apache.org/docs/apache-airflow/2.2.5/howto/set-up-database.html)

* Setup MySQL DB for Airflow 

```
* Setup The Mysql Server (On Database Host)

wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm

md5sum mysql57-community-release-el7-9.noarch.rpm

sudo rpm -ivh mysql57-community-release-el7-9.noarch.rpm

sudo yum install --nogpgcheck mysql-server -y

sudo systemctl start mysqld

sudo systemctl status mysqld

sudo mysql_secure_installation

mysqladmin -u root -p version

mysql -u root -p

CREATE DATABASE airflow_db DEFAULT CHARACTER SET utf8;
CREATE USER 'airflow'@'%' IDENTIFIED BY 'P@ssw0rd';
GRANT ALL PRIVILEGES ON airflow_db.* TO 'airflow'@'%';
FLUSH PRIVILEGES;

exit;
```



* To access the airflow webserver `http://<pulic_ip>:8080`



