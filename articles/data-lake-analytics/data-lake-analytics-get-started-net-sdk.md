---
title: Az Azure Data Lake Analytics használatának első lépései a .NET SDK-val | Microsoft Docs
description: 'Ebből a cikkből megtudhatja, hogyan használhatja a .NET SDK-t Data Lake Store-fiókok létrehozásához, Data Lake Analytics-feladatok létrehozásához, valamint a U-SQL nyelven írt feladatok elküldéséhez. '
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/23/2016
ms.author: edmaca

---
# Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései a .NET SDK-val
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure .NET SDK-t a [U-SQL](data-lake-analytics-u-sql-get-started.md) nyelven írt feladatok elküldéséhez a Data Lake Analytics szolgáltatásba. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

Az oktatóanyag során elkészít egy C#-konzolalkalmazást egy olyan U-SQL-feladat elküldéséhez, amely beolvas egy tabulátorral elválasztott értékeket (TSV) tartalmazó fájlt, és azt vesszővel elválasztott értékeket (CSV) tartalmazó fájllá konvertálja. Ha ugyanezt az oktatóanyagot más támogatott eszközök használatával szeretné elvégezni, kattintson a cikk tetején található fülekre.

## Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Visual Studio 2015, Visual Studio 2013 4. frissítéssel vagy Visual Studio 2012 és telepített Visual C++**.
* **Microsoft Azure SDK for .NET 2.5-ös vagy újabb verzió**.  Telepítse a [Webplatform-telepítővel](http://www.microsoft.com/web/downloads/platform.aspx).
* **Egy Azure Data Lake Analytics-fiók**. Lásd: [Manage Data Lake Analytics using Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) (A Data Lake Analytics kezelése az Azure .NET SDK-val).

## Konzolalkalmazás létrehozása
Az oktatóanyag során keresési naplókat fog feldolgozni.  A keresési napló tárolható Data Lake-adattárban vagy Azure Blob Storage-ban. 

A mintául szolgáló keresési napló a nyilvános Azure blobtárolóban található. Az alkalmazásban le fogja tölteni a fájlt a munkaállomására, majd feltölti a fájlt a Data Lake Analytics-fiók alapértelmezett Data Lake Store-fiókjába.

**Alkalmazás létrehozása**

1. Nyissa meg a Visual Studiót.
2. Hozzon létre egy C# konzolalkalmazást.
3. Nyissa meg a NuGet Package Management-konzolt, és futtassa az alábbi parancsokat:
   
        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage
4. Adjon hozzá egy új fájlt a **SampleUSQLScript.txt** nevű projekthez, majd illessze be az alábbi U-SQL-parancsfájlt. A Data Lake Analytics-feladatok nyelve a U-SQL. További információk a U-SQL-ről: [U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md) és [U-SQL nyelvi referencia](http://go.microsoft.com/fwlink/?LinkId=691348).
   
        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
   
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();
   
    Ez a U-SQL-parancsfájl beolvassa a forrásadatfájlt az **Extractors.Tsv()** segítségével, majd létrehoz egy csv-fájlt az **Outputters.Csv()** használatával. 
   
    A C# programban elő kell készítenie a **/Samples/Data/SearchLog.tsv** fájlt, és az **/Output/** mappát.    
   
    Egyszerűbb relatív útvonalakat használni az alapértelmezett Data Lake-fiókokban tárolt fájlokhoz. De használhat abszolút elérési utakat is.  Példa: 
   
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
   
    A társított tárfiókokban lévő fájlok eléréséhez abszolút elérési utakat kell használnia.  A társított Azure Storage-fiókban tárolt fájlok szintaxisa:
   
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
   
   > [!NOTE]
   > Az Azure Data Lake szolgáltatásnak van egy ismert hibája.  Ha a példaalkalmazás megszakad vagy hibába ütközik, előfordulhat, hogy manuálisan kell törölnie a szkript által létrehozott Data Lake Store- és Data Lake Analytics-fiókokat.  Ha nem ismeri a portált, [Az Azure Data Lake Analytics kezelése az Azure portállal](data-lake-analytics-manage-use-portal.md) című útmutatóból elsajátíthatja az első lépéseket.       
   > 
   > 
5. A Program.cs fájlba illessze be az alábbi kódot:
   
        using System;
        using System.IO;
        using System.Collections.Generic;
        using System.Threading;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.WindowsAzure.Storage.Blob;
   
        namespace SdkSample
        {
          class Program
          {
            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
   
            private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
            private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";
   
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
   
            private static void Main(string[] args)
            {
                string localFolderPath = @"c:\temp\";
   
                // Connect to Azure
                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);
   
                SetupClients(creds, SUBSCRIPTIONID);
   
                // Transfer the source file from a public Azure Blob container to Data Lake Store.
                CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
                UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
                WaitForNewline("Source data file prepared.", "Submitting a job.");

                // Submit the job
                Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                WaitForNewline("Job submitted.", "Waiting for job completion.");

                // Wait for job completion
                WaitForJob(jobId);
                WaitForNewline("Job completed.", "Downloading job output.");

                // Download job output
                DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");

                WaitForNewline("Job output downloaded. You can now exit.");
            }

            public static ServiceClientCredentials AuthenticateAzure(
                string domainName,
                string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
            {
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                _adlaClient.SubscriptionId = subscriptionId;

                _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                Console.WriteLine(reason + "\r\nPress ENTER to continue...");

                Console.ReadLine();

                if (!String.IsNullOrWhiteSpace(nextAction))
                    Console.WriteLine(nextAction);
            }


            // List all Data Lake Analytics accounts within the subscription
            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
                for (int i = 0; i < accounts.Count; i++)
                {
                    Console.WriteLine(accounts[i].Name);
                }

                return accounts;
            }
            public static Guid SubmitJobByPath(string scriptPath, string jobName)
            {
                var script = File.ReadAllText(scriptPath);

                var jobId = Guid.NewGuid();
                var properties = new USqlJobProperties(script);
                var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
                var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

                return jobId;
            }

            public static JobResult WaitForJob(Guid jobId)
            {
                var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                while (jobInfo.State != JobState.Ended)
                {
                    jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                }
                return jobInfo.Result.Value;
            }
          }
        }

1. Az alkalmazás futtatásához nyomja le az **F5** billentyűt. A kimenet a következőképpen fog kinézni:
   
    ![Azure Data Lake Analytics-feladat U-SQL .NET SDK-kimenete](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)
2. Ellenőrizze a kimeneti fájlt.  Az alapértelmezett útvonal és a fájlnév a c:\Temp\SearchLog-from-Data-Lake.csv.

## Lásd még:
* Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
* Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
* A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével). és [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348) (U-SQL nyelvi referencia).
* Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
* A Data Lake Analytics áttekintése: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

<!--HONumber=Sep16_HO4-->


