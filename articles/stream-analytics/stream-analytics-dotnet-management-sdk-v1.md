---
title: Felügyeleti .NET SDK v1.x Azure Stream Analytics szolgáltatáshoz
description: Ismerkedés a Stream Analytics felügyeleti .NET SDK-t. Megtudhatja, hogyan állíthatja be, és az analytics-feladatok futtatásához. Hozzon létre egy projektet, bemenetek, kimenetek és átalakításokat.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0f9e889a15933953af275460ba12906db6f3adec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106532"
---
# <a name="set-up-and-run-analytics-jobs-using-azure-stream-analytics-api-for-net"></a>Állítsa be, és a .NET-hez készült Azure Stream Analytics API használatával az analytics-feladatok futtatása
Megtudhatja, hogyan állíthatja be, és a Stream Analytics API használatával a felügyeleti .NET SDK-val .NET-keretrendszerhez készült elemzési feladatok futtatásához. Projekt beállítása, hozzon létre bemeneti és kimeneti források átalakítások és kezdő, és állítsa le a feladatok. Az analytics-feladatokhoz streamelheti a Blob storage-ból, vagy az eseményközpontok adatait.

Tekintse meg a [management dokumentációja a Stream Analytics API a .NET-hez](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Az Azure Stream Analytics egy teljes körűen felügyelt szolgáltatást, amely kis késleltetésű, magas rendelkezésre állású, méretezhető és összetett Eseményfeldolgozási keresztül streamelési adatok a felhőben. Stream Analytics segítségével az ügyfelek streamelési feladatok beállítása a data-adatfolyamok elemzése, és lehetővé teszi, hogy a meghajtó közel valós idejű elemzését.  

> [!NOTE]
> Ebben a cikkben szereplő mintakódban továbbra is az Azure Stream Analytics felügyeleti .NET SDK-t (1.x) régebbi verzióját használja. A mintakód SDK naprakész verzióját használja, tekintse meg [a felügyeleti .NET SDK használata a Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk).

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
       # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the    Register-AzureRMProvider cmdlet to register the provider namespace
       #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Állítsa be egy bemeneti forrás- és kimeneti szeretne csatlakozni a feladathoz.

## <a name="set-up-a-project"></a>Projekt beállítása
Segítségével hozhat létre egy analytics-feladatban a Stream Analytics API a .NET-hez, először állítsa be a projekthez.

1. Hozzon létre egy Visual Studio C# nyelvet használó .NET-konzolalkalmazást.
2. A Package Manager konzol futtassa a következő parancsokat a NuGet-csomagok telepítéséhez. Az első rekordon az Azure Stream Analytics felügyeleti .NET SDK. A második érték az Azure Active Directory-ügyfél-hitelesítéshez használandó.

```powershell
Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
```

3. Adja hozzá a következő **appSettings** szakaszt az App.config fájlhoz:

   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
     <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
   </appSettings>
   ```

    Cserélje le az értékeket **SubscriptionId** és **ActiveDirectoryTenantId** az Azure-előfizetés és a bérlői azonosítókkal rendelkező. Ezeket az értékeket a következő Azure PowerShell-parancsmag futtatásával kaphat:

        Get-AzureAccount

4. A .csproj fájlban adja hozzá a következő hivatkozást:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

1. Adja hozzá a következő **használatával** utasításokat a projekt forrásfájljához (Program.cs):

```csharp
using System;
using System.Configuration;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.StreamAnalytics;
using Microsoft.Azure.Management.StreamAnalytics.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

2. Adja hozzá a segítő hitelesítési módszert:

   ```csharp
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Hozzon létre egy Stream Analytics felügyeleti ügyfél
A **StreamAnalyticsManagementClient** kezelheti a feladat és a feladat-összetevők, például a bemeneti, kimeneti és átalakítás az objektum teszi lehetővé.

Adja hozzá a következő kódot elejéhez a **fő** módszer:

   ```csharp
   string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
   string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
   string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
   string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
   string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";
   // Get authentication token
   TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
       ConfigurationManager.AppSettings["SubscriptionId"],
       GetAuthorizationHeader().Result);
   // Create Stream Analytics management client
   StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);
   ```

A **resourceGroupName** változó értéke ugyanaz, mint a létrehozott vagy az előfeltételként felsorolt lépéseket követi az erőforráscsoport nevére kell lennie.

Feladat létrehozása a hitelesítő adatok bemutató aspektusa automatizálása, tekintse meg [hitelesítése egy egyszerű szolgáltatást az Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Ez a cikk fennmaradó részéből tegyük fel, hogy ez a kód elején a **fő** metódust.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
Az alábbi kód létrehoz egy Stream Analytics-feladat a megadott erőforráscsoportban. Hozzáadandó egy bemeneti, kimeneti és átalakítása a feladatot később.

   ```csharp
   // Create a Stream Analytics job
   JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
   {
       Job = new Job()
       {
           Name = streamAnalyticsJobName,
           Location = "<LOCATION>",
           Properties = new JobProperties()
           {
               EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
               Sku = new Sku()
               {
                   Name = "Standard"
               }
           }
       }
   };
   JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Hozzon létre egy Stream Analytics bemeneti forrás
