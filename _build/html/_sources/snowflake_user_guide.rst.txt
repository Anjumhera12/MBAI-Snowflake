============
Introduction
============

The objective of this document is to provide a structured **user and implementation guide** for **Matchbook AI’s Snowflake Connector**, covering both **business users** and **IT administrators**. Connectors enable existing Snowflake customers to seamlessly integrate Matchbook AI’s cleanse, match, enrich, and monitor APIs into their environment. This empowers users to streamline data transformation and enrichment workflows without leaving their existing platform. 

1. **Guide Users in Data Management** – Help business users navigate Matchbook AI, manage source data, and download matched records. 

2. **Assist with Technical Implementation** – Provide IT teams with clear instructions on setting up, configuring, and optimizing Matchbook AI within Snowflake and Data Sharing. 

**Benefits**: 

* **Snowflake** can act as the **data ingestion point for D&B**, enabling advanced data cleansing, **real-time matching**, and enrichment without requiring users to switch between multiple tools or platforms. 

* Improves the efficiency of data pipelines, reduces **decision-making latency**, and **lowers the manual costs associated with data processing**. 

* Empowers customers to proactively manage their data to adapt to changes in business KPIs. 

* Enables **real-time monitoring** of data to ensure accuracy and relevance. 

* Eliminates issues related to inaccurate data, outdated information, and data silos. 

1. User Guide 
=============

This guide provides business users and IT administrators with a structured approach to integrating Matchbook AI’s cleanse, match, enrich, and monitor APIs within Snowflake. It helps business users navigate Matchbook AI, manage source data, and download matched records.  

1.1 Log in to Snowflake 
-----------------------

Navigate to your Snowflake account using the provided URL: https://app.snowflake.com/matchbook/dev/#/homepage  

.. figure:: images/1.png

1.2 Access Snowflake account for individual 
-------------------------------------------

Each user has unique login credentials. Log in using your assigned **username** and **password**.

.. figure:: images/2.png

Once logged in, you will be directed to the **Snowflake console dashboard**. 

.. figure:: images/3.png

1.3 Setting Up the Database and Schema
--------------------------------------

Creating Database and Schema 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

From the left side menu, go to '**Data**' and select '**Databases**', then click on '**+ Database**' to create a new one.

.. figure:: images/4.png

A pop-up window will appear. Enter a name for the database and a comment (optional), then click '**Create**'.  

.. figure:: images/5.png

Once the database is created, it will appear on the left-hand side. Click on the name of the database you created, then click on '**+ Schema**'. 

.. figure:: images/6.png

A pop-up window will appear. Enter the name for the schema and a comment (optional), then click '**Create**' to create the schema.

.. figure:: images/7.png

1.4 Creating Worksheets
-----------------------

Create Separate worksheet 
^^^^^^^^^^^^^^^^^^^^^^^^^

A separate worksheet should be created for each client to share their data and results. To create a worksheet, on the dashboard, select **Worksheets** and click on **Go to Worksheets**. 

(or) from the left-hand menu, select **Projects** -> **Worksheets**. 

.. figure:: images/8.png

You will be redirected to a new page, select **SQL Worksheet** from the drop-down menu.

.. figure:: images/9.png

Configuring Database and Schema
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you select the SQL Worksheet, a new page opens.  

**Select the Role and Virtual Warehouse**:   

Click on the dot icon as shown in the screen below. 

.. figure:: images/10.png

As each worksheet can access only the allowed databases, select the **Role** and **Virtual Warehouse**, as shown below. 

.. figure:: images/11.png

**Use case**: Suppose a client wants to log in using the **DATAENGINEER** role and execute code with a **Medium** warehouse size. In this case, they should select the appropriate **Role** and **Virtual Warehouse** as demonstrated above.  

Role: DATAENGINEER 

Run on warehouse: DEVELOPER_MEDIUM 

**Select Database and Schema**: 

Select the database and schema by clicking on **No Database selected** drop-down menu as shown in the screen below.

.. figure:: images/12.png

**Note**: *Only the specified database and schema will have execution access unless explicit permissions are granted to other databases and schemas.*  

**Use case**: Select the database and schema as shown in the screen below. Here, SNOWFLAKE CONNECTOR is selected as database and SFCONNECTOR is selected as schema. 

.. figure:: images/13.png

**Note**: *Once the worksheet is created, it will appear on the dashboard.* 

Once the user opens a new worksheet and selects the appropriate database and schema, they need to execute the shared SQL scripts one by one. These scripts grant the necessary permissions required for the Snowflake app or the logged-in user to run the connector process.  

The first script to be executed is the **initialization script**.  

Running this script creates an egress network rule, which is needed to whitelist the MBAI server URL. 

.. figure:: images/14.png

It also creates the secret store table where the information required for calling MBAI APIs is stored. 

.. figure:: images/15.png

To create a table, click on the schema you created, then click on the '**Create**' dropdown menu.

.. figure:: images/16.png

From the menu, select '**Table**' and choose '**From File**'. 

.. figure:: images/17.png

Adding source data assets 
^^^^^^^^^^^^^^^^^^^^^^^^^

A pop-up window opens to select a warehouse. 

**Use case**: Here, DEVELOPER MEDIUM is selected. 

After selecting the warehouse, a pop-up window will appear. Import input data using **CSV** or **Excel** files by clicking on **Browse**. Select the database, schema, and table, and provide a name for the file you are uploading. Then, click '**Next**'.  

.. figure:: images/18.png

Load data 
^^^^^^^^^

After clicking on **Next**, it takes few seconds to load data into table. Ensure all required columns are correctly populated with metadata. Click **Load** data to table to complete the process.

.. figure:: images/19.png

A success message is displayed after the data is loaded. 

.. figure:: images/20.png

**Note**:  

* *Clicking on 'Query Data' opens a new worksheet*. 
* *Clicking on 'Done' closes the pop-up, and you can then create a worksheet from the dashboard*. 

After this, the remaining scripts should be executed one by one. It is recommended to create separate worksheets for each script to be executed. 

.. figure:: images/21.png

The table below includes the **scripts**. 

.. code-block:: python

    
  DROP TABLE IF EXISTS SNOWFLAKECONNECTOR.SFCONNECTOR.BUSINESS_ENTITIES_INPUTS_KULDIP; 
  DROP TABLE IF EXISTS SNOWFLAKECONNECTOR.FINAL_SCHEMA.MATCH_MATCHOUTPUT_KULDIP; 
  DROP TABLE IF EXISTS SNOWFLAKECONNECTOR.FINAL_SCHEMA.MATCH_ENRICHMENT_KULDIP; 
  DROP TABLE IF EXISTS SNOWFLAKECONNECTOR.FINAL_SCHEMA.STG_KULDIP; 

  

  Call MATCHBOOK_MAINSCRIPT ('SNOWFLAKE_DEV','SFCONNECTOR','AAAA','FINAL_SCHEMA','MATCHOUTPUT_AAAA',50,'[SRC::Demo]'); 
  call matchbook_enrichmentscript('SNOWFLAKE_DEV', 'FINAL_SCHEMA', 'MATCHOUTPUT_DB_147_TESTING_070401', 'FINAL_SCHEMA', 'ENRICHMENT_DB_147_TESTING_070401', 50, '["Firmographics"]'); 
  call downloadmatcheddata('SNOWFLAKE_DEV','FINAL_SCHEMA','STG_KULDIP','FINAL_SCHEMA','MATCHOUTPUT_DB_147_TESTING_070401',10,'[SRC::Demo]'); 

  

  Select * from "SNOWFLAKECONNECTOR"."FINAL_SCHEN"."SNOWFLAKE_DEV_MATCHOUTPUT_DB_147_TESTING_070401"; 


