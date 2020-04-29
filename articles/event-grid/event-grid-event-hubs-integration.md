---
title: 'Oktatóanyag: Event Hubs-adatraktárba való küldés az adattárházba – Event Grid'
description: 'Oktatóanyag: a Azure Event Grid és a Event Hubs használatával végezhető el az adatáttelepítés egy SQL Data Warehouse. Egy Azure-függvény használatával kéri le a rögzítési fájlt.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 6f5bd129b175210cd5b9415a65b8db06d904e24d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73718183"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Oktatóanyag: stream big data adattárházba
Az Azure [Event Grid](overview.md) egy intelligens esemény-útválasztási szolgáltatás, amely lehetővé teszi, hogy az alkalmazásokból és szolgáltatásokból érkező értesítésekre (eseményekre) reagáljon. Például elindíthat egy Azure-függvényt az Azure Blob Storage-ba vagy Azure Data Lake Storageba rögzített Event Hubs-adat feldolgozásához, és áttelepítheti azokat más adattárakba. Ez a [Event Hubs és Event Grid integrációs minta](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) azt mutatja be, hogyan használhatók a Event Hubs a Event Grid a blob Storage-ból rögzített Event Hubs adatok SQL Data Warehouseba való zökkenőmentes áttelepítéséhez.

![Az alkalmazás áttekintése](media/event-grid-event-hubs-integration/overview.png)

Ez az ábra az oktatóanyagban felépített megoldás munkafolyamatát ábrázolja: 

1. Az Azure Event hub-nak elküldett adathalmazok egy Azure Blob Storage-tárolóban vannak rögzítve.
2. Az adatrögzítés befejezése után létrejön egy esemény, amely egy Azure Event gridre lesz küldve. 
3. Az Event Grid továbbítja ezt az eseményt az Azure Function alkalmazásnak.
4. A Function alkalmazás az esemény adatainak blob URL-címét használja a blobnak a tárolóból való lekéréséhez. 
5. A Function alkalmazás áttelepíti a blobot az Azure SQL-adattárházba. 

