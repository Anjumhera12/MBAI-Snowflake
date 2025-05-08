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

.. figure:: images/1.png

Once logged in, you will be directed to the **Snowflake console dashboard**. 



1.3 Setting Up the Database and Schema
-------------------------------------- 

Creating Database and Schema 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

From the left side menu, go to '**Data**' and select '**Databases**', then click on '**+ Database**' to create a new one.



A pop-up window will appear. Enter a name for the database and a comment (optional), then click '**Create**'.  



Once the database is created, it will appear on the left-hand side. Click on the name of the database you created, then click on '**+ Schema**'. 



A pop-up window will appear. Enter the name for the schema and a comment (optional), then click '**Create**' to create the schema.



1.4 Creating Worksheets
------------------------ 

Create Separate worksheet 
^^^^^^^^^^^^^^^^^^^^^^^^^

A separate worksheet should be created for each client to share their data and results. To create a worksheet, on the dashboard, select **Worksheets** and click on **Go to Worksheets**. 

(or) from the left-hand menu, select **Projects**-> **Worksheets**. 



You will be redirected to a new page, select **SQL Worksheet** from the drop-down menu.



Configuring Database and Schema
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 

Once you select the SQL Worksheet, a new page opens.  

**Select the Role and Virtual Warehouse**:   

Click on the dot icon as shown in the screen below. 



As each worksheet can access only the allowed databases, select the **Role** and **Virtual Warehouse**, as shown below. 



**Use case**: Suppose a client wants to log in using the **DATAENGINEER** role and execute code with a **Medium** warehouse size. In this case, they should select the appropriate **Role** and **Virtual Warehouse** as demonstrated above.  

Role: DATAENGINEER 

Run on warehouse: DEVELOPER_MEDIUM 

**Select Database and Schema**: 

Select the database and schema by clicking on **No Database selected** drop-down menu as shown in the screen below. 