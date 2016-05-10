<properties urlDisplayName="Use Hadoop Oozie in HDInsight" pageTitle="Use Hadoop Oozie in HDInsight | Azure" metaKeywords="" description="Use Hadoop Oozie in HDInsight, a big data solution. Learn how to define an Oozie workflow, and submit an Oozie job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadop Oozie in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Use Oozie with Hadoop in HDInsight

Learn how to define a workflow, and how to run the workflow on HDInsight. To learn Oozie coordinator, see [Use time-based Oozie Coordinator with HDInsight][Use time-based Oozie Coordinator with HDInsight].

**Estimated time to complete:** 40 minutes

## In this article

1.  [What is Oozie][What is Oozie]
2.  [Prerequisites][Prerequisites]
3.  [Define Oozie workflow file][Define Oozie workflow file]
4.  [Deploy the Oozie project and prepare the tutorial][Deploy the Oozie project and prepare the tutorial]
5.  [Run workflow][Run workflow]
6.  [Next steps][Next steps]

## <span id="whatisoozie"></span></a>What is Oozie

Apache Oozie is a workflow/coordination system that manages Hadoop jobs. It is integrated with the Hadoop stack and supports Hadoop jobs for Apache MapReduce, Apache Pig, Apache Hive, and Apache Sqoop. It can also be used to schedule jobs specific to a system, like Java programs or shell scripts.

The workflow you will implement contains two actions:

![Workflow diagram][Workflow diagram]

1.  A Hive action runs a HiveQL script to count the occurrences of each log level type in a log4j log file. Each log4j log consists of a line of fields that contains a [LOG LEVEL] field to show the type and the severity. For example:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    The Hive script output is similar to:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    For more information on Hive, see [Use Hive with HDInsight][Use Hive with HDInsight].

2.  A Sqoop action exports the HiveQL action output to a table on Azure SQL database. For more information on Sqoop, see [Use Sqoop with HDInsight][Use Sqoop with HDInsight].

> [WACOM.NOTE] For supported Oozie versions on HDInsight clusters, see [What's new in the cluster versions provided by HDInsight?][What's new in the cluster versions provided by HDInsight?].

> [WACOM.NOTE] This tutorials works on HDInsight cluster version 2.1 and 3.0. This article has not been tested on HDInsight emulator.

## <span id="prerequisites"></span></a>Prerequisites

Before you begin this tutorial, you must have the following:

-   **A workstation** with Azure PowerShell installed and configured. For instructions, see [Install and configure Azure PowerShell][Install and configure Azure PowerShell]. To execute PowerShell scripts, you must run Azure PowerShell as administrator and set the execution policy to *RemoteSigned*. See [Run Windows PowerShell scripts][Run Windows PowerShell scripts].
-   **An HDInsight cluster**. For information on creating an HDInsight cluster, see [Provision HDInsight clusters][Provision HDInsight clusters], or [Get started with HDInsight][Get started with HDInsight]. You will need the following data to go through the tutorial:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Cluster property</th>
    <th align="left">PowerShell variable name</th>
    <th align="left">Value</th>
    <th align="left">Description</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight cluster name</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">The HDInsight cluster on which you will run this tutorial.</td>
    </tr>
    <tr class="even">
    <td align="left">HDInsight cluster username</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">The HDInsight cluster user username.</td>
    </tr>
    <tr class="odd">
    <td align="left">HDInsight cluster user password</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">The HDInsight cluster user password.</td>
    </tr>
    <tr class="even">
    <td align="left">Azure storage account name</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">An Azure Storage account available to the HDInsight cluster. For this tutorial, use the default storage account specified during the cluster provision process.</td>
    </tr>
    <tr class="odd">
    <td align="left">Azure Blob container name</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">For this example, use the Azure Blob storage container used for the default HDInsight cluster file system. By default, it has the same name as the HDInsight cluster.</td>
    </tr>
    </tbody>
    </table>

-   **An Azure SQL Database**. You must configure a firewall rule for the SQL Database server to allow access from your workstation. For instructions on creating a SQL database and configuring firewall, see [Get started using Azure SQL database][Get started using Azure SQL database]. This article provides a PowerShell script for creating the SQL database table needed for this tutorial.

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">SQL database property</th>
    <th align="left">PowerShell variable name</th>
    <th align="left">Value</th>
    <th align="left">Description</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">SQL database server name</td>
    <td align="left">$sqlDatabaseServer</td>
    <td align="left"></td>
    <td align="left">The SQL Database server to which Sqoop will export data.</td>
    </tr>
    <tr class="even">
    <td align="left">SQL database login name</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">SQL Database login name.</td>
    </tr>
    <tr class="odd">
    <td align="left">SQL database login password</td>
    <td align="left">$sqlDatabaseLoginPassword</td>
    <td align="left"></td>
    <td align="left">SQL Database login password.</td>
    </tr>
    <tr class="even">
    <td align="left">SQL database name</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">The Azure SQL Database to which Sqoop will export data.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] By default an Azure SQL database allows connections from Azure services like Azure HDInsight. If this firewall setting is disabled, you must enabled it from Azure Management portal. For instruction on creating SQL database and configuring firewall rules, see [Create and Configure SQL Database][Create and Configure SQL Database].

