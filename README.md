# Apche-Atlas-for-RDBMS

### using post man or curl 

so i have idea to add mysql to apache atlas using pyspark and sqoop
in saprk we can insert using hive_support() just add config of hive in spark session or default config 

another thing is Using Rest API

Cloudera’s HDP and CDP provides native integration of Apache Atlas with Hadoop databases like Apache Hive, where databases and tables created in Hive will be automatically synchronised to Atlas, and can then be later used with Apache Ranger for authorisation. However, Atlas’s integration with other traditional databases like MySQL is limited. In this post, I will give you some basic examples on how you can import entities from RDBMS into Atlas by making use of Atlas’ Restful APIs.

Before we dive into each steps on how to create entities via Atlas API, I would like to spend a bit of time to explain how entities are setup in Atlas, at least for the context of databases. In Atlas, you can have below hierarchies:

Instances
Databases
Tables
Columns
Basically Instance is parent of database, database contains tables and columns are defined in each table. So my steps is broken down into each entity to create parent entities before proceeding to create child entities.