**Note**: *If we need to re-import the worksheet or deploy a new version of the code, we use '*DROP TABLE*' and perform cleanup*. 

* **CALL MATCHBOOK_MAINSCRIPT**: Runs the main Matchbook workflow to cleanse, match, and process your source data. 
  This is the primary script to kick off the end-to-end data pipeline. 

* **CALL MATCHBOOK_ENRICHMENTSCRIPT**: Enriches matched records with additional attributes like firmographics from D&B. 
  Use this after matching to add more context and value to your data. 

* **CALL DOWNLOADMATCHEDDATA**: Exports the final set of matched and enriched records from Snowflake. 
  Use this to retrieve your processed data for reporting or downstream use.  

As the user runs these scripts, they trigger specific stored procedures and functions that Snowflake uses to connect to the MBAI server and retrieve the required data for various tasks.

Here is an example of how it looks: 

**Procedures**: 

.. figure:: images/22.png

**Functions**:

.. figure:: images/23.png

Inside the stage folder, the supporting Python scripts need to be imported. These scripts contain supporting functions for calling the MBAI APIs. They are used within the procedures and functions defined in the SQL scripts.

1.5 Deploying Scripts and Sharing Data in Snowflake
---------------------------------------------------
 
**Script Deployment** 

1. Deploy all three scripts in the Snowflake environment using the correct **Role**, **Database**, and **Schema** as shown. 

2. Run the procedure in your worksheet. Once successfully created, a confirmation message will appear in the **Snowflake message box**. 

.. figure:: images/25.png

**Data Sharing Process** 

1. User Data Sharing 

a. Users share **sample data**, identified by a **client ID** (e.g., MBQA1). 

b. Data is available within a **specific schema**, allowing users to share either the **schema** or **table**. 

2. Granting Access 

a. Perform the **grant share** action to provide access to the recipient. 

b. Verify the recipient and confirm that **Matchbook AI** has access to the shared data. 

3. Process Establishment 

a. Customer Responsibilities: Follow the outlined steps to share data via **Delta Sharing**. 

b. Our Responsibilities: Configure processes and setups based on the shared data. 

4. Process Configuration 

a. Job Creation: Set up scripts for **cleanse, match, classify, enrich, and refresh**. 

b. Configure **job settings** and **parameters** accordingly. 

c. Start by configuring the **share name** in the **Cleanse Match** job. 

**Note**: This sharing process is manual. Our system will automatically generate a **Database** linked to your **client ID**, ensuring seamless access. Each client will have a unique identifier. 

**Viewing Shared Tables** 

To view the tables shared with you, follow these steps: 

1.5.1 Managing the key vault
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Snowflake does not support adding secrets through a standard user account. Instead, secrets must be managed by an **authorized user**. 

* Secrets can be organized within a **secret store**, where each store is dedicated to a specific client. For example, we establish **client-specific scopes** to manage API keys and secrets securely. 

* To demonstrate, we can **list the secrets** within a secret store by specifying the **client's name** as the secret store. Within this store, we can manage and update various credentials, such as **tokens, API keys, and other sensitive data**. 

1.5.2 Managing Auth token
^^^^^^^^^^^^^^^^^^^^^^^^^

* The authentication token is used to **automate specific processes**, such as triggering the **enrichment data process**. To achieve this, we make calls to the **Snowflake REST API**. 

* This token is **generated directly from Snowflake** and is specific to the **Snowflake database**, not our own system. It is solely used to **authenticate and trigger Snowflake API calls** for process execution. 

1.5.3 Retrieving the Token for the Client 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Each script **generates a token at the beginning**, which is then **used throughout the execution** to avoid multiple token generations. A **new token is only created if the existing one expires**, ensuring efficient token management and reducing unnecessary requests. Once processed, the **output table will contain new records**, and the **next script will trigger automatically**.  

1.5.4 Process Enrichment Data 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A Python script is created for each client, integrating essential values such as **MB_QA1, API keys**, and secrets for initial setup. This script automates the generation of required components, allowing clients to set up jobs while enabling direct REST API calls, eliminating manual intervention.

**Enrichment Classes Optimization** 

Optimizing enrichment classes by improving stage table performance and centralizing parameters for streamlined management. 

* **Stage Table Optimization**- Enhance the structure and performance of the stage table. 

* **Centralized Parameters**- Consolidate and organize all parameters in a single location for efficient management. 

*Optimization*
""""""""""""""

To improve efficiency, the process focuses on enhancing the stage table's structure and performance, centralizing parameters for better management, and implementing input merge updates to streamline data handling.  

* Enhance the structure and performance of the stage table. 

* Centralize all parameters for better management. 

* Implement input merge updates for efficiency.

.. figure:: images/26.png

*Run Jobs*
""""""""""

Go to the **Procedure** tab within the schema and select the **Enrichment** script. 

*Running the Cleansematch Process* 
""""""""""""""""""""""""""""""""""

The Cleansematch process is a crucial first step in data processing. It involves executing the script with the required arguments, validating configurations, and managing the Database and tables.  

Initial Setup 
~~~~~~~~~~~~~

* The **Cleansematch** process is mandatory and should be run first. 

* Execute the script with the required arguments. 

.. figure:: images/27.png

Configuration and Validation 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Verify that **batch_size** is between 1 and 50; otherwise, an error will be thrown. 

* Validate the **delta_sharing_mode** configuration parameter. 

* Ensure the **share name** matches the **client_id**. 

Database and Table Management 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* If the **Database** does not exist, it will be created automatically. 

* If the **destination_table** is unavailable, clone the **source_table** and add a **Status** column, defaulting to **NEW**. 

*Process Enrichment Data*
""""""""""""""""""""""""""

If the delta_sharing_mode configuration parameter is provided, the system validates the provider using the delta_sharing_identifier. If a valid provider is not found, an error is thrown. When clients share data via delta sharing, they must ensure that the share name matches the client_id, as this is essential for identifying the correct share when multiple clients are involved. If there is a mismatch, an error will be triggered. Once the share is successfully identified, the system will automatically create the Database if it does not already exist.   

If the **destination_table** (where the match output result will be stored) is not available: 

* Clone the **source table**. 

* Add a new column called **Status** and set its value to **NEW**. 

* Set **NEW** as the default value so that all newly inserted records have this status. 

If the **destination_table** is available: 

* Sync the **source_table** and **destination_table** using the primary key provided by the user. Match records and add any that do not exist in the **destination_table**. 

* Sync records with **ERROR** status that have been updated in the **source_table**, update them in the **destination_table**, and set their status to **NEW** for reprocessing. 

* During the syncing process, save the current **UTC timestamp** in the table as a checkpoint for future synchronization of records with **ERROR** status. 

For all records in the **destination_table**: 

* If a record is **NEW** and **Tags** is **NULL** or **EMPTY**, update **Tags** to **NULL** if it is **EMPTY**. 

* If the **Tags** parameter is provided, update records with the provided value in **Tags**. 

