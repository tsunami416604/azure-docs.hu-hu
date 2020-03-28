---
title: 'Oktatóanyag: Eseményközpontok adatainak küldése az adattárházba – Eseményrács'
description: 'Oktatóanyag: Ismerteti, hogyan használhatja az Azure Event Grid és az Event Hubs adatok áttelepítése egy SQL Data Warehouse. Egy Azure-függvényt használ a Capture fájl lekéréséhez.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6f5bd129b175210cd5b9415a65b8db06d904e24d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718183"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Oktatóanyag: Big Data streamelése adattárházba
Az Azure [Event Grid](overview.md) egy intelligens esemény-útválasztási szolgáltatás, amely lehetővé teszi, hogy reagáljon az alkalmazásokból és szolgáltatásokból érkező értesítésekre (eseményekre). Például elindíthatja az Azure-függvényt az Azure Blob storage-ba vagy az Azure Data Lake Storage-ba rögzített Event Hubs-adatok feldolgozásához, és áttelepítheti az adatokat más adattárakba. Ez [az Event Hubs és Event Grid integrációs minta bemutatja,](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) hogyan használhatja az Event Hubs with Event Grid segítségével a rögzített Eseményközpontok adatainak zökkenőmentes áttelepítését a blobtárolóból az SQL Data Warehouse-ba.

![Az alkalmazás áttekintése](media/event-grid-event-hubs-integration/overview.png)

Ez az ábra az oktatóanyagban készített megoldás munkafolyamatát ábrázolja: 

1. Az Azure-eseményközpontba küldött adatok at egy Azure blob storage rögzíti.
2. Amikor az adatrögzítés befejeződött, egy esemény jön létre, és egy Azure-eseményrácsba küldi. 
3. Az eseményrács továbbítja az eseményadatokat egy Azure-függvényalkalmazásba.
4. A függvényalkalmazás a blob URL-címét használja az eseményadatokban a blob lekéréséhez a tárolóból. 
5. A függvényalkalmazás áttelepíti a blob adatokat egy Azure SQL-adattárházba. 

Ebben a cikkben az alábbi lépéseket kell tennie:

