---
title: Azure-Blobok betöltése az Azure-ba Adatkezelő
description: Ebből a cikkből megtudhatja, hogyan küldhet a Storage-fiókadatok az Azure Adatkezelőba egy Event Grid-előfizetés használatával.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 47870410741cf96e289014fab5a9c2eab26759b1
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096415"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Blobok betöltése az Azure Adatkezelőba Event Grid értesítésekre való feliratkozással

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-grid-resource-manager.md)

Az Azure Adatkezelő egy gyors és méretezhető adatelemzési szolgáltatás a napló-és telemetria. Folyamatos betöltést (betöltést) biztosít a blob-tárolóba írt blobokból. 

Ebből a cikkből megtudhatja, hogyan állíthat be [Azure Event Grid](/azure/event-grid/overview) -előfizetést, és hogyan irányíthat eseményeket az Azure adatkezelő egy Event hub használatával. A kezdéshez rendelkeznie kell egy olyan Event Grid-előfizetéssel rendelkező Storage-fiókkal, amely értesítéseket küld az Azure Event Hubsnak. Ezután létre fog hozni egy Event Grid adatátviteli folyamatot, és az egész rendszeren megtekintheti az adatfolyamot.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/).
* [Egy fürt és egy adatbázis](create-cluster-database-portal.md).
* [Egy Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Egy Event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Event Grid előfizetés létrehozása a Storage-fiókban

1. A Azure Portal keresse meg a Storage-fiókját.
1. Válassza az **események** > **esemény-előfizetés**lehetőséget.

    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Az **alapszintű** lapon az **esemény-előfizetés létrehozása** ablakban adja meg a következő értékeket:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Name (Név) | *test-Grid – kapcsolatok* | A létrehozni kívánt Event Grid neve.|
    | Esemény sémája | *Event Grid séma* | Az Event gridhez használandó séma. |
    | Témakör típusa | *Storage-fiók* | Az Event Grid-témakör típusa |
    | Témakör erőforrása | *gridteststorage* | A Storage-fiók neve. |
    | Előfizetés minden eseménytípus esetében | *egyértelmű* | Ne kapjon értesítést minden eseményről. |
    | Definiált események típusai | *BLOB létrehozva* | Mely adott eseményekről kap értesítést. |
    | Végpont típusa | *Event Hubs* | Az eseményeket küldő végpont típusa. |
    | Végpont | *test-hub* | A létrehozott eseményközpont. |
    | | |

1. Ha egy adott tárolóból kívánja nyomon követni a fájlokat, válassza a **szűrők** lapot. Az értesítések szűrőit az alábbiak szerint állítsa be:
    * A **tulajdonos kezdete** mező a blob-tároló *szövegkonstans* -előtagja. Ahogy az alkalmazott minta *startswith*, több tárolóra is kiterjedhet. Nem engedélyezettek a helyettesítő karakterek.
     A *következőket kell beállítani* : *`/blobServices/default/containers/`* [Container előtag]
    * A **tulajdonos végződik** mező a blob *literál* utótagja. Nem engedélyezettek a helyettesítő karakterek.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Céltábla létrehozása az Azure Data Explorerben

Hozzon létre egy táblázatot az Azure Adatkezelőban, ahol Event Hubs küldi az adatküldést. Hozza létre a táblát a fürtben és az előfeltételekben előkészített adatbázist.

1. Az Azure Portalon, a fürt alatt válassza a **Lekérdezés** lehetőséget.

    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-grid/query-explorer-link.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** elemet a betöltött adatot fogadó tábla (TestTable) létrehozásához.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Létrehozási lekérdezés futtatása](media/ingest-data-event-grid/run-create-table.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** elemet a bejövő JSON-adattípusok a tábla oszlopnevek és adattípusai (TestTable) szerinti leképezéséhez.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Event Grid adatkapcsolatok létrehozása az Azure-ban Adatkezelő

Most kapcsolódjon az Azure Adatkezelő Event Grid, hogy a blob-tárolóba áramló adatok továbbítva legyenek a teszt táblába. 

1. Az eszközsáv **Értesítések** elemének kiválasztásával győződjön meg arról, hogy az eseményközpont üzembe helyezése sikeresen megtörtént.

1. A létrehozott fürt alatt válassza az **adatbázisok** > **TestDatabase**elemet.

    ![Tesztadatbázis kiválasztása](media/ingest-data-event-grid/select-test-database.png)

1. Válassza **az adatfeldolgozás** > **adatkezelési kapcsolatok hozzáadása**elemet.

    ![Adatfeldolgozás](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Válassza ki a kapcsolattípus típusát: **blob Storage**.

1. Töltse ki az űrlapot a következő információkkal, majd válassza a **Létrehozás**lehetőséget.

    ![Eseményközpont-kapcsolat](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatkapcsolat neve | *test-hub-connection* | Az Azure Adatkezelőban létrehozni kívánt kapcsolódás neve.|
    | Storage-fiók előfizetése | Az előfizetés azonosítója | Az előfizetés-azonosító, amelyben a Storage-fiók található.|
    | Tárfiók | *gridteststorage* | A korábban létrehozott Storage-fiók neve.|
    | Event Grid | *test-Grid – kapcsolatok* | A létrehozott Event Grid neve. |
    | Eseményközpont neve | *test-hub* | A létrehozott Event hub. A program automatikusan kitölti ezt a mezőt az Event Grid kiválasztásakor. |
    | Fogyasztói csoport | *test-group* | A létrehozott Event hub-ban definiált fogyasztói csoport. |
    | | |

    Céltábla:

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *TestTable* | A **TestDatabase** adatbázisban létrehozott tábla. |
    | Adatformátum | *JSON* | A támogatott formátumok a következők: Avro, CSV, JSON, többsoros JSON, PSV, rendszerállapot-kimutatás, SCSV, TSV, RAW és TXT. Támogatott tömörítési beállítások: zip és GZip |
    | Oszlopleképezés | *TestMapping* | A **TestDatabase** adatbázisban létrehozott leképezés, amely a bejövő JSON-adatokat leképezi a **TestTable** tábla esetében használt oszlopnevekre és adattípusokra.|
    | | |
    
## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Most, hogy az Azure Adatkezelő és a Storage-fiók csatlakoztatva van, létrehozhat mintaadatok, és feltöltheti azt a blob Storage-ba.

Egy kisméretű rendszerhéj-parancsfájllal dolgozunk, amely néhány alapszintű Azure CLI-parancs kiadásával kommunikál az Azure Storage-erőforrásokkal. Ez a szkript létrehoz egy új tárolót a Storage-fiókban, feltölt egy meglévő fájlt (blobként) a tárolóba, majd felsorolja a tárolóban lévő blobokat. A [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) használatával közvetlenül a portálon hajthatja végre a parancsfájlt.

Mentse az adatok fájlba, és töltse fel a következő szkripttel:

```Json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```bash
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> A legjobb betöltési teljesítmény elérése érdekében a betöltéshez elküldött tömörített Blobok *tömörítetlen* méretét tájékoztatni kell. Mivel Event Grid értesítések csak alapvető adatokat tartalmaznak, a méretre vonatkozó információkat explicit módon közölni kell. A tömörítetlen méretre vonatkozó információk a blob-metaadatok `rawSizeBytes` tulajdonságának beállításával állíthatók be a *tömörítetlen* adatok mérete bájtban megadva.

### <a name="ingestion-properties"></a>Betöltési tulajdonságok

Megadhatja a blob betöltési [tulajdonságait](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) a blob metaadatainak használatával.

Ezek a tulajdonságok megadhatók:

|**Tulajdonság** | **Tulajdonság leírása**|
|---|---|
| `rawSizeBytes` | A nyers (tömörítetlen) adatmennyiség mérete. A Avro/ork/parketta esetében ez a méret a formátumra jellemző tömörítés alkalmazása előtt.|
| `kustoTable` |  A meglévő céltábla neve. Felülbírálja a `Data Connection` panelen beállított `Table`. |
| `kustoDataFormat` |  Adatformátum. Felülbírálja a `Data Connection` panelen beállított `Data format`. |
| `kustoIngestionMappingReference` |  A használandó meglévő betöltési leképezés neve. Felülbírálja a `Data Connection` panelen beállított `Column mapping`.|
| `kustoIgnoreFirstRecord` | Ha `true`értékre van állítva, a Kusto figyelmen kívül hagyja a blob első sorát. Táblázatos formátumú adatokat (CSV, TSV vagy hasonló) használva figyelmen kívül hagyhatja a fejléceket. |
| `kustoExtentTags` | A [címkét](/azure/kusto/management/extents-overview#extent-tagging) jelölő karakterlánc, amely az adott egységhez lesz csatolva. |
| `kustoCreationTime` |  Felülbírálja a blob [$IngestionTimeét](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) ISO 8601-karakterláncként formázva. A visszatöltésére használata. |

> [!NOTE]
> Az Azure Adatkezelő nem törli a Blobok utáni betöltést.
> Tartsa meg a blobokat a thrre öt napig.
> Az [Azure Blob Storage életciklusával](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) kezelheti a Blobok törlését. 

## <a name="review-the-data-flow"></a>Az adatfolyam áttekintése

> [!NOTE]
> Az Azure Adatkezelő összesítési (batch-) szabályzattal rendelkezik a betöltési folyamat optimalizálására tervezett adatfeldolgozáshoz.
Alapértelmezés szerint a házirend 5 percre van konfigurálva.
Szükség esetén később is megváltoztathatja a szabályzatot. Ebben a cikkben néhány perc késését várhatjuk el.

1. Az Azure Portal az Event Grid alatt az alkalmazás futása alatt látható a nyárs tevékenység.

    ![Event Grid gráf](media/ingest-data-event-grid/event-grid-graph.png)

1. A következő lekérdezés a tesztadatbázison való futtatásával ellenőrizze, hogy hány üzenet került át eddig a pillanatig az adatbázisba.

    ```Kusto
    TestTable
    | count
    ```

1. Az üzenetek tartalmának megtekintéséhez futtassa a következő lekérdezést a tesztelési adatbázisban.

    ```Kusto
    TestTable
    ```

    Az eredményhalmaznak a következőhöz hasonlóan kell kinéznie.

    ![Üzenetek eredményhalmaza](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi az Event Grid ismételt használatát, törölje a **test-hub-RG**törlését, hogy elkerülje a költségek felmerülését.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen, majd a létrehozott erőforráscsoport.  

    Ha a bal oldali menü össze van csukva, kattintson a ![Kibontás gombra](media/ingest-data-event-grid/expand.png) a kinyitásához.

   ![A törölni kívánt erőforráscsoport kiválasztása](media/ingest-data-event-grid/delete-resources-select.png)

1. A **test-resource-group** alatt válassza az **Erőforráscsoport törlése** elemet.

1. Az új ablakban adja meg a törölni kívánt erőforráscsoport nevét (*test-hub-RG*), majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Adatkezelő lekérdezése](web-query-data.md)