> [WACOM.NOTE] Fill the values into the tables. It will be helpful for going through this tutorial.

## <span id="defineworkflow"></span></a>Define Oozie workflow and the related HiveQL script

Oozie workflows definitions are written in hPDL (a XML Process Definition Language). The default workflow file name is *workflow.xml*. You will save the workflow file locally, and deploy it to HDInsight cluster using Azure PowerShell later in this tutorial.

The Hive action in the workflow calls a HiveQL script file. This script file contains three HiveQL statements:

1.  **The DROP TABLE statement** deletes the log4j Hive table in case it exists.
2.  **The CREATE TABLE statement** creates a log4j Hive external table pointing to the location of the log4j log file. The field delimiter is ",". The default line delimiter is "\\n". Hive external table is used to avoid the data file being removed from the original location, in case you want to run the Oozie workflow multiple times.
3.  **The INSERT OVERWRITE statement** counts the occurrences of each log level type from the log4j Hive table, and saves the output to an Azure Storage - Blob (WASB) location.

There is a known Hive path issue. You will run into this problem when submitting an Oozie job. The instructions for fixing the issue can be found at [TechNet Wiki][TechNet Wiki].

**To define the HiveQL script file to be called by the workflow:**

1.  Create a text file with the following content:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    There are three variables used in the script:

    -   ${hiveTableName}
    -   ${hiveDataFolder}
    -   ${hiveOutputFolder}

    The workflow definition file (workflow.xml in this tutorial) will pass these values to this HiveQL script at run-time.

