---
title: 'Gyors útmutató: Azure-Blobok tölti be az Azure Data Explorer'
description: Ez a rövid útmutatóban megismerheti, hogyan tárolási fiók adatokat küldeni az Azure Data Explorer Event Grid-előfizetés használatával.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 01/30/2019
ms.openlocfilehash: 19db47610449ced45fa61610bbe964042e815c7a
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051852"
---
# <a name="quickstart-ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Gyors útmutató: Blobok tölti be az adatkezelőt az Azure Event Grid értesítések való feliratkozással

Az Azure Data Explorer az napló és a telemetriai adatok gyors és méretezhető exploration szolgáltatás. A blobok, blob-tárolók írt folyamatos Adatbetöltési (adatok betöltése) kínál. 

Ebben a rövid útmutató beállítani egy [Azure Event Grid](/azure/event-grid/overview) előfizetés és a egy eseményközpont-n keresztül az Azure Data Explorer események átirányítása. Első lépésként kell egy event grid-előfizetést, amely értesítéseket küld az Azure Event Hubs egy tárfiókot. Ezután fogja hozzon létre egy Event Grid adatkapcsolat, és tekintse meg az adatokat a folyamat során a rendszer.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/).
* [A fürt és az adatbázis](create-cluster-database-portal.md).
* [Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Egy eseményközpont](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Hozzon létre egy Event Grid-előfizetést a tárfiókban

1. Az Azure Portalon keresse meg a tárfiókot.
1. Válassza ki **események** > **esemény-előfizetés**.

    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Az a **esemény-előfizetés létrehozása** ablakot a **alapszintű** lapra, adja meg a következő értékeket:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Name (Név) | *test-grid-connection* | Az event griddel, hogy a létrehozni kívánt nevére.|
    | Eseményséma | *Event Grid-séma* | A séma, amely az event grid kell használni. |
    | Témakörtípus | *Tárfiók* | Az event grid-témakör típusa. |
    | Témakörerőforrás | *gridteststorage* | A tárfiók neve. |
    | Előfizetés az összes eseménytípusra | *törlés* | Nem minden eseményt a értesítést kaphat. |
    | Definiált eseménytípusok | *Létrehozott blob* | Értesítés küldése a meghatározott események. |
    | Végpont típusa | *Event Hubs* | A végpont, amelyhez az események küldése típusa. |
    | Végpont | *test-hub* | A létrehozott eseményközpont. |
    | | |

1. Válassza ki a **további funkciók** lapon, ha szeretné nyomon követni a fájlokat az adott tárolóban. Állítsa be a szűrőket, az értesítéseket a következőképpen:
    * **Megkezdődik a tulajdonos** mező a *szövegkonstans* blobtároló előtag. Mivel a alkalmazni minta *startswith*, több tárolót is kiterjedhetnek. A helyettesítő karakterek nem engedélyezettek.
     Ez *kell* értéke a következő lehet: *`/blobServices/default/containers/`*[tároló előtag]
    * **Ér véget a tulajdonos** mező a *szövegkonstans* a blob-utótagját. A helyettesítő karakterek nem engedélyezettek.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Céltábla létrehozása az Azure Data Explorerben

Tábla létrehozása az Azure az adatkezelőt, ahol az Event Hubs elküldi az adatokat. A tábla létrehozása a fürt és -adatbázis előkészítése az előfeltételeket.

1. Az Azure Portalon, a fürt alatt válassza a **Lekérdezés** lehetőséget.

    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-grid/query-explorer-link.png)

1. A következő parancs másolja be az ablakot, és válassza ki **futtatása** a tábla (TestTable), amelyek megkapják a feldolgozott adatok létrehozásához.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Létrehozási lekérdezés futtatása](media/ingest-data-event-grid/run-create-table.png)

1. A következő parancs másolja be az ablakot, és válassza ki **futtatása** való leképezéséhez a tábla (TestTable) oszlop nevükkel és adattípusukkal bejövő JSON-adatokat.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Hozzon létre egy Event Grid adatkapcsolat Azure adatkezelőjében

Mostantól csatlakozhat az event grid Azure Data Explorer, úgy, hogy a tárfiókba kerülnek a blob-tároló adatok streamelése a teszt táblába.

1. Az eszközsáv **Értesítések** elemének kiválasztásával győződjön meg arról, hogy az eseményközpont üzembe helyezése sikeresen megtörtént.