> [!div class="checklist"]
> * Azure Resource Manager-sablon használatával az infrastruktúra üzembe helyezéséhez: egy eseményközpont, egy tárfiók, egy függvényalkalmazás, egy SQL-adattárház.
> * Hozzon létre egy táblát az adatraktárban.
> * Adjon hozzá kódot a függvényalkalmazáshoz.
> * Iratkozzon fel az eseményre. 
> * Futtassa az adatokat az eseményközpontba adatokat küldő alkalmazást.
> * Áttelepített adatok megtekintése az adatraktárban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* [Visual Studio 2019](https://www.visualstudio.com/vs/) számítási feladatok: .NET asztali fejlesztés, Azure-fejlesztés, ASP.NET és webfejlesztés, Node.js fejlesztés és Python fejlesztés.
* Töltse le az [EventHubsCaptureEventGridDemo mintaprojektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) a számítógépre.

## <a name="deploy-the-infrastructure"></a>Az infrastruktúra üzembe helyezése
Ebben a lépésben a szükséges infrastruktúrát [erőforrás-kezelő sablonnal](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)telepíti. A sablon telepítésekor a következő erőforrások jönnek létre:

* Eseményközpont, amelyen engedélyezve van a Rögzítés funkció.
* A rögzített fájlok tárfiókja. 
* Alkalmazásszolgáltatási csomag a függvényalkalmazás üzemeltetéséhez
* Függvényalkalmazás az esemény feldolgozásához
* SQL Server az adattárház üzemeltetéséhez
* SQL Data Warehouse a migrált adatok tárolásához

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Az Azure Cloud Shell elindítása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
2. Felül válassza a **Cloud Shell** gombot.

    ![Azure portál](media/event-grid-event-hubs-integration/azure-portal.png)
3. A böngésző alján megnyitott Cloud Shell látható.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Ha a Cloud Shellben megjelenik egy lehetőség a **Bash** és a **PowerShell**közötti választáshoz, válassza a **Bash**lehetőséget. 
5. Ha első alkalommal használja a Cloud Shellt, hozzon létre egy tárfiókot a **Tárház létrehozása**lehetőséget választva. Az Azure Cloud Shell bizonyos fájlok tárolásához Azure-tárfiókra van szükség. 

    ![Tároló létrehozása a felhőalapú rendszerhéj számára](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Várjon, amíg a Cloud Shell inicializálása megtörténik. 

    ![Tároló létrehozása a felhőalapú rendszerhéj számára](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

1. Hozzon létre egy Azure-erőforráscsoportot a következő CLI-parancs futtatásával: 
    1. Másolja a következő parancsot a Cloud Shell ablakba

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Az **erőforráscsoport** nevének megadása
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
2. Az előző szakaszban említett összes erőforrás (eseményközpont, tárfiók, függvényalkalmazás, SQL-adattárház) üzembe helyezése a következő CLI-parancs futtatásával: 
    1. Másolja és illessze be a parancsot a Cloud Shell ablakba. Azt is megteheti, hogy szeretne másolni/ beilleszteni egy ön által választott szerkesztőbe, értékeket beállítani, majd a parancsot a Cloud Shellbe. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Adja meg a következő entitások értékeit:
        1. A korábban létrehozott erőforráscsoport neve.
        2. Az eseményközpont névterének neve. 
        3. Az eseményközpont neve. Az értéket úgy hagyhatja meg, ahogy van (hubdatamigration).
        4. Az SQL-kiszolgáló neve.
        5. Az SQL-felhasználó és a jelszó neve. 
        6. Az SQL adattárház neve
        7. A tárfiók neve. 
        8. A függvényalkalmazás neve. 
    3.  A parancs futtatásához nyomja le az **ENTER** billentyűt a Cloud Shell ablakban. Ez a folyamat eltarthat egy ideig, mivel egy csomó erőforrást hoz létre. A parancs eredményében győződjön meg arról, hogy nem történt hiba. 
    

### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Az Azure Cloud Shellben váltson PowerShell-módra. Válassza a lefelé mutató nyilat az Azure Cloud Shell bal felső sarkában, és válassza a **PowerShell**lehetőséget.

    ![Váltás a PowerShellre](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Hozzon létre egy Azure-erőforráscsoportot a következő parancs futtatásával: 
    1. Másolja és illessze be a következő parancsot a Cloud Shell ablakba.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Adja meg az **erőforráscsoport**nevét.
    3. Nyomja le az Enter billentyűt. 
3. Az előző szakaszban említett összes erőforrás (eseményközpont, tárfiók, függvényalkalmazás, SQL-adattárház) üzembe helyezése a következő parancs futtatásával:
    1. Másolja és illessze be a parancsot a Cloud Shell ablakba. Azt is megteheti, hogy szeretne másolni/ beilleszteni egy ön által választott szerkesztőbe, értékeket beállítani, majd a parancsot a Cloud Shellbe. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Adja meg a következő entitások értékeit:
        1. A korábban létrehozott erőforráscsoport neve.
        2. Az eseményközpont névterének neve. 
        3. Az eseményközpont neve. Az értéket úgy hagyhatja meg, ahogy van (hubdatamigration).
        4. Az SQL-kiszolgáló neve.
        5. Az SQL-felhasználó és a jelszó neve. 
        6. Az SQL adattárház neve
        7. A tárfiók neve. 
        8. A függvényalkalmazás neve. 
    3.  A parancs futtatásához nyomja le az **ENTER** billentyűt a Cloud Shell ablakban. Ez a folyamat eltarthat egy ideig, mivel egy csomó erőforrást hoz létre. A parancs eredményében győződjön meg arról, hogy nem történt hiba. 

### <a name="close-the-cloud-shell"></a>A felhőhéj bezárása 
Zárja be a felhőhéjat a **Cloud Shell** gomb kiválasztásával a portál (vagy) **X** gombbal a Cloud Shell ablakának jobb felső sarkában. 

### <a name="verify-that-the-resources-are-created"></a>Az erőforrások létrehozásának ellenőrzése

1. Az Azure Portalon válassza a bal oldali menü **erőforráscsoportok.** 
2. Az erőforráscsoportok listájának szűréséhez írja be az erőforráscsoport nevét a keresőmezőbe. 
3. Válassza ki az erőforráscsoportot a listában.

    ![Az erőforráscsoport kiválasztása](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Ellenőrizze, hogy az erőforráscsoportban a következő erőforrások jelennek-e meg:

    ![Erőforrások az erőforráscsoportban](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Tábla létrehozása az SQL Data Warehouse-ban
Hozzon létre egy táblát az adatraktárban a [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) parancsfájl futtatásával. A parancsfájl futtatásához használhatja a Visual Studio vagy a Lekérdezésszerkesztő a portálon. Az alábbi lépések bemutatják a Lekérdezésszerkesztő használatát: 

1. Az erőforráscsoport erőforrásainak listájában válassza ki az SQL-adatraktárt. 
2. Az SQL adattárház lapon válassza a bal oldali menü **Lekérdezésszerkesztő (előnézet)** parancsát. 

    ![SQL adattárház lap](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Írja be az SQL-kiszolgáló **felhasználójának** és **jelszavának** nevét, majd kattintson az **OK gombra.** 

    ![SQL Server-hitelesítés](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. A lekérdezésablakban másolja és futtassa a következő SQL-parancsfájlt: 

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

    ![SQL-lekérdezés futtatása](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Tartsa nyitva ezt a lapot vagy ablakot, hogy ellenőrizhesse, hogy az adatok az oktatóanyag végén jönnek-e létre. 


## <a name="publish-the-azure-functions-app"></a>Az Azure Functions-alkalmazás közzététele

1. Indítsa el a Visual Studiót.
2. Nyissa meg az **EventHubsCaptureEventGrid.sln** megoldást, amelyet az előfeltételek részeként a [GitHubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) töltött le.
3. A Megoldáskezelőben kattintson a jobb gombbal a **FunctionEGDWDumper** elemre, majd válassza a **Közzététel** lehetőséget.

   ![Függvényalkalmazás közzététele](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Ha a következő képernyő látható, válassza a **Start**gombot. 

   ![Közzététel indítása gomb](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. A **Közzétételi cél** kiválasztása lapon jelölje be a **Meglévő kijelölése** beállítást, és válassza **a Profil létrehozása**lehetőséget. 

   ![Közzétételi cél kiválasztása](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. Az App Service-lapon válassza ki az **Azure-előfizetést,** válassza ki a **függvényalkalmazást** az erőforráscsoportban, és válassza az **OK gombot.** 

   ![App Service lap](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Miután a Visual Studio konfigurálta a profilt, kattintson a **Közzététel** elemre.

   ![A Közzététel gomb kiválasztása](media/event-grid-event-hubs-integration/select-publish.png)

A függvény közzététele után feliratkozhat az eseményre.

## <a name="subscribe-to-the-event"></a>Feliratkozás az eseményre

1. Egy webböngésző új lapján vagy új ablakában keresse meg az [Azure Portalt.](https://portal.azure.com)
2. Az Azure Portalon válassza a bal oldali menü **erőforráscsoportok.** 
3. Az erőforráscsoportok listájának szűréséhez írja be az erőforráscsoport nevét a keresőmezőbe. 
4. Válassza ki az erőforráscsoportot a listában.

    ![Az erőforráscsoport kiválasztása](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Válassza ki az App Service-csomagot a listában. 
5. Az App Service-csomag lapon válassza a bal oldali menü **alkalmazások** parancsát, és válassza ki a függvényalkalmazást. 

    ![A függvények alkalmazás kiválasztása](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Bontsa ki a függvényalkalmazást, bontsa ki a függvényeket, és válassza ki a funkciót. 

    ![Az Azure-függvény kiválasztása](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Az eszköztáron válassza az **Eseményrács-előfizetés hozzáadása lehetőséget.** 
8. Az **Eseményrács-előfizetés létrehozása** lapon tegye a következő műveleteket: 
    1. A **TÉMAKÖR RÉSZLETEI** szakaszban tegye a következő műveleteket:
        1. Válassza ki az Azure-előfizetését.
        2. Válassza ki az Azure erőforráscsoportot.
        3. Jelölje ki az Eseményközpontok névterét.
    2. Az **EVENT SUBSCRIPTION DETAILS (ESEMÉNY- ELŐFIZETÉS RÉSZLETEI)** lapon adja meg az előfizetés nevét (például captureEventSub), és válassza a **Create (Create) lehetőséget.** 

        ![Eseményrács-előfizetés létrehozása](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Az alkalmazás futtatása az adatok létrehozásához
Végeztünk az eseményközpont, az SQL-adattárház, az Azure-függvényalkalmazás és az esemény-előfizetés beállításával. Mielőtt futtatna egy alkalmazást, amely adatokat állít elő az eseményközpont számára, konfigurálnia kell néhány értéket.

1. Az Azure Portalon keresse meg az erőforráscsoportot, ahogy korábban tette. 
2. Jelölje ki az Eseményközpontok névterét.
3. Az **Event Hubs Namespace (Eseményközpontok névtér) lapján** válassza a bal oldali menü **Megosztott hozzáférési házirendjei** lehetőséget.
4. A házirendek listájában válassza a **RootManageSharedAccessKey** elemet. 
5. Jelölje ki a Másolás gombot a **Kapcsolatkarakterlánc-elsődleges billentyű** szövegmező mellett. 

    ![Az eseményközpont névterének kapcsolati karakterlánca](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Lépjen vissza a Visual Studio-megoldáshoz. 
2. A WindTurbineDataGenerator projektben nyissa meg a **program.cs** fájlt.
5. Cserélje le a két állandó értékét. Az **EventHubConnectionString** állandónál használja a másolt értéket. Az eseményközpont neveként használja a **hubdatamigration** értéket. Ha az eseményközpontnak más nevet használt, adja meg ezt a nevet. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Hozza létre a megoldást. Futtassa a **WindTurbineGenerator.exe** alkalmazást. 
7. Néhány perc elteltével kérdezze le a migrált adatokat az adattárház táblájából.

    ![Lekérdezés eredményei](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Az eseményközpont által létrehozott eseményadatok
Az Event Grid elküldi az eseményadatokat az előfizetőknek. A következő példa az eseményadatokat mutatja be, amelyek akkor jönnek létre, amikor egy eseményközponton keresztül történő adatfolyam-továbbítás egy blobban rögzítésre kerül. Különösen figyelje `fileUrl` meg, `data` hogy az objektumban lévő tulajdonság a tárolóban lévő blobra mutat. A függvényalkalmazás ezt az URL-címet használja a blobfájl rögzített adatokkal való lekéréséhez.

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
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Hubs Capture megismeréséhez tekintse meg [az Event Hubs Capture Azure Portallal való engedélyezését](../event-hubs/event-hubs-capture-enable-through-portal.md) bemutató cikket.
* A minta beállításával és futtatásával kapcsolatos további információért lásd az [Event Hubs Capture- és Event Grid-mintát](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
