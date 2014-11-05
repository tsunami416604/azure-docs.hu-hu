<properties urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="Analyze Twitter data with Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive to analyze Twitter data on Hadoop in HDInsight to find the usage frequency of a particular word." metaCanonical="" services="HDInsight" documentationCenter="" title="Analyze Twitter data with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Analyze Twitter data with Hadoop in HDInsight

Learn how to analyze Twitter data with Hadoop using Hive in HDInsight.

Social websites are one of the major driving forces for Big Data adoption. Public APIs provided by sites like Twitter are a useful source of data for analyzing and understanding popular trends. In this tutorial, you will connect to the Twitter web service to get some Tweets using the Twitter streaming API, and then you will use Hive to get a list of Twitter users that sent most Tweets that contained a certain word.

**Estimated time to complete:** 30 minutes

## In this article

-   [Prerequisites][Prerequisites]
-   [Get Twitter feed][Get Twitter feed]
-   [Create a HiveQL script][Create a HiveQL script]
-   [Process the data using Hive][Process the data using Hive]
-   [Tutorial clean up][Tutorial clean up]
-   [Next steps][Next steps]

## <span id="prerequisites"></span></a>Prerequisites

Before you begin this tutorial, you must have the following:

-   **A workstation** with Azure PowerShell installed and configured. For instructions, see [Install and configure Azure PowerShell][Install and configure Azure PowerShell]. To execute PowerShell scripts, you must run Azure PowerShell as administrator and set the execution policy to *RemoteSigned*. See [Run Windows PowerShell scripts][Run Windows PowerShell scripts].

    Before running PowerShell scripts, make sure you are connected to your Azure subscription using the following cmdlet:

        Add-AzureAccount

    If you have multiple Azure subscriptions, use the following cmdlet to set the current subscription:

        Select-AzureSubscription <AzureSubscriptionName>

-   **An Azure HDInsight cluster**. For instructions on cluster provision, see [Get started using HDInsight][Get started using HDInsight] or [Provision HDInsight clusters][Provision HDInsight clusters]. You will need the following data to go through the tutorial:

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
    <td align="left">This is your HDInsight cluster name.</td>
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

**Understand HDInsight storage**

HDInsight uses Azure Blob storage for data storage. It is called *WASB* or *Azure Storage - Blob*. WASB is Microsoft's implementation of HDFS on Azure Blob storage. For more information see [Use Azure Blob storage with HDInsight][Use Azure Blob storage with HDInsight].

When you provision an HDInsight cluster, a Blob storage container is designated as the default file system, just like in HDFS. In addition to this container, you can add additional containers from either the same Azure storage account or different Azure storage accounts during the provision process. For instructions on adding additional storage accounts, see [Provision HDInsight clusters][Provision HDInsight clusters].

> [WACOM.NOTE] To simplify the PowerShell script used in this tutorial, all of the files are stored in the default file system container, located at */tutorials/twitter*. By default this container has the same name as the HDInsight cluster name. If you choose to use a different container to store these files, please update the script accordingly.

The WASB syntax is:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Only the *wasb://* syntax is supported in HDInsight cluster version 3.0. The older *asv://* syntax is supported in HDInsight 2.1 and 1.6 clusters, but it is not supported in HDInsight 3.0 clusters and it will not be supported in later versions.

> The WASB path is virtual path. For more information see [Use Azure Blob storage with HDInsight][Use Azure Blob storage with HDInsight].

For a file stored in the default file system container. it can be accessed from HDInsight using any of the following URIs (use tweets.txt as an example):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
    wasb:///tutorials/twitter/tweets.txt
    /tutorials/twitter/tweets.txt

If you want to access the file directly from the storage account, the blob name for the file is:

    tutorials/twitter/tweets.txt

The following table lists the files used in this tutorial:

| Files                              | Description                                                                                      |
|------------------------------------|--------------------------------------------------------------------------------------------------|
| /tutorials/twitter/data/tweets.txt | The source data for the Hive job.                                                                |
| /tutorials/twitter/output          | The output folder for the Hive job. The default file Hive job output file name is **000000\_0**. |
| tutorials/twitter/twitter.hql      | The HiveQL script file.                                                                          |
| /tutorials/twitter/jobstatus       | The Hadoop job status.                                                                           |

