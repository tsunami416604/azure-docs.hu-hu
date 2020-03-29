---
title: Felügyeleti .NET SDK az Azure Stream Analytics szolgáltatáshoz
description: Ismerkedés a Stream Analytics Management .NET SDK szolgáltatással. Ismerje meg, hogyan állíthat be és futtathat elemzési feladatokat. Hozzon létre egy projektet, bemeneteket, kimeneteket és átalakításokat.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 20be2c56635faa4f77ae8e8e6afc3c1ece6d4942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426261"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Felügyeleti .NET SDK: Elemzési feladatok beállítása és futtatása az Azure Stream Analytics API-val .
Ismerje meg, hogyan állíthatja be és futtathat elemzési feladatokat a .NET Stream Analytics API-jával a Management .NET SDK használatával. Állítson be egy projektet, hozzon létre bemeneti és kimeneti forrásokat, átalakításokat, valamint indítsa el és állítsa le a feladatokat. Az elemzési feladatokhoz adatokat streamelhet a Blob storage-ból vagy egy eseményközpontból.

Tekintse meg a [.NET Stream Analytics API felügyeleti referenciadokumentációját.](https://msdn.microsoft.com/library/azure/dn889315.aspx)

Az Azure Stream Analytics egy teljes körűen felügyelt szolgáltatás, amely alacsony késleltetésű, magas rendelkezésre állású, méretezhető, összetett eseményfeldolgozást biztosít a felhőben lévő streamelési adatokon keresztül. A Stream Analytics lehetővé teszi az ügyfelek számára, hogy streamelési feladatokat állítsanak be az adatfolyamok elemzéséhez, és lehetővé teszi számukra, hogy közel valós idejű elemzéseket vezessenek.  

> [!NOTE]
> Ebben a cikkben frissítettük a mintakódot az Azure Stream Analytics Management .NET SDK v2.x verzióval. A késés (1.x) SDK-verziót használó mintakódot [a Management .NET SDK v1.x használata a Stream Analytics szolgáltatáshoz című témakörben tartalmazza.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1)

## <a name="prerequisites"></a>Előfeltételek
A cikk megkezdése előtt a következő követelményekkel kell rendelkeznie:

* Telepítse a Visual Studio 2019-et vagy 2015-öt.
* Töltse le és telepítse [az Azure .NET SDK](https://azure.microsoft.com/downloads/)alkalmazást.
* Hozzon létre egy Azure-erőforráscsoportot az előfizetésében. A következő példa egy minta Azure PowerShell-parancsfájl. Az Azure PowerShell-információkról az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben talál.  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Állítson be egy bemeneti forrás- és kimeneti célt a feladathoz, amelyhez csatlakozni szeretne.

## <a name="set-up-a-project"></a>Projekt beállítása
Elemzési feladat létrehozásához használja a Stream Analytics API-t a .NET-hez, és először állítsa be a projektet.

1. Hozzon létre egy Visual Studio C# .NET konzolalkalmazást.
2. A Package Manager konzolon futtassa a következő parancsokat a NuGet csomagok telepítéséhez. Az első az Azure Stream Analytics Management .NET SDK. A második az Azure-ügyfél hitelesítése.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Adja hozzá a következő **appBeállítások** szakaszt az App.config fájlhoz:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    A **SubscriptionId** és az **ActiveDirectoryTenantId** értékeit az Azure-előfizetésés a bérlői azonosítók helyére cserélheti. Ezeket az értékeket a következő Azure PowerShell-parancsmag futtatásával szerezheti be:

   ```powershell
      Get-AzureAccount
   ```

4. Adja meg a következő hivatkozást a .csproj fájlban:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Adja hozzá a következőket a projekt forrásfájljába (Program.cs) a következő **utasítások használatával:**
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Adjon hozzá egy hitelesítési segítő módszert:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Stream Analytics felügyeleti ügyfél létrehozása
A **StreamAnalyticsManagementClient** objektum lehetővé teszi a feladat és a feladat-összetevők, például a bemenet, a kimenet és az átalakítás kezelését.

Adja hozzá a következő kódot a **fő** módszer elejéhez:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

A **resourceGroupName** változó értékének meg kell egyeznie az előfeltételként leírt lépésekben létrehozott vagy kiválasztott erőforráscsoport nevével.

A feladatalapítás hitelesítő adatok megjelenítésének elemének automatizálásához olvassa el az egyszerű szolgáltatás hitelesítése az Azure Resource Manager segítségével című [dokumentumban.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

A cikk további szakaszai azt feltételezik, hogy ez a kód a **Fő** módszer elején található.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
A következő kód létrehoz egy Stream Analytics-feladatot a megadott erőforráscsoport alatt. Később hozzáad egy bemeneti, kimeneti és átalakítási adatokat a feladathoz.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Stream Analytics-bemeneti forrás létrehozása
A következő kód létrehoz egy Stream Analytics bemeneti forrást a blob bemeneti forrástípussal és a CSV-szerializálással. Eseményközpont-bemeneti forrás létrehozásához használja **az EventHubStreamInputDataSource** metódust a **BlobStreamInputDataSource**helyett. Hasonlóképpen testreszabhatja a bemeneti forrás szerializálási típusát is.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

A blobstorage-ból vagy egy eseményközpontból származó bemeneti források egy adott feladathoz vannak kötve. Ha ugyanazt a bemeneti forrást szeretné használni a különböző feladatokhoz, újra meg kell hívnia a metódust, és másik feladatnevet kell megadnia.

## <a name="test-a-stream-analytics-input-source"></a>Stream Analytics-bemeneti forrás tesztelése
A **TestConnection** metódus azt teszteli, hogy a Stream Analytics-feladat képes-e csatlakozni a bemeneti forráshoz, valamint a bemeneti forrás típusának egyéb szempontjaihoz. Például a blob bemeneti forrás egy korábbi lépésben létrehozott, a módszer ellenőrzi, hogy a storage-fiók neve és kulcspár használható-e a storage-fiókhoz való csatlakozáshoz, valamint ellenőrizze, hogy a megadott tároló létezik-e.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Stream Analytics kimeneti cél létrehozása
A kimeneti cél létrehozása hasonló a Stream Analytics bemeneti forrás létrehozásához. A bemeneti forrásokhoz hasonlóan a kimeneti célok egy adott feladathoz vannak kötve. Ha ugyanazt a kimeneti célt szeretné használni a különböző feladatokhoz, újra meg kell hívnia a metódust, és másik feladatnevet kell megadnia.

A következő kód létrehoz egy kimeneti cél (Azure SQL-adatbázis). Testreszabhatja a kimeneti cél adattípusát és/vagy szerializálási típusát.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Stream Analytics kimeneti céltesztelése
A Stream Analytics kimeneti cél is rendelkezik a **TestConnection** módszer rel a kapcsolatok teszteléséhez.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Stream Analytics-átalakítás létrehozása
A következő kód létrehoz egy Stream Analytics-átalakítást a "select * from Input" lekérdezéssel, és meghatározza, hogy egy streamelési egységet foglaljon le a Stream Analytics-feladathoz. A streamelési egységek módosításáról az [Azure Stream Analytics-feladatok méretezése című témakörben](stream-analytics-scale-jobs.md)talál további információt.

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

A bemeneti és kimeneti adatokhoz hasonlóan az átalakítás is az adott Stream Analytics-feladathoz van kötve, amely alatt jött létre.

## <a name="start-a-stream-analytics-job"></a>Stream Analytics-feladat elindítása
A Stream Analytics-feladat és annak bemenetei, kimenetei és átalakítása létrehozása után a **Start** metódus hívásával indíthatja el a feladatot.

A következő mintakód elindítja a Stream Analytics-feladatot, amelynek egyéni kimeneti kezdési időpontja 2012.

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Stream Analytics-feladat leállítása
A Stream Analytics-feladat futtatását leállíthatja a **Stop** metódus hívásával.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Stream Analytics-feladat törlése
A **Törlés** metódus törli a feladatot, valamint az alapul szolgáló alerőforrásokat, beleértve a bemenet(ek)et, a kimenet(ek)et és a feladat átalakítását.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
Megtanulta a .NET SDK elemzési feladatok létrehozásának és futtatásának alapjait. További információ: a következő cikkek:

* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Az Azure Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
