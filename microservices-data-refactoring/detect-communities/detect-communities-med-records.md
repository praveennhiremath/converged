# Run the community detection algorithm

## Introduction

In this lab, using the graphs which were created in the previous labs, we applied the community detection algorithm, which identifies the communities within the graphs. The community detection algorithm takes the input graphs and identities strong connectivity within graphs, and forms multiple smaller communities. Infomap is used for community detection in this lab.

Estimated Time: 10 minutes

### Objectives

In this lab, you will:

* Create a graph in Graph Studio from the Graph Client.
* Detect the communities using the Infomap.

### Prerequisites

This lab assumes you have the following:

* You are running on Java 10+ in OCI Console. 
* All previous labs were completed successfully.

## Task 1: Change the graph properties

1. Make sure you are on the same session of cloud shell. If not, set the required environment variables as we did in the setup.

    ```text
   <copy>
    export DRA_HOME=${HOME}/microservices-data-refactoring
   </copy>
   ```

2. Navigate to the project
    ```text
   <copy>
    cd ${HOME}/microservices-data-refactoring/dra-graph-client
   </copy>
   ```

3. Update the property `graph_name` of a newly created graph on smaller data in the src/main/resources/graph-config.properties file.

    ```text
   <copy>
    vi src/main/resources/graph-config.properties
   </copy>
   ```
    Update the value for the below properties.

    ```text
    graph_name : Name of the graph created in Graph Studio.

    ```
    Save and exit.
	
## Task 2: Compile and Run the Community Detection on medical records

Here, We are using the smaller graph created in Lab 5. You can also run on the main graph, which is created in Lab 3, or any data which you loaded through SQL Tuning Sets.

1. Compile the maven project

    ```text
   <copy>
    mvn compile
   </copy>
   ```
   
2. Execute the project to see the identified clusters using the Infomap Algorithm

    ```text
   <copy>
   mvn exec:java -Dexec.mainClass=com.oracle.ms.app.InfomapGraphClient -Dexec.args="5"
   </copy>
   ```

   Where
   * com.oracle.ms.app.InfomapGraphClient - The main class which loads the graph and runs the Infomap to identify the Clusters.
   * 5 is MaxNumberOfIterations for Infomap Algorithm. You can change it to any positive integer.

   Output

   Job Details:
    ```text
   name=Environment Creation - 18 GBstype= ENVIRONMENT_CREATION created_by= ADMIN
   Graph : PgxGraph[name=MED_REC_PG_OBJ_259_G, N=259, E=972, created=1664544333468]
   ```

    	The output of Infomap will have the Community Ids with the nodes in that community, as shown below

    ```text
    +----------------------------------------+
    | Community | TABLE_NAME                 |
    +----------------------------------------+
    | 0         | PRSNL_RELTN_ACTIVITY       |
    | 0         | CLINICAL_SERVICE_RELTN     |
    | 0         | ENCNTR_PRSNL_RELTN         |
    | 0         | PE_STATUS_REASON           |
    | 0         | PCT_CARE_TEAM              |
    | 0         | PERSON_PERSON_RELTN        |
    | 0         | PERSON_INFO                |
    | 0         | DEPT_ORD_STAT_SECURITY     |
    | 0         | PERSON_PRSNL_RELTN         |
    | 0         | PRSNL_ORG_RELTN            |
    ------------------------------------------
     ```

    Here we have shown only for first ten nodes for reference. Similarly, we will have communities detected for all 974 nodes. Detailed analysis is done on the smaller graph in the next lab.
	
## Task 3: Analysis of newly formed clusters on medical records

## Task 4: Adjust constraints and reform the communities of medical records

1. For example, we consider the node named `RAD_REPORT_DETAIL`, want to move from cluster 3 to cluster 2.

2. Get the nodes of the target cluster to which we want to move node `RAD_REPORT_DETAIL`. And check for the edges from `RAD_REPORT_DETAIL` to nodes of target cluster and update the `TOTAL_AFFINITY` of those edges to 1.

    NOTE: We must have an edge from the source node to nodes of the target cluster.

    Go to SQL developer and execute the below query.

    ```text
   <copy>
   UPDATE MED_RECS_AD_TABLE_MAP SET TOTAL_AFFINITY = 1 WHERE TABLE_MAP_ID IN 
   (SELECT DISTINCT(TABLE_MAP_ID) AS MATCHED_IDS_OF_EDGES_TO_BE_UPDATED FROM MED_RECS_AD_TABLE_MAP
   OR (TABLE2 = 'RAD_REPORT_DETAIL' AND TABLE1 IN (#{COMMA_SEPARATED_NODES_OF_TARGET_CLUSTER})))
   WHERE (TABLE1 = 'RAD_REPORT_DETAIL' AND TABLE2 IN (#{COMMA_SEPARATED_NODES_OF_TARGET_CLUSTER}));
    </copy>
    ```

3. Run the Infomap algorithm again on the updated data. Follow the same steps from Lab 6, Task 2, and verify whether the required is moved to the intended clusters.
	
Please **proceed to the next lab** to do so.

## Acknowledgements

* **Author** - Praveen Hiremath, Developer Advocate
* **Contributors** -  Praveen Hiremath, Developer Advocate
* **Last Updated By/Date** - Praveen Hiremath, Developer Advocate, October 2022
