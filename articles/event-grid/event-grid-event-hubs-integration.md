---
title: Küldés az Event Hubs-adatok a data warehouse – Event Grid
description: Ismerteti, hogyan lehet az Azure Event Grid és az Event Hubs használatával telepítse át az adatokat egy SQL Data Warehouse. Azure-függvény használatával kérje le a rögzítési fájlt.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 01/13/2019
ms.author: spelluru
ms.openlocfilehash: c2c49563bf505ce70c4900c6c0a8e41c0f6ac9c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176616"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Oktatóanyag: Big Data típusú adatok streamelése adattárházba
Azure [Event Grid](overview.md) egy intelligens esemény-útválasztó szolgáltatás, amely lehetővé teszi értesítések (események) reagálni az alkalmazások és szolgáltatások. Például képes aktiválhat egy Azure-függvény, amely egy Azure Blob storage vagy az Azure Data Lake Storage rögzítésének befejeztével az Event Hubs-adatok feldolgozásához, és egyéb az adattárak az adatok áttelepítését. Ez [az Event Hubs és az Event Grid integráció minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) bemutatja, hogyan használatával az Event Hubs az Event Griddel áttelepíthetik rögzített az Event Hubs-adatok a blob storage-ból egy SQL Data Warehouse.

![Az alkalmazás áttekintése](media/event-grid-event-hubs-integration/overview.png)

Ez az ábra a megoldás hoz létre, ebben az oktatóanyagban a munkafolyamat ábrázolja: 

1. Azure event hub küldött adatok rögzítése egy Azure blob Storage.
2. Az adatváltozások rögzítése befejeződése után az esemény van és elküldhet az Azure event griddel. 
3. Az event grid továbbítja az Azure-függvényalkalmazás eseményadatokat.
4. A függvényalkalmazás az eseményadatokat a blob lekérése a storage a blob URL-címet használ. 
5. A függvényalkalmazás áttelepíti a blob-adatokat egy Azure SQL data warehouse-bA. 

Ebben a cikkben hajtsa végre a következő lépéseket:

