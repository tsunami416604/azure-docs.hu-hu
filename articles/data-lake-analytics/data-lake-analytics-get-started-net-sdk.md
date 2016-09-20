<properties 
   pageTitle="Az Azure Data Lake Analytics használatának első lépései a .NET SDK-val | Azure" 
   description="Ebből a cikkből megtudhatja, hogyan használhatja a .NET SDK-t Data Lake Store-fiókok létrehozásához, Data Lake Analytics-feladatok létrehozásához, valamint a U-SQL nyelven írt feladatok elküldéséhez. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/22/2016"
   ms.author="edmaca"/>

# Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései a .NET SDK-val

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Ebből a cikkből megtudhatja, hogyan használhatja az Azure .NET SDK-t Azure Data Lake Analytics-fiókok létrehozásához, Data Lake Analytics-feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok elküldéséhez Data Lake Analytics-fiókokba. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

Az oktatóanyag során elkészít egy U-SQL parancsfájlt tartalmazó C# konzolalkalmazást, amely beolvas egy tabulátorral elválasztott értékeket (TSV) tartalmazó fájlt, és azt vesszővel elválasztott értékeket (CSV) tartalmazó fájllá konvertálja. Ha ugyanezt az oktatóanyagot más támogatott eszközök használatával szeretné elvégezni, kattintson a szakasz tetején található fülekre.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

##Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

- **Visual Studio 2015, Visual Studio 2013 4. frissítéssel vagy Visual Studio 2012 és telepített Visual C++**.
- **Microsoft Azure SDK for .NET 2.5-ös vagy újabb verzió**.  Telepítse a [Webplatform-telepítővel](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)**. 
- Hozzon létre egy Azure Active Directory- (AAD-) alkalmazást, és kérje le az **ügyfél-azonosítóját**, a **bérlőazonosítóját** és a **kulcsát**. További információ az AAD-alkalmazásokról és útmutató az ügyfél-azonosító lekéréséhez: [Create Active Directory application and service principal using portal](../resource-group-create-service-principal-portal.md) (Active Directory-alkalmazás és egyszerű szolgáltatás létrehozása a portál használatával). A válasz URI és a kulcs a portálon is elérhető lesz az alkalmazás és a kulcs létrehozását követően.


##Konzolalkalmazás létrehozása

Az oktatóanyag során keresési naplókat fog feldolgozni.  A keresési napló tárolható Data Lake-adattárban vagy Azure Blob Storage-ban. 

A rendszer átmásolt egy mintául szolgáló keresési naplót a nyilvános Azure Blob-tárolóba. Az alkalmazásban le fogja tölteni a fájlt a munkaállomására, majd feltölti a fájlt az alapértelmezett Data Lake Store-fiókba.

**Alkalmazás létrehozása**

1. Nyissa meg a Visual Studiót.
2. Hozzon létre egy C# konzolalkalmazást.
3. Nyissa meg a NuGet Package Management-konzolt, és futtassa az alábbi parancsokat:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
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

    >[AZURE.NOTE] Az Azure Data Lake szolgáltatásnak van egy ismert hibája.  Ha a példaalkalmazás megszakad vagy hibába ütközik, előfordulhat, hogy manuálisan kell törölnie a szkript által létrehozott Data Lake Store- és Data Lake Analytics-fiókokat.  Ha nem ismeri a portált, [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md) útmutatóból elsajátíthatja az első lépéseket.       
       
5. A Program.cs fájlba illessze be az alábbi kódot:

        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System;
        using System.Collections.Generic;
        using System.IO;
        
        namespace SdkSample
        {
          class Program
          {
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            private static string _adlaAccountName;
            private static string _adlsAccountName;
            private static string _resourceGroupName;
            private static string _location;
            private static string _tenantId;
            private static string _subId;
            private static string _clientId;
            private static string _clientKey;
        
            private static void Main(string[] args)
            {
              _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
              _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>"; // TODO: Replace this value with the name for a NEW Analytics account.
              _resourceGroupName = "<RESOURCE-GROUP>"; // TODO: Replace this value. This resource group should already exist.
              _location = "East US 2";
              _tenantId = "<TENANT-ID>";
              _subId = "<SUBSCRIPTION-ID>";
              _clientId = "<CLIENT-ID>";
              _clientKey = "<CLIENT-KEY>";
        
              string localFolderPath = @"c:\temp\"; // TODO: Make sure this exists and contains SampleUSQLScript.txt.
              var tokenCreds = Authenticate(_tenantId, _clientId, _clientKey);
        
              SetupClients(tokenCreds, _subId); 
        
              // Run sample scenarios
              WaitForNewline("Authenticated.", "Creating NEW accounts.");
              CreateAccounts();
              WaitForNewline("Accounts created.", "Preparing the source data file.");
        
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
              WaitForNewline("Job output downloaded.", "Deleting accounts.");
        
              // Delete accounts
              _adlaClient.Account.Delete(_resourceGroupName, _adlaAccountName);
              _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
        
              WaitForNewline("Accounts deleted. You can now exit.");
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
              Console.WriteLine(reason + "\r\nPress ENTER to continue...");
        
              Console.ReadLine();
        
              if (!String.IsNullOrWhiteSpace(nextAction))
                Console.WriteLine(nextAction);
            }
        
            public static TokenCredentials Authenticate(string tenantId, string clientId, string clientKey)
            {
              var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + _tenantId);
              var creds = new ClientCredential(_clientId, _clientKey);
              var tokenAuthResult = authContext.AcquireTokenAsync("https://management.core.windows.net/", creds).Result;
        
              return new TokenCredentials(tokenAuthResult.AccessToken);
            }
        
            public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
            {
              _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
              _adlaClient.SubscriptionId = subscriptionId;
        
              _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);
        
              _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(tokenCreds);
        
              _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
              _adlsClient.SubscriptionId = subscriptionId;
        
              _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }
        
            public static void CreateAccounts()
            {
              //ADLS account first, ADLA requires an ADLS account
              var adlsParameters = new DataLakeStoreAccount(location: _location);
              _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
        
              var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(_adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
              var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: _adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
              var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: _location);
              _adlaClient.Account.Create(_resourceGroupName, _adlaAccountName, adlaParameters);
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
          }
        }

6. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

## Lásd még:

- Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
- Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
- Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
- A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével). és [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348) (U-SQL nyelvi referencia).
- Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
- A Data Lake Analytics áttekintése: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).



<!--HONumber=sep16_HO1-->


