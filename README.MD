Prerequisites Check
=============================
1.  To simply makes sure your list of packages from all repositories and PPA's is up to date:<br />
```
sudo apt update
```
2.  Install java Open jdk 1.8: <br />
```
sudo apt install openjdk-8-jdk -y
```
3.  Check java version and installaltion status: <br />
```
java -version
```
```
javac -version
```
4.  To open ssh connection between hadoop and Linux machine, Install open ssh server: <br />
```
sudo apt install openssh-server openssh-client -y
```
5.  Creating a new user who will be using hadoop(If you want to install hadoop on current user, skip this step): <br />
```
sudo adduser hdoop
```
a.  Login with newly created user: <br />

```
su - hdoop
```
Now if you are currently in Desktop folder, change the directory to Root folder:
```
cd ..
```
b. Generate key to connect from local machine to hadoop: <br />
```
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
```        
c. Put the key in Authorized keys:
```
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```   
d. Changing the permission of Authorizefd file:
```
chmod 0600 ~/.ssh/authorized_keys
```   
Now checking if hdoop user can create a connection to localhost:
```
ssh localhost
```  
If it shows the information related to last login from 127.0.0.1 then the ssh installtion is successful. <br />


Download Hadoop 3.3.0
===============================
We are downloading hadoop version 3.3.0 for this setup using below command from Official Apache website:
```
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.0/hadoop-3.3.0.tar.gz
```  
The file hadoop-3.3.0.tar.gz is saved locally now. <br />
Extracting the hadoop-3.3.0.tar.gz file using below command: <br />
```
tar xzf hadoop-3.3.0.tar.gz
```  

Setting up config files for hadoop Single node Cluster: 
=================================
Adding "hdoop" user as administrator:
---------------------
1. Go to root user which has admin prviledges(The user with which you configured linux):
In my case it is akash.
```
su - akash
```
2. Now run the below command to add hdoop user as admin:
```
sudo adduser hdoop sudo
```
3.  Navigate back to hdoop user:
```
su - hdoop
```

NOTE: While runng some commands system may ask you for password, provide the password to run the respective commands.

Configuring 1st file (.bashrc):
---------------------
Run the below command to open .bashrc file:
```
sudo nano .bashrc
```
Add below lines in this file at the end:
```
export HADOOP_HOME=/home/hdoop/hadoop-3.3.0
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS"-Djava.library.path=$HADOOP_HOME/lib/nativ"
```
Now press Ctrl + X and then press Y.

To reload the changes from .bashrc file:
```
source ~/.bashrc
```

Configuring 2nd File (hadoop-env.sh):
---------------------
Run the below command to open hadoop-env.sh file:
```
sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
Add below line in this file at the end:
```
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
Now press Ctrl + X and then press Y.

Configuring 3rd File (core-site.xml):
---------------------
Run the below command to open hadoop-env.sh file:
```
sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```

Add below lines in this file(between `<configuration>` and `</configuration>` ) :

```
   <property>
        <name>hadoop.tmp.dir</name>
        <value>/home/hdoop/tmpdata</value>
        <description>A base for other temporary directories.</description>
    </property>
    <property>
        <name>fs.default.name</name>
        <value>hdfs://localhost:9000</value>
        <description>The name of the default file system></description>
    </property>
```
Now press Ctrl + X and then press Y.

Configuring 4th File (hdfs-site.xml):
---------------------
Run the below command to open hdfs-site.xml file:
```
sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
We are configuring single node cluster and single replication property. <br />
Add below lines in this file(between `<configuration>` and `</configuration>` ) :

```
<property>
  <name>dfs.data.dir</name>
  <value>/home/hdoop/dfsdata/namenode</value>
</property>
<property>
  <name>dfs.data.dir</name>
  <value>/home/hdoop/dfsdata/datanode</value>
</property>
<property>
  <name>dfs.replication</name>
  <value>1</value>
</property>
```
Now press Ctrl + X and then press Y.

Configuring 5th File (mapred-site.xml):
================================================
Run the below command to open mapred-site.xml file:
```
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

Add below lines in this file(between `<configuration>` and `</configuration>` ) :

```
<property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
</property>
```
Now press Ctrl + X and then press Y.

Configuring 6th File (yarn-site.xml):
==================================================
Run the below command to open mapred-site.xml file:
```
sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```


Add below lines in this file(between `<configuration>` and `</configuration>` ) :

```
<property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
</property>
<property>
  <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
  <name>yarn.resourcemanager.hostname</name>
  <value>127.0.0.1</value>
</property>
<property>
  <name>yarn.acl.enable</name>
  <value>0</value>
</property>
<property>
  <name>yarn.nodemanager.env-whitelist</name>
  <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
</property>
```
Now press Ctrl + X and then press Y.

Launching Hadoop
==================================
Formatting the Namenode:
---------------------
```
hdfs namenode -format
```
Running start-all.sh Script to start All 4 daemons (Namenode, Datanode, Nodemanager and Resourcemanager):
---------------------
Navigate to hadoop-3.3.0/sbin/
```
~/hadoop-3.3.0/sbin/
```
Run the script start-all.sh :
```
./start-dfs.sh
```
Now run the below command to check if all the daemons are running or not :
```
jps
```
To access information about resource manager current jobs, successful and failed jobs, Open this link in browser:
```
http://localhost:8088/cluster
```
 


