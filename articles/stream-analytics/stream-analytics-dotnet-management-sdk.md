---
title: Felügyeleti .NET SDK-t az Azure Stream Analytics szolgáltatáshoz
description: Ismerkedés a Stream Analytics felügyeleti .NET SDK-t. Megtudhatja, hogyan állíthatja be, és az analytics-feladatok futtatásához. Hozzon létre egy projektet, bemenetek, kimenetek és átalakításokat.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 53d9345784c16412c643f3b50506bf6abbab93ec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094902"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Felügyeleti .NET SDK-val: Állítsa be, és futtassa az analytics-feladatok az Azure Stream Analytics API használatával a .NET-hez
Megtudhatja, hogyan állíthatja be, és a Stream Analytics API használatával a felügyeleti .NET SDK-val .NET-keretrendszerhez készült elemzési feladatok futtatásához. Projekt beállítása, hozzon létre bemeneti és kimeneti források átalakítások és kezdő, és állítsa le a feladatok. Az analytics-feladatokhoz streamelheti a Blob storage-ból, vagy az eseményközpontok adatait.

Tekintse meg a [management dokumentációja a Stream Analytics API a .NET-hez](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Az Azure Stream Analytics egy teljes körűen felügyelt szolgáltatást, amely kis késleltetésű, magas rendelkezésre állású, méretezhető és összetett Eseményfeldolgozási keresztül streamelési adatok a felhőben. Stream Analytics segítségével az ügyfelek streamelési feladatok beállítása a data-adatfolyamok elemzése, és lehetővé teszi, hogy a meghajtó közel valós idejű elemzését.  

> [!NOTE]
> Ebben a cikkben szereplő mintakódban frissítettük az Azure Stream Analytics felügyeleti .NET SDK 2.x verziójával. A mintakód a felhasználási lagecy (1.x) SDK-verzió használatával, tekintse meg [használja a felügyeleti .NET SDK-val v1.x a Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* Telepítse a Visual Studio 2017 vagy 2015-öt.
* Töltse le és telepítse [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Hozzon létre egy Azure-erőforráscsoportot az előfizetésében. Az alábbiakban látható egy minta Azure PowerShell-parancsfájlt. Azure PowerShell-információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
   #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Állítsa be egy bemeneti forrás- és kimeneti szeretne csatlakozni a feladathoz.

## <a name="set-up-a-project"></a>Projekt beállítása
Segítségével hozhat létre egy analytics-feladatban a Stream Analytics API a .NET-hez, először állítsa be a projekthez.

1. Hozzon létre egy Visual Studio C# nyelvet használó .NET-konzolalkalmazást.
2. A Package Manager konzol futtassa a következő parancsokat a NuGet-csomagok telepítéséhez. Az első rekordon az Azure Stream Analytics felügyeleti .NET SDK. A második érték van, az Azure ügyfél-hitelesítéshez.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Adja hozzá a következő **appSettings** szakaszt az App.config fájlhoz:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Cserélje le az értékeket **SubscriptionId** és **ActiveDirectoryTenantId** az Azure-előfizetés és a bérlői azonosítókkal rendelkező. Ezeket az értékeket a következő Azure PowerShell-parancsmag futtatásával kaphat:

   ```powershell
      Get-AzureAccount
   ```

4. A .csproj fájlban adja hozzá a következő hivatkozást:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Adja hozzá a következő **használatával** utasításokat a projekt forrásfájljához (Program.cs):
   
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

6. Adja hozzá a segítő hitelesítési módszert:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Hozzon létre egy Stream Analytics felügyeleti ügyfél
A **StreamAnalyticsManagementClient** kezelheti a feladat és a feladat-összetevők, például a bemeneti, kimeneti és átalakítás az objektum teszi lehetővé.

Adja hozzá a következő kódot elejéhez a **fő** módszer:

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

A **resourceGroupName** változó értéke ugyanaz, mint a létrehozott vagy az előfeltételként felsorolt lépéseket követi az erőforráscsoport nevére kell lennie.

Feladat létrehozása a hitelesítő adatok bemutató aspektusa automatizálása, tekintse meg [hitelesítése egy egyszerű szolgáltatást az Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Ez a cikk fennmaradó részéből tegyük fel, hogy ez a kód elején a **fő** metódust.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
Az alábbi kód létrehoz egy Stream Analytics-feladat a megadott erőforráscsoportban. Hozzáadandó egy bemeneti, kimeneti és átalakítása a feladatot később.

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

## <a name="create-a-stream-analytics-input-source"></a>Hozzon létre egy Stream Analytics bemeneti forrás
Az alábbi kód létrehoz egy Stream Analytics bemeneti forrás a blob bemeneti forrása típusa és a fürt megosztott kötetei szolgáltatás szerializálási. Hozzon létre egy event hubs ezen bemeneti forrása, használja a **EventHubStreamInputDataSource** helyett **BlobStreamInputDataSource**. Hasonló módon szabhatja testre a bemeneti forrás szerializálás típusa.

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

Bemeneti forrás a Blob storage vagy az eseményközpontok felé, vannak társítva egy adott feladat. Az ugyanazon bemeneti forrás használja a különböző feladatokhoz, hívja meg ismét a metódust, és adjon meg egy másik feladatnévvel.

## <a name="test-a-stream-analytics-input-source"></a>A Stream Analytics bemeneti forrás tesztelése
A **TestConnection** metódus teszteli, hogy tud csatlakozni a bemeneti forrás, valamint egyéb szempontok a bemeneti forrás típusaként a egyedi-e a Stream Analytics-feladat. Például a korábbi lépésben létrehozott blob bemeneti forrás, a metódus ellenőrzi, hogy a Tárfiók nevét és a kulcspárt a csatlakozás a tárfiókhoz, valamint ellenőrizze, hogy létezik-e a megadott tároló használhatók.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Hozzon létre egy Stream Analytics kimeneti célpontjává
Egy kimeneti célpontjává létrehozása nagyon hasonlít egy Stream Analytics bemeneti forrás létrehozása. A bemeneti források, például egy adott feladat kimeneti tárolók vannak társítva. Az azonos kimeneti célpontjává használ a különböző feladatok, hívja meg ismét a metódust, és adjon meg egy másik feladatnévvel.

Az alábbi kód létrehoz egy kimeneti tárolóhoz (az Azure SQL database). Testre szabhatja a kimeneti célpontjává adattípus és/vagy szerializálási típushoz.

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

## <a name="test-a-stream-analytics-output-target"></a>A Stream Analytics kimeneti célpontjává tesztelése
A Stream Analytics kimeneti célpontjává is rendelkezik a **TestConnection** kapcsolatok tesztelési módszer.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Hozzon létre egy Stream Analytics-átalakítás
Az alábbi kód létrehoz egy Stream Analytics-átalakítás lekérdezése "kiválasztása * bemeneti" és a egy streamelési egység lefoglalása a Stream Analytics-feladat meghatározza. A folyamatos átviteli egységek beállítása a további információkért lásd: [Scale Azure Stream Analytics-feladatok](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Például a bemeneti és kimeneti átalakítást is vannak kötve, a konkrét Stream Analytics-feladat alapján lett létrehozva.

## <a name="start-a-stream-analytics-job"></a>Stream Analytics-feladat indítása
Miután létrehozott egy Stream Analytics-feladat és a input(s) kimenete és átalakítása, elkezdheti a feladat meghívása a **Start** metódust.

A következő példa egy Stream Analytics-feladat egy egyéni kimeneti kezdési időponttal beállítása a 2012. December 12., 12:12:12 kód kezdődik (UTC):

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
Egy futó Stream Analytics-feladat meghívásával állítsa le a **leállítása** metódust.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Stream Analytics-feladat törlése
A **törlése** metódus törli a feladatot, valamint az alapul szolgáló alárendelt erőforrások, például a input(s) kimenete és átalakítási feladat.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Támogatás kérése
További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
Bemutattuk, hozhat létre, és az analytics-feladatok futtatása a .NET SDK használatával kapcsolatos alapfogalmakat. További információ:

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Az Azure Stream Analytics felügyeleti .NET SDK-val](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
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