1. A létrehozott fürt, válassza a **adatbázisok** > **TestDatabase**.

    ![Tesztadatbázis kiválasztása](media/ingest-data-event-grid/select-test-database.png)

1. Válassza ki **adatbetöltés** > **adatkapcsolat hozzáadása**.

    ![Adatfeldolgozás](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Válassza ki a kapcsolat típusa: **A BLOB Storage-**.

1. Töltse ki az űrlapot a következő információkat, és válassza ki **létrehozás**.

    ![Eseményközpont-kapcsolat](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatkapcsolat neve | *test-hub-connection* | Hozzon létre az Azure Data Explorer kívánt kapcsolat nevére.|
    | Tárfiók-előfizetés | Az előfizetés-azonosító | A storage-fiókot tartalmazó előfizetés azonosítója.|
    | Tárfiók | *gridteststorage* | A korábban létrehozott tárfiók neve.|
    | Event Grid | *test-grid-connection* | Az event grid létrehozott neve. |
    | Eseményközpont neve | *test-hub* | Az event hubs, amelyet Ön hozott létre. Válasszon ki egy event grid során a rendszer automatikusan kitölti ezt a mezőt. |
    | Fogyasztói csoport | *test-group* | A fogyasztói csoportot az Ön által létrehozott eseményközpont definiálva. |
    | | |

    Céloldali tábla:

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *TestTable* | A **TestDatabase** adatbázisban létrehozott tábla. |
    | Adatformátum | *JSON* | Támogatott formátumok a következők: Avro, CSV, JSON, TÖBBSOROS JSON, PSV, Rendszerállapot, SCSV, TSV és TXT. |
    | Oszlopleképezés | *TestMapping* | A **TestDatabase** adatbázisban létrehozott leképezés, amely a bejövő JSON-adatokat leképezi a **TestTable** tábla esetében használt oszlopnevekre és adattípusokra.|
    | | |

## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Most, hogy az Azure Data Explorer és a storage-fiók csatlakoztatva, mintaadatok létrehozása, és töltse fel a blob Storage.

Egy kis héjparancsfájlt, amely néhány alapvető Azure CLI-parancsokat használhatja az Azure Storage-erőforrások is együttműködünk. Ez a szkript létrehoz egy új tárolót a storage-fiókban, tölt fel egy már létező fájlt (a blob) az adott tárolóhoz, és ezután kilistázza a tárolóban található blobokat. Használhat [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) közvetlenül a portálon a szkript végrehajtásához.

Az adatok mentése fájlba, és töltse fel ezt a parancsfájlt:

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
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

## <a name="review-the-data-flow"></a>Az adatfolyam áttekintése

> [!NOTE]
> Az adatkezelő az Azure-összesítési (kötegelés) szabályzat optimalizálja a betöltési folyamat adatbetöltés rendelkezik.
A házirend alapértelmezés szerint 5 perc van konfigurálva.
Módosíthatja a szabályzatot egy későbbi időpontban, szükség lesz. Ebben a rövid útmutatóban egy néhány perces késés várható.

1. Az Azure Portalon az event grid alatt jelenik meg a megnövekedett tevékenység az alkalmazás futása közben.

    ![Event grid-grafikon](media/ingest-data-event-grid/event-grid-graph.png)

1. A következő lekérdezés a tesztadatbázison való futtatásával ellenőrizze, hogy hány üzenet került át eddig a pillanatig az adatbázisba.

    ```Kusto
    TestTable
    | count
    ```

1. Az üzenetek a tartalom megtekintéséhez futtassa a következő lekérdezést a test-adatbázisban.

    ```Kusto
    TestTable
    ```

    Az eredményhalmaznak a következőhöz hasonlóan kell kinéznie.

    ![Üzenetek eredményhalmaza](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné újra használni az event griddel, tisztítása **test-hub-rg**költségek elkerülése érdekében.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen, majd a létrehozott erőforráscsoport.  

    Ha a bal oldali menü össze van csukva, kattintson a ![Kibontás gombra](media/ingest-data-event-grid/expand.png) a kinyitásához.

   ![A törölni kívánt erőforráscsoport kiválasztása](media/ingest-data-event-grid/delete-resources-select.png)

1. A **test-resource-group** alatt válassza az **Erőforráscsoport törlése** elemet.

1. Az új ablakban adja meg a törölni kívánt erőforráscsoport nevét (*test-hub-rg*), majd válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: Az Azure Data Explorer adatok lekérdezése](web-query-data.md)
