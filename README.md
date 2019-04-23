# iTrust

iTrust configuarion in ubuntu 16.04, with memory size equal to or larger than **4 GB**

```
sudo apt-get update && sudo apt-get upgrade
```

### Install java 8

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

* check JDK version

```
vagrant@ubuntu-xenial:~$ java -version
java version "1.8.0_201"
Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.201-b09, mixed mode)
```

* Display JAVA_HOME

```
/usr/lib/jvm/java-8-oracle
```

### Install google-chrome

google chrome with version 74.0 is required


```
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
echo 'deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main' | sudo tee /etc/apt/sources.list.d/google-chrome.list
sudo apt-get update
sudo apt-get install google-chrome-unstable
```

check installed version

```
vagrant@ubuntu-xenial:~/iTrust$ google-chrome --version
Google Chrome 74.0.3729.6 dev
```

### Install Apache Maven

```
sudo apt-get install maven
```

* check Maven version

```
vagrant@ubuntu-xenial:~$ mvn --version
Apache Maven 3.3.9
Maven home: /usr/share/maven
Java version: 1.8.0_201, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-8-oracle/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "4.4.0-141-generic", arch: "amd64", family: "unix"
```

### Install MySQL, set root password "root"

```
sudo apt-get install mysql-server
```

* check MySQL version:

```
vagrant@ubuntu-xenial:~$ mysql --version
mysql  Ver 14.14 Distrib 5.7.25, for Linux (x86_64) using  EditLine wrapper
```

* Log into MySQL by executing

```
mysql -u root -p
```

* Execute the following commands for root user

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' IDENTIFIED BY 'root' WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit
```

Note: If we want to remove MySQL compeletely, do the following steps:

* Use apt to uninstall and remove all MySQL packages:

```
sudo apt-get remove --purge mysql-server mysql-client mysql-common -y
sudo apt-get autoremove -y
sudo apt-get autoclean
```

* Remove the MySQL folder:

```
sudo  rm -rf /etc/mysql
```

* Delete all MySQL files on your server:

```
sudo find / -iname 'mysql*' -exec rm -rf {} \;
```


### Set up iTrust

* Go to folder 

```
cd /home/vagrant/iTrust/src/main/java
```

* Create **db.properties** and **email.properties** from templates

```
cp db.properties.template db.properties
cp email.properties.template email.properties
```

* Modify two property files


### Perform maven test

* Go to folder

```
cd /home/vagrant/iTrust
```

* Builds the database and creates sample data (Take about 2 mins for the first time)

```
mvn -f pom-data.xml process-test-classes
```

* Runs the unit tests, launches the server, runs the integration tests, and then brings the server back down (Takes about 5 mins)

```
mvn clean test verify checkstyle:checkstyle -Djetty.port=8888
```

Done.

### Install Redis

* Install packages and dependencies

```
sudo apt-get update
sudo apt-get install build-essential tcl
```

* Download and extract the source code

```
cd /tmp
curl -O http://download.redis.io/redis-stable.tar.gz
tar xzvf redis-stable.tar.gz
cd redis-stable
make
make test
sudo make install
```

* Configure Redis

We create a configuration directory.

```
sudo mkdir /etc/redis
sudo cp /tmp/redis-stable/redis.conf /etc/redis
sudo vim /etc/redis/redis.conf
```

In the file, find the supervised directive. Currently, this is set to no.

```
. . .

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
supervised systemd

. . .
```

Next, find the dir directory. This option specifies the directory that Redis will use to dump persistent data. We need to pick a location that Redis will have write permission and that isn't viewable by normal users.

```
. . .

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.
dir /var/lib/redis

. . .
```

* Create a Redis systemd Unit File

```
sudo vim /etc/systemd/system/redis.service
```

Add the following content to the /etc/systemd/system/redis.service file

```
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
```

* Create the Redis User, Group and Directories

```
sudo adduser --system --group --no-create-home redis
sudo mkdir /var/lib/redis
sudo chown redis:redis /var/lib/redis
sudo chmod 770 /var/lib/redis
```

* Start or restart the Redis Service

```
sudo systemctl start redis
sudo systemctl status redis
sudo systemctl restart redis
```

* Test the Redis Instance Functionality

```
redis-cli

```