* If a record is **not processed** (**Processed = False**), it will be considered for **CleanseMatch** processing. 

We will call the **MatchEnrich API** based on the **batch_size** value, processing data in chunks with parallelism determined by **batch_size**. After each chunk is processed, the results will be updated in the **destination_table**. At the end of the process, the **Process Enrichment Data** job will be triggered automatically, but only if there is data to be processed.

.. list-table::
    :header-rows: 1

    * - Field
      - Description
    * - Parameters
      - 
    * - The Database
      - Unity Database name where input data is available.  
        If mode is delta sharing, then Database belongs to where you want to store your output results.  
        If mode is direct, then Database belongs to where your input data is available and want to store the output result as well.
    * - Client identifiers
      - Uniquely identifies different clients in delta sharing mode.
    * - Source schema
      - Name of schema where source data is available.
    * - detailing data 
      - 
    * - Destination schema
      - Name of schema where you want to store match output data.
    * - API URL
      - Required to call Matchbook AI APIs.
    * - batch size
      - Size of batch. This allows parallel Matchbook API calls. Must be between 1 to 50.
    * - A primary key
      - Column name from source data which is uniquely identified across the entire dataset. This is needed to properly function jobs, making updates in the dataset, etc.
    * - An instance URL
      - Required to call Databricks' REST APIs.

**Note**: *This pertains to our instance, as it is running on our dataset*. 

.. list-table::
    :header-rows: 1

    * - Field
      - Description
    * - Parameters
      - 
    * - Source schema
      - Name of schema where source data is available.
    * - Source table
      - Name of table in which source data is available to do the matching.
    * - Column mapping
      - Workflow needs to call Matchbook API with specific fields, if your data does not have exact columns then you can create a mapping where you can map your columns to Matchbook AI’s conventions. 
    * - Parallel API calls
      - Maximum is 50.
    * - Scrd ID
      - Source record ID associated with each record.

Downloading Matched Data
""""""""""""""""""""""""

.. figure:: images/28.png

After the enrichment process, download the matched data. If a record previously had no matches, it may now have multiple matches, and a **DUNS number** will be generated.  

2. Implementation guide 
=======================

This integration enables Snowflake customers to seamlessly utilize Matchbook AI’s CleanseMatch and enrichment APIs for efficient data transformation and enhancement within their Snowflake environment. By incorporating these APIs, users can perform advanced data cleansing, matching, and enrichment without the need to switch between multiple tools or platforms. This streamlines data pipelines, ensuring accuracy, consistency, and readiness for downstream analysis. Additionally, the integration is designed for scalability, allowing users to process large datasets while maintaining high performance and reliability.   

2.1 Implementation Commands.sql
-------------------------------

Run the scripts **one by one**, ensuring that each executes successfully and returns a **successful message**. Follow the **step-by-step process** in the correct **sequence order**, without skipping any commands. 

.. figure:: images/29.png

**Note**: *During Onboarding process, the* **Implementations_Commands.sql** *file will be shared*. 

2.2 General Requirements 
------------------------

The **General Requirements** outline the essential components necessary for the seamless operation and management of the integration. These requirements ensure security, efficiency, and consistency across various processes. Key aspects include Secrets Management, Cleanup, Logging, Onboarding, Common configuration, Retreival. Let's go through each one in detail.  

2.2.1 Secrets Management
^^^^^^^^^^^^^^^^^^^^^^^^

All sensitive information and common configurations must be securely stored in a secret manager. This includes API credentials and other client-specific settings. Secrets must be scoped per client to ensure data isolation and prevent cross-access. 

Scripts should dynamically retrieve values like the API key, secret, URL, auth token, and subdomain- all provided by Matchbook AI from the secret manager at runtime. Common configurations are centrally managed to promote consistency across jobs. 

.. figure:: images/30.png

**Column Mapping** 

If the customer’s input data has different column names than those expected by Matchbook AI, a column mapping must be defined. This mapping ensures the data aligns with Matchbook’s required structure and enables proper processing. 

Details on each configuration item and how to retrieve or use them are available in section 2.2.5 and section 2.2.6 of this document. 

.. figure:: images/31.png

2.2.2 Cleanup 
^^^^^^^^^^^^^

A script is required to drop all tables in the Snowflake environment associated with a specific client. It should accept a client identifier as an argument and remove all tables linked to that client. 

.. figure:: images/21.png

2.2.3 Logging
^^^^^^^^^^^^^^

All scripts must support verbose logging to effectively monitor progress and track execution details. The logging should utilize standard Python logging or an equivalent framework in Snowflake, ensuring clear, structured, and accessible log records for debugging and analysis.  


.. code-block:: python

   
  import logging 

  logging.warning("There are no data to process.") 


2.2.4 Onboarding 
^^^^^^^^^^^^^^^^

Currently, our workflows in Databricks execute Python scripts with runtime arguments. To automate client onboarding, we use scripts that take workflow names and arguments to create workflows instead of configuring them manually. This process is facilitated through Databricks SDKs, and an equivalent should be available in Snowflake for seamless interaction with its components. The script below accepts arguments such as workflow name, client ID, and runtime parameters to create a workflow accordingly. Since we manage three main workflows, similar scripts should be developed to generate workflows using different sets of arguments.


.. code-block:: python

  created_job = workspace_client.jobs.create(name=f'{client_id} - Cleansematch', email_notifications=jobs.JobEmailNotifications( on_failure=['user@email.com'], no_alert_for_skipped_runs=True), timeout_seconds=0, max_concurrent_runs=1, tasks=[ jobs.Task(task_key="Cleansematch", run_if=RunIf(RunIf.ALL_SUCCESS), spark_python_task=jobs.SparkPythonTask( python_file="/Users/knaghera@matchbookai.com/.ide/Databricks_notebooks-9cd5b82c/main.py", parameters=[ "--client_id", f"{client_id}", "--source_schema", f"{source_schema}", "--source_table", f"{source_table}", "--destination_schema", f"{destination_schema}", "--destination_table", f"{destination_table}", "--batch_size", f"{batch_size}", "--tags", f"{tags}" ] ), existing_cluster_id=cluster_id, timeout_seconds=0, notification_settings=jobs.TaskNotificationSettings( no_alert_for_skipped_runs=False, no_alert_for_canceled_runs=False, alert_on_last_attempt=False )) ], run_as=jobs.JobRunAs(user_name='user@email.com'))  

2.2.5 Common Configuration 
^^^^^^^^^^^^^^^^^^^^^^^^^^

We maintain several common configurations shared across multiple jobs, ensuring consistency and efficiency. These configurations are securely stored in the secret manager for centralized access, allowing scripts to retrieve them dynamically as needed. 

.. list-table::
    :header-rows: 1

    * - Name
      - Data Type
      - Retrieval
      - Usage
    * - api_key
      - string
      - Matchbook AI portal
      - Required to call Matchbook AI APIs.
    * - api_secret
      - string
      - Matchbook AI portal
      - Required to call Matchbook AI APIs.
    * - api_url
      - string
      - https://api.my.matchbookservices.com
      - Required to call Matchbook AI APIs.
    * - Database
      - string
      - Snowflake portal
      - This is the name of the database. each client will have their separate database in Snowflake.
    * - column_mapping
      - string
      - Custom
      - Workflow needs to call Matchbook API with specific fields. If your data does not have exact columns, you can map your columns to Matchbook AI’s conventions.
    * - job_id
      - string
      - Snowflake portal
      - We want to trigger the Process Enrichment Data after completion of Cleansematch & Download Matched Data jobs. This job id belongs to the Process Enrichment Data.
    * - primary_key
      - string
      - Input dataset
      - Column name from source data which is uniquely identified across the entire dataset. This is needed to properly function jobs, make updates, etc.
    * - subdomain
      - string
      - Matchbook AI portal
      - Required to call Matchbook AI APIs. We pass it in the API call while retrieving token.

