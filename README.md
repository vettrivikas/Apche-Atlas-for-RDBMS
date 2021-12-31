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
Save this file into rdbms_instance.json and then run below curl command to create this entity in Atlas:

curl -u : --negotiate -X POST -d @rdbms_instace.json \
-H "Content-Type: application/json" 'http://{atlas-url}:21000/api/atlas/v2/entity'
Output looks like below:

{"mutatedEntities":{"CREATE":[{"typeName":"rdbms_instance","attributes":{"qualifiedName":"[email protected] cluster_test2"},"guid":"7bd3e28d-34a2-40a3-807e-82c9a3c64e43","status":"ACTIVE"}]},"guidAssignments":{"-1146866634034252":"7bd3e28d-34a2-40a3-807e-82c9a3c64e43"}}
Since it is a bit of hard to read, I will re-format it so that we can see it more clearly:

{
    "guidAssignments": {
        "-1146866634034252": "7bd3e28d-34a2-40a3-807e-82c9a3c64e43"
    },
    "mutatedEntities": {
        "CREATE": [
            {
                "attributes": {
                    "qualifiedName": "[email protected] cluster_test2"
                },
                "guid": "7bd3e28d-34a2-40a3-807e-82c9a3c64e43",
                "status": "ACTIVE",
                "typeName": "rdbms_instance"
            }
        ]
    }
}
Take a note on the “guid” that is assigned to this entity, which we will need to use below to reference it. The value in my case is “7bd3e28d-34a2-40a3-807e-82c9a3c64e43“.

Now, go to Atlas web UI and confirm that the entities is created, by searching by Type “rdbms_instance”:



OK, so far so good, let’s continue the the rest of the entities. Next one is Database:

{
  "entity": {
    "attributes": {
      "owner": "admin",
      "ownerName": "admin",
      "name": "default",
      "displayText": "default DB",
      "qualifiedName": "[email protected]_test2",
      "description": "DB description",
      "instance": {
        "guid": "7bd3e28d-34a2-40a3-807e-82c9a3c64e43",
        "typeName": "rdbms_instance",
        "entityStatus": "ACTIVE"
      }
    },
    "typeName": "rdbms_db",
    "status": "ACTIVE"
  }
}
The names are pretty self-explanatory, one thing to be noted is the “instance” attribute, where you need to define the reference to the previous rdbms_instance that we just created. Make sure that you enter the guid value of “7bd3e28d-34a2-40a3-807e-82c9a3c64e43” in this section so that the new DB entity we create will be linked to the Instance entity. Again use curl to create the new entity after saving the file as rdbms_db.json:

curl -u : --negotiate -X POST -d @rdbms_db.json \
-H "Content-Type: application/json" 'http://{atlas-url}:21000/api/atlas/v2/entity'
{
    "guidAssignments": {
        "-1146866634034253": "eb8afae0-e899-4b9e-81b0-380680e9eca5"
    },
    "mutatedEntities": {
        "CREATE": [
            {
                "attributes": {
                    "qualifiedName": "[email protected]_test2"
                },
                "guid": "eb8afae0-e899-4b9e-81b0-380680e9eca5",
                "status": "ACTIVE",
                "typeName": "rdbms_db"
            }
        ],
        "UPDATE": [
            {
                "attributes": {
                    "description": "mysql instance",
                    "name": "mysql instance",
                    "owner": "admin",
                    "qualifiedName": "[email protected]_test2"
                },
                "classificationNames": [],
                "displayText": "mysql instance",
                "guid": "7bd3e28d-34a2-40a3-807e-82c9a3c64e43",
                "meaningNames": [],
                "meanings": [],
                "status": "ACTIVE",
                "typeName": "rdbms_instance"
            }
        ]
    }
}
Again, take a note of new guid created for the DB entity: eb8afae0-e899-4b9e-81b0-380680e9eca5, and continue with Table entity creation:

