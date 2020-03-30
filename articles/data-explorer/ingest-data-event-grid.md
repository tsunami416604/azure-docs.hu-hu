---
title: Az Azure Blobok betöltése az Azure Data Explorerbe
description: Ebben a cikkben megtudhatja, hogyan küldhet tárfiók adatait az Azure Data Explorer egy Event Grid-előfizetés használatával.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370440"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Blobok betöltése az Azure Data Explorerbe az Event Grid-értesítésekre való feliratkozással

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-grid-resource-manager.md)

Az Azure Data Explorer egy gyors és skálázható adatfeltárási szolgáltatás a napló- és telemetriai adatokhoz. Folyamatos betöltést (adatbetöltést) kínál a blobtárolókba írt blobokból. 

Ebben a cikkben megtudhatja, hogyan állíthat be egy [Azure Event Grid-előfizetést,](/azure/event-grid/overview) és hogyan irányíthatja az eseményeket az Azure Data Explorer egy eseményközponton keresztül. Először rendelkeznie kell egy olyan eseményrács-előfizetéssel rendelkező tárfiókkal, amely értesítéseket küld az Azure Event Hubs-nak. Ezután létrehoz egy Eseményrács adatkapcsolatot, és láthatja az adatfolyamot a rendszerben.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Hozzon létre egy [ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)
* [Fürt és adatbázis](create-cluster-database-portal.md).
* [Tárfiók.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)
* [Egy eseményközpont](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Event Grid-előfizetés létrehozása a tárfiókban

1. Az Azure Portalon keresse meg a tárfiókot.
1. Válassza az > **Eseményesemény-előfizetés lehetőséget.** **Events**

    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Az **Alaplap Esemény-előfizetés létrehozása** ablakában adja meg a következő értékeket: **Basic**

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Név | *tesztrács-csatlakozás* | A létrehozni kívánt eseményrács neve.|
    | Eseményséma | *Event Grid-séma* | Az eseményrácshoz használandó séma. |
    | Témakör típusa | *Tárfiók* | Az eseményrács témakörének típusa. |
    | Témakör erőforrás | *gridteststorage* | A tárfiók neve. |
    | Feliratkozás az összes eseménytípusra | *Egyértelmű* | Ne kapjon értesítést minden eseményről. |
    | Definiált eseménytípusok | *Létrehozott blob* | Mely konkrét eseményekről kaphatértesítést. |
    | Végpont típusa | *Event Hubs* | Az eseményeket küldő végpont típusa. |
    | Végpont | *test-hub* | A létrehozott eseményközpont. |
    | | |

1. Válassza a **Szűrők** lapot, ha egy adott tárolóból szeretné nyomon követni a fájlokat. Az értesítések szűrőit a következőképpen állítsa be:
    * **Tárgy kezdődik** mező a blob tároló *konstans* előtagja. Mivel az alkalmazott minta *kezdődik,* akkor több tárolóra is kiterjedhet. Helyettesítő karakterek nem engedélyezettek.
     A következőképpen *kell* beállítani: *`/blobServices/default/containers/`*[tároló előtag]
    * **Tárgy végződik mező** a *blob konstans* utótagja. Helyettesítő karakterek nem engedélyezettek.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Céltábla létrehozása az Azure Data Explorerben

Hozzon létre egy táblát az Azure Data Explorerben, ahol az Event Hubs adatokat küld. Hozza létre a táblát a fürtben és az adatbázisban készített az előfeltételekben.

1. Az Azure Portalon, a fürt alatt válassza a **Lekérdezés** lehetőséget.

    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-grid/query-explorer-link.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget a bevitt adatokat fogadó tábla (TestTable) létrehozásához.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Létrehozási lekérdezés futtatása](media/ingest-data-event-grid/run-create-table.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget, ha a bejövő JSON-adatokat a tábla oszlopnevéhez és adattípusához (TestTable) szeretné leképezni.

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Eseményrács-adatkapcsolat létrehozása az Azure Data Explorerben

Most csatlakozzon az Event Grid az Azure Data Explorer, így a blob tárolóba áramló adatok streamelése a teszttáblába. 

1. Az eszközsáv **Értesítések** elemének kiválasztásával győződjön meg arról, hogy az eseményközpont üzembe helyezése sikeresen megtörtént.

1. A létrehozott fürt alatt válassza az **Adatbázisok** > **tesztadatbázis lehetőséget.**

    ![Tesztadatbázis kiválasztása](media/ingest-data-event-grid/select-test-database.png)

1. Válassza **az Adatbetöltés** > **Adatkapcsolat hozzáadása**lehetőséget.

    ![Adatfeldolgozás](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Válassza ki a kapcsolat típusát: **Blob Storage**.

1. Töltse ki az űrlapot a következő információkkal, és válassza a **Létrehozás gombot.**

    ![Eseményközpont-kapcsolat](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatkapcsolat neve | *test-hub-connection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | Tárfiók-előfizetés | Az előfizetés azonosítója | Az előfizetés-azonosító, ahol a tárfiók található.|
    | Tárfiók | *gridteststorage* | A korábban létrehozott tárfiók neve.|
    | Event Grid | *tesztrács-csatlakozás* | A létrehozott eseményrács neve. |
    | Eseményközpont neve | *test-hub* | A létrehozott eseményközpont. Ez a mező automatikusan kitöltődik, amikor kiválaszt egy eseményrácsot. |
    | Fogyasztói csoport | *test-group* | A létrehozott eseményközpontban definiált fogyasztói csoport. |
    | | |

    Céltábla:

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *TestTable* | A **TestDatabase** adatbázisban létrehozott tábla. |
    | Adatformátum | *JSON* | A támogatott formátumok: Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW és TXT. Támogatott tömörítési beállítások: Zip és GZip |
    | Oszlopleképezés | *TestMapping* | A **TestDatabase** adatbázisban létrehozott leképezés, amely a bejövő JSON-adatokat leképezi a **TestTable** tábla esetében használt oszlopnevekre és adattípusokra.|
    | | |
    
## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Most, hogy az Azure Data Explorer és a tárfiók csatlakoztatva van, létrehozhat mintaadatokat, és feltöltheti azokkal a blobtárba.

Egy kis rendszerhéj-parancsfájllal fogunk dolgozni, amely néhány alapvető Azure CLI-parancsot ad ki az Azure Storage-erőforrásokkal való együttműködéshez. Ez a parancsfájl létrehoz egy új tárolót a tárfiókban, feltölt egy meglévő fájlt (blobként) a tárolóba, majd felsorolja a blobokat a tárolóban. Az [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) használatával közvetlenül a portálon futtathatja a parancsfájlt.

Mentse az adatokat egy fájlba, és töltse fel ezzel a szkripttel:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
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
> A legjobb betöltési teljesítmény elérése érdekében a betöltésre benyújtott tömörítetlen blobok *tömörítetlen* méretét közölni kell. Mivel az Event Grid-értesítések csak alapvető részleteket tartalmaznak, a méretadatokat explicit módon kell közölni. A tömörítetlen méret adatokat meg `rawSizeBytes` adható a tulajdonság a blob metaadatok a *tömörítetlen* adatméret bájtban.

### <a name="ingestion-properties"></a>Betöltési tulajdonságok

Megadhatja a blob betöltési [tulajdonságait](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) a blob metaadatokon keresztül.

Ezek a tulajdonságok a következők:

|**Tulajdonság** | **Tulajdonság leírása**|
|---|---|
| `rawSizeBytes` | A nyers (tömörítetlen) adatok mérete. Az Avro/ORC/Parketta esetében ez a méret a formátumspecifikus tömörítés alkalmazása előtt.|
| `kustoTable` |  A meglévő céltábla neve. Felülírja `Table` a `Data Connection` késen lévő készletet. |
| `kustoDataFormat` |  Adatformátum. Felülírja `Data format` a `Data Connection` késen lévő készletet. |
| `kustoIngestionMappingReference` |  A használandó meglévő betöltési leképezés neve. Felülírja `Column mapping` a `Data Connection` késen lévő készletet.|
| `kustoIgnoreFirstRecord` | Ha `true`a beállítás , Kusto figyelmen kívül hagyja a blob első sorát. Táblázatos formátumú adatokban (CSV, TSV vagy hasonló) használja a fejlécek figyelmen kívül hagyására. |
| `kustoExtentTags` | Az eredményül kapott mértékben csatolandó [címkéket](/azure/kusto/management/extents-overview#extent-tagging) jelölő karakterlánc. |
| `kustoCreationTime` |  Felülbírálja a [blob $IngestionTime,](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) ISO 8601 karakterláncként formázva. Feltöltéshez használható. |

> [!NOTE]
> Az Azure Data Explorer nem törli a blobok betöltés után.
> Tartsa meg a blobok thrre öt napig.
> Az [Azure Blob tárolási életciklusa](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) a blob törlésének kezeléséhez. 

## <a name="review-the-data-flow"></a>Az adatfolyam áttekintése

> [!NOTE]
> Az Azure Data Explorer rendelkezik egy aggregációs (kötegelési) szabályzat adatbetöltési célja, hogy optimalizálja a betöltési folyamatot.
Alapértelmezés szerint a házirend 5 percre van konfigurálva.
Szükség esetén később módosíthatja a szabályzatot. Ebben a cikkben néhány perces késésre számíthat.

1. Az Azure Portalon az eseményrács alatt láthatja a tevékenység kiugrását az alkalmazás futása közben.

    ![Eseményrács-diagram](media/ingest-data-event-grid/event-grid-graph.png)

1. A következő lekérdezés a tesztadatbázison való futtatásával ellenőrizze, hogy hány üzenet került át eddig a pillanatig az adatbázisba.

    ```kusto
    TestTable
    | count
    ```

1. Az üzenetek tartalmának megtekintéséhez futtassa a következő lekérdezést a tesztadatbázisban.

    ```kusto
    TestTable
    ```

    Az eredményhalmaznak a következőhöz hasonlóan kell kinéznie.

    ![Üzenetek eredményhalmaza](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi, hogy használja az eseményrácsot újra, törölje a **teszt-hub-rg,** a költségek elkerülése érdekében.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen, majd a létrehozott erőforráscsoport.  

    Ha a bal oldali menü össze van csukva, kattintson a ![Kibontás gombra](media/ingest-data-event-grid/expand.png) a kinyitásához.

   ![A törölni kívánt erőforráscsoport kiválasztása](media/ingest-data-event-grid/delete-resources-select.png)

1. A **test-resource-group** alatt válassza az **Erőforráscsoport törlése** elemet.

1. Az új ablakban adja meg a törlendő erőforráscsoport nevét (*teszt-hub-rg*), majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

* [Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
