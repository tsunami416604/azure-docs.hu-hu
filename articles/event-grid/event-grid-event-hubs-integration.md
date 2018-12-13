---
title: Küldés az Event Hubs-adatok a data warehouse – Event Grid
description: Ismerteti, hogyan lehet az Azure Event Grid és az Event Hubs használatával telepítse át az adatokat egy SQL Data Warehouse. Azure-függvény használatával kérje le a rögzítési fájlt.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 08/22/2018
ms.author: tomfitz
ms.openlocfilehash: 0b77d0cc32464fe8b7ac28f491f2cb23b0790ba7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097607"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Oktatóanyag: Stream big Data típusú adatok egy data warehouse-bA

Az Azure [Event Grid](overview.md) egy intelligens esemény-útválasztó szolgáltatás, amellyel reagálhat az alkalmazásokból és szolgáltatásokból érkező értesítésekre. Például aktiválhat egy Azure Functions-függvényt, amely egy Azure Blob Storage-ben vagy Data Lake Store-ban rögzített Event Hubs-adatokat dolgoz fel, majd migrálja az adatokat más adattárakba. Ez az [Event Hubs Capture- és Event Grid-minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) bemutatja, hogy az Event Hubs Capture és az Event Grid használatával hogyan lehet zökkenőmentesen migrálni az Event Hubs-adatokat a Blob Storage-ből egy SQL Data Warehouse-adattárházba.

![Az alkalmazás áttekintése](media/event-grid-event-hubs-integration/overview.png)

A Capture lehívja az adatokat az eseményközpontba érkező adatstreamből, és Avro adatformátumot használó tárolóblobokat hoz létre belőlük. Amikor a Capture létrehozza a blobot, ezzel kivált egy eseményt. Az Event Grid elküldi az előfizetőknek az eseménnyel kapcsolatos adatokat. Esetünkben az eseményadatok az Azure Functions-végpontra lesznek küldve. Az eseményadatok tartalmazzák a létrehozott blob elérési útját. A függvény lekéri a fájlt ezzel az URL-címmel, majd elküldi az adattárházba.

Ebben a cikkben:

* Üzembe helyezzük a következő infrastruktúrát:
  * Eseményközpont aktív Capture szolgáltatással
  * Tárfiók a Capture által küldött fájlokhoz
  * Azure App Service-csomag a függvényalkalmazás futtatásához
  * Függvényalkalmazás az esemény feldolgozásához
  * SQL Server az adattárház üzemeltetéséhez
  * SQL Data Warehouse a migrált adatok tárolásához
* Tábla létrehozása az adattárházban
* Kód hozzáadása a függvényalkalmazáshoz
* Feliratkozás az eseményre
* Az adatokat az eseményközpontra küldő alkalmazás futtatása
* A migrált adatok megtekintése az adattárházban

## <a name="about-the-event-data"></a>Tudnivalók az eseményadatokról

