---
title: 'Végpontok között blobbetöltés az Azure Data Explorer betöltése C-n keresztül #'
description: Ebben a cikkben megtudhatja, hogyan kell bedolgozni a blobokat az Azure Data Explorerbe egy c#-t használó teljes körű példával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 0711484c4fff24c5dcd3c18effce596a92bc30c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964515"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-c"></a>Végpontok között blobbetöltés az Azure Data Explorer betöltése C-n keresztül #

> [!div class="op_single_selector"]
> * [C #](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Az Azure Data Explorer egy gyors és skálázható adatfeltárási szolgáltatás a napló- és telemetriai adatokhoz. Ez a cikk egy teljes körű példát ad arra, hogyan lehet adatokat beadni az Azure Blob storage-ból az Azure Data Explorerbe. 

Megtudhatja, hogyan hozhat létre programozott módon egy erőforráscsoportot, egy tárfiókot és egy tárolót, egy eseményközpontot, valamint egy Azure Data Explorer-fürtöt és-adatbázist. Azt is megtudhatja, hogyan konfigurálhatja az Azure Data Explorerprogramot az új tárfiókból származó adatok betöltéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="install-c-nuget"></a>C# NuGet telepítése

* Telepítse a [Microsoft.Azure.Management.kusto alkalmazást.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Telepítse a [Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)alkalmazást.
* Telepítse a [Microsoft.Azure.Management.EventGrid programot.](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/)
* Telepítse a [Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)alkalmazást.
* Telepítse a [Microsoft.Rest.ClientRuntime.Azure.Authentication hitelesítést](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) a hitelesítéshez.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Mintakód 

A következő kód példa egy lépésről-lépésre folyamat, amely az Azure Data Explorer adatbetöltését eredményezi. 

Először hozzon létre egy erőforráscsoportot. Azure-erőforrásokat is létrehozhat, például egy tárfiókot és egy tárolót, egy eseményközpontot és egy Azure Data Explorer-fürtöt és-adatbázist, és hozzáadja a rendszerbiztonsági tagokat. Ezután hozzon létre egy Azure Event Grid-előfizetést, valamint egy tábla- és oszlopleképezést az Azure Data Explorer-adatbázisban. Végül hozza létre az adatkapcsolatot az Azure Data Explorer konfigurálásához az új tárfiókból származó adatok betöltéséhez. 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//Path to the Azure Resource Manager template JSON from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

//principals
string principalIdForCluster = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
string roleForClusterPrincipal = "AllDatabasesAdmin";
string tenantIdForClusterPrincipal = tenantId;
string principalTypeForCluster = "App";
string principalIdForDatabase = "xxxxxxxx@xxxxxxxx.com";//User Email
string roleForDatabasePrincipal = "Admin";
string tenantIdForDatabasePrincipal = tenantId;
string principalTypeForDatabase = "User";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, database, and add principals by using an Azure Resource Manager template.");
var parameters = new Dictionary<string, Dictionary<string, object>>();
parameters["eventHubNamespaceName"] = new Dictionary<string, object>(capacity: 1) {{"value", eventHubNamespaceName}};
parameters["eventHubName"] = new Dictionary<string, object>(capacity: 1) {{"value", eventHubName }};
parameters["storageAccountName"] = new Dictionary<string, object>(capacity: 1) {{"value", storageAccountName }};
parameters["containerName"] = new Dictionary<string, object>(capacity: 1) {{"value", storageContainerName }};
parameters["kustoClusterName"] = new Dictionary<string, object>(capacity: 1) {{"value", kustoClusterName }};
parameters["kustoDatabaseName"] = new Dictionary<string, object>(capacity: 1) {{"value", kustoDatabaseName }};
parameters["principalIdForCluster"] = new Dictionary<string, object>(capacity: 1) {{"value", principalIdForCluster }};
parameters["roleForClusterPrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", roleForClusterPrincipal }};
parameters["tenantIdForClusterPrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", tenantIdForClusterPrincipal }};
parameters["principalTypeForCluster"] = new Dictionary<string, object>(capacity: 1) {{"value", principalTypeForCluster }};
parameters["principalIdForDatabase"] = new Dictionary<string, object>(capacity: 1) {{"value", principalIdForDatabase }};
parameters["roleForDatabasePrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", roleForDatabasePrincipal }};
parameters["tenantIdForDatabasePrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", tenantIdForDatabasePrincipal }};
parameters["principalTypeForDatabase"] = new Dictionary<string, object>(capacity: 1) {{"value", principalTypeForDatabase }};
            
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **Beállítás** | **Mező leírása** |
|---|---|---|
| tenantId | A bérlőazonosítója. Könyvtárazonosítónak is nevezik.|
| subscriptionId | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| ügyfél-azonosító | Az alkalmazás ügyfélazonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| ügyféltitkos | Az alkalmazás ügyféltka-tka, amely hozzáférhet a bérlő erőforrásaihoz. |

## <a name="test-the-code-example"></a>A példakód tesztelése

1. Töltsön fel egy fájlt a tárfiókba.

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**Beállítás** | **Mező leírása**|
    |---|---|---|
    | storageConnectionString | A programozottan létrehozott tárfiók kapcsolati karakterlánca.|

2. Futtasson egy tesztlekérdezést az Azure Data Explorerben.

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport törléséhez és az erőforrások törléséhez használja a következő parancsot:

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>További lépések

*  A fürt és az adatbázis létrehozásának egyéb módjairól az [Azure Data Explorer-fürt és -adatbázis létrehozása](create-cluster-database-csharp.md)című témakörben olvashat.
* A betöltési módszerekről az [Azure Data Explorer adatbetöltése](ingest-data-overview.md)című témakörben olvashat bővebben.
* A webalkalmazásról a [Rövid útmutató: Lekérdezési adatok az Azure Data Explorer webes felhasználói felületén](web-query-data.md)című témakörben olvashat.
* [Lekérdezések írása](write-queries.md) a Kusto lekérdezési nyelvével.