2.  Save the file as **C:\\Tutorials\\UseOozie\\useooziewf.hql** using the **ANSI(ASCII)** encoding (Use Notepad if your text editor doesn't provide the option). This script file will be deployed to HDInsight cluster later in the tutorial.

**To define a workflow**

1.  Create a text file with the following content:

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/> 

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg> 
                <arg>${sqlDatabaseConnectionString}</arg> 
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg> 
                <arg>--export-dir</arg> 
                <arg>${hiveOutputFolder}</arg> 
                <arg>-m</arg> 
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    There are two actions defined in the workflow. The start-to action is *RunHiveScript*. If the action runs *ok*, the next action is *RunSqoopExport*.

    The RunHiveScript has several variables. You will pass the values when you submit the Oozie job from your workstation using Azure PowerShell.

    | Workflow variables | Description                                                                                                                                                             |
    |--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | ${jobTracker}      | Specify the URL of the hadoop job tracker. Use **jobtrackerhost:9010** on HDInsight cluster version 2.0 and 3.0.                                                        |
    | ${nameNode}        | Specify the URL of the hadoop namenode. Use the default file system WASB address. For example, *wasb://\<containerName\>@\<storageAccountName\>.blob.core.windows.net*. |
    | ${queueName}       | Specifies the queuename that the job will be submitted to. Use **default**.                                                                                             |

    | Hive action variable | Description                                                      |
    |----------------------|------------------------------------------------------------------|
    | ${hiveDataFolder}    | The source directory for the Hive Create Table command.          |
    | ${hiveOutputFolder}  | The output folder for the INSERT OVERWRITE statement.            |
    | ${hiveTableName}     | The name of the Hive table that references the log4j data files. |

    | Sqoop action variable          | Description                                                                                                     |
    |--------------------------------|-----------------------------------------------------------------------------------------------------------------|
    | ${sqlDatabaseConnectionString} | SQL Database connection string.                                                                                 |
    | ${sqlDatabaseTableName}        | The SQL Database table where the data will be exported to.                                                      |
    | ${hiveOutputFolder}            | The output folder for the Hive INSERT OVERWRITE statement. This is the same folder for Sqoop Export export-dir. |

    For more information on Oozie workflow and using workflow actions, see [Apache Oozie 4.0 documentation][Apache Oozie 4.0 documentation] (for HDInsight cluster version 3.0) or [Apache Oozie 3.3.2 documentation][Apache Oozie 3.3.2 documentation] (for HDInsight cluster version 2.1).

2.  Save the file as **C:\\Tutorials\\UseOozie\\workflow.xml** using the ANSI (ASCII) encoding (Use Notepad if your text editor doesn't provide the option).

## <span id="deploy"></span></a>Deploy the Oozie project and prepare the tutorial

You will run an Azure PowerShell script to perform the following:

-   Copy the HiveQL script (useoozie.hql) Azure Blob storage, wasb:///tutorials/useoozie/useoozie.hql.
-   Copy workflow.xml to wasb:///tutorials/useoozie/workflow.xml.
-   Copy the data file (/example/data/sample.log) to wasb:///tutorials/useoozie/data/sample.log.
-   Create a SQL Database table for storing Sqoop export data. The table name is *log4jLogCount*.

**Understand HDInsight storage**

HDInsight uses Azure Blob Storage for data storage. It is called *WASB* or *Azure Storage - Blob*. WASB is Microsoft's implementation of HDFS on Azure Blob storage. For more information see [Use Azure Blob storage with HDInsight][Use Azure Blob storage with HDInsight].

When you provision an HDInsight cluster, an Azure Storage account and a specific Blob storage container from that account is designated as the default file system, just like in HDFS. In addition to this storage account, you can add additional storage accounts from either the same Azure subscription or different Azure subscriptions during the provision process. For instructions on adding additional storage accounts, see [Provision HDInsight clusters][Provision HDInsight clusters]. To simply the PowerShell script used in this tutorial, all of the files are stored in the default file system container, located at */tutorials/useoozie*. By default this container has the same name as the HDInsight cluster name.
The WASB syntax is:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Only the *wasb://* syntax is supported in HDInsight cluster version 3.0. The older *asv://* syntax is supported in HDInsight 2.1 and 1.6 clusters, but it is not supported in HDInsight 3.0 clusters and it will not be supported in later versions.

> [WACOM.NOTE] The WASB path is a virtual path. For more information see [Use Azure Blob storage with HDInsight][Use Azure Blob storage with HDInsight].

A file stored in the default file system container can be accessed from HDInsight using any of the following URIs (using workflow.xml as an example):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

If you want to access the file directly from the storage account, the blob name for the file is:

    tutorials/useoozie/workflow.xml

**Understand Hive internal table and external table**

There are a few things you need to know about Hive internal table and external table:

-   The CREATE TABLE command creates an internal table, also known as a managed table. The data file must be located in the default container.
-   The CREATE TABLE command moves the data file to the /hive/warehouse/<tablename> folder on the default container.
-   The CREATE EXTERNAL TABLE command creates an external table. The data file can be located outside the default container.
-   The CREATE EXTERNAL TABLE command does not move the data file.
-   The CREATE EXTERNAL TABLE command doesn't allow any sub-folders under the folder specified in the LOCATION clause. This is the reason why the tutorial makes a copy of the sample.log file.

For more information, see [HDInsight: Hive Internal and External Tables Intro][HDInsight: Hive Internal and External Tables Intro].

**To prepare the tutorial**

1.  Open Windows PowerShell ISE (On Windows 8 Start screen, type **PowerShell\_ISE** and then click **Windows PowerShell ISE**. See [Start Windows PowerShell on Windows 8 and Windows][Start Windows PowerShell on Windows 8 and Windows]).
2.  In the bottom pane, run the following command to connect to your Azure subscription:

        Add-AzureAccount

    You will be prompted to enter your Azure account credentials. This method of adding a subscription connection times out, and after 12 hours, you will have to run the cmdlet again.

    > [WACOM.NOTE] If you have multiple Azure subscriptions and the default subscription is not the one you want to use, use the **Select-AzureSubscription** cmdlet to select the current subscription.

3.  Copy the following script into the script pane, and then set the first six variables

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial  
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    For more descriptions of the variables, see the [Prerequisites][Prerequisites] section in this tutorial.

4.  Append the following to the script in the script pane:

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {       
            Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

5.  Click **Run Script** or press **F5** to run the script. The output shall be similar to:

    ![Tutorial preparation output][Tutorial preparation output]

## <span id="run"></span></a>Run the Oozie project

Azure PowerShell currently doesn't provide any cmdlets for defining Oozie jobs. You can use
the Invoke-RestMethod PowerShell cmdlet to invoke Oozie web services. The Oozie Web Services API is a HTTP REST JSON API. For more information on Oozie Web Services API, see [Apache Oozie 4.0 documentation][Apache Oozie 4.0 documentation] (for HDInsight cluster version 3.0) or [Apache Oozie 3.3.2 documentation][Apache Oozie 3.3.2 documentation] (for HDInsight cluster version 2.1).

**To submit an Oozie job**

1.  Open Windows PowerShell ISE (On Windows 8 Start screen, type **PowerShell\_ISE** and then click **Windows PowerShell ISE**. See [Start Windows PowerShell on Windows 8 and Windows][Start Windows PowerShell on Windows 8 and Windows]).

2.  Copy the following script into the script pane, and then set the first ten variables (skip the 6th, $storageUri).

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF variables
        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    For more descriptions of the variables, see the [Prerequisites][Prerequisites] section in this tutorial.

3.  Append the following to the script. This part defines the Oozie payload:

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>$clusterUsername</value>
           </property>

           <property>
               <name>oozie.wf.application.path</name>
               <value>$oozieWFPath</value>
           </property>

        </configuration>
        "@

4.  Append the following to the script. This part checks the Oozie web service status:

        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

5.  Append the following to the script. This part creates and starts an Oozie job:

        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        # start Oozie job
        Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6.  Append the following to the script. This part checks the Oozie job status:

        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7.  If your HDinsight cluster is version 2.1, replace "<https://$clusterName.azurehdinsight.net:443/oozie/v2/>" with "<https://$clusterName.azurehdinsight.net:443/oozie/v1/>". HDInsight cluster version 2.1 does not supports version 2 of the web services.

8.  Click **Run Script** or press **F5** to run the script. The output will be similar to:

    ![Tutorial run workflow output][Tutorial run workflow output]

9.  Connect to your SQL Database to see the exported data.

**To check the job error log**

To troubleshoot a workflow, the Oozie log file can be found at
*C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* or *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* from the cluster headnode. For information on RDP, see [Administering HDInsight clusters using Management portal][Administering HDInsight clusters using Management portal].

**To re-run the tutorial**

To re-run the workflow, you must perform the following:

-   delete the Hive script output file
-   delete the data in the log4jLogsCount table

Here is a sample PowerShell script that you can use:

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

## <span id="nextsteps"></span></a>Next steps

In this tutorial, you have learned how to define an Oozie workflow, and how to run an Oozie job using Azure PowerShell. To learn more, see the following articles:

-   [Use time-based Oozie Coordinator with HDInsight][Use time-based Oozie Coordinator with HDInsight]
-   [Get started with HDInsight][Get started with HDInsight]
-   [Get started with the HDInsight Emulator][Get started with the HDInsight Emulator]
-   [Use Azure Blob storage with HDInsight][Use Azure Blob storage with HDInsight]
-   [Administer HDInsight using PowerShell][Administer HDInsight using PowerShell]
-   [Upload data to HDInsight][Upload data to HDInsight]
-   [Use Sqoop with HDInsight][Use Sqoop with HDInsight]
-   [Use Hive with HDInsight][Use Hive with HDInsight]
-   [Use Pig with HDInsight][Use Pig with HDInsight]
-   [Develop C# Hadoop streaming jobs for HDInsight][Develop C# Hadoop streaming jobs for HDInsight]
-   [Develop Java MapReduce programs for HDInsight][Develop Java MapReduce programs for HDInsight]

  [Use time-based Oozie Coordinator with HDInsight]: ../hdinsight-use-oozie-coordinator-time/
  [What is Oozie]: #whatisoozie
  [Prerequisites]: #prerequisites
  [Define Oozie workflow file]: #defineworkflow
  [Deploy the Oozie project and prepare the tutorial]: #deploy
  [Run workflow]: #run
  [Next steps]: #nextsteps
  [Workflow diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
  [Use Hive with HDInsight]: ../hdinsight-use-hive/
  [Use Sqoop with HDInsight]: ../hdinsight-use-sqoop/
  [What's new in the cluster versions provided by HDInsight?]: ../hdinsight-component-versioning/
  [Install and configure Azure PowerShell]: ../install-and-configure-powershell/
  [Run Windows PowerShell scripts]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Provision HDInsight clusters]: ../hdinsight-provision-clusters/
  [Get started with HDInsight]: ../hdinsight-get-started/
  [Get started using Azure SQL database]: ../sql-database-get-started/
  [Create and Configure SQL Database]: ../sql-database-create-configure/
  [TechNet Wiki]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [Apache Oozie 4.0 documentation]: http://oozie.apache.org/docs/4.0.0/
  [Apache Oozie 3.3.2 documentation]: http://oozie.apache.org/docs/3.3.2/
  [Use Azure Blob storage with HDInsight]: ../hdinsight-use-blob-storage/
  [HDInsight: Hive Internal and External Tables Intro]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Start Windows PowerShell on Windows 8 and Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [Tutorial preparation output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
  [Tutorial run workflow output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png
  [Administering HDInsight clusters using Management portal]: ../hdinsight-administer-use-management-portal/
  [Get started with the HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [Administer HDInsight using PowerShell]: ../hdinsight-administer-use-powershell/
  [Upload data to HDInsight]: ../hdinsight-upload-data/
  [Use Pig with HDInsight]: ../hdinsight-use-pig/
  [Develop C# Hadoop streaming jobs for HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Develop Java MapReduce programs for HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
