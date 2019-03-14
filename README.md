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

```
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
echo 'deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main' | sudo tee /etc/apt/sources.list.d/google-chrome.list
sudo apt-get update
sudo apt-get install google-chrome-stable
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
