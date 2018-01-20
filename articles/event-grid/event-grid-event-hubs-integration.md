---
title: "Azure Event rács és az Event Hubs-integráció"
description: "Ismerteti, hogyan lehet Azure esemény rács és az Event Hubs használatával telepítse át az adatokat egy SQL Data Warehouse"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: b315bd77a47a6f106c5768da56828a5169de5fe9
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Az adatfolyam big Data típusú adatok az data warehouse-bA

Azure [esemény rács](overview.md) intelligens esemény útválasztási szolgáltatás, amely lehetővé teszi az alkalmazások és szolgáltatások értesítések reagálni azokra. A [Event Hubs rögzítése és az esemény rács minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) használata Azure Event Hubs rögzítése Azure esemény rács zökkenőmentesen át adatokat az event hubs egy SQL Data Warehouse jeleníti meg.

![Alkalmazás – áttekintés](media/event-grid-event-hubs-integration/overview.png)

Az event hubs adatokat küld, rögzítési kéri le az adatokat az adatfolyamból és az Avro formátum hoz létre az adatok tárolási BLOB. Rögzítés a blob állít elő, amikor egy eseményt váltja ki. Esemény rács elosztása az előfizetők az eseményről találhat adatokat. Az eseményadatok ebben az esetben az Azure Functions végpont küldi. Az eseményadat tartalmazza a létrehozott blob elérési útját. A függvény kérje le a fájlt, és küldje el az adatraktár URL-címet használja.

Ebben a cikkben meg:

* A következő infrastruktúra központi telepítéséhez:
  * Az Event hubs a rögzítés engedélyezve
  * A fájlok rögzítése a Storage-fiók
  * A függvény alkalmazás tárolására szolgáló az Azure app service-csomag
  * Az esemény feldolgozása függvény-alkalmazás
  * Az adatraktárt futtató SQL Server
  * Az SQL Data Warehouse az áttelepített adatok tárolásához
* Táblázat létrehozása az adatraktárban
* Adja hozzá a kódot a függvény alkalmazásba
* Az esemény előfizetés
* Futtassa az alkalmazást, amely adatokat küld az event hubs
* Adatraktár áttelepített adatok megtekintése

## <a name="about-the-event-data"></a>Az adatok körül forog esemény