Az Event Grid elküldi az eseményadatokat az előfizetőknek. Az alábbi példában eseményadatok láthatók egy Capture-fájl létrehozásához. Figyelje meg különösen a `fileUrl` tulajdonságot a `data` objektumban. A függvényalkalmazás megkapja ezt az értéket, és a használatával lekéri a Capture-fájlt.

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

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* [Visual Studio 2017 15.3.2-es vagy újabb verziója](https://www.visualstudio.com/vs/) a következőkhöz készült számítási feladatokkal: .NET asztali fejlesztés, Azure-fejlesztés, ASP.NET- és webfejlesztés, Node.js-fejlesztés és Python-fejlesztés.
* Kattintson a számítógépre letöltött [EventHubsCaptureEventGridDemo mintaprojektre](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

## <a name="deploy-the-infrastructure"></a>Az infrastruktúra üzembe helyezése

Ebben a cikkben az egyszerűség kedvéért egy Resource Manager-sablonnal helyezzük üzembe a szükséges infrastruktúrát. Az üzembe helyezett erőforrások megtekintéséhez lásd a [sablont](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json).

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

Amikor a rendszer kéri, adjon meg egy jelszót.

## <a name="create-a-table-in-sql-data-warehouse"></a>Tábla létrehozása az SQL Data Warehouse-ban

Az adattárházhoz a [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) szkript futtatásával adhat hozzá egy új táblát. A szkriptet a Visual Studio vagy a portálon a Lekérdezésszerkesztő használatával futtathatja.

A futtatandó szkript:

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

## <a name="publish-the-azure-functions-app"></a>Az Azure Functions-alkalmazás közzététele

1. Nyissa meg az [EventHubsCaptureEventGridDemo mintaprojektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) a Visual Studio 2017-ben (15.3.2-es vagy újabb verzió).

1. A Megoldáskezelőben kattintson a jobb gombbal a **FunctionEGDWDumper** elemre, majd válassza a **Közzététel** lehetőséget.

   ![Függvényalkalmazás közzététele](media/event-grid-event-hubs-integration/publish-function-app.png)

1. Válassza az **Azure-függvényalkalmazás**, majd a **Meglévő kiválasztása** lehetőséget. Kattintson a **Publish** (Közzététel) elemre.

   ![Cél függvényalkalmazás](media/event-grid-event-hubs-integration/pick-target.png)

1. Válassza ki a sablonnal üzembe helyezett függvényalkalmazást. Kattintson az **OK** gombra.

   ![Függvényalkalmazás kiválasztása](media/event-grid-event-hubs-integration/select-function-app.png)

1. Miután a Visual Studio konfigurálta a profilt, kattintson a **Közzététel** elemre.

   ![A Közzététel gomb kiválasztása](media/event-grid-event-hubs-integration/select-publish.png)

A függvény közzététele után feliratkozhat az eseményre.

## <a name="subscribe-to-the-event"></a>Feliratkozás az eseményre

1. Nyissa meg az [Azure Portal](https://portal.azure.com/). Válassza ki az erőforráscsoportot és a függvényalkalmazást.

   ![Függvényalkalmazás megtekintése](media/event-grid-event-hubs-integration/view-function-app.png)

1. Válassza ki a függvényt.

   ![Függvény kiválasztása](media/event-grid-event-hubs-integration/select-function.png)

1. Válassza az **Event Grid-előfizetés hozzáadása** lehetőséget.

   ![Előfizetés hozzáadása](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Adja meg az Event Grid-előfizetés nevét. Eseménytípusként használja az **Event Hubs-névterek** típust. Adja meg az értékeket az Event Hubs-névtér példányának kiválasztásához. A feliratkozó végpontjánál hagyja meg a megadott értéket. Kattintson a **Létrehozás** gombra.

   ![Előfizetés létrehozása](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>Az alkalmazás futtatása az adatok létrehozásához

Végeztünk az eseményközpont, az SQL-adattárház, az Azure-függvényalkalmazás és az esemény-előfizetés beállításával. A megoldás készen áll az eseményközpontból az adattárházba való adatmigrálásra. Mielőtt futtatna egy alkalmazást, amely adatokat állít elő az eseményközpont számára, konfigurálnia kell néhány értéket.

1. A portálon válassza ki az eseményközpont névterét. Válassza a **Kapcsolati sztringek** lehetőséget.

   ![A Kapcsolati sztringek lehetőség kiválasztása](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Válassza a **RootManageSharedAccessKey** elemet.

   ![Kulcs kiválasztása](media/event-grid-event-hubs-integration/show-root-key.png)

3. Másolja a **kapcsolati sztring elsődleges kulcsát**

   ![Kulcs másolása](media/event-grid-event-hubs-integration/copy-key.png)

4. Térjen vissza a Visual Studio-projekthez. A WindTurbineDataGenerator projektben nyissa meg a **program.cs** fájlt.

5. Cserélje le a két állandó értékét. Az **EventHubConnectionString** állandónál használja a másolt értéket. Az eseményközpont neveként használja a **hubdatamigration** értéket.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Hozza létre a megoldást. Futtassa a WindTurbineGenerator.exe alkalmazást. Néhány perc elteltével kérdezze le a migrált adatokat az adattárház táblájából.

## <a name="next-steps"></a>További lépések

* Az Azure üzenetkezelési szolgáltatások különbségeiről [az üzenetkézbesítő Azure-szolgáltatás kiválasztásának ismertetésében](compare-messaging-services.md) olvashat.
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Az Event Hubs Capture megismeréséhez tekintse meg [az Event Hubs Capture Azure Portallal való engedélyezését](../event-hubs/event-hubs-capture-enable-through-portal.md) bemutató cikket.
* A minta beállításával és futtatásával kapcsolatos további információért lásd az [Event Hubs Capture- és Event Grid-mintát](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).