2.2.6 Retrieval 
^^^^^^^^^^^^^^^

**Column Mapping**: The column mapping should adhere to the following format, where the keys remain fixed, and the values correspond to the dataset's column names.

.. code-block:: python

   
  { 

  'SrcRecordId': 'SrcId', 

  'CompanyName': 'Company', 

  'Address': 'Address', 

  'Address2': 'Address2', 

  'City': 'City', 

  'State': 'State', 

  'PostalCode': 'PostalCode', 

  'Country': 'Country', 

  'PhoneNbr': 'PhoneNbr', 

  'Tags': 'Tags', 

  'UserName': 'UserName', 

  'InLanguage': 'InLanguage', 

  'InpDUNS': 'InpDUNS', 

  'RegistrationNumber': 'RegistrationNumber', 

  'RegistrationNumberType': 'RegistrationNumberType', 

  'Domain': 'Domain', 

  'Email': 'Email' 

   } 


2.3 Workflow 
------------

2.3.1 Cleansematch (main.py) 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This script is the core process for cleansing and matching source data using the Matchbook AI API. It standardizes input data, removes duplicates, and enriches records by leveraging advanced matching algorithms while retrieving configurations securely from the secret manager.  

*Job Configurations*
""""""""""""""""""""

Job configurations define the essential parameters required for executing a job. These configurations specify how data should be processed, where it is sourced from, and where the output should be stored. Each configuration includes a name, requirement status, data type, and description of its usage.  

.. list-table::
    :header-rows: 1

    * - Name
      - Required
      - Data Type
      - Usage
    * - client_id
      - Yes
      - string
      - Uniquely identifies different clients in delta sharing mode.
    * - source_schema
      - Yes
      - string
      - Name of schema where source data is available.
    * - source_table
      - Yes
      - string
      - Name of table in which source data is available to do the matching.
    * - destination_schema
      - Yes
      - string
      - Name of schema where you want to store match output data.
    * - destination_table
      - Yes
      - string
      - Name of table where you want to store match output data.
    * - batch_size
      - Yes
      - string
      - Size of batch, this will allow to make parallel Matchbook API calls, must be between 1 to 50.
    * - tags
      - No
      - string
      - If you want to pass tags in Matchbook API call.

*Process* 
"""""""""

1. **Parse**, **Prepare**, and **Set Arguments** & **Secret Manager Values** 

Extract and validate required arguments and configurations from the secret manager. 

2. **Validate batch_size Configuration** 

Ensure batch_size is between 1 and 50; otherwise, throw an error. 

3. **Check and Create Database** 

Verify if the database exists; if not, create it. Typically, client_id serves as the database name. 

4. **Check and Create destination_schema** 

Ensure the destination_schema exists; if not, create it. 

5. **Handle destination_table** (**where we store the match output result**) 

* If destination_table does not exist: 

i. Clone the source_table. 

ii. Add a new column called Status and set its default value to NEW. 

iii. Make it default as NEW so when new records get inserted, it will be set to NEW.  

* If destination_table exists: 

i. Sync source_table and destination_table using the primary key provided by the user, do the matching and if the record does not exist in the destination_table, then we will add it.  

ii. Sync records which is already processed with ERROR status. If the record is already updated in source_table, then update them in destination_table, and reset their status to NEW for reprocessing. 

iii. Store the current UTC timestamp as a checkpoint for future syncing of ERROR records. 

6. **Update Records in destination_table** 

* If Status is NEW and Tags is NULL or empty, update Tags to NULL. 

* If a Tags parameter is provided, update records with the given value. 

* If UserName is NULL, update it to 'Snowflake_User'. 

* If a Processed column exists, retrieve records where Processed = NULL or Processed = FALSE based on batch_size. 

* If no Processed column exists, retrieve records in batches using batch_size. 

* If no records are available, display a message such as Chunk x empty. 

7. **Process Data** 

* Mark retrieved records as MATCHING. 

* Call the MatchEnrich API in parallel, using batch_size to create chunks and optimize performance. 

* Update the results in destination_table after processing each chunk. 

8. **Trigger Process Enrichment Data Job** 

If data is available for processing, automatically trigger the Process Enrichment Data job at the end of the workflow. 

Columns added by Matchbook AI in the destination_table 
""""""""""""""""""""""""""""""""""""""""""""""""""""""

.. list-table::
    :header-rows: 1

    * - Column
      - Purpose
    * - StatusCode
      - Status code returned by API call
    * - ResultText
      - Matched, Matched Candidates, or any other message provided by API like error
    * - Status
      - Status of the record
    * - MatchOutput
      - Match output data returned by Matchbook AI API
    * - EnrichmentOutput
      - Enrichment data returned by Matchbook AI API
    * - Processed
      - Mark record as processed so it will skip next time
    * - ProcessedAt
      - Timestamp in UTC when record was processed
    * - DnBDUNSNumber
      - DnBDUNSNumber of record

**Note**: 

* *The batch_size parameter must be respected, as it is directly proportional to the client’s API rate limit. Parallel API calls can be made, but they must remain within the batch_size limit*. 

* *API calls should be retried in case of failures. HTTP status codes 500, 502, 503, 504, and 429 should trigger a retry. The maximum number of retries is 3, with an exponential backoff before each retry*. 

2.3.2 Download Matched Data (refresh_records.py)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This job oversees the retrieval of manually reviewed and updated records from the Matchbook AI portal. It ensures that any data stewardship actions performed by users, such as corrections, approvals, or enhancements, are accurately reflected in the Snowflake environment.  

Navigate to the **Matchbook AI portal**, and from the **left-hand menu**, select **Data Stewardship**. Under this section, choose **Low Confidence** to access records that require manual review. 

.. figure:: images/32.png

The **Low Confidence** section in data stewardship is designed to help users manage records that require manual review due to lower confidence scores. These are typically cases where automated rules don't provide sufficient certainty for acceptance and need user intervention to ensure high data integrity. 

For example, if your **Auto-Acceptance** rule is configured to accept candidates with a **confidence code of 8, 9, or 10**, then any D&B candidate with a **confidence code of 7 or lower** will be routed to the low confidence queue for review. 

Within this section, users have powerful tools to **filter, accept, reject, and score** records. To begin reviewing, navigate to the **Order by Columns Equals** field and select **SrcRecordID** — this may already be pre-selected by default. Once selected, a table of candidates will be displayed. To view and interact with a specific record, simply click on the **‘+’ icon** next to it, as shown in the screen below. 

.. figure:: images/33.png

Under the **Matchbook Rank** column, you will see the **score percentage**. Based on the **highest score**, click on the **black rectangle** under the **Match** column, as shown below. Once selected, it will turn **green**, indicating that the record has been chosen.  

.. figure:: images/34.png

Now, click on **Update** on the top-right to update the selected record. 

.. figure:: images/35.png

Click on **Dashboard** from the **left-hand menu**. Under **Background Process Statistics**, you will see the **execution time** taken to complete the process.

.. figure:: images/36.png

*Job Configurations*
""""""""""""""""""""

Job configurations define the essential parameters for storing, retrieving, and processing data.

.. list-table::
    :header-rows: 1

    * - Name
      - Required
      - Data Type
      - Usage
    * - client_id
      - Yes
      - string
      - Uniquely identifies different clients in delta sharing mode.
    * - source_schema
      - Yes
      - string
      - Name of schema where we want to store the match output stage data.
    * - source_table
      - Yes
      - string
      - Name of table in which we want to store the match output stage data
    * - destination_schema
      - Yes
      - string
      - Name of schema where match output data is available.
    * - destination_table
      - Yes
      - string
      - Name of table where match output data is available.
    * - batch_size
      - Yes
      - string
      - Size of batch, this will allow to make parallel Matchbook API calls, must be between 1 to 50.

*Source Table Schema*
"""""""""""""""""""""