> [!div class="checklist"]
> * Az infrastruktúra üzembe helyezése az Azure Resource Manager-sablon használatával: egy eseményközpontba, tárfiók, függvényalkalmazás, egy SQL data warehouse-bA.
> * Hozzon létre egy táblát az adatraktárban.
> * Adja hozzá a kódot a függvényalkalmazáshoz.
> * Feliratkozás az esemény. 
> * Futtassa az alkalmazást, amely adatokat küld az event hubs.
> * Áttelepített adatok megtekintése a data warehouse-ban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Visual Studio 2017 15.3.2-es vagy újabb verziója](https://www.visualstudio.com/vs/) a következőkhöz készült számítási feladatokkal: .NET asztali fejlesztés, Azure-fejlesztés, ASP.NET- és webfejlesztés, Node.js-fejlesztés és Python-fejlesztés.
* Töltse le a [EventHubsCaptureEventGridDemo mintaprojektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) a számítógépre.

## <a name="deploy-the-infrastructure"></a>Az infrastruktúra üzembe helyezése
Ebben a lépésben üzembe helyezi a szükséges infrastruktúra egy [Resource Manager-sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Ha a sablon üzembe helyezéséhez a következő erőforrások jönnek létre:

* Event hubs rögzítési funkciója engedélyezve a.
* Storage-fiók a rögzített fájlok számára. 
* App service-csomagot a függvényalkalmazás üzemeltetéséhez
* Függvényalkalmazás az esemény feldolgozásához
* SQL Server az adattárház üzemeltetéséhez
* SQL Data Warehouse a migrált adatok tárolásához

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Az Azure Portalon az Azure Cloud Shell indítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Válassza ki **Cloud Shell** gombra az oldal tetején.

    ![Azure Portal](media/event-grid-event-hubs-integration/azure-portal.png)
3. A Cloud Shellben megnyitása a böngészőben alján megjelenik.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Ha megjelenik egy lehetőség, hogy a Cloud shellben **Bash** és **PowerShell**válassza **Bash**. 
5. Ha a Cloud Shell az első alkalommal használ, hozzon létre egy tárfiókot kiválasztásával **tároló létrehozása**. Az Azure Cloud Shell egy Azure storage-fiók egyes fájlok tárolásához szükséges. 

    ![Cloud Shell tároló létrehozása](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Várjon, amíg a Cloud Shellben inicializálva van. 

    ![Cloud Shell tároló létrehozása](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

1. Hozzon létre egy Azure-erőforráscsoportot a következő CLI-parancs futtatásával: 
    1. Másolja és illessze be az alábbi parancsot a Cloud Shell-ablakról

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Adjon meg egy nevet a **erőforráscsoport**
    2. Nyomja le az **ENTER** billentyűt. 

        Például:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. A következő CLI-parancs futtatásával (event hubs, tárfiók, functions-alkalmazás, az SQL data warehouse) az előző szakaszban említett összes erőforrást üzembe: 
    1. Másolja és illessze be a parancsot a Cloud Shell ablakába. Azt is megteheti érdemes másolja és illessze be azokat egy tetszőleges szövegszerkesztőben, beállítása és másolja a parancsot a Cloud Shellben. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Adja meg az értékeket a következő entitásokat:
        1. A korábban létrehozott erőforráscsoport nevét.
        2. Az eseményközpont-névtér nevét. 
        3. Az eseményközpont neve. Az érték (hubdatamigration), mert hagyhatja.
        4. Az SQL server neve.
        5. Az SQL-felhasználó és a jelszó neve. 
        6. Az SQL data warehouse neve
        7. A tárfiók nevére. 
        8. A függvényalkalmazás neve. 
    3.  Nyomja meg **ENTER** a Cloud Shell-ablakban futtassa a parancsot. Ez a folyamat eltarthat egy ideig, mivel sok erőforrást hoz létre. A parancs eredménye győződjön meg arról, hogy nem történt nincsenek hibák. 
    

### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Az Azure Cloud Shellben váltson át PowerShell módba. Válassza ki a lefelé mutató nyíl az Azure Cloud Shell a bal felső sarkában, és válassza ki **PowerShell**.

    ![Váltson át PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Hozzon létre egy Azure-erőforráscsoportot a következő parancs futtatásával: 
    1. Másolja és illessze be az alábbi parancsot a Cloud Shell ablakába.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Adjon meg egy nevet a **erőforráscsoport**.
    3. Nyomja le az ENTER billentyűt. 
3. A következő parancs futtatásával (event hubs, tárfiók, functions-alkalmazás, az SQL data warehouse) az előző szakaszban említett összes erőforrást üzembe:
    1. Másolja és illessze be a parancsot a Cloud Shell ablakába. Azt is megteheti érdemes másolja és illessze be azokat egy tetszőleges szövegszerkesztőben, beállítása és másolja a parancsot a Cloud Shellben. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Adja meg az értékeket a következő entitásokat:
        1. A korábban létrehozott erőforráscsoport nevét.
        2. Az eseményközpont-névtér nevét. 
        3. Az eseményközpont neve. Az érték (hubdatamigration), mert hagyhatja.
        4. Az SQL server neve.
        5. Az SQL-felhasználó és a jelszó neve. 
        6. Az SQL data warehouse neve
        7. A tárfiók nevére. 
        8. A függvényalkalmazás neve. 
    3.  Nyomja meg **ENTER** a Cloud Shell-ablakban futtassa a parancsot. Ez a folyamat eltarthat egy ideig, mivel sok erőforrást hoz létre. A parancs eredménye győződjön meg arról, hogy nem történt nincsenek hibák. 

### <a name="close-the-cloud-shell"></a>Zárja be a Cloud Shellben 
Zárja be a cloud shellben kiválasztásával a **Cloud Shell** a portál gombjára (vagy) **X** gombra a Cloud Shell-ablakról jobb felső sarkában. 

### <a name="verify-that-the-resources-are-created"></a>Győződjön meg arról, hogy az erőforrások létrejönnek

1. Az Azure Portalon válassza ki a **erőforráscsoportok** a bal oldali menüben. 
2. Erőforráscsoportok listájának szűrése kifejezést a keresőmezőbe írja be az erőforráscsoport nevét. 
3. Válassza ki az erőforráscsoportot a listáról.

    ![Jelölje ki az erőforráscsoportot](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Győződjön meg arról, hogy megjelenik-e a következő erőforrások az erőforráscsoportban:

    ![Az erőforráscsoportban lévő erőforrásokat](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Tábla létrehozása az SQL Data Warehouse-ban
Hozzon létre egy táblát az adattárház futtatásával a [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) parancsfájlt. A parancsfájl futtatásához használhatja a Visual Studio vagy a Lekérdezésszerkesztő a portálon. A következő lépések bemutatják, hogyan használható a Lekérdezésszerkesztő: 

1. Az erőforráscsoportban lévő erőforrásokat listájában válassza ki az SQL data warehouse. 
2. Válassza ki az SQL data warehouse lapon **Lekérdezésszerkesztő (előzetes verzió)** a bal oldali menüben. 

    ![Az SQL data warehouse lapon](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Adja meg a nevét **felhasználói** és **jelszó** az SQL server, és válassza ki a **OK**. 

    ![SQL Server-hitelesítés](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. A lekérdezési ablakban másolja, és futtassa a következő SQL-parancsfájlt: 

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

    ![SQL-lekérdezés futtatásához](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Ne zárja be a lapon vagy ablakban így ellenőrizheti, hogy az adatok létrejött-e az oktatóanyag végén. 


## <a name="publish-the-azure-functions-app"></a>Az Azure Functions-alkalmazás közzététele

1. Indítsa el a Visual Studio 2017-ben. 
2. Nyissa meg a **EventHubsCaptureEventGridDemo.sln** megoldás, amelyikbe kibontotta a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) az Előfeltételek részeként.
3. A Megoldáskezelőben kattintson a jobb gombbal a **FunctionEGDWDumper** elemre, majd válassza a **Közzététel** lehetőséget.

   ![Függvényalkalmazás közzététele](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Ha a következő képernyőt látja, válassza ki a **Start**. 

   ![Kezdő közzététel gomb](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. Az a **válasszon egy közzétételi célként** lapon válassza ki a **válasszon meglévő** lehetőséget, majd kattintson **profil létrehozása**. 

   ![Közzétételi cél kiválasztása](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Az App Service-ben oldalán válassza ki a **Azure-előfizetés**, jelölje be a **függvényalkalmazás** az erőforráscsoportot, és válassza ki a **OK**. 

   ![Az App Service lap](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Miután a Visual Studio konfigurálta a profilt, kattintson a **Közzététel** elemre.

   ![A Közzététel gomb kiválasztása](media/event-grid-event-hubs-integration/select-publish.png)

A függvény közzététele után feliratkozhat az eseményre.

## <a name="subscribe-to-the-event"></a>Feliratkozás az eseményre

1. Új lap vagy egy webes böngésző új ablakban, keresse meg a [az Azure portal](https://portal.azure.com).
2. Az Azure Portalon válassza ki a **erőforráscsoportok** a bal oldali menüben. 
3. Erőforráscsoportok listájának szűrése kifejezést a keresőmezőbe írja be az erőforráscsoport nevét. 
4. Válassza ki az erőforráscsoportot a listáról.

    ![Jelölje ki az erőforráscsoportot](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Válassza ki az App Service-csomagot a listából. 
5. Az App Service-csomag lapon válasszon **alkalmazások** a bal oldali menüben, és válassza ki a függvényalkalmazást. 

    ![A functions-alkalmazás kiválasztása](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Bontsa ki a függvényalkalmazást, bontsa ki a Funkciók, és válassza ki a függvényt. 

    ![Az Azure-függvény kiválasztása](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Válassza ki **hozzáadása Event Grid-előfizetés** az eszköztáron. 
8. Az a **Event Grid-előfizetés létrehozása** lapon, tegye a következőket: 
    1. Az a **TÉMAKÖR RÉSZLETESEN** területén tegye a következőket:
        1. Válassza ki az Azure-előfizetését.
        2. Válassza ki az Azure-erőforráscsoportot.
        3. Válassza ki az Event Hubs-névtér.
    2. Az a **esemény-ELŐFIZETÉS RÉSZLETEIT** lap, adja meg az előfizetés nevét (például: captureEventSub), és válassza ki **létrehozás**. 

        ![Event Grid-előfizetés létrehozása](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Az alkalmazás futtatása az adatok létrehozásához
Végeztünk az eseményközpont, az SQL-adattárház, az Azure-függvényalkalmazás és az esemény-előfizetés beállításával. Mielőtt futtatna egy alkalmazást, amely adatokat állít elő az eseményközpont számára, konfigurálnia kell néhány értéket.

1. Az Azure Portalon keresse meg az erőforráscsoportot, a korábban végrehajtott. 
2. Válassza ki az Event Hubs-névtér.
3. Az a **Event Hubs-Namespace** lapon jelölje be **megosztott elérési házirendek** a bal oldali menüben.
4. Válassza ki **RootManageSharedAccessKey** szabályzatok listájában. 
5. Válassza ki a másolási gomb mellett a **kapcsolati karakterlánc – elsődleges kulcs** szövegmezőben. 

    ![Az eseményközpont-névtér kapcsolati karakterlánca](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Lépjen vissza a Visual Studio-megoldás. 
2. A WindTurbineDataGenerator projektben nyissa meg a **program.cs** fájlt.
5. Cserélje le a két állandó értékét. Az **EventHubConnectionString** állandónál használja a másolt értéket. Az eseményközpont neveként használja a **hubdatamigration** értéket. Ha az event hubs egy másik nevet használt, adja meg ezt a nevet. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Hozza létre a megoldást. Futtassa a **WindTurbineGenerator.exe** alkalmazás. 
7. Néhány perc elteltével kérdezze le a migrált adatokat az adattárház táblájából.

    ![Lekérdezés eredményei](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Az event hub által generált események adatainak
Az Event Grid elküldi az eseményadatokat az előfizetőknek. A következő példában az eseményadatok jönnek létre, ha egy blob egy eseményközponton keresztül adatstreamelési van rögzítve. Figyelje meg, ilyen például a `fileUrl` tulajdonságot a `data` objektumra mutat a blob Storage. A függvényalkalmazás az URL-cím használatával kérje le a rögzített adatok a blob-fájlt.

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


## <a name="next-steps"></a>További lépések

* Az Azure üzenetkezelési szolgáltatások különbségeiről [az üzenetkézbesítő Azure-szolgáltatás kiválasztásának ismertetésében](compare-messaging-services.md) olvashat.
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Az Event Hubs Capture megismeréséhez tekintse meg [az Event Hubs Capture Azure Portallal való engedélyezését](../event-hubs/event-hubs-capture-enable-through-portal.md) bemutató cikket.
* A minta beállításával és futtatásával kapcsolatos további információért lásd az [Event Hubs Capture- és Event Grid-mintát](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
