---
title: Esemény-adatok áttelepítése az SQL Data Warehouse – Azure Event Hubs |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan rögzíthet adatokat az eseményközpontból az SQL Data Warehouse-ba egy eseményrács által meghívott Azure-függvénnyel.
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.custom: seodec18
ms.date: 12/06/2018
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 03ebdabf60882a73eb15cbd36481068591bbd3bc
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086283"
---
# <a name="migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Rögzített az Event Hubs-adatok áttelepítése SQL Data Warehouse Event Grid és az Azure Functions használatával

Az Event Hubs [Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) a legegyszerűbb megoldás a streamelt Event Hubs-adatok Azure Blob Storage- vagy Azure Data Lake Store-fiókba történő automatikus továbbítására. Később feldolgozhatja és kézbesítheti az adatokat bármely más választott tárolási célhelyre, például az SQL Data Warehouse vagy Cosmos DB tárhelyekre. Ebben az oktatóanyagban megtudhatja, hogyan rögzíthet adatokat az eseményközpontból az SQL Data Warehouse-ba egy [eseményrács](https://docs.microsoft.com/azure/event-grid/overview) által meghívott Azure-függvénnyel.

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   Először hozzon létre egy eseményközpontot, engedélyezze a **Capture** funkciót, és állítsa be az Azure Blob Storage tárolót célként. A WindTurbineGeneratorral létrehozott adatok az eseményközpontba lesznek streamelve, majd a rendszer automatikusan Avro-fájlokként rögzíti őket az Azure Storage-ban. 
*   Ezután hozzon létre egy Azure Event Grid-előfizetést, ennek az Event Hubs-névtér legyen a forrása és az Azure Functions végpont legyen a célja.
*   Amikor az Event Hubs Capture funkcióján keresztül egy új Avro-fájl érkezik az Azure Storage-blobba, az Event Grid a blob URI-jével értesíti az Azure Functionst. Az Azure Functions ezután migrálja az adatot a blobból egy SQL Data Warehouse-ba.

Az oktatóanyag során a következő lépéseket hajtja végre: 

> [!div class="checklist"]
> * Az infrastruktúra üzembe helyezése
> * Kód közzététele egy Functions-alkalmazásra
> * Event Grid-előfizetés létrehozása a Functions-alkalmazásból
> * Mintaadatok streamelése az Event Hubsba. 
> * Rögzített adatok ellenőrzése az SQL Data Warehouse-ban

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017 szoftver 15.3.2-es vagy újabb verziója](https://www.visualstudio.com/vs/). Telepítés közben győződjön meg arról, hogy a következő számítási feladatokat is telepíti: .NET asztali fejlesztés, Azure-fejlesztés, ASP.NET- és webfejlesztés, Node.js-fejlesztés és Python-fejlesztés
- Töltse le a [Git-mintát](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo). A mintamegoldás az alábbi összetevőket tartalmazza:
    - *WindTurbineDataGenerator* – Egy egyszerű közzétevő, amely szélturbina-mintaadatokat küld egy olyan eseményközpontnak, amelyen a Capture engedélyezve van
    - *FunctionDWDumper* – Egy Azure-függvény, amely Event Grid-értesítést kap, ha az Azure Storage blobba egy Avro-fájlt rögzít a rendszer. Megkapja a blob URI elérési útvonalát, kiolvassa a tartalmát és közzéteszi ezt az adatot egy SQL Data Warehouse-ba.

### <a name="deploy-the-infrastructure"></a>Az infrastruktúra üzembe helyezése
Az Azure PowerShell vagy Azure CLI használatával helyezheti üzembe az oktatóanyag elvégzéséhez szükséges infrastruktúrát ennek az [Azure Resource Manager-sablonnak](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json) a segítségével. Ez a sablon a következő erőforrásokat hozza létre:

-   Eseményközpont engedélyezett Capture szolgáltatással
-   Tárfiók a rögzített eseményadatokhoz
-   Azure App Service-csomag a Functions alkalmazás üzemeltetéséhez
-   Függvényalkalmazás a rögzített eseményfájlok feldolgozásához
-   SQL Server a Data Warehouse üzemeltetéséhez
-   SQL Data Warehouse a migrált adatok tárolásához

A következő szakaszok szolgáltatják az Azure CLI és Azure PowerShell parancsokat az oktatóanyaghoz szükséges infrastruktúra üzembe helyezéséhez. Frissítse a következő objektumok neveit a parancsok futtatása előtt: 

- Azure-erőforráscsoport 
- Az erőforráscsoport régiója
- Event Hubs-névtér
- Eseményközpont
- Azure SQL-kiszolgáló
- SQL-felhasználó (és jelszó)
- Azure SQL-adatbázis
- Azure Storage 
- Azure Functions-alkalmazás

Hosszabb időt vehet igénybe, amíg a szkriptek létrehozzák az összes Azure-összetevőt. Csak akkor lépjen tovább, ha a szkript futása már befejeződött. Ha az üzembe helyezés valamilyen okból sikertelen, törölje az erőforráscsoportot, hárítsa el a jelentett hibát, és futtassa újra a parancsot. 

#### <a name="azure-cli"></a>Azure CLI
A sablon Azure parancssori felülettel történő üzembe helyezéséhez használja a következő parancsokat:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
A sablon PowerShell-lel történő üzembe helyezéséhez használja a következő parancsokat:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>Tábla létrehozása az SQL Data Warehouse-ban 
Ahhoz, hogy létrehozzon egy táblát az SQL Data Warehouse-ban, futtassa a [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) szkriptet a [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), az [SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-ssms.md) vagy a Portálon a Lekérdezésszerkesztő használatával. 

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

## <a name="publish-code-to-the-functions-app"></a>Kód közzététele a Functions-alkalmazásba

1. Nyissa meg az *EventHubsCaptureEventGridDemo.sln* megoldást a Visual Studio 2017-ben (15.3.2-es vagy újabb verzió). 

1. A Megoldáskezelőben kattintson a jobb gombbal a *FunctionEGDWDumper* elemre, majd válassza a **Közzététel** lehetőséget.

   ![Függvényalkalmazás közzététele](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Válassza az **Azure-függvényalkalmazás**, majd a **Meglévő kiválasztása** lehetőséget. Kattintson a **Publish** (Közzététel) elemre.

   ![Cél függvényalkalmazás](./media/store-captured-data-data-warehouse/pick-target.png)

1. Válassza ki a sablonnal üzembe helyezett függvényalkalmazást. Kattintson az **OK** gombra.

   ![Függvényalkalmazás kiválasztása](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Miután a Visual Studio konfigurálta a profilt, kattintson a **Közzététel** elemre.

   ![A Közzététel gomb kiválasztása](./media/store-captured-data-data-warehouse/select-publish.png)

A függvény közzététele után feliratkozhat a rögzítés eseményre az Event Hubsból!


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Event Grid-előfizetés létrehozása a Functions-alkalmazásból
 
1. Nyissa meg az [Azure Portal](https://portal.azure.com/). Válassza ki az erőforráscsoportot és a függvényalkalmazást.

   ![Függvényalkalmazás megtekintése](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Válassza ki a függvényt.

   ![Függvény kiválasztása](./media/store-captured-data-data-warehouse/select-function.png)

1. Válassza az **Event Grid-előfizetés hozzáadása** lehetőséget.

   ![Előfizetés hozzáadása](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Adja meg az Event Grid-előfizetés nevét. Eseménytípusként használja az **Event Hubs-névterek** típust. Adja meg az értékeket az Event Hubs-névtér példányának kiválasztásához. A feliratkozó végpontjánál hagyja meg a megadott értéket. Kattintson a **Létrehozás** gombra.

   ![Előfizetés létrehozása](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Mintaadatok létrehozása  
Beállította az eseményközpontot, az SQL-adattárházat, az Azure-függvényalkalmazást és az Event Grid-előfizetést. Miután frissítette a kapcsolati sztringet és az eseményközpont nevét a forráskódban, futtassa a WindTurbineDataGenerator.exe programot, hogy adatstreameket hozzon létre az eseményközpontba. 

1. A portálon válassza ki az eseményközpont névterét. Válassza a **Kapcsolati sztringek** lehetőséget.

   ![A Kapcsolati sztringek lehetőség kiválasztása](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Válassza a **RootManageSharedAccessKey** elemet.

   ![Kulcs kiválasztása](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Másolja a **kapcsolati sztring elsődleges kulcsát**

   ![Kulcs másolása](./media/store-captured-data-data-warehouse/copy-key.png)

4. Térjen vissza a Visual Studio-projekthez. A *WindTurbineDataGenerator* projektben nyissa meg a *program.cs* fájlt.

5. Frissítse az **EventHubConnectionString** és az **EventHubName** értékeit a kapcsolati sztringre és az eseményközpont nevére. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Állítsa össze a megoldást, majd futtassa a WindTurbineGenerator.exe alkalmazást. 

## <a name="verify-captured-data-in-data-warehouse"></a>A rögzített adatok ellenőrzése az adattárházban
Néhány perc elteltével kérdezze le az SQL adattárház tábláját. Megfigyelheti, hogy a rendszer a WindTurbineDataGenerator által létrehozott adatokat az Event Hubra streamelte, rögzítette egy Azure Storage-tárolóban, majd migrálta az Azure-függvény SQL Data Warehouse táblájába.  

## <a name="next-steps"></a>További lépések 
Ha gyakorlatban is használható elemzésekre vágyik, használjon hatékony adatvizualizációs eszközöket az adattárházával.

Ez a cikk bemutatja a [Power BI és SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi) használatát