## <span id="feed"></span></a>Get Twitter feed

In this tutorial, you will use the [Twitter streaming APIs][Twitter streaming APIs]. The specific Twitter steaming API you will use is [statuses/filter][statuses/filter].

[Tweets data][Tweets data] is stored in the JSon format that contains a complex nested structure. Instead of writing many lines of code using a conventional programming language, you can transform this nested structure into a Hive table, so that it can be queried by a SQL-like language called HiveQL.

Twitter uses OAuth to provide authorized access to its API. OAuth is an authentication protocol that allows users to approve application to act on their behalf without sharing their password. More information can be found at [oauth.net][oauth.net] or in the excellent [Beginner's Guide to OAuth][Beginner's Guide to OAuth] from Hueniverse.

The first step to use OAuth is to create a new application on the Twitter Developer site.

**To create a Twitter application**

1.  Sign in to [][]<https://apps.twitter.com/></a>.Click the **Sign up now** link if you don't have a Twitter account.
2.  Click **Create New App**.
3.  Enter **Name**, **Description**, **Website**. You can make up a URL for the Website field. The following table shows some sample values to use:

    | Field       | Value                         |
    |-------------|-------------------------------|
    | Name        | MyHDInsightApp                |
    | Description | MyHDInsightApp                |
    | Website     | http://www.myhdinsightapp.com |

4.  Check **Yes, I agree**, and then click **Create your Twitter application**.
5.  Click the **Permissions** tab. The default permission is **Read only**. This is sufficient for this tutorial.
6.  Click the **API Keys** tab.
7.  Click **Create my access token**.
8.  Click **Test OAuth** in the upper right corner of the page.
9.  Write down **consumer key**, **Consumer secret**, **Access token**, and **Access token secret**. You will need the values later in the tutorial.

In this tutorial, you will use PowerShell to make a web service call. The other popular tool to make web service calls is [*Curl*][*Curl*]. Curl can be downloaded from [here][here].

> [WACOM.NOTE] When use the curl command on Windows, use double-quotes instead of single-quotes for the option values.

**To get Tweets**

1.  Open Windows PowerShell ISE (On Windows 8 Start screen, type **PowerShell\_ISE** and then click **Windows PowerShell ISE**. See [Start Windows PowerShell on Windows 8 and Windows][Start Windows PowerShell on Windows 8 and Windows]).

2.  Copy the following script into the script pane:

        Write-Host "Set variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $destBlobName = "tutorials/twitter/data/tweets.txt"

        $trackString = "Azure, Cloud, HDInsight"
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 100  #about 3 minutes every 100 lines

        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 

        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
                if ($count%10 -eq 0){
                    write-host " --- " -NoNewline
                    Get-Date -Format hh:mm:sstt
                }

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }

        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream) 

        $sReader.close()
        Write-Host "Complete!" -ForegroundColor Green

3.  Set the first nine variables in the script:

    | Variable                 | Description                                                                                                                                                                                          |
    |--------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName      | The Azure Storage account used for the default HDInsight cluster file system. It is the storage account specified at provision. See [Prerequisites][Prerequisites].                                               |
    | $containerName           | The Blob container used for the default HDInsight cluster file system. It is the container specified at provision. See [Prerequisites][Prerequisites].                                                            |
    | $destBlobName            | This is the output blob name. The default values is **tutorials/twitter/data/tweets.txt**. If you change the default value, you will need to update the PowerShell scripts accordingly.              |
    | $trackString             | The web service will return tweets related to these keywords. The default value is **Azure, Cloud, HDInsight**. If you change the default value, you will update the PowerShell scripts accordingly. |
    | $lineMax                 | The value determines how many tweets the script will read. It takes about three minutes to read 100 tweets. You can set a larger number, but it will take more time to download.                     |
    | $oauth\_consumer\_key    | This is the Twitter application **consumer key** you wrote down earlier when you create the Twitter application.                                                                                     |
    | $oauth\_consumer\_secret | This is the Twitter application **consumer secret** you wrote down earlier.                                                                                                                          |
    | $oauth\_token            | This is the Twitter application **access token** you wrote down earlier.                                                                                                                             |
    | $oauth\_token\_secret    | This is the Twitter application **access token secret** you wrote down earlier.                                                                                                                      |

