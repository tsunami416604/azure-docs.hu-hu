---
title: Az Azure Stream Analytics-kezelés .NET SDK |} Microsoft Docs
description: Első lépések a Stream Analytics felügyeleti .NET SDK-val. Megtudhatja, hogyan állítson be és az analytics-feladatok futtatásához. Hozzon létre egy projekt, bemenetek, kimenetek és átalakításához.
keywords: .NET SDK-val analytics API
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: jeanb
ms.openlocfilehash: 8c6b0f8ef457c7d40673f27b67a6d33592908fad
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>.NET SDK-kezelés: Beállítása és az Azure Stream Analytics API használatával a .NET-hez analytics-feladatok futtatása
Megtudhatja, hogyan állítson be és használja a Stream Analytics API Management .NET SDK használatával .NET analytics-feladatok futtatása. Projekt beállítása, hozzon létre a bemeneti és kimeneti adatforrások, átalakítások, és úgy indítsa és feladatok. Az analytics-feladatok adatok Blob-tároló vagy az eseményközpont folyamatos átviteléhez.

Tekintse meg a [felügyeleti referenciadokumentációt tartalmaz a Stream Analytics API .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Az Azure Stream Analytics egy olyan teljes körűen felügyelt szolgáltatás biztosít alacsony késésű, magas rendelkezésre állású, méretezhető, összetett Eseményfeldolgozási keresztül a streamelési adatok a felhőben. A Stream Analytics lehetővé teszi az ügyfelek a folyamatos átviteli feladatok beállítása az adatfolyamokat elemzése, és lehetővé teszi a közel valós idejű elemzési meghajtó.  

> [!NOTE]
> Ebben a cikkben a példakód Azure Stream Analytics Management .NET SDK v2.x verziójával lett frissítve. A által használt lagecy (1.x) SDK-verzió használó példakódot, talál [a Management .NET SDK v1.x használja a Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* Telepítse a Visual Studio 2017 vagy 2015.
* Töltse le és telepítse [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Azure-erőforráscsoport létrehozása az előfizetésben. Egy Azure PowerShell-parancsfájlpélda a következő: Azure PowerShell információkért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview);  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Beállítása egy bemeneti forrás- és kimeneti használatára. A további tudnivalókat [bemenet hozzáadása](stream-analytics-add-inputs.md) állíthat be egy minta bemeneti és [hozzáadása kimenetek](stream-analytics-add-outputs.md) állíthat be egy minta kimenet.

## <a name="set-up-a-project"></a>Projekt beállítása
Az analytics-feladat használja a Stream Analytics API a .NET-hez, először állítsa be a projekthez.

1. Hozzon létre egy Visual Studio C# .NET konzolalkalmazást.
2. A Package Manager-konzolon, a következő parancsokat a NuGet-csomagok. Az első címtárra az Azure Stream Analytics felügyeleti .NET SDK. A második érték van az Azure ügyfél-hitelesítéshez.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
3. Adja hozzá a következő **appSettings** szakaszt az App.config fájlban:
   
        <appSettings>
          <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
          <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
        </appSettings>

    Cserélje le az értékeket **SubscriptionId** és **ActiveDirectoryTenantId** a Azure-előfizetés és a bérlői azonosítók. Ezeket az értékeket a következő Azure PowerShell-parancsmag futtatásával kérhető le:

        Get-AzureAccount

4. Adja hozzá a .csproj fájlban a következő hivatkozást:

        <Reference Include="System.Configuration" />

5. Adja hozzá a következő **használatával** utasítást, hogy a projekt forrásfájl (Program.cs):
   
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Threading;
        using System.Threading.Tasks;
        
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Rest;
6. Adja hozzá a segítő hitelesítési módszert:

   ```
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Hozzon létre egy Stream Analytics management ügyfél
A **StreamAnalyticsManagementClient** objektum lehetővé teszi, hogy a feladat, és a feladat összetevők, például a bemeneti, kimeneti és átalakítása kezelheti.

Adja hozzá a következő kódot elejéhez a **fő** módszert:

   ```
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

A **resourceGroupName** változó értékét meg kell egyeznie az erőforráscsoport létrehozása, vagy az előfeltételként szükséges lépések kivételezett nevével.

Feladat létrehozása a hitelesítő adatok megjelenítési aspektusa automatizálását, tekintse meg [hitelesítéséhez az Azure Resource Manager szolgáltatásnevet](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Ez a cikk fennmaradó részében feltételezik, hogy ez a kód elején a **fő** metódust.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
A következő kódot a Stream Analytics-feladat a megadott erőforráscsoport alapján hoz létre. Hozzáadandó egy bemeneti, kimeneti és átalakítása a feladatot később.

   ```
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

## <a name="create-a-stream-analytics-input-source"></a>Hozzon létre egy Stream Analytics bemeneti forrása
A következő kódot a Stream Analytics bemeneti forrás a blob bemeneti forrása típusa és a fürt megosztott kötetei szolgáltatás szerializálási hoz létre. Az event hubs ezen bemeneti forrása hozhat létre **EventHubStreamInputDataSource** helyett **BlobStreamInputDataSource**. Hasonlóképpen testre szabhatja a bemeneti forrás a szerializálási típushoz.

   ```
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

Beviteli móddal, akár a Blob-tároló vagy egy eseményközpontba kötődnek, egy adott feladat. Használja az ugyanazon bemeneti forrás a különböző feladatok, hívja meg ismét a metódust, és adjon meg egy másik feladatnévvel.

## <a name="test-a-stream-analytics-input-source"></a>A Stream Analytics bemeneti forrás tesztelése
A **TestConnection** metódus teszteli, hogy a Stream Analytics-feladat csatlakozni tudjanak a bemeneti forrás a bemeneti forrás típusa különleges, valamint egyéb szempontokat. Például a blobbemeneti forrás egy korábbi lépésben létrehozott, a a módszerrel ellenőrzi, hogy a Tárfiók nevét és a kulcspár használatával lehet csatlakozni a tárfiókhoz, valamint a ellenőrizze, hogy létezik-e a megadott tároló.

   ```
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>A Stream Analytics kimeneti cél létrehozásához
Egy kimeneti cél létrehozása nagyon hasonlít a Stream Analytics bemeneti forrás létrehozása. Beviteli móddal, például a kimeneti célokat egy adott feladat vannak társítva. Kimeneti egyazon célobjektum használ a különböző feladatokhoz, hívja meg ismét a metódust, és adjon meg egy másik feladatnévvel.

Az alábbi kód létrehoz egy kimeneti célhoz (Azure SQL-adatbázis). Testre szabhatja a kimeneti adatok céltípus és/vagy szerializálási típushoz.

   ```
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

## <a name="test-a-stream-analytics-output-target"></a>A Stream Analytics kimeneti cél tesztelése
A Stream Analytics kimeneti cél is tartozik. a **TestConnection** kapcsolatok tesztelése a módszert.

   ```
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Hozzon létre egy Stream Analytics átalakítása
A következő kódot a Stream Analytics átalakítás hoz létre a lekérdezés "Válasszon * bemeneti" és egy streamelési egységet a Stream Analytics-feladat lefoglalni határozza meg. A streamelési egységek beállítása további információkért lásd: [Scale Azure Stream Analytics-feladatok](stream-analytics-scale-jobs.md).

   ```
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Például a bemeneti és kimeneti átalakítás is van kötve az adott Stream Analytics-feladat csoportban hozták létre.

## <a name="start-a-stream-analytics-job"></a>A Stream Analytics-feladat indítása
Miután létrehozta a Stream Analytics-feladat, és a input(s), kimenete és átalakítása, megkezdheti a feladat meghívása a **Start** metódust.

A következő példa egy egyéni kimeneti kezdési idejű Stream Analytics-feladat beállítása a 2012. December 12., 12:12:12 kód indítása (UTC):

   ```
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>A Stream Analytics-feladat leállítása
Egy futó Stream Analytics-feladat meghívásával leállíthatja a **leállítása** metódust.

   ```
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>A Stream Analytics-feladat törlése
A **törlése** metódus törli a feladatot, valamint az alapul szolgáló alárendelt erőforrások, például az input(s), kimenete és átalakítása a feladat.

   ```
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Támogatás kérése
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
Hogy megismerte a .NET SDK használatával hozhat létre és futtathat analytics-feladatok alapjait. További információ:

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Az Azure Stream Analytics felügyeleti .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
