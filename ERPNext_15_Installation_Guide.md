# Installing ERPNext v15 on Ubuntu 22.04 LTS

## Setting Environment Variables:
Before proceeding with the installation, it's essential to set up a few environment variables for configuration. Open a terminal and execute the following commands:

```bash
export FRAPPE_USER=frappe
export SITE_NAME=site1.localhost
```

**FRAPPE_USER**: This variable represents the desired username for the Frappe application. Choose a meaningful and secure username for Frappe user access.

**SITE_NAME**: This variable defines the desired site name for your ERPNext instance. Ensure it reflects your organization or project's name and domain. In this example, we are using "site1.localhost" as the site name.

## Updating and Upgrading Packages:
The update command retrieves the latest information about available packages from the repositories.

The upgrade command installs the latest versions of all installed packages on your system.

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
```


## Creating and Configuring a New User:
To enhance security and manage system access, it's recommended to create a dedicated user for running Frappe and ERPNext.
* The **adduser** command creates a new user.
* The **usermod** command adds the newly created user to the sudo group, granting administrative privileges.
* The **su** command is used to switch to the newly created user. You will be prompted to enter the user's password.


```bash
sudo adduser $FRAPPE_USER

sudo usermod -aG sudo $FRAPPE_USER

su $FRAPPE_USER

cd /home/$FRAPPE_USER

pwd

```

## Set Environment Variables Within User's Context:
```bash
export FRAPPE_USER=frappe
export SITE_NAME=site1.localhost
```

## Installing Python 3.11 and Development Tools:

The provided sequence of commands facilitates the installation and configuration of Python 3.11 on the system. Firstly, the deadsnakes/ppa repository is added to enable access to newer Python versions. Subsequently, the system package information is updated, and Python 3.11 is installed. The installed Python version is then verified, and essential development tools, including Python development files, setuptools, and pip, are installed. Additionally, the Python 3.11 virtual environment is set up, allowing for the isolation of Python environments for distinct projects.

```bash
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update

sudo apt install python3.11
python3.11 --version

sudo apt-get install python3-dev
sudo apt-get install python3-setuptools python3-pip
sudo apt install python3.11-venv
```

## Setting Up Database Components:

This set of commands configures and enhances the system's database capabilities. The first command, sudo apt-get install software-properties-common, installs tools for managing software repositories efficiently. Next, sudo apt install mariadb-server installs the MariaDB server, a robust relational database management system. The third command, sudo mysql_secure_installation, guides through securing the MariaDB installation by configuring key security settings. Finally, sudo apt-get install libmysqlclient-dev adds development files necessary for building applications compatible with MySQL. 

```bash
sudo apt-get install software-properties-common

sudo apt install mariadb-server

sudo mysql_secure_installation

sudo apt-get install libmysqlclient-dev
```

## Configuring MySQL Settings:
The provided command appends essential configurations to the MySQL settings file (/etc/mysql/my.cnf), including user details, file paths, and server optimizations for improved performance and character set compatibility.


```bash
echo "
[server]
user = mysql
pid-file = /run/mysqld/mysqld.pid
socket = /run/mysqld/mysqld.sock
basedir = /usr
datadir = /var/lib/mysql
tmpdir = /tmp
lc-messages-dir = /usr/share/mysql
bind-address = 127.0.0.1
query_cache_size = 16M
log_error = /var/log/mysql/error.log

[mysqld]
innodb-file-format=barracuda
innodb-file-per-table=1
innodb-large-prefix=1
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
" | sudo tee -a /etc/mysql/my.cnf
```


The command "sudo service mysql restart" is used to restart the MySQL service after making configuration changes, and "sudo service mysql status" is employed to check the current status of the MySQL service.

```bash
sudo service mysql restart
sudo service mysql status
```

## Installing and Managing Redis Server:
The provided commands install Redis server using "sudo apt-get install redis-server," start the Redis service with "sudo service redis-server start," and check its status using "sudo service redis-server status."

```bash
sudo apt-get install redis-server

sudo service redis-server start

sudo service redis-server status
```

## Installing Node Version Manager (NVM) and Yarn:
Setting up a Node.js development environment with NVM and Yarn

```bash
sudo apt install curl

curl  https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash

source ~/.bashrc

nvm list-remote

nvm install lts/hydrogen

sudo npm install -g yarn
```
Note : While using the curl command, if you encounter an SSL or connection error, try this command.
```bash
git config --global --unset-all remote.origin.proxy
```


## Install wkhtmltopdf
Wkhtmltopdf is an open source simple and much effective command-line shell utility that enables user to convert any given HTML (Web Page) to PDF document or an image (jpg, png, etc)
```bash
sudo apt-get install xvfb libfontconfig wkhtmltopdf
```

## Setting Up and Initiating Frappe Bench:
Setting up and starting Frappe Bench in development mode with version 5.19.0, Frappe framework version 15, and Python 3.11

```bash
sudo -H pip3 install frappe-bench==5.19.0

bench --version

bench init frappe-bench --frappe-branch version-15 --python python3.11

cd frappe-bench/

bench start
```

## Initializing, Configuring, and Starting Frappe Bench for ERPNext:
Initializing and starting Frappe Bench for ERPNext with version 15 and site configuration.

```bash
bench new-site $SITE_NAME

bench --site $SITE_NAME add-to-hosts

bench get-app erpnext --branch version-15

bench --site $SITE_NAME install-app erpnext

bench use $SITE_NAME

bench start
```

Open your browser and go to this address http://localhost:8000 to log in.


## Configuring Permissions and Installing Supervisor and Nginx for Production Setup:

```bash
chmod -R o+rx /home/$FRAPPE_USER

sudo apt-get -y install supervisor nginx

sudo service nginx start
sudo service nginx status

sudo service supervisor start
sudo service supervisor status
```

## Configuring Frappe Bench for Production:
```bash
sudo bench setup production $FRAPPE_USER
```

## Updating Supervisor Configuration and Reloading Nginx:
```bash
sudo /usr/bin/supervisorctl update

sudo systemctl reload nginx
```

<br><br>

## [ NOTE: ] Development
## To switch from production to development mode...
```bash
  # To temporarily halt the ERP system on production, execute the command to stop the relevant services. 
  sudo service supervisor stop
  sudo service redis stop
  sudo service nginx stop

  
  # To initiate development mode, ensure you have a Procfile in the frappe-bench directory. Execute the command 'bench setup procfile' to set it up.
  bench setup procfile

  # To start develop server
  bench start

  ```

  ## To resume production mode again.
  ```bash
    # Start services:
    sudo service supervisor start
    sudo service redis start
    sudo service nginx start

    # Check service status:
    sudo service supervisor status
    sudo service redis status
    sudo service nginx status
  ```
