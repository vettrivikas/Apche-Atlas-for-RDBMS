# Apche-Atlas-for-RDBMS


so i have idea to add mysql to apache atlas using pyspark and sqoop
in saprk we can insert using hive_support() just add config of hive in spark session or default config 

another thing is Using Rest API

As more and more companies store large amount of data, either privately or in the cloud, data governance has become more and more important, which can help companies to decide:

Where is the source of data
How data is transformed
When data was modified
How data is classified
etc etc

Cloudera’s HDP and CDP provides native integration of Apache Atlas with Hadoop databases like Apache Hive, where databases and tables created in Hive will be automatically synchronised to Atlas, and can then be later used with Apache Ranger for authorisation. However, Atlas’s integration with other traditional databases like MySQL is limited. In this post, I will give you some basic examples on how you can import entities from RDBMS into Atlas by making use of Atlas’ Restful APIs.

Before we dive into each steps on how to create entities via Atlas API, I would like to spend a bit of time to explain how entities are setup in Atlas, at least for the context of databases. In Atlas, you can have below hierarchies:

Instances
Databases
Tables
Columns
Basically Instance is parent of database, database contains tables and columns are defined in each table. So my steps is broken down into each entity to create parent entities before proceeding to create child entities.

--------------------------------------------------------------------------------
Firstly, let’s create Instance entity, let’s review the JSON data for instance:
{
  "entity": {
    "attributes": {
      "owner": "admin",
      "ownerName": "admin",
      "name": "mysql instance",
      "qualifiedName": "[email protected] cluster_test2",
      "rdbms_type": "mysql",
      "description": "mysql instance description",
      "contact_info": "your contact info",
      "platform": "Linux",
      "hostname": "mysql.hostname.com",
      "protocol": "mysql protocal",
      "port": "3306"
    },
    "typeName": "rdbms_instance",
    "status": "ACTIVE"
  }
}
---------------------------------------------------------------------------------





