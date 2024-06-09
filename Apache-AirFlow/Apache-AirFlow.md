Apache Airflow
--------------

* [Refer Here](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/overview.html) Apache Airflow Architecture 


* If you are using centos8 
```
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
yum update -y
```

* PreReq:

```
sudo yum install -y python38-devel.x86_64

python3.8 -V
```

### Airflow Setup

* [Refer Here](https://airflow.apache.org/docs/apache-airflow/stable/installation/installing-from-pypi.html#installation-from-pypi) Airflow setup official docs 

```
yum install -y python3 python3-pip python3-devel gcc-c++ libffi-devel mariadb-devel
echo "export AIRFLOW_HOME=/opt/apache-airflow/airflow" >> ~/.bashrc 
ln -s /usr/bin/python3.8 /usr/local/lib/python3.8 
source ~/.bashrc 
python3 -m venv airflow_env
source airflow_env/bin/activate
pip3 install --upgrade pip setuptools

export AIRFLOW_HOME=/opt/apache-airflow/airflow
pip3 install "apache-airflow" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.9.1/constraints-3.8.txt"
airflow db init 
```

* Once you install airflow crate airflow user 

```
airflow users create \
    --username admin \
    --password admin \
    --firstname Admin \
    --lastname User \
    --role Admin \
    --email admin@cgs.com
```

* To configure LocalExecutor we need external db Supported db [Refer Here](https://airflow.apache.org/docs/apache-airflow/2.2.5/howto/set-up-database.html)

* We are going with Mysql DB 

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

* To use backend db for airflow we need to change DB Configuration in `airflow.cfg`

```
vi airflow.cfg
# comment the below configuration 
sql_alchemy_conn = sqlite:////opt/apache-airflow/airflow/airflow.db

sql_alchemy_conn = mysql+mysqldb://airflow:P@ssw0rd@localhost/airflow_db
```

* Again we need to crate the user 

```
airflow users create \
    --username admin \
    --password admin \
    --firstname Admin \
    --lastname User \
    --role Admin \
    --email admin@cgs.com
```

* To access the airflow webserver `http://<pulic_ip>:8080`



