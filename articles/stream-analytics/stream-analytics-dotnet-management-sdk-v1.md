---
title: Felügyeleti .NET SDK v1.x az Azure Stream Analytics |} Microsoft Docs
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
ms.openlocfilehash: 377213fdd0231fafc9b87cb43e234ee6bba0a745
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="management-net-sdk-v1x-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Felügyeleti .NET SDK v1.x: állítsa össze, és az Azure Stream Analytics API használatával a .NET-hez analytics-feladatok futtatása
Megtudhatja, hogyan állítson be és használja a Stream Analytics API Management .NET SDK használatával .NET analytics-feladatok futtatása. Projekt beállítása, hozzon létre a bemeneti és kimeneti adatforrások, átalakítások, és úgy indítsa és feladatok. Az analytics-feladatok adatok Blob-tároló vagy az eseményközpont folyamatos átviteléhez.

Tekintse meg a [felügyeleti referenciadokumentációt tartalmaz a Stream Analytics API .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Az Azure Stream Analytics egy olyan teljes körűen felügyelt szolgáltatás biztosít alacsony késésű, magas rendelkezésre állású, méretezhető, összetett Eseményfeldolgozási keresztül a streamelési adatok a felhőben. A Stream Analytics lehetővé teszi az ügyfelek a folyamatos átviteli feladatok beállítása az adatfolyamokat elemzése, és lehetővé teszi a közel valós idejű elemzési meghajtó.  

> [!NOTE]
> Ebben a cikkben a példakód továbbra is az Azure Stream Analytics Management .NET SDK örökölt (1.x) verzióját használja. A mintakód SDK naprakész verzióját használja, ellenőrizze [felügyeleti .NET SDK használata a Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk).

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
2. A Package Manager-konzolon, a következő parancsokat a NuGet-csomagok. Az első címtárra az Azure Stream Analytics felügyeleti .NET SDK. A második érték az Azure Active Directory-ügyfél-hitelesítéshez használandó.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
3. Adja hozzá a következő **appSettings** szakaszt az App.config fájlban:
   
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

    Cserélje le az értékeket **SubscriptionId** és **ActiveDirectoryTenantId** a Azure-előfizetés és a bérlői azonosítók. Ezeket az értékeket a következő Azure PowerShell-parancsmag futtatásával kérhető le:

        Get-AzureAccount

4. Adja hozzá a .csproj fájlban a következő hivatkozást:

        <Reference Include="System.Configuration" />

1. Adja hozzá a következő **használatával** utasítást, hogy a projekt forrásfájl (Program.cs):
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. Adja hozzá a segítő hitelesítési módszert:

   ```   
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

## <a name="create-a-stream-analytics-management-client"></a>Hozzon létre egy Stream Analytics management ügyfél
A **StreamAnalyticsManagementClient** objektum lehetővé teszi, hogy a feladat, és a feladat összetevők, például a bemeneti, kimeneti és átalakítása kezelheti.

Adja hozzá a következő kódot elejéhez a **fő** módszert:

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

A **resourceGroupName** változó értékét meg kell egyeznie az erőforráscsoport létrehozása, vagy az előfeltételként szükséges lépések kivételezett nevével.

Feladat létrehozása a hitelesítő adatok megjelenítési aspektusa automatizálását, tekintse meg [hitelesítéséhez az Azure Resource Manager szolgáltatásnevet](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Ez a cikk fennmaradó részében feltételezik, hogy ez a kód elején a **fő** metódust.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
A következő kódot a Stream Analytics-feladat a megadott erőforráscsoport alapján hoz létre. Hozzáadandó egy bemeneti, kimeneti és átalakítása a feladatot később.

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


## <a name="create-a-stream-analytics-input-source"></a>Hozzon létre egy Stream Analytics bemeneti forrása
A következő kódot a Stream Analytics bemeneti forrás a blob bemeneti forrása típusa és a fürt megosztott kötetei szolgáltatás szerializálási hoz létre. Az event hubs ezen bemeneti forrása hozhat létre **EventHubStreamInputDataSource** helyett **BlobStreamInputDataSource**. Hasonlóképpen testre szabhatja a bemeneti forrás a szerializálási típushoz.

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

Beviteli móddal, akár a Blob-tároló vagy egy eseményközpontba kötődnek, egy adott feladat. Használja az ugyanazon bemeneti forrás a különböző feladatok, hívja meg ismét a metódust, és adjon meg egy másik feladatnévvel.

## <a name="test-a-stream-analytics-input-source"></a>A Stream Analytics bemeneti forrás tesztelése
A **TestConnection** metódus teszteli, hogy a Stream Analytics-feladat csatlakozni tudjanak a bemeneti forrás a bemeneti forrás típusa különleges, valamint egyéb szempontokat. Például a blobbemeneti forrás egy korábbi lépésben létrehozott, a a módszerrel ellenőrzi, hogy a Tárfiók nevét és a kulcspár használatával lehet csatlakozni a tárfiókhoz, valamint a ellenőrizze, hogy létezik-e a megadott tároló.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>A Stream Analytics kimeneti cél létrehozásához
Egy kimeneti cél létrehozása nagyon hasonlít a Stream Analytics bemeneti forrás létrehozása. Beviteli móddal, például a kimeneti célokat egy adott feladat vannak társítva. Kimeneti egyazon célobjektum használ a különböző feladatokhoz, hívja meg ismét a metódust, és adjon meg egy másik feladatnévvel.

Az alábbi kód létrehoz egy kimeneti célhoz (Azure SQL-adatbázis). Testre szabhatja a kimeneti adatok céltípus és/vagy szerializálási típushoz.

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

## <a name="test-a-stream-analytics-output-target"></a>A Stream Analytics kimeneti cél tesztelése
A Stream Analytics kimeneti cél is tartozik. a **TestConnection** kapcsolatok tesztelése a módszert.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Hozzon létre egy Stream Analytics átalakítása
A következő kódot a Stream Analytics átalakítás hoz létre a lekérdezés "Válasszon * bemeneti" és egy streamelési egységet a Stream Analytics-feladat lefoglalni határozza meg. A streamelési egységek beállítása további információkért lásd: [Scale Azure Stream Analytics-feladatok](stream-analytics-scale-jobs.md).

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

Például a bemeneti és kimeneti átalakítás is van kötve az adott Stream Analytics-feladat csoportban hozták létre.

## <a name="start-a-stream-analytics-job"></a>A Stream Analytics-feladat indítása
Miután létrehozta a Stream Analytics-feladat, és a input(s), kimenete és átalakítása, megkezdheti a feladat meghívása a **Start** metódust.

A következő példa egy egyéni kimeneti kezdési idejű Stream Analytics-feladat beállítása a 2012. December 12., 12:12:12 kód indítása (UTC):

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>A Stream Analytics-feladat leállítása
Egy futó Stream Analytics-feladat meghívásával leállíthatja a **leállítása** metódust.

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>A Stream Analytics-feladat törlése
A **törlése** metódus törli a feladatot, valamint az alapul szolgáló alárendelt erőforrások, például az input(s), kimenete és átalakítása a feladat.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

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