The **Source Table Schema** outlines the key fields used to store and track source records, including their identifiers, processing status, timestamps, and enrichment details.  

.. list-table::
    :header-rows: 1

    * - Name
      - Purpose
    * - SrcRecordId
      - Source record is associated with each record.
    * - Tags
      - Tags which were used while calling the Matchbook AI API.
    * - DnBDUNSNumber
      - DnBDUNSNumber of record.
    * - CreatedAt
      - UTC timestamp of when record was inserted.
    * - Matched
      - Whether CleanseMatchResult API call was performed for this record or not.
    * - MatchedAt
      - When CleanseMatchResult API call was performed, UTC timestamp.
    * - Enriched
      - Whether RefreshDUNS API call was performed for this record or not.
    * - EnrichedAt
      - When RefreshDUNS API call was performed, UTC timestamp.

*Process* 
""""""""""

1. **Parse, Prepare, and Set Arguments & Secret Manager Values**

Extract and validate required arguments and configurations from the secret manager. 

2. **Validate batch_size Configuration** 

Ensure batch_size is between 1 and 50; otherwise, throw an error. 

3. **Ensure Required Schemas Exist** 

Create source_schema and destination_schema if they do not already exist. 

4. **Fetch Data from Matchbook API** 

* Continuously call the DownloadMatchOutput API until IsLastPage = true. 

* Collect and store the API response in source_table. 

5. **Remove Duplicates** 

Eliminate duplicate records from source_table using SrcRecordId, Tags, and DnBDUNSNumber. 

6. **Process Matched Records** 

* If records exist in source_table, proceed with processing. 

* Identify unmatched records (Matched = False or Matched = NULL). 

* Get records from the source_table order by the SrcRecordId using LIMIT by batch_size. 

* Call the CleanseMatchResult API and update destination_table. 

* Sync updates in destination_table based on SrcRecordId (and Tags, if available in both source and destination tables). 

* Update source_table, setting Matched = True and MatchedAt to the current UTC timestamp. 

7. **Process Enriched Records** 

* Identify non-enriched records (Enriched = False or Enriched = NULL). 

* Get records from the source_table order by the SrcRecordId using LIMIT by batch_size. 

* Call the RefreshDUNS API and update destination_table. 

* Sync updates in destination_table based on SrcRecordId (and Tags, if available in both tables). 

* Update source_table, setting Enriched = True and EnrichedAt to the current UTC timestamp. 

8. **Trigger Process Enrichment Data Job**

Automatically trigger the Process Enrichment Data job if there is data to be processed. 

9. **Clean Up source_table** 

Remove records where both Matched = True and Enriched = True. 

2.3.3 Process Enrichment Data (process_enchrichment_data.py) 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This job is responsible for processing enrichment data.  
 
*Job Configurations*
""""""""""""""""""""

The **Job Configurations** define key parameters for processing and storing enrichment data. 

.. list-table::
    :header-rows: 1

    * - Name
      - Required
      - Data Type
      - Usage
    * - client_id
      - Yes
      - string
      - Uniquely identifies different clients in delta sharing mode.
    * - source_schema
      - Yes
      - string
      - Name of schema where match output data is available.
    * - source_table
      - Yes
      - string
      - Name of table where match output data is available.
    * - destination_schema
      - Yes
      - string
      - Name of schema where you want to store the enrichment data.
    * - destination_table
      - Yes
      - string
      - Name of table where you want to store the enrichment data.
    * - batch_size
      - Yes
      - string
      - Size of batch, this will allow to process data in chunk.
    * - enrichment_types
      - Yes
      - string
      - Type of enrichments you want to consider for processing.

*Destination Table Schema*
""""""""""""""""""""""""""

The **Destination Table Schema** defines the structure for storing enriched data, including enrichment type, DnBDUNSNumber, response details, and timestamps.

.. list-table::
    :header-rows: 1

    * - Field
      - Description
    * - EnrichmentType
      - Type of enrichment, this is tied with enrichment_types configuration.
    * - DnBDUNSNumber
      - DnBDUNSNumber of record.
    * - ResponseJSON
      - Enrichment response JSON.
    * - CreatedAt
      - When this record was inserted in UTC timestamp.

*Process Flow*
""""""""""""""

1. **Parse, Prepare, and Set Arguments & Secret Manager Values** 

Extract and validate required arguments and configurations from the secret manager. 

2. **Validate batch_size Configuration** 

Ensure batch_size is between 1 and 50; otherwise, throw an error. 

3. **Retrieve Enriched Records** 

* Get all records with columns primary_key, EnrichmentOutput from the source_table where Status is ENRICHED order by primary_key limit batch_size. 

* If enrichment_types is same as passed argument enrichment_types then proceed further. That is proceed only if enrichment_types matches the provided argument. 

* Extract EnrichmentType and ResponseJSON from the retrieved records. 

* If destination_table does not exist, create it and insert the extracted data. 

* If destination_table exists: 

i. If a record with the same DnBDUNSNumber and EnrichmentType exists, update ResponseJSON and CreatedAt. 

ii. Otherwise, insert a new record with EnrichmentType, DnBDUNSNumber, ResponseJSON, and CreatedAt. 

* For all the records that are retrieved from the source_table, mark status as PROCESSED_ENRICHMENT_DATA using primary_key. 

4. **Skip Processing if No Records Found** 

If there are no records to process, skip the above steps.

2.3.4 Monitoring 
^^^^^^^^^^^^^^^^

By integrating the Monitoring workflow with the Snowflake environment, organizations can efficiently automate the process of tracking and managing data operations. This setup involves invoking two critical APIs- one for download monitoring notifications and another for download transfer DUNS output. These API calls are orchestrated via a Snowflake stored procedure, which executes the workflow and stores the resulting data into designated Snowflake tables. This enables real-time visibility into data movements and provides clients with structured outputs that can be leveraged for further processing, auditing, or analytics.  

Go to '**Projects**' in the left-side menu and select '**Worksheets**'.

.. figure:: images/37.png

On the top-right, click on the drop-down menu. 

.. figure:: images/38.png

Select **Create SQL Worksheet** as shown in the screen below.

.. figure:: images/39.png

Click on the drop-down menu and select the database and schema as shown below.

.. figure:: images/40.png

For the Snowflake implementation, there are three main scripts involved.  

1. **Integration Monitoring**: This primarily handles the configuration of secrets required for making API calls. This includes details such as the client ID, API key, API secret, and the backend API endpoint- specifically, the Matchbook server API. These credentials and endpoints are essential to securely authenticate and communicate with the APIs during the monitoring workflow. 

.. figure:: images/41.png

**SQL Command**: 

.. code-block:: python
  

  ---- SET UP START --------- 

  CREATE OR REPLACE NETWORK RULE SNOWFLAKECONNECTOR.SFCONNECTOR.NR_API 

  MODE = EGRESS 

  TYPE = HOST_PORT 

  VALUE_LIST = ('api-qa.my.matchbookservices.com:443'); 

 

 

  CREATE OR REPLACE SECRET SNOWFLAKECONNECTOR.SFCONNECTOR.SECRET_API 

  TYPE = GENERIC_STRING 

  SECRET_STRING = 'aTZVYnNOSEtoVUF5SVAwWTBQeWFXZz09OnQ2bUNobHYzR1RlVHJ5eFk0czhaUWc9PQ=='; 

 

  CREATE OR REPLACE EXTERNAL ACCESS INTEGRATION apis_access_integration 

  ALLOWED_NETWORK_RULES = (SNOWFLAKECONNECTOR.SFCONNECTOR.NR_API) 

  ALLOWED_AUTHENTICATION_SECRETS = (SNOWFLAKECONNECTOR.SFCONNECTOR.SECRET_API) 

  ENABLED = TRUE; 

 

  ---- SET UP END --------- 

  ------Secrets Creation--------------------------------- 

  CREATE OR REPLACE TABLE monitoring_dev_secrets_store ( 

         key_name STRING, 

         secret_value STRING 

     ); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ('client_id', 'monitoring_dev'); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ('api_key', 'Ne5HFLeAFPZ5lWJkyLB9sQ=='); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ('api_secret', 'sEWOxfEZ9VRDbwLCiJT71w=='); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ( 

         'api_url', 

         'https://api-qa.my.matchbookservices.com' 

     ); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ( 

         'auth_token', 

         'aTZVYnNOSEtoVUF5SVAwWTBQeWFXZz09OnQ2bUNobHYzR1RlVHJ5eFk0czhaUWc9PQ==' 

     ); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ('Database', 'SNOWFLAKECONNECTOR'); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) VALUES ('column_mapping','{ 

     "SrcRecordId": "SrcId", 

     "CompanyName": "Company", 

     "Address": "Address", 

     "Address2": "Address2", 

     "City": "City", 

     "State": "State", 

     "PostalCode": "PostalCode", 

     "Country": "Country", 

     "PhoneNbr": "PhoneNbr", 

     "Tags": "Tags", 

     "UserName": "UserName", 

     "InLanguage": "InLanguage", 

     "InpDUNS": "InpDUNS", 

     "RegistrationNumber": "RegistrationNumber", 

     "RegistrationNumberType": "RegistrationNumberType", 

     "Domain": "Domain", 

     "Email": "Email" 

  }'); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ( 

         'databricks_instance_url', 

         'https://dbc-64cc9203-e861.cloud.databricks.com' 

     ); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ( 

         'delta_sharing_identifier', 

         'aws:us-east-1:53a661c7-c5c7-4b43-a35f-57c2279805fe' 

     ); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ('job_id', '549094540964005'); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ('primary_key', 'Index'); 

  INSERT INTO monitoring_dev_secrets_store (key_name, secret_value) 

  VALUES ('subdomain', 'mb-qa1'); 

 

 

  --------------------------------------------------------- 

  ---- SET UP START --------- 

 

  CREATE OR REPLACE NETWORK RULE api_network_rule 

   MODE = EGRESS 

   TYPE = HOST_PORT 

   VALUE_LIST = ('api-qa.my.matchbookservices.com','0.0.0.0:443','dbc-64cc9203-e861.cloud.databricks.com'); 

 

  CREATE OR REPLACE NETWORK RULE SNOWFLAKECONNECTOR.SFCONNECTOR.NR_API 

   MODE = EGRESS 

   TYPE = HOST_PORT 

   VALUE_LIST = ('api-qa.my.matchbookservices.com:443'); 

 

  CREATE OR REPLACE SECRET SNOWFLAKECONNECTOR.SFCONNECTOR.SECRET_API 

   TYPE = GENERIC_STRING 

  SECRET_STRING = 'aTZVYnNOSEtoVUF5SVAwWTBQeWFXZz09OnQ2bUNobHYzR1RlVHJ5eFk0czhaUWc9PQ=='; 

 

  CREATE OR REPLACE EXTERNAL ACCESS INTEGRATION apis_access_integration 

   ALLOWED_NETWORK_RULES = (SNOWFLAKECONNECTOR.SFCONNECTOR.NR_API) 

   ALLOWED_AUTHENTICATION_SECRETS = (SNOWFLAKECONNECTOR.SFCONNECTOR.SECRET_API) 

   ENABLED = TRUE; 

 

  ---- SET UP END --------- 


2. **Monitoring UDF**: This is where the core logic of the workflow is implemented. Within this script, there are three main functions. The first function is responsible for generating an access token, which is required to authenticate and access the Matchbook server. This access token is essential for making the subsequent API calls. Once the access token is obtained, the second function uses it to call the **DownloadMonitoringNotification API**. The response from this API call is written into the first target table. After that, the third function makes a second API call to **DownloadTransferDUNS API**, and the response from this call is written into another target table.

.. figure:: images/41.png

**SQL Command**: 

.. code-block:: python

   
  CREATE OR REPLACE PROCEDURE DoMonitoring( 

     PageNumber INT, 

     ApiName STRING, 

     MarkAsExported BOOLEAN, 

     DownloadNotificationTable STRING, 

     TransferDUNSTable STRING 

  ) 

  RETURNS STRING 

  LANGUAGE PYTHON 

  RUNTIME_VERSION = '3.8' 

  PACKAGES = ('requests', 'snowflake-snowpark-python') 

  HANDLER = 'main' 

  EXTERNAL_ACCESS_INTEGRATIONS = (apis_access_integration) 

  AS 

  $$ 

  import requests 

  import json 

  import random 

  from datetime import datetime 

  from snowflake.snowpark import Session 

 

  def main(session: Session, PageNumber, ApiName, MarkAsExported, DownloadNotificationTable, TransferDUNSTable): 

     # Fetch secrets from the secrets store 

     secrets = session.sql("SELECT key_name, secret_value FROM MONITORING_DEV_SECRETS_STORE").collect() 

     secrets_dict = {row['KEY_NAME']: row['SECRET_VALUE'] for row in secrets} 

 

     # Extract required parameters from secrets 

     api_url = secrets_dict['api_url'] 

     subDomain = secrets_dict['subdomain'] 

     auth_token = secrets_dict['auth_token'] 

 

     headers = { 

         "Authorization": f"Basic {auth_token}" 

     } 

 

     # Call the GetToken API 

     get_token_url = f"{api_url}/GetToken?subDomain={subDomain}" 

     try: 

         response = requests.get(get_token_url, headers=headers) 

         response.raise_for_status() 

         token_data = response.json() 

         access_token = token_data['access_token'] 

     except requests.exceptions.RequestException as e: 

         return { 

             "Processed": False, 

             "Error": f"GetToken API request failed: {str(e)}", 

             "StatusCode": e.response.status_code if e.response else None, 

             "ResultText": e.response.text if e.response else None, 

             "Status": "ERROR" 

         } 

 

     # Call the DownloadMonitoringNotifications API 

     download_url = f"{api_url}/DownloadMonitoringNotifications?token={access_token}&PageNumber={PageNumber}&ApiName={ApiName}&MarkAsExported={MarkAsExported}" 

     try: 

         download_response = requests.get(download_url, headers={"Authorization": f"Bearer {access_token}"}) 

         download_response.raise_for_status() 

         download_data = download_response.json() 

 

         # Create table if it doesn't exist 

         session.sql(f""" 

         CREATE TABLE IF NOT EXISTS {DownloadNotificationTable} ( 

             id STRING, 

             type STRING, 

             response STRING, 

             timestamp TIMESTAMP 

         ) 

         """).collect() 

 

         # Prepare response with metadata 

         response_with_metadata = { 

             "id": str(random.randint(1000, 9999)), 

             "type": "DownloadMonitoringNotifications", 

             "response": json.dumps(download_data).replace("'", "''"), # Escape single quotes 

             "timestamp": datetime.now() 

         } 

 

         # Insert response into the table 

         insert_query = f""" 

         INSERT INTO {DownloadNotificationTable} (id, type, response, timestamp) 

         VALUES ('{response_with_metadata["id"]}', '{response_with_metadata["type"]}', '{response_with_metadata["response"]}', '{response_with_metadata["timestamp"]}') 

         """ 

         session.sql(insert_query).collect() 

     except requests.exceptions.RequestException as e: 

         return { 

             "Processed": False, 

             "Error": f"DownloadMonitoringNotifications API request failed: {str(e)}", 

             "StatusCode": e.response.status_code if e.response else None, 

             "ResultText": e.response.text if e.response else None, 

             "Status": "ERROR" 

         } 

 

     # Call the DownloadTransferDUNS API 

     transfer_url = f"{api_url}/DownloadTransferDUNS?token={access_token}&MarkAsExported={MarkAsExported}" 

     try: 

         transfer_response = requests.get(transfer_url, headers={"Authorization": f"Bearer {access_token}"}) 

         transfer_response.raise_for_status() 

         transfer_data = transfer_response.json() 

 

         # Create table if it doesn't exist 

         session.sql(f""" 

         CREATE TABLE IF NOT EXISTS {TransferDUNSTable} ( 

             id STRING, 

             type STRING, 

             response STRING, 

             timestamp TIMESTAMP 

         ) 

         """).collect() 

 

         # Prepare response with metadata 

         transfer_response_with_metadata = { 

             "id": str(random.randint(1000, 9999)), 

             "type": "DownloadTransferDUNS", 

             "response": json.dumps(transfer_data).replace("'", "''"), # Escape single quotes 

             "timestamp": datetime.now() 

         } 

 

         # Insert response into the table 

         transfer_insert_query = f""" 

         INSERT INTO {TransferDUNSTable} (id, type, response, timestamp) 

         VALUES ('{transfer_response_with_metadata["id"]}', '{transfer_response_with_metadata["type"]}', '{transfer_response_with_metadata["response"]}', '{transfer_response_with_metadata["timestamp"]}') 

         """ 

         session.sql(transfer_insert_query).collect() 

      except requests.exceptions.RequestException as e: 

         return { 

             "Processed": False, 

             "Error": f"DownloadTransferDUNS API request failed: {str(e)}", 

             "StatusCode": e.response.status_code if e.response else None, 

             "ResultText": e.response.text if e.response else None, 

             "Status": "ERROR" 

         } 

 

     return 'API calls completed successfully' 

 

  $$; 


The below **stored procedure** is responsible for orchestrating the entire process, including making all the API calls. When we execute this stored procedure, we pass several parameters: 

.. figure:: images/42.png

.. list-table::
    :header-rows: 1

    * - Field
      - Description
    * - Page Number
      - This corresponds to the paginated Download Monitoring Notification API.  
        Example: we might pass page 1. That is 1, --PageNumber.
    * - Group Name
      - This is the identifier for the group under which the clients are registered. We're querying data related to this specific group.  
        Example: **‘Matchbook_blocks_Finanace_Test_1’**
    * - Mark as Exported
      - We are passing this as **false** for this run.
    * - Target Table Names
      - **`DOWNLOAD_MONITORING_OUTPUT`** and **`DOWNLOAD_TRANSFER_DUNS_OUTPUT`** are the two tables where the API responses will be written.

Once the procedure is executed, the first API call (**DownloadMonitoringNotification**) runs successfully, and the response is written into the first table. This response is typically a large JSON payload and is dumped as-is into the table. 

.. figure:: images/43.png

Next, the second API call (**DownloadTransferDUNS**) is made, and its response is written into the second table.

.. figure:: images/44.png

3. **Testing**: The third part of the script is primarily used for **testing** the overall flow. It triggers the execution of the UDF and validates the end-to-end process. As part of this implementation, two new tables are created to store the responses from each of the API calls.

.. figure:: images/45.png

**SQL Command**: 


.. code-block:: python

   
  delete from SNOWFLAKECONNECTOR.SFCONNECTOR.DOWNLOAD_MONITORING_OUTPUT where id > 0; 

 

  delete from SNOWFLAKECONNECTOR.SFCONNECTOR.DOWNLOAD_TRANSFER_DUNS_OUTPUT where id > 0; 

 

 

  --- TEST MONITORING SNOWFLAKE APP --- 

  CALL DoMonitoring( 

     1, -- PageNumber 

     'Matchbook_Blocks_Finance_TEST_1', -- ApiName 

     false, -- MarkAsExported 

     'DOWNLOAD_MONITORING_OUTPUT', -- DownloadNotificationTable 

     'DOWNLOAD_TRANSFER_DUNS_OUTPUT' -- TransferDUNSTable 

  ); 

 

  --- VERIFY THE MONITORING RESULT IN TABLES 

  select * from SNOWFLAKECONNECTOR.SFCONNECTOR.DOWNLOAD_MONITORING_OUTPUT; 

 

  select * from SNOWFLAKECONNECTOR.SFCONNECTOR.DOWNLOAD_TRANSFER_DUNS_OUTPUT; 


The two tables involved are: 

1. **DOWNLOAD_MONITORING_OUTPUT** – contains the large JSON response from the first API. 

.. figure:: images/46.png

2. **DOWNLOAD_TRANSFER_DUNS_OUTPUT** – contains the response from the second API call. 

.. figure:: images/47.png

2.4 Data Movement with Status
-----------------------------

The **Data Movement with Status** defines different processing stages for records, each assigned a specific status code. These statuses help track the progress of data as it moves through matching, enrichment, and processing workflows.  

.. list-table::
    :header-rows: 1

    * - Field
      - Description
    * - NEW
      - 0
    * - MATCHING
      - 1
    * - DS_QUEUE
      - 2
    * - MATCHED
      - 3
    * - ENRICHED
      - 4
    * - ERROR
      - 5
    * - PROCESSED_ENRICHMENT_DATA
      - 6

*Cleansematch* 
""""""""""""""

1. **Initial Cloning** 

  When a record is cloned from source_table to destination_table, it is marked as **NEW**. 

2. **Before API Call** 

  Before calling the MatchEnrich API, the record status is updated to **MATCHING**. 

3. **Processing API Response** 

* If the API call returns **status code 200**: 

 i. If ResultText is **Match Candidates**, mark the record as **DS_QUEUE**. 

 ii. If ResultText is **Matched**: 

  * If EnrichmentOutput is **not empty**, mark the record as **ENRICHED**. 

  * If EnrichmentOutput is **empty**, mark the record as **MATCHED**. 

* If the API call returns any **status code other than 200**, mark the record as **ERROR**. 

*Download Matched Data*
"""""""""""""""""""""""

