# Installation and Configuration of Hive in a Multi node Hadoop Cluster

| github: [:cloud:](https://github.com/cloudmesh-community/fa18-516-29/blob/master/project-paper/report.md)

## Introduction:
 The following section describes the installation and configuration of Hive on Hadoop cluster.
 | github: [:cloud:](https://github.com/cloudmesh-community/fa18-516-29/blob/master/project-paper/report.md)

## Motivation

 Hive provides a datawarehousing solution on hadoop and can be used to do computation on data which is of relational format.This has
 a great use case in industries where most of the data are in relational format in legacy systems and are archived in disks.
 When there is a business use case to bring such data on a Big Data Platform and do some analytics on them,then Hive comes as the first choice of platform.Hive can run on multiple execution engines and can be integrated with Spark so that the underlying computation engine is Spark and not mapreduce.This increases the performance of hive queries greatly.Tez is another execution engine of choice with   Hive.Both have an advantage over map reduce that they do the computation of data in memory by creating immutable datasets and do not write the intermediate query outputs to the hdfs like mapreduce.This saves lot of Physical I/O and makes the processing much faster.
 
## Software
 Hive-2.3,Derby 10.4.2
 
## Keywords
 Amazon EC2,Hadoop,Spark,Hive

## Installation

  i. Download and Install Hive 2.3 which is compatible with Hadoop 2.9 only in the master machine.
      
      wget https://www-us.apache.org/dist/hive/hive-2.3.3/apache-hive-2.3.3-bin.tar.gz -P ~/hive_installation
   
  ii.Uncompress the tar file in a directory $HIVE_HOME
      
      tar zxvf ~/hive_installation/apache-hive-* -C ~/hive_home
      
  iii.Set up the env variables in all the .profile and .bashrc of all the servers
      
      export HIVE_HOME=/home/ubuntu/hive_home/apache-hive-2.3.3-bin
      export PATH=$PATH:$HIVE_HOME/bin
      export HIVE_CONF_DIR=/home/ubuntu/hive_home/apache-hive-2.3.3-bin/conf
      export CLASSPATH=$CLASSPATH:/home/ubuntu/hive_home/apache-hive-2.3.3-bin/lib/*:.
      
   iv.Download and Install derby database for the metadatastore of hive
      
      wget http://archive.apache.org/dist/db/derby/db-derby-10.4.2.0/db-derby-10.4.2.0-bin.tar.gz
      
      tar zxvf db-derby-10.4.2.0-bin.tar.gz -C ~/derby_home
      
   v. Set up the env variables for Derby in .bashrc and .profile
      
      export DERBY_HOME=/home/ubuntu/derby_home/db-derby-10.4.2.0-bin
      export PATH=$PATH:$DERBY_HOME/bin
      export CLASSPATH=$CLASSPATH:$DERBY_HOME/lib/derby.jar:$DERBY_HOME/lib/derbytools.jar
      
   vi. Load profile in all the servers
        ~/.profile
        
   vii. Initialize the schema in derby in $HIVE_HOME folder.This will create a metastore_db directory which is the database
         directory and will be loaded in memory.
         
         cd $HIVE_HOME
         schematool -dbType derby -initSchema

 ## Hive Configuration:

   Replace the values of ${system:java.io.tmpdir}/${system:user.name} in hive-site.xml with the actual values:
          
	  hive.exec.local.scratchdir /tmp/ubuntu
          
	  hive.querylog.location /tmp/ubuntu
          
	
## Running Hive

     i. Create directories in HDFS for hive tables:
     
        hadoop fs -mkdir /user/externaltables/insurancedata
        hadoop fs -copyFromLocal insurance_datafile /user/externaltables/insurancedata/
        
     ii. Start the hive terminal by typing command hive
         
	 hive
         
     iii. Create an external table in hive pointing to the file in hdfs:
         
         create external table if not exists insurance_data_1(
         policyID int,
         statecode char(2),
         county string,
         eq_site_limit decimal,
         hu_site_limit decimal,
         fl_site_limit decimal,
         fr_site_limit decimal,
         tiv_2011 decimal,
         tiv_2012 decimal,
         eq_site_deductible decimal,
         hu_site_deductible decimal,
         fl_site_deductible decimal,
         fr_site_deductible decimal,
         point_latitude decimal,
         point_longitude decimal,
         line string,
         construction string,
         point_granularity int)
         comment 'Test data about insurance data'
         row format delimited
         fields terminated by ','
         stored as textfile
         location '/user/externaltables/insurancedata/';
         
       iv. Run a query in hive to count the number of policies:
       
          select count(policyID) from insurance_data_1;