{
  "entity": {
    "attributes": {
      "owner": "admin",
      "ownerName": "admin",
      "name": "employee",
      "db": {
        "guid": "eb8afae0-e899-4b9e-81b0-380680e9eca5",
        "typeName": "rdbms_db"
      },
      "qualifiedName": "[email protected]_test2",
      "description": "table description",
      "replicatedTo": null,
      "replicatedFrom": null
    },
    "typeName": "rdbms_table",
    "status": "ACTIVE"
  }
}
This time, make sure the “db” section has correct “guid” referenced, save the file as rdbms_table.json and use command below:

curl -u : --negotiate -X POST -d @rdbms_table.json \
-H "Content-Type: application/json" 'http://{atlas-url}:21000/api/atlas/v2/entity'
{
    "guidAssignments": {
        "-1146866634034255": "0c748af2-86aa-4b12-baf8-37676374b9c6"
    },
    "mutatedEntities": {
        "CREATE": [
            {
                "attributes": {
                    "qualifiedName": "[email protected]_test2"
                },
                "guid": "0c748af2-86aa-4b12-baf8-37676374b9c6",
                "status": "ACTIVE",
                "typeName": "rdbms_table"
            }
        ],
        "UPDATE": [
            {
                "attributes": {
                    "description": "DB description",
                    "name": "default",
                    "owner": "admin",
                    "qualifiedName": "[email protected]_test2"
                },
                "classificationNames": [],
                "displayText": "default",
                "guid": "eb8afae0-e899-4b9e-81b0-380680e9eca5",
                "meaningNames": [],
                "meanings": [],
                "status": "ACTIVE",
                "typeName": "rdbms_db"
            }
        ]
    }
}
Last one is columns for the above table, I will just create one column in my example, others will be the same. JSON data as below:

{
  "entity": {
    "attributes": {
      "owner": "admin",
      "ownerName": "admin",
      "name": "age",
      "qualifiedName": "[email protected]_test2",
      "default_value": null,
      "isPrimaryKey": false,
      "indexes": [],
      "isNullable": false,
      "data_type": "integer",
      "comment": "name comment",
      "table": {
        "guid": "0c748af2-86aa-4b12-baf8-37676374b9c6",
        "typeName": "rdbms_table"
      }
    },
    "typeName": "rdbms_column",
    "status": "ACTIVE"
  }
}
Same deal here, make sure under “table” section you have correct “guid“, save file as rdbms_column_age.json and run:

curl -u : --negotiate -X POST -d @rdbms_column_age.json \
-H "Content-Type: application/json" 'http://{atlas-url}:21000/api/atlas/v2/entity'
{
    "guidAssignments": {
        "-1146866634034261": "d30d9640-0bbd-4f73-bd0e-ced29dc1dcb9"
    },
    "mutatedEntities": {
        "CREATE": [
            {
                "attributes": {
                    "qualifiedName": "[email protected]_test2"
                },
                "guid": "d30d9640-0bbd-4f73-bd0e-ced29dc1dcb9",
                "status": "ACTIVE",
                "typeName": "rdbms_column"
            }
        ],
        "UPDATE": [
            {
                "attributes": {
                    "createTime": 0,
                    "description": "table description",
                    "name": "employee",
                    "owner": "admin",
                    "qualifiedName": "[email protected]_test2"
                },
                "classificationNames": [],
                "displayText": "employee",
                "guid": "0c748af2-86aa-4b12-baf8-37676374b9c6",
                "meaningNames": [],
                "meanings": [],
                "status": "ACTIVE",
                "typeName": "rdbms_table"
            }
        ]
    }
}
To confirm that all entities are created successfully and linked with each other, go back to Atlas web UI and check the Table entity, you should be able to see the columns under it and the database it belongs to:


For more information regarding Atlas API entry points, please review the Atlas REST API doc.

This concludes my post about how to create RDBMS entities in Atlas for data governance. I am not sure if there is an easy way to bundle all entity data together so that you can create everything in one post. If you know please add comments below to let me know.