1. **Processing Results from CleanseMatchResult API** 

* If the API call returns **status code 200**: 

i. If ResultText is **Match Candidates**, mark the record as **DS_QUEUE**. 

ii. If ResultText is **Matched**, mark the record as **MATCHED**. 

* If the API call returns any **status code other than 200**, mark the record as **ERROR**. 

2. **Processing Results from RefreshDUNS API** 

* If the API call returns **status code 200**: 

i. If Output is **not empty**, mark the record as **ENRICHED**. 

ii. If Output is **empty**, mark the record as **ERROR**. 

* If the API call returns any **status code other than 200**, mark the record as **ERROR**. 

*Process Enrichment Data* 
"""""""""""""""""""""""""

After processing enrichment data, update the record status to **PROCESSED_ENRICHMENT_DATA**.  

2.5 API Calls Guidelines 
-------------------------

* Authenticate using **JWT tokens**. 

* Cache tokens to reduce redundant authentication requests. 

* If the API returns **401**, refresh the token and retry. 

* Implement retry logic for **500, 502, 503, 504, and 429** errors using **exponential backoff**, with a maximum of **3 retries**. 

2.6 Task Scheduling
--------------------

Task Scheduling in Snowflake enables the automation of SQL operations, including calling stored procedures, at defined intervals or in a specific sequence. Snowflake provides flexible options such as time based CRON SCHEDULE or AFTER. 

