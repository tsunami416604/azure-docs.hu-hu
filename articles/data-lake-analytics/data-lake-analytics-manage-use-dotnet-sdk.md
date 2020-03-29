---
title: Az Azure Data Lake Analytics kezelése az Azure .NET SDK használatával
description: Ez a cikk bemutatja, hogyan használhatja az Azure .NET SDK-t a Data Lake Analytics-feladatokat, adatforrásokat, & felhasználókat kezelő alkalmazások írásához.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 0a10af73d754596e9b5bb34b2974d7f1647d06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60617707"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Az Azure Data Lake Analytics kezelése – .NET-alkalmazás

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti, hogyan kezelheti az Azure Data Lake Analytics-fiókok, adatforrások, felhasználók és feladatok az Azure .NET SDK használatával írt alkalmazás használatával. 

## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio 2015, Visual Studio 2013 4. frissítéssel vagy Visual Studio 2012 és telepített Visual C++**.
* **Microsoft Azure SDK for .NET 2.5-ös vagy újabb verzió**.  Telepítse a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx).
* **Szükséges NuGet csomagok**

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

|Csomag|Verzió|
|-------|-------|
|[Microsoft.Rest.ClientRuntime.Azure.Hitelesítés](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0-előnézet|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0-előzetes|

Ezeket a csomagokat a NuGet parancssorból telepítheti a következő parancsokkal:

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Gyakori változók

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Hitelesítés

Több lehetősége van az Azure Data Lake Analytics szolgáltatásba való bejelentkezésre. A következő kódrészlet egy példát mutat be az interaktív felhasználói hitelesítéssel egy előugró ablakkal való hitelesítésre.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

A **Data** [Lake Analytics .NET hitelesítési beállításai](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options) GetCreds_User_Popup és az egyéb hitelesítési lehetőségek kódját is lefedik.


## <a name="create-the-client-management-objects"></a>Ügyfélfelügyeleti objektumok létrehozása

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>Fiókok kezelése

### <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

Ha még nem hozott létre egyet, a Data Lake Analytics-összetevők létrehozásához rendelkeznie kell egy Azure Resource Group.If you have already created one, you must have a Azure Resource Group to create your Data Lake Analytics components. Szüksége van a hitelesítési hitelesítő adatokra, az előfizetés-azonosítóra és egy helyre. Az alábbi kód bemutatja, hogyan hozhat létre erőforráscsoportot:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

További információ: Azure Resource Groups and Data Lake Analytics.

### <a name="create-a-data-lake-store-account"></a>Data Lake Store-fiók létrehozása

Az ADLA-fiókhoz mindig ADLS-fiók szükséges. Ha még nem rendelkezik használni, létrehozhat egyet a következő kóddal:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

A következő kód ADLS-fiókot hoz létre

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Data Lake Store-fiókok listázása

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Data Lake Analytics-fiókok listázása

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Fiók létezése

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Fiókkal kapcsolatos információk beszerezése

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Fiók eltávolítása

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Az alapértelmezett Data Lake Store-fiók beszerezése

Minden Data Lake Analytics-fiókhoz alapértelmezett Data Lake Áruházbeli fiók szükséges. Ezzel a kóddal határozhatja meg az Analytics-fiók alapértelmezett Áruház-fiókját.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Adatforrások kezelése

A Data Lake Analytics jelenleg a következő adatforrásokat támogatja:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure storage-fiók](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Hivatkozás Azure Storage-fiókra

Az Azure Storage-fiókokra mutató hivatkozásokat hozhat létre.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Az Azure Storage adatforrások listázása

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Data Lake Store adatforrások listázása

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>Mappák és fájlok feltöltése és letöltése

A Data Lake Store fájlrendszer-ügyfélügyfél-kezelő objektummal az azure-ból a következő módszerekkel tölthet fel és tölthet le fájlokat vagy mappákat a helyi számítógépre:

- UploadFolder mappa
- UploadFile (Feltöltőfájl)
- DownloadFolder (Letöltési mappa)
- DownloadFile fájl

Ezeknek a módszereknek az első paramétere a Data Lake Store-fiók neve, amelyet a forrás- és a célelérési út paraméterei követnek.

A következő példa bemutatja, hogyan tölthet le egy mappát a Data Lake Store-ban.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Fájl létrehozása Data Lake Áruházbeli fiókban

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Az Azure Storage-fiók elérési útvonalának ellenőrzése

A következő kód ellenőrzi, hogy létezik-e egy Azure Storage-fiók (storageAccntName) egy Data Lake Analytics-fiókban (analyticsAccountName), és hogy létezik-e tároló (containerName) az Azure Storage-fiókban.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>Katalógus és feladatok kezelése

A DataLakeAnalyticsCatalogManagementClient objektum az egyes Azure Data Lake Analytics-fiókhoz biztosított SQL-adatbázis kezeléséhez biztosít módszereket. A DataLakeAnalyticsJobManagementClient módszereket biztosít az adatbázisban U-SQL parancsfájlokkal futtatott feladatok küldéséhez és kezeléséhez.

### <a name="list-databases-and-schemas"></a>Adatbázisok és sémák listázása

A több dolgot is felsorolhatja, a leggyakoribb az adatbázisok és a séma. A következő kód beszerzi az adatbázisok gyűjteményét, majd számba veszi az egyes adatbázisok sémáját.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>Táblázatoszlopok listázása

A következő kód bemutatja, hogyan érheti el az adatbázist egy Data Lake Analytics katalógusfelügyeleti ügyféllel a megadott tábla oszlopainak listázásához.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>U-SQL-feladat elküldése

A következő kód bemutatja, hogyan használhatja a Data Lake Analytics feladatkezelési ügyfél egy feladat küldéséhez.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>Sikertelen feladatok listázása

Az alábbi kód a sikertelen feladatokra vonatkozó információkat sorolja fel.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Folyamatok listázása

A következő kód a fiókba küldött feladatok egyes folyamatainak adatait sorolja fel.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Ismétlődések listája

A következő kód a fiókba küldött feladatok ismétlődésével kapcsolatos információkat sorolja fel.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Gyakori grafikonforgatókönyvek

### <a name="look-up-user-in-the-aad-directory"></a>Felhasználó felkeresése az AAD-könyvtárban

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>Egy felhasználó ObjectId azonosítójának beszereznie az AAD könyvtárban

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Számítási házirendek kezelése

A DataLakeAnalyticsManagementClient objektum módszereket biztosít a Data Lake Analytics-fiók számítási szabályzatainak kezeléséhez.

### <a name="list-compute-policies"></a>Számítási házirendek listázása

A következő kód beolvassa a Data Lake Analytics-fiók számítási szabályzatainak listáját.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Új számítási házirend létrehozása

A következő kód létrehoz egy új számítási szabályzatot a Data Lake Analytics-fiókhoz, a megadott felhasználó számára elérhető maximális Atus-t 50-re, a minimális feladatprioritást pedig 250-re állítva.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>További lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)