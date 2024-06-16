Apache Airflow
--------------

* [Refer Here](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/overview.html) Apache Airflow Architecture 

* Now, follow these steps to install Airflow.

#### 1. Create the user, directory and dependencies for Airflow

```bash
yum install -y gcc openssl-devel bzip2-devel libffi-devel zlib-devel 
sudo adduser airflow 
sudo mkdir -p /opt/apache-airflow/airflow
sudo chown $USER:$USER /opt/apache-airflow
```

#### 2. Set up the Python environment

1. **Create a virtual environment**
   ```bash
   python -m venv /opt/apache-airflow/env_airflow
   ```

2. **Activate the virtual environment**
   ```bash
   source /opt/apache-airflow/env_airflow/bin/activate
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
#### 5. Install MySQL Database for Airflow or You can use the existing on if you have 

1. **Supported Database [refer here](https://airflow.apache.org/docs/apache-airflow/stable/howto/set-up-database.html)**

    ```bash
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

2. **Configure Airflow to use MySQL**
   Open the `airflow.cfg` file in the `$AIRFLOW_HOME` directory and set the following:
   ```ini
   sql_alchemy_conn = mysql+mysqldb://airflow:your_password@localhost/airflow
   ```

3. **Initialize the database**
   ```bash
   airflow db init
   ```

#### 6. Create Airflow User

1. **Create an admin user**
   ```bash
   airflow users create \
       --username admin \
       --firstname FIRST_NAME \
       --lastname LAST_NAME \
       --role Admin \
       --email admin@example.com
   ```

#### 7. Running Airflow

1. **Start the Airflow web server**
   ```bash
   airflow webserver -D --port 8080
   ```

2. **Start the Airflow scheduler**
   ```bash
   source /opt/apache-airflow/venv/bin/activate
   airflow scheduler -D 
   ```

#### 8. Systemd Service

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
   ExecStart=/opt/apache-airflow/evn_airflow/bin/airflow webserver -p 8080 --pid /opt/apache-airflow/airflow/airflow-webserver.pid --stdout /opt/apache-airflow/airflow/airflow-webserver.out --stderr /opt/apache-airflow/airflow/airflow-webserver.err
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
   Environment="AIRFLOW_HOME=/opt/apache-airflow/airflow"
   User=airflow
   Group=airflow
   ExecStart=/opt/apache-airflow/evn_airflow/bin/airflow scheduler --pid /opt/apache-airflow/airflow/scheduler/airflow-scheduler.pid --stdout /opt/apache-airflow/apache-airflow/airflow/scheduler/airflow-scheduler.out --stderr /opt/apache-airflow/airflow/scheduler/airflow-scheduler.err
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


#### 9. Airflow Tunning 

1.  To configure LocalExecutor we need external db Supported db [Refer Here](https://airflow.apache.org/docs/apache-airflow/2.2.5/howto/set-up-database.html)

#### 10. Access Airflow Webserver

1. To access the airflow webserver `http://<pulic_ip>:8080`



AIRFLOW_VERSION=2.9.2
PYTHON_VERSION="$(python3 --version | cut -d " " -f 2 | cut -d "." -f 1-2)"
CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"