.. figure:: images/48.png

1. Create a Warehouse for Task Execution
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

First, create a warehouse that will be used to execute the scheduled tasks. This warehouse will automatically suspend after 5 minutes of inactivity and automatically resume when needed.  

Make sure the stored procedure SFCONNECTOR.MATCHBOOK_MAINSCRIPT is already created and tested. It should be a **callable stored procedure**.


.. code-block:: python

   
  CREATE WAREHOUSE IF NOT EXISTS my_test_warehouse  

  WAREHOUSE_SIZE = 'SMALL'  

  AUTO_SUSPEND = 300 -- Suspends after 5 minutes of inactivity  

  AUTO_RESUME = TRUE; 


2. Task Setup
^^^^^^^^^^^^^

*Option 1: Monitoring Job Every 5 mins* 
"""""""""""""""""""""""""""""""""""""""

Next, we will create a task that runs the stored procedure **DoMonitoring** every 5 minutes. Before creating the task, we ensure any existing task with the same name is dropped.


.. code-block:: python

   
  DROP TASK IF EXISTS my_monitoring_task_test;  

 

  CREATE TASK my_monitoring_task_test  

  WAREHOUSE = 'my_test_warehouse'  

  SCHEDULE = 'USING CRON */5 * * * * UTC' -- This runs the task every 5 minutes  

  AS  

  CALL DoMonitoring(  

  1, -- PageNumber  

  'Matchbook_Blocks_Finance_TEST_1', -- ApiName  

  false, -- MarkAsExported  

  'DOWNLOAD_MONITORING_OUTPUT', -- DownloadNotificationTable 'DOWNLOAD_TRANSFER_DUNS_OUTPUT' -- TransferDUNSTable ); 


**Resume the task to start the Scheduling**: 

By default, tasks are created in a suspended state. We need to resume the task to start the scheduling. 


.. code-block:: python

   
  ALTER TASK my_monitoring_task_test RESUME; 


.. figure:: images/49.png

**Verify the task is running correctly**:  

You can verify the task is running correctly by checking its details. 


.. code-block:: python


  SHOW TASKS LIKE 'my_monitoring_task_test';


**Suspend the task after testing**:  

Once you have tested the task and confirmed it is working as expected, you can suspend it.


.. code-block:: python


  ALTER TASK my_monitoring_task_test SUSPEND; 


*Option 2: Production Job Sequence with Dependencies* 
"""""""""""""""""""""""""""""""""""""""""""""""""""""