4.  Press **F5** to run the script. If you run into problems, as a workaround, select all the lines, and then press **F8**.
5.  You shall see "Complete!" at the end of the output. Any of the error message will be displayed in red.

As a validation procedure, you can check the output file, **/tutorials/twitter/data/tweets.txt**, on your Azure Blob storage using an Azure storage explorer, or Azure PowerShell. For a sample PowerShell script for listing files, see [Use Blob storage with HDInsight][Use Blob storage with HDInsight].

## <span id="script"></span></a>Create a HiveQL script

Using Azure PowerShell, you can run multiple HiveQL statements one at a time, or package the HiveQL statement into a script file. In this tutorial, you will create a HiveQL script. The script file must be uploaded to WASB. In the next section, you will run the script file using Azure PowerShell.

The HiveQL script will perform the following:

1.  **Drop the tweets\_raw table** in case the table already exists.
2.  **Create the tweets\_raw Hive table**. This temporary Hive structured table holds the data for further ETL processing. For information on partition, see [Hive tutorial][Hive tutorial].
3.  **Load data** from the source folder, /tutorials/twitter/data. The large Tweets dataset in nested JSon format has now been transformed into a temporary Hive table structure.
4.  **Drop the tweets table** in case the table already exists;
5.  **Create the tweets table**. Before you can query against the Tweets dataset using Hive, you need to run another ETL process. This ETL process defines a more detailed table schema for the data that you have stored in the "twitter\_raw" table.
6.  **Insert overwrite table**. This complex Hive script will kick off a set of long Map Reduce jobs by the Hadoop cluster. Depending on your dataset and the size of your cluster, this could take about 10 minutes.
7.  **Insert overwrite directory**. Run a query and output the dataset to a file. This query will return a list of Twitter users that sent most tweets that contained the word "Azure".

**To Create a Hive script and upload it to Azure**

1.  Open Windows PowerShell ISE.
2.  Copy the following script into the script pane:

        Write-Host "Define variables ..." -ForegroundColor Green
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"

        $hqlScriptFile = "tutorials/twitter/twitter.hql"

        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;

        DROP TABLE tweets_raw;
        CREATE TABLE tweets_raw (
            json_response STRING
        ) 
        PARTITIONED BY (filesequence INT);

        LOAD DATA INPATH '$sourceDataPath'
        INTO TABLE tweets_raw
        PARTITION (filesequence = 1);

        DROP TABLE tweets;

        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        )
        PARTITIONED BY (filesequence INT);

        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        PARTITION (filesequence)
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(  
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response,
            filesequence
        WHERE (length(json_response) > 500);

        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc 
            FROM tweets 
            WHERE text like "%Azure%" 
            GROUP BY name,screen_name 
            ORDER BY cc DESC LIMIT 10;
        "@

        Write-Host "Define the connection string ..." -ForegroundColor Green
        $storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)

        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)

        Write-Host "Complete!" -ForegroundColor Green

3.  Set the first two variables in the script:

    | Variable            | Description                                                                                                                                            |
    |---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
    | $storageAccountName | The Azure Storage account used for the default HDInsight cluster file system. It is the storage account specified at provision. See [Prerequisites][Prerequisites]. |
    | $containerName      | The Blob container used for the default HDInsight cluster file system. It is the container specified at provision. See [Prerequisites][Prerequisites].              |
    | $sourceDataPath     | The WASB location where the Hive queries will read the data from. You don't need to change this variable.                                              |
    | $outputPath         | The WASB location where the Hive queries will output the results. You don't need to change this variable.                                              |
    | $hqlScriptFile      | The location and the file name of the HiveQL script file.                                                                                              |

4.  Press **F5** to run the script. If you run into problem, as a workaround, select all the lines, and then press **F8**.
5.  You shall see "Complete!" at the end of the output. Any of the error message will be displayed in red.