Ebben a cikkben a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Az infrastruktúra üzembe helyezéséhez használjon Azure Resource Manager sablont: egy Event hub, egy Storage-fiók, egy Function alkalmazás, egy SQL-adattárház.
> * Hozzon létre egy táblát az adattárházban.
> * Kód hozzáadása a Function alkalmazáshoz.
> * Előfizetés az eseményre. 
> * Olyan alkalmazás futtatása, amely adatokat küld az Event hub-nak.
> * Megtekintheti az áttelepített adatraktárban tárolt adatfájlokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* [Visual studio 2019](https://www.visualstudio.com/vs/) a következő számítási feladatokkal: .net Desktop-fejlesztés, Azure-fejlesztés, ASP.net és webfejlesztés, Node. js-fejlesztés és Python-fejlesztés.
* Töltse le a [EventHubsCaptureEventGridDemo minta projektet](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) a számítógépre.

## <a name="deploy-the-infrastructure"></a>Az infrastruktúra üzembe helyezése
Ebben a lépésben üzembe helyezi a szükséges infrastruktúrát egy [Resource Manager-sablonnal](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). A sablon központi telepítésekor a következő erőforrások jönnek létre:

* Az Event hub engedélyezve van a rögzítési funkcióval.
* A rögzített fájlok Storage-fiókja. 
* App Service-csomag a Function app üzemeltetéséhez
* Függvényalkalmazás az esemény feldolgozásához
* SQL Server az adattárház üzemeltetéséhez
* SQL Data Warehouse a migrált adatok tárolásához

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Azure Cloud Shell elindítása Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Kattintson a felül **Cloud Shell** gombra.

    ![Azure Portal](media/event-grid-event-hubs-integration/azure-portal.png)
3. Megjelenik a böngésző alján megnyíló Cloud Shell.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Ha a Cloud Shell a **bash** és a **PowerShell**közötti választás lehetőséget látja, válassza a **bash**elemet. 
5. Ha első alkalommal használja a Cloud Shell, hozzon létre egy Storage-fiókot a **tároló létrehozása**lehetőség kiválasztásával. Azure Cloud Shell szükség van egy Azure Storage-fiókra néhány fájl tárolásához. 

    ![Tároló létrehozása a Cloud shellhez](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Várjon, amíg a Cloud Shell inicializálása be nem fejeződik. 

    ![Tároló létrehozása a Cloud shellhez](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

1. Hozzon létre egy Azure-erőforráscsoportot az alábbi CLI-parancs futtatásával: 
    1. Másolja és illessze be a következő parancsot a Cloud Shell ablakába

        ```azurecli
        az group create -l eastus -n <Name for the resource group>
        ```
    1. Adja meg az **erőforráscsoport** nevét.
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
2. Az alábbi CLI-parancs futtatásával telepítse az előző szakaszban említett összes erőforrást (Event hub, Storage-fiók, functions-alkalmazás, SQL-adattárház): 
    1. Másolja és illessze be a parancsot a Cloud Shell ablakába. Azt is megteheti, hogy az Ön által választott szerkesztőbe szeretne másolni/beilleszteni, értékeket állít be, majd a parancsot a Cloud Shellba másolja. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigrationSample \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Értékek megadása a következő entitásokhoz:
        1. A korábban létrehozott erőforráscsoport neve.
        2. Az Event hub-névtér neve. 
        3. Az Event hub neve. Az értéket (hubdatamigration) is meghagyhatja.
        4. Az SQL Server neve.
        5. Az SQL-felhasználó és a jelszó neve. 
        6. Az SQL-adatraktár neve
        7. A Storage-fiók neve. 
        8. A Function alkalmazás neve. 
    3.  A parancs futtatásához nyomja le az **ENTER** billentyűt a Cloud Shell ablakban. Ez a folyamat hosszabb időt is igénybe vehet, hiszen egy csomó erőforrást hoz létre. A parancs eredményében ellenőrizze, hogy nincsenek-e hibák. 
    

### <a name="use-azure-powershell"></a>Azure PowerShell használatával

1. Azure Cloud Shell váltson át PowerShell módba. A Azure Cloud Shell bal felső sarkában válassza a lefelé mutató nyilat, majd kattintson a **PowerShell**elemre.

    ![Váltás a PowerShellre](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Hozzon létre egy Azure-erőforráscsoportot a következő parancs futtatásával: 
    1. Másolja és illessze be a következő parancsot a Cloud Shell ablakába.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location westcentralus
        ```
    2. Adja meg az **erőforráscsoport**nevét.
    3. Nyomja le az Enter billentyűt. 
3. Telepítse az előző szakaszban említett összes erőforrást (Event hub, Storage-fiók, functions alkalmazás, SQL-adattárház) a következő parancs futtatásával:
    1. Másolja és illessze be a parancsot a Cloud Shell ablakába. Azt is megteheti, hogy az Ön által választott szerkesztőbe szeretne másolni/beilleszteni, értékeket állít be, majd a parancsot a Cloud Shellba másolja. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Értékek megadása a következő entitásokhoz:
        1. A korábban létrehozott erőforráscsoport neve.
        2. Az Event hub-névtér neve. 
        3. Az Event hub neve. Az értéket (hubdatamigration) is meghagyhatja.
        4. Az SQL Server neve.
        5. Az SQL-felhasználó és a jelszó neve. 
        6. Az SQL-adatraktár neve
        7. A Storage-fiók neve. 
        8. A Function alkalmazás neve. 
    3.  A parancs futtatásához nyomja le az **ENTER** billentyűt a Cloud Shell ablakban. Ez a folyamat hosszabb időt is igénybe vehet, hiszen egy csomó erőforrást hoz létre. A parancs eredményében ellenőrizze, hogy nincsenek-e hibák. 

### <a name="close-the-cloud-shell"></a>A Cloud Shell lezárása 
A Cloud Shell ablak jobb felső sarkában, a portál (vagy) **X** gombján a **Cloud Shell** gombra kattintva zárhatja be a Cloud shellt. 

### <a name="verify-that-the-resources-are-created"></a>Az erőforrások létrehozásának ellenőrzése

1. A Azure Portal a bal oldali menüben válassza az **erőforráscsoportok** lehetőséget. 
2. Az erőforráscsoportok listájának szűréséhez írja be az erőforráscsoport nevét a keresőmezőbe. 
3. Válassza ki az erőforráscsoportot a listában.

    ![Az erőforráscsoport kiválasztása](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Győződjön meg arról, hogy az erőforráscsoport következő erőforrásai láthatók:

    ![Az erőforráscsoporthoz tartozó erőforrások](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>Tábla létrehozása az SQL Data Warehouse-ban
Hozzon létre egy táblázatot az adattárházban a [CreateDataWarehouseTable. SQL](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) parancsfájl futtatásával. A szkript futtatásához használhatja a Visual studiót vagy a lekérdezés-szerkesztőt a portálon. A következő lépések bemutatják, hogyan használhatja a lekérdezés-szerkesztőt: 

1. Az erőforráscsoport erőforrásainak listájában válassza ki az SQL-adattárházat. 
2. Az SQL-adattárház lapon a bal oldali menüben válassza a **Lekérdezés-szerkesztő (előzetes verzió)** lehetőséget. 

    ![SQL-adattárház lapja](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Adja meg az SQL Server **felhasználójának** és **jelszavának** nevét, majd kattintson **az OK gombra**. 

    ![SQL Server-hitelesítés](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. A lekérdezési ablakban másolja és futtassa a következő SQL-parancsfájlt: 

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
5. Tartsa meg ezt a lapot vagy ablakot úgy, hogy az oktatóanyag végén ellenőrizze, hogy létrejöttek-e az adatgyűjtés. 


## <a name="publish-the-azure-functions-app"></a>Az Azure Functions-alkalmazás közzététele

1. Indítsa el a Visual Studiót.
2. Nyissa meg a **EventHubsCaptureEventGridDemo. SLN** megoldást, amelyet az előfeltételek részeként letöltött a [githubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) .
3. A Megoldáskezelőben kattintson a jobb gombbal a **FunctionEGDWDumper** elemre, majd válassza a **Közzététel** lehetőséget.

   ![Függvényalkalmazás közzététele](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Ha a következő képernyő jelenik meg, kattintson a **Start**gombra. 

   ![Közzététel indítása gomb](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. A **Közzététel célhelyének** kiválasztása lapon válassza a **meglévő kijelölése** lehetőséget, majd kattintson a **profil létrehozása**lehetőségre. 

   ![Közzétételi cél kiválasztása](media/event-grid-event-hubs-integration/publish-select-existing.png)
6. A App Service lapon válassza ki az **Azure-előfizetését**, válassza ki a **Function alkalmazást** az erőforráscsoporthoz, és kattintson az **OK gombra**. 

   ![App Service lap](media/event-grid-event-hubs-integration/publish-app-service.png) 
1. Miután a Visual Studio konfigurálta a profilt, kattintson a **Közzététel** elemre.

   ![A Közzététel gomb kiválasztása](media/event-grid-event-hubs-integration/select-publish.png)

A függvény közzététele után feliratkozhat az eseményre.

## <a name="subscribe-to-the-event"></a>Feliratkozás az eseményre

1. A webböngésző új lapján vagy új ablakában navigáljon a [Azure Portal](https://portal.azure.com).
2. A Azure Portal a bal oldali menüben válassza az **erőforráscsoportok** lehetőséget. 
3. Az erőforráscsoportok listájának szűréséhez írja be az erőforráscsoport nevét a keresőmezőbe. 
4. Válassza ki az erőforráscsoportot a listában.

    ![Az erőforráscsoport kiválasztása](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Válassza ki a App Service tervet a listából. 
5. A App Service terv lapon válassza az **alkalmazások** lehetőséget a bal oldali menüben, és válassza ki a Function alkalmazást. 

    ![Válassza ki a functions alkalmazást](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Bontsa ki a Function alkalmazást, bontsa ki a függvények elemet, majd válassza ki a függvényt. 

    ![Az Azure-függvény kiválasztása](media/event-grid-event-hubs-integration/select-function-add-button.png)
7. Válassza a **Event Grid előfizetés hozzáadása** lehetőséget az eszköztáron. 
8. A **Event Grid-előfizetés létrehozása** lapon hajtsa végre a következő műveleteket: 
    1. A **témakör részletei** szakaszban hajtsa végre a következő műveleteket:
        1. Válassza ki az Azure-előfizetését.
        2. Válassza ki az Azure-erőforráscsoportot.
        3. Válassza ki a Event Hubs névteret.
    2. Az **esemény-előfizetés részletei** lapon adja meg az előfizetés nevét (például: captureEventSub), majd válassza a **Létrehozás**lehetőséget. 

        ![Event Grid előfizetés létrehozása](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Az alkalmazás futtatása az adatok létrehozásához
Végeztünk az eseményközpont, az SQL-adattárház, az Azure-függvényalkalmazás és az esemény-előfizetés beállításával. Mielőtt futtatna egy alkalmazást, amely adatokat állít elő az eseményközpont számára, konfigurálnia kell néhány értéket.

1. A Azure Portalban navigáljon az erőforráscsoporthoz, mint korábban. 
2. Válassza ki a Event Hubs névteret.
3. A **Event Hubs névtér** lapon válassza a bal oldali menüben a **megosztott elérési házirendek** elemet.
4. A szabályzatok listájában válassza a **RootManageSharedAccessKey** lehetőséget. 
5. Kattintson a **kapcsolódási karakterlánc – elsődleges kulcs** szövegmező melletti Másolás gombra. 

    ![Az Event hub-névtér kapcsolati karakterlánca](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Térjen vissza a Visual Studio-megoldáshoz. 
2. A WindTurbineDataGenerator projektben nyissa meg a **program.cs** fájlt.
5. Cserélje le a két állandó értékét. Az **EventHubConnectionString** állandónál használja a másolt értéket. Az eseményközpont neveként használja a **hubdatamigration** értéket. Ha más nevet használt az Event hub számára, adja meg a nevet. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Hozza létre a megoldást. Futtassa a **WindTurbineGenerator. exe** alkalmazást. 
7. Néhány perc elteltével kérdezze le a migrált adatokat az adattárház táblájából.

    ![Lekérdezés eredményei](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Az Event hub által generált esemény-adatértékek
Az Event Grid elküldi az eseményadatokat az előfizetőknek. Az alábbi példa azokat az eseményeket mutatja be, amelyek akkor jönnek létre, amikor az Event hub-on keresztül történő adatátvitelt egy blobban rögzíti. Figyelje meg, hogy az `fileUrl` `data` objektum tulajdonsága a tárolóban lévő blobra mutat. A Function alkalmazás ezt az URL-címet használja a rögzített adattal rendelkező blob-fájl lekéréséhez.

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
