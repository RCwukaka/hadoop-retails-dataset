# Deployment of framework system(hadoop + mahout)

### operating system
  macOS
  
### hadoop
  version： hadoop-3.3.1<br/>
  Download : https://hadoop.apache.org/releases.html  <br/>
  deploy step:<br/>
  <strong>1.</strong> Enable the MAC remote login function and add the current user to the list of allowed users.<br/>
  ![image](https://user-images.githubusercontent.com/12046845/143590015-b4cad92e-2058-4b57-9956-13d4a54ff624.png)
  <br/><br/>
  <strong>2.</strong> create ssh <br/>
  <code> ssh-keygen -t rsa </code> <br/>
  <code> cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys </code><br/>
  <code> ssh localhost</code> <br/>
  you can check hadoop in <br/>
  <code>hadoop version</code><br/>
  <br/>
  <strong>3.</strong>Configure Hadoop<br/>
  The Hadoop configuration files to be modified are under <code>hadoop-3.3.1/etc/Hadoop/</code>, including:<br/>
  <code>hadoop-env.sh core-site.xml hdfs-site.xml mapred-site.xml yarn-site.xml</code><br/>
  <strong>3.1 </strong>Modify <code>hadoop-env.sh</code> file<br/>
  change <code>JAVA_HOME</code> path in <code>hadoop-env.sh</code><br/>
  change <code>core-site.xml </code><br/>
  ```xml
    <configuration>
      <property>
      <name>fs.defaultFS</name>
      <value>hdfs://localhost:9000</value>
      </property>
      <property>
      <name>hadoop.tmp.dir</name>
      <value>/Users/chenren/hadoop-3.3.1/tmp</value>
      </property>
    </configuration>
  ```
  change <code>hdfs-site.xml </code><br/>
  ```xml
  <configuration>
    <property>
    <name>dfs.replication</name>
    <value>1</value>
    </property>
    <property>
    <name>dfs.permissions</name>
    <value>false</value>    <!--关闭防火墙-->
    </property>
  </configuration>
  ```
  change <code>mapred-site.xml </code><br/>
  ```xml
  <configuration>
    <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
    </property>
  </configuration>
  ```
  change <code>yarn-site.xml </code><br/>
  ```xml
  <configuration>
    <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>localhost</value>
    </property>
    <property>
    <!--NodeManager获取数据的方式-->
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
    </property>
    <property>
     <name>yarn.application.classpath</name>
    <value>/Users/chenren/hadoop-3.3.1/etc/hadoop:/Users/chenren/hadoop-3.3.1/share/hadoop/common/lib/*:/Users/chenren/hadoop-3.3.1/share/hadoop/common/*:/Users/chenren/hadoop-3.3.1/share/hadoop/hdfs:/Users/chenren/hadoop-3.3.1/share/hadoop/hdfs/lib/*:/Users/chenren/hadoop-3.3.1/share/hadoop/hdfs/*:/Users/chenren/hadoop-3.3.1/share/hadoop/mapreduce/*:/Users/chenren/hadoop-3.3.1/share/hadoop/yarn:/Users/chenren/hadoop-3.3.1/share/hadoop/yarn/lib/*:/Users/chenren/hadoop-3.3.1/share/hadoop/yarn/*</value>
    </property>
  </configuration>
  ```
  after modify<br/>
  make some folder in hadoop <br/>
  ```sheel
  hadoop fs -mkdir hdfs://localhost:9000/testData
  hadoop fs -mkdir hdfs://localhost:9000/outPut
  hadoop fs -put /home/hadoop/Desktop/data/testdata hdfs://localhost:9000/testdata/
  ```
  ![image](https://user-images.githubusercontent.com/12046845/143594697-ba14008c-73c1-4915-a5a9-d973c9e1c55f.png)

  <strong>4.</strong>start Hadoop<br/>
  <code>./start-all.sh</code><br/>
  
### mahout
  version： mahout-0.91 <br/>
  Download: http://archive.apache.org/dist/mahout/0.9/  <br/>
  deploy step:<br/>
  after hadoop run<br/>
  run
  ```shell
  hadoop jar /Users/chenren/mahout-0.9/mahout-examples-0.9-job.jar org.apache.mahout.fpm.pfpgrowth.FPGrowthDriver -i /testdata/retail.dat -o /outPut -method  mapreduce -s 1000 -regex '\\[\\]'
  ```
  after analyse end, run<br/>
  <code>mahout seqdumper -i /outPut/parallelcounting/part-r-00000 -o ~/retail_kmeans.txt</code>
  
### dataset
  the retail.dat in this repository.the data come from http://fimi.uantwerpen.be/data/.

### result
![image](https://user-images.githubusercontent.com/12046845/143579289-accd8cf0-fe3a-4a42-b31a-4892ed2c33b8.png)