As a validation procedure, you can check the output file, **/tutorials/twitter/twitter.hql**, on your Azure Blob storage using an Azure storage explorer, or Azure PowerShell. For a sample PowerShell script for listing files, see [Use Blob storage with HDInsight][Use Blob storage with HDInsight].

## <a name="process"></a> Process Twitter data using Hive

You have finished all the preparation work. Now, you can invoke the Hive script and check the results.

### Submit a Hive job

Use the following PowerShell script to run the Hive script. You will need to set the first variable.

    Write-Host "Set the variables ... " -ForegroundColor Green
    $clusterName = "<HDInsightClusterName>"

    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    $statusFolder = "/tutorials/twitter/jobstatus"

    Write-Host "Invoke Hive ... " -ForegroundColor Green
    Use-AzureHDInsightCluster $clusterName
    $response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable

    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### Check the results

Use the following PowerShell script to check the Hive job output. You will need to set the first two variables.

    Write-Host "Set the variables ... " -ForegroundColor Green
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "Display the output ..." -ForegroundColor Green
    cat "./$blob"

> [WACOM.NOTE] The Hive table uses \\001 as the field delimiter. The delimiter is not visible in the output.

After the analysis results has been placed on WASB, you can export the data to Azure SQL database/SQL server, export the data to Excel using Power Query, or connect your application to the data using Hive ODBC driver. For more information, see [Use Sqoop with HDInsight][Use Sqoop with HDInsight] ,[Analyze flight delay data using HDInsight][Analyze flight delay data using HDInsight], [Connect Excel to HDInsight with Power Query][Connect Excel to HDInsight with Power Query], and [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][Connect Excel to HDInsight with the Microsoft Hive ODBC Driver].

## <span id="cleanup"></span></a>Tutorial clean up

In case you want to re-run the tutorial, you will need:

-   **Re-create the Tweets data file**. The source Tweets data file has been removed by the Hive job. You will need to generate a new one. The file name is **tutorials/twitter/data/tweets.txt**. See [Get Twitter feed][Get Twitter feed].

## <span id="nextsteps"></span></a>Next Steps

In this tutorial we have seen how to transform unstructured Json dataset into structured Hive table to query, explore, and analyze data from Twitter using HDInsight on Azure. To learn more, see:

-   [Get started with HDInsight][Get started using HDInsight]
-   [Analyze flight delay data using HDInsight][Analyze flight delay data using HDInsight]
-   [Connect Excel to HDInsight with Power Query][Connect Excel to HDInsight with Power Query]
-   [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][Connect Excel to HDInsight with the Microsoft Hive ODBC Driver]
-   [Use Sqoop with HDInsight][Use Sqoop with HDInsight]

  [Prerequisites]: #prerequisites
  [Get Twitter feed]: #feed
  [Create a HiveQL script]: #script
  [Process the data using Hive]: #process
  [Tutorial clean up]: #cleanup
  [Next steps]: #nextsteps
  [Install and configure Azure PowerShell]: ../install-configure-powershell
  [Run Windows PowerShell scripts]: http://technet.microsoft.com/en-us/library/ee176961.aspx
  [Get started using HDInsight]: ../hdinsight-get-started/
  [Provision HDInsight clusters]: ../hdinsight-provision-clusters/
  [Use Azure Blob storage with HDInsight]: ../hdinsight-use-blob-storage/
  [Twitter streaming APIs]: https://dev.twitter.com/docs/streaming-apis
  [statuses/filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
  [Tweets data]: https://dev.twitter.com/docs/platform-objects/tweets
  [oauth.net]: http://oauth.net/
  [Beginner's Guide to OAuth]: http://hueniverse.com/oauth/
  []: https://apps.twitter.com/
  [*Curl*]: http://curl.haxx.se
  [here]: http://curl.haxx.se/download.html
  [Start Windows PowerShell on Windows 8 and Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [Use Blob storage with HDInsight]: ../hdinsight-use-blob-storage/#powershell
  [Hive tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial
  [Use Sqoop with HDInsight]: ../hdinsight-use-sqoop/
  [Analyze flight delay data using HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Connect Excel to HDInsight with Power Query]: ../hdinsight-connect-excel-power-query/
  [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver]: ../hdinsight-connect-excel-hive-ODBC-driver/