Az alábbi kód létrehoz egy Stream Analytics bemeneti forrás a blob bemeneti forrása típusa és a fürt megosztott kötetei szolgáltatás szerializálási. Hozzon létre egy event hubs ezen bemeneti forrása, használja a **EventHubStreamInputDataSource** helyett **BlobStreamInputDataSource**. Hasonló módon szabhatja testre a bemeneti forrás szerializálás típusa.

   ```csharp
   // Create a Stream Analytics input source
   InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
   {
       Input = new Input()
       {
           Name = streamAnalyticsInputName,
           Properties = new StreamInputProperties()
           {
               Serialization = new CsvSerialization
               {
                   Properties = new CsvSerializationProperties
                   {
                       Encoding = "UTF8",
                       FieldDelimiter = ","
                   }
               },
               DataSource = new BlobStreamInputDataSource
               {
                   Properties = new BlobStreamInputDataSourceProperties
                   {
                       StorageAccounts = new StorageAccount[]
                       {
                           new StorageAccount()
                           {
                               AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                               AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                           }
                       },
                       Container = "samples",
                       PathPattern = ""
                   }
               }
           }
       }
   };
   InputCreateOrUpdateResponse inputCreateResponse =
   client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);
   ```

Bemeneti forrás a Blob storage vagy az eseményközpontok felé, vannak társítva egy adott feladat. Az ugyanazon bemeneti forrás használja a különböző feladatokhoz, hívja meg ismét a metódust, és adjon meg egy másik feladatnévvel.

## <a name="test-a-stream-analytics-input-source"></a>A Stream Analytics bemeneti forrás tesztelése
A **TestConnection** metódus teszteli, hogy tud csatlakozni a bemeneti forrás, valamint egyéb szempontok a bemeneti forrás típusaként a egyedi-e a Stream Analytics-feladat. Például a korábbi lépésben létrehozott blob bemeneti forrás, a metódus ellenőrzi, hogy a Tárfiók nevét és a kulcspárt a csatlakozás a tárfiókhoz, valamint ellenőrizze, hogy létezik-e a megadott tároló használhatók.

   ```csharp
   // Test input source connection
   DataSourceTestConnectionResponse inputTestResponse =
       client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Hozzon létre egy Stream Analytics kimeneti célpontjává
Egy kimeneti célpontjává létrehozása nagyon hasonlít egy Stream Analytics bemeneti forrás létrehozása. A bemeneti források, például egy adott feladat kimeneti tárolók vannak társítva. Az azonos kimeneti célpontjává használ a különböző feladatok, hívja meg ismét a metódust, és adjon meg egy másik feladatnévvel.

Az alábbi kód létrehoz egy kimeneti tárolóhoz (az Azure SQL database). Testre szabhatja a kimeneti célpontjává adattípus és/vagy szerializálási típushoz.

   ```csharp
   // Create a Stream Analytics output target
   OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
   {
       Output = new Output()
       {
           Name = streamAnalyticsOutputName,
           Properties = new OutputProperties()
           {
               DataSource = new SqlAzureOutputDataSource()
               {
                   Properties = new SqlAzureOutputDataSourceProperties()
                   {
                       Server = "<YOUR DATABASE SERVER NAME>",
                       Database = "<YOUR DATABASE NAME>",
                       User = "<YOUR DATABASE LOGIN>",
                       Password = "<YOUR DATABASE LOGIN PASSWORD>",
                       Table = "<YOUR DATABASE TABLE NAME>"
                   }
               }
           }
       }
   };
   OutputCreateOrUpdateResponse outputCreateResponse =
       client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);
   ```

## <a name="test-a-stream-analytics-output-target"></a>A Stream Analytics kimeneti célpontjává tesztelése
A Stream Analytics kimeneti célpontjává is rendelkezik a **TestConnection** kapcsolatok tesztelési módszer.

   ```csharp
   // Test output target connection
   DataSourceTestConnectionResponse outputTestResponse =
       client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Hozzon létre egy Stream Analytics-átalakítás
Az alábbi kód létrehoz egy Stream Analytics-átalakítás lekérdezése "kiválasztása * bemeneti" és a egy streamelési egység lefoglalása a Stream Analytics-feladat meghatározza. A folyamatos átviteli egységek beállítása a további információkért lásd: [Scale Azure Stream Analytics-feladatok](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a Stream Analytics transformation
   TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
   {
       Transformation = new Transformation()
       {
           Name = streamAnalyticsTransformationName,
           Properties = new TransformationProperties()
           {
               StreamingUnits = 1,
               Query = "select * from Input"
           }
       }
   };
   var transformationCreateResp =
       client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);
   ```

Például a bemeneti és kimeneti átalakítást is vannak kötve, a konkrét Stream Analytics-feladat alapján lett létrehozva.

## <a name="start-a-stream-analytics-job"></a>Stream Analytics-feladat indítása
Miután létrehozott egy Stream Analytics-feladat és a input(s) kimenete és átalakítása, elkezdheti a feladat meghívása a **Start** metódust.

A következő példa egy Stream Analytics-feladat egy egyéni kimeneti kezdési időponttal beállítása a 2012. December 12., 12:12:12 kód kezdődik (UTC):

   ```csharp
   // Start a Stream Analytics job
   JobStartParameters jobStartParameters = new JobStartParameters
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
   };
   
   LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName,    jobStartParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Stream Analytics-feladat leállítása
Egy futó Stream Analytics-feladat meghívásával állítsa le a **leállítása** metódust.

   ```csharp
   // Stop a Stream Analytics job
   LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Stream Analytics-feladat törlése
A **törlése** metódus törli a feladatot, valamint az alapul szolgáló alárendelt erőforrások, például a input(s) kimenete és átalakítási feladat.

   ```csharp
   // Delete a Stream Analytics job
   LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);
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