Before you begin, make sure your **stored procedures** are created and tested (e.g., SFCONNECTOR.MATCHBOOK_MAINSCRIPT).  

**Create Task 1: Run Main Script Daily at 2 AM UTC** 


.. code-block:: python


  CREATE OR REPLACE TASK SFCONNECTOR.RUN_MATCHBOOK_MAINSCRIPT_TASK 

  WAREHOUSE = <your_warehouse> 

  SCHEDULE = 'USING CRON 0 2 * * * UTC'  -- Runs daily at 2 AM UTC 

  AS 

  CALL SFCONNECTOR.MATCHBOOK_MAINSCRIPT(); 


Once after successful completion of the the first script, the second script is going to run. 

**Create Task 2: Run Enrichment Script After Task 1**  


.. code-block:: python


  CREATE OR REPLACE TASK SFCONNECTOR.TASK_MATCHBOOK_ENRICHMENTSCRIPT 

  WAREHOUSE = <your_warehouse> 

  AFTER SFCONNECTOR.TASK_MATCHBOOK_MAINSCRIPT 

  AS 

  CALL SFCONNECTOR.MATCHBOOK_ENRICHMENTSCRIPT(); 


**Create Task 3: Run Download Refresh Script After Task 2**


.. code-block:: python


  CREATE OR REPLACE TASK SFCONNECTOR.TASK_MATCHBOOK_DOWNLOADREFRESHSCRIPT 

  WAREHOUSE = <your_warehouse> 

  AFTER SFCONNECTOR.TASK_MATCHBOOK_ENRICHMENTSCRIPT 

  AS 

  CALL SFCONNECTOR.MATCHBOOK_DOWNLOADREFRESHSCRIPT(); 


3. Activating Tasks 
^^^^^^^^^^^^^^^^^^^

Tasks are created in a suspended state. Resume them to activate scheduling: 


.. code-block:: python


  ALTER TASK SFCONNECTOR.TASK_MATCHBOOK_MAINSCRIPT RESUME; 

  ALTER TASK SFCONNECTOR.TASK_MATCHBOOK_ENRICHMENTSCRIPT RESUME; 

  ALTER TASK SFCONNECTOR.TASK_MATCHBOOK_DOWNLOADREFRESHSCRIPT RESUME; 


4. Running Tasks Manually (Optional)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to manually trigger a task: 


.. code-block:: python


  EXECUTE TASK SFCONNECTOR.RUN_MATCHBOOK_MAINSCRIPT_TASK; 


Or call the stored procedure directly: 


.. code-block:: python


  CALL SFCONNECTOR.MATCHBOOK_MAINSCRIPT(); 


5. Monitor Task Execution 
-------------------------

Check execution history for any task: 


.. code-block:: python


  SELECT * 

  FROM TABLE(INFORMATION_SCHEMA.TASK_HISTORY( 

     TASK_NAME => 'SFCONNECTOR.TASK_MATCHBOOK_MAINSCRIPT', 

     RESULT_LIMIT => 10 

  )); 