Esemény rács elosztása az eseményadatok az előfizetőknek. A következő példa bemutatja eseményadatok a rögzítés fájl létrehozása. Figyelje meg, a `fileUrl` tulajdonságot a `data` objektum. A függvény app megkapja ezt az értéket, és kérje le a rögzítést fájlt használja.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez kell rendelkeznie:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* [A Visual studio 15.3.2 2017 verzió vagy újabb](https://www.visualstudio.com/vs/) a munkaterhelések: .NET asztali fejlesztési, Azure fejlesztési, az ASP.NET és a webes fejlesztési, Node.js fejlesztői és Python fejlesztői.
* A [EventHubsCaptureEventGridDemo mintaprojektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) a számítógép letölti.

## <a name="deploy-the-infrastructure"></a>Az infrastruktúra központi telepítéséhez

Ez a cikk leegyszerűsítése telepít egy Resource Manager sablonhoz szükséges infrastruktúrát. Tekintse meg a telepített erőforrások esetén, tekintse meg a [sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Az előzetes kiadásban esemény rács támogatja **westus2** és **westcentralus** régiók. Ezzel a régiók egyikéhez sem az erőforráscsoport helye.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Adjon meg egy jelszót értéket, amikor a rendszer kéri.

## <a name="create-a-table-in-sql-data-warehouse"></a>Létrehoz egy táblát az SQL Data Warehouse

Az adatraktár futtatásával táblázat felvétele a [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) parancsfájl. A parancsfájl futtatásához használja a Visual Studio vagy a lekérdezés-szerkesztő a portálon.

A parancsfájl futtatásához a következő:

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>Az Azure Functions alkalmazás közzététele

1. Nyissa meg a [EventHubsCaptureEventGridDemo mintaprojektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) a Visual Studio 2017 (15.3.2 vagy újabb).

2. A Megoldáskezelőben kattintson a jobb gombbal **FunctionDWDumper**, és válassza ki **közzététel**.

   ![Függvény alkalmazás közzététele](media/event-grid-event-hubs-integration/publish-function-app.png)

3. Válassza ki **Azure függvény App** és **válasszon meglévő**. Kattintson az **OK** gombra.

   ![Cél függvény alkalmazás](media/event-grid-event-hubs-integration/pick-target.png)

4. Válassza ki a funkció a sablon segítségével telepített alkalmazást. Kattintson az **OK** gombra.

   ![Függvény alkalmazás kiválasztása](media/event-grid-event-hubs-integration/select-function-app.png)

5. Visual Studio a profil konfigurálásakor válassza ki a **közzététel**.

   ![Válassza ki közzététele](media/event-grid-event-hubs-integration/select-publish.png)

6. Miután közzétette a függvény, navigáljon a [Azure-portálon](https://portal.azure.com/). Válassza ki az erőforrás-csoport és függvény alkalmazást.

   ![Függvény alkalmazás megtekintése](media/event-grid-event-hubs-integration/view-function-app.png)

7. Válassza ki a függvényt.

   ![Függvény kiválasztása](media/event-grid-event-hubs-integration/select-function.png)

8. Az URL-cím beszerzése a függvény. Az esemény-előfizetés létrehozásakor kell az URL-cím.

   ![Függvény URL-címének beolvasása](media/event-grid-event-hubs-integration/get-function-url.png)

9. Másolja az értékét.

   ![URL-Címének másolása](media/event-grid-event-hubs-integration/copy-url.png)

## <a name="subscribe-to-the-event"></a>Az esemény előfizetés

Az Azure parancssori felület vagy a portál segítségével az esemény előfizetni. Ez a cikk mindkét megközelítés jeleníti meg.

### <a name="portal"></a>Portál

1. Válassza ki az Event Hubs névtér **esemény rács** a bal oldalon.

   ![Válassza ki az esemény rács](media/event-grid-event-hubs-integration/select-event-grid.png)

2. Adjon hozzá egy esemény-előfizetést.

   ![Az esemény-előfizetés felvétele](media/event-grid-event-hubs-integration/add-event-subscription.png)

3. Adja meg az esemény-előfizetést. Az Azure Functions URL-cím használata másolt. Kattintson a **Létrehozás** gombra.

   ![Adja meg az előfizetés értékek](media/event-grid-event-hubs-integration/provide-values.png)

### <a name="azure-cli"></a>Azure CLI

Az esemény előfizetni a következő parancsokat (igénylő 2.0.24 verzió vagy újabb, az Azure CLI):

```azurecli-interactive
namespaceid=$(az resource show --namespace Microsoft.EventHub --resource-type namespaces --name <your-EventHubs-namespace> --resource-group rgDataMigrationSample --query id --output tsv)
az eventgrid event-subscription create \
  --resource-id $namespaceid \
  --name captureEventSub \
  --endpoint <your-function-endpoint>
```

## <a name="run-the-app-to-generate-data"></a>Futtassa az alkalmazást létrehozni

Az event hubs, az SQL data warehouse, Azure függvény app és Eseményelőfizetés beállítása befejeződött. A megoldás készen áll a át adatokat az event hubs az adatraktárba. Mielőtt futtatná az olyan alkalmazás, amely az eseményközpont adatokat állít elő, kell néhány értékeket állíthat be.

1. A portálon válassza ki az event hub névtér. Válassza ki **kapcsolati karakterláncok**.

   ![Válassza ki a kapcsolati karakterláncok](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Select **RootManageSharedAccessKey**

   ![Válassza ki a kulcs](media/event-grid-event-hubs-integration/show-root-key.png)

3. Másolás **kapcsolati karakterlánc - elsődleges kulcs**

   ![Kulcs másolása](media/event-grid-event-hubs-integration/copy-key.png)

4. Lépjen vissza, ha a Visual Studio-projektet. A WindTurbineDataGenerator projektben nyissa meg a **program.cs**.

5. Cserélje le a két konstans érték. A másolt értéket **EventHubConnectionString**. Használja az eseményközpont neveként az **EventHubName**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://tfdatamigratens.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. A megoldás felépítéséhez. Futtassa a WindTurbineGenerator.exe alkalmazást. Után néhány perc alatt a táblának az adatraktár az áttelepített adatok lekérdezése.

## <a name="next-steps"></a>További lépések

* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Megismerkedhet az Event Hubs rögzítéséhez, lásd: [engedélyezése Event Hubs rögzítése az Azure portál használatával](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Beállíthatja és futtathatja a minta kapcsolatos további információkért lásd: [Event Hubs rögzítése és az esemény rács minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).