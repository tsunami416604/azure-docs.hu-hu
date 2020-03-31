---
title: Az Azure Data Explorer vezérlőparancsainak használata az Azure Data Factoryban
description: Ebben a témakörben használja az Azure Data Explorer vezérlőparancsait az Azure Data Factoryban
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264486"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Az Azure Data Factory parancstevékenységének használata az Azure Data Explorer vezérlőparancsainak futtatásához

[Az Azure Data Factory](/azure/data-factory/) (ADF) egy felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi, hogy az adatokon végzett tevékenységek kombinációját hajtsa végre. Az ADF segítségével adatalapú munkafolyamatokat hozhat létre az adatok mozgatásának és az adatok átalakításának koordinálására és automatizálására. Az **Azure Data Explorer command** tevékenység az Azure Data Factory lehetővé teszi, hogy az Azure Data Explorer [vezérlőparancsok at](/azure/kusto/concepts/#control-commands) ADF-munkafolyamaton belül futtathatja. Ez a cikk bemutatja, hogyan hozhat létre egy folyamatot egy keresőtevékenységgel és egy Azure Data Explorer parancstevékenységet tartalmazó ForEach tevékenységtel.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Azure Data Explorer-fürt és -adatbázis](create-cluster-database-portal.md)
* Adatforrás.
* [Adatgyár](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Új folyamat létrehozása

1. Válassza a **Ceruza készítése** eszközt. 
1. Hozzon létre egy **+** új folyamatot a legördülő menü **folyamatának** kiválasztásával.

   ![új folyamat létrehozása](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Keresstevékenységet

A [keresés tevékenység](/azure/data-factory/control-flow-lookup-activity) lekérheti az adatkészletbármely Azure Data Factory által támogatott adatforrások. A kimenet a lookup tevékenység használható a ForEach vagy más tevékenység.

1. A **Tevékenységek** ablaktábla **Általános**területén válassza a **Kereskövetési** tevékenység lehetőséget. Húzza a jobb oldali fővászonra.
 
    ![követési tevékenység kiválasztása](media/data-factory-command-activity/select-activity.png)

1. A vászon most már tartalmazza a létrehozott keresést. A vászon alatti fülek segítségével módosíthatja a vonatkozó paramétereket. **Általában**nevezze át a tevékenységet. 

    ![követési tevékenység szerkesztése](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Kattintson az üres vászonterületre a csővezeték tulajdonságainak megtekintéséhez. Az **Általános** lapon nevezheti át a folyamatot. A csővezeték ünk neve *pipeline-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Azure Data Explorer-adatkészlet létrehozása a keresőtevékenységben

1. A **Beállítások párbeszédpanelen**válassza ki az előre létrehozott Azure Data Explorer **adatforrás-adatkészletet,** vagy válassza **a + Új** lehetőséget új adatkészlet létrehozásához.
 
    ![adatkészlet hozzáadása a keresgaszínbeállításokban](media/data-factory-command-activity/lookup-settings.png)

1. Válassza ki az **Azure Data Explorer (Kusto)** adatkészlet et **az Új adatkészlet** ablakból. Az új adatkészlet hozzáadásához válassza a **Folytatás** lehetőséget.

   ![új adatkészlet kiválasztása](media/data-factory-command-activity/select-new-dataset.png) 

1. Az Azure Data Explorer új adatkészlet-paraméterei láthatók a **Beállítások**menüben. A paraméterek frissítéséhez válassza a **Szerkesztés**lehetőséget.

    ![keressbeállításokat az Azure Data Explorer adatkészlettel](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Megnyílik az **AzureDataExplorerTable** új lapja a fővásznon. 
    * Válassza **az Általános** lehetőséget, és szerkesztheti az adatkészlet nevét. 
    * Válassza **a Kapcsolat** lehetőséget az adatkészlet tulajdonságainak szerkesztéséhez. 
    * Válassza ki a **Csatolt szolgáltatást** a legördülő menüből, vagy válassza a **+ Új** lehetőséget új csatolt szolgáltatás létrehozásához.

    ![Az Azure Data Explorer adatkészlet tulajdonságainak szerkesztése](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Új csatolt szolgáltatás létrehozásakor megnyílik az **Új csatolt szolgáltatás (Azure Data Explorer)** lap:

    ![ADX új csatolt szolgáltatás](media/data-factory-command-activity/adx-new-linked-service.png)

   * Válassza az Azure Data Explorer csatolt **szolgáltatásának nevét.** **Szükség esetén adja hozzá a leírást.**
   * A **Connect via integrációs futásidejű**, módosítsa az aktuális beállításokat, ha szükséges. 
   * A **Fiókkiválasztási módszerben** válassza ki a fürtöt a következő két módszer egyikével: 
        * Válassza ki az **Azure-előfizetésből** választógombot, és válassza ki az **Azure-előfizetési** fiókját. Ezután válassza ki a **fürtöt**. Megjegyzés: a legördülő lista csak a felhasználóhoz tartozó fürtöket sorolja fel.
        * Ehelyett válassza a Kézi választógomb **megadása** lehetőséget, és adja meg a **végpontot** (fürt URL-címét).
    * Adja meg a **bérlőt.**
    * Adja meg **az egyszerű szolgáltatásazonosítót.** A fő azonosítónak rendelkeznie kell a megfelelő engedélyekkel, a használt parancs által megkövetelt jogosultsági szintnek megfelelően.
    * Válassza **az Egyszerű szolgáltatáskulcs** gombot, és írja be **az Egyszerű szolgáltatáskulcsot**.
    * Válassza ki az **adatbázist** a legördülő menüből. Másik lehetőségként jelölje be **a Szerkesztés** jelölőnégyzetet, és írja be az adatbázis nevét.
    * Válassza **a Kapcsolat tesztelése** lehetőséget a létrehozott kapcsolt szolgáltatáskapcsolat teszteléséhez. Ha tud csatlakozni a beállításhoz, zöld pipa jelenik meg **a Sikeres kapcsolat** jellel.
    * A csatolt szolgáltatás létrehozásának befejezéséhez válassza a **Befejezés** lehetőséget.

1. Miután beállítottegy csatolt szolgáltatást, az **AzureDataExplorerTable-kapcsolatban** > **Connection**adja hozzá **a táblanevét.** Válassza **az Adatok előnézete**lehetőséget, hogy az adatok megfelelően jelenjenek meg.

   Az adatkészlet most már készen áll, és folytathatja a folyamat szerkesztését.

### <a name="add-a-query-to-your-lookup-activity"></a>Lekérdezés hozzáadása a hirdetési tevékenységhez

1. A **4-docs** > **folyamatban a beállítások** lekérdezést **adnak** a Lekérdezés szövegmezőjébe, például:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Szükség szerint módosítsa a **Lekérdezés időhamát,** vagy **a Nincs csonkolás** és az Első sor **csak** tulajdonságát. Ebben a folyamatban megtartjuk az alapértelmezett **lekérdezési időoutot,** és törölje a jelölőnégyzeteket. 

    ![A külső tevékenység végső beállításai](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Minden tevékenység létrehozása 

A [For-Each](/azure/data-factory/control-flow-for-each-activity) tevékenység egy gyűjtemény ismétlésére és egy adott tevékenységek ciklusban történő végrehajtására szolgál. 

1. Most hozzáad egy minden tevékenységhez a folyamathoz. Ez a tevékenység feldolgozza a lookup tevékenységből visszaadott adatokat. 
    * A **Tevékenységek** ablaktábla **Iterációs & feltételes ek**csoportban jelölje ki a **ForEach** tevékenységet, és húzza a vászonra.
    * Rajzoljon egy vonalat a lookup tevékenység kimenete és a Vásznon lévő ForEach tevékenység bemenete között, hogy összekösse őket.

        ![ForEach tevékenység](media/data-factory-command-activity/for-each-activity.png)

1.  Jelölje ki a ForEach tevékenységet a vásznon. Az alábbi **Beállítások** lapon:
    * Jelölje be a **Szekvenciális** jelölőnégyzetet, hogy nem lesz-e bejelölve a keresési eredmények feldolgozása, vagy hagyja bejelölve a párhuzamos feldolgozás létrehozásához.
    * **Batch count**beállítása .
    * A **Tételek területen**adja meg a következő hivatkozást a kimeneti értékre: * @activity("Lookup1").output.value*

       ![ForEach tevékenység beállításai](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Hozzon létre egy Azure Data Explorer parancs tevékenység a ForEach tevékenység

1. Kattintson duplán a ForEach tevékenységre a vásznon, hogy megnyissa egy új vásznon a ForEach tevékenységének megadásához.
1. A **Tevékenységek** ablaktáblán az **Azure Data Explorer**csoportban válassza ki az Azure Data Explorer **parancstevékenységét,** és húzza a vászonra.

    ![Az Azure Data Explorer parancstevékenysége](media/data-factory-command-activity/adx-command-activity.png)

1.  A **Kapcsolat** lapon jelölje ki ugyanazt a korábban létrehozott csatolt szolgáltatást.

    ![Az Azure Data Explorer parancstevékenység-kapcsolat lapja](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. A **Parancs** lapon adja meg a következő parancsot:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    A **parancs** arra utasítja az Azure Data Explorert, hogy exportálja egy adott lekérdezés eredményeit egy blobtárolóba, tömörített formátumban. Aszinkron módon fut (az aszinkron módosítóval).
    A lekérdezés a lekérdezés a lekérdezés eredményének minden egyes sorának adatbázis-oszlopával foglalkozik. A **parancs időmeghosszabbítása** változatlan maradhat.

    ![parancstevékenység](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > A parancstevékenység a következő korlátokkal rendelkezik:
    > * Méretkorlát: 1 MB-os válaszméret
    > * Időkorlát: 20 perc (alapértelmezett), 1 óra (maximum).
    > * Szükség esetén az [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands)használatával hozzáfűzhet egy lekérdezést az eredményhez az eredményhirdetés méretének/idejének csökkentéséhez.

1.  Most a csővezeték készen áll. A fő folyamatnézetre a folyamat nevére kattintva térhet vissza.

    ![Az Azure Data Explorer parancsfolyamata](media/data-factory-command-activity/adx-command-pipeline.png)

1. A folyamat közzététele előtt válassza a **Debug** lehetőséget. A folyamat előrehaladása a **Kimenet** lapon figyelhető.

    ![Az Azure Data Explorer parancstevékenység-kimenete](media/data-factory-command-activity/command-activity-output.png)

1. Az **összes közzététele,** majd az **Eseményindító hozzáadása** a folyamat futtatásához. 

## <a name="control-command-outputs"></a>Vezérlőparancs kimenetei

A parancstevékenység-kimenet szerkezetét az alábbiakban részletezzük. Ezt a kimenetet a folyamat következő tevékenysége használhatja.

### <a name="returned-value-of-a-non-async-control-command"></a>Nem aszinkron vezérlőparancs visszaadott értéke

A nem aszinkron vezérlőparancsban a visszaadott érték szerkezete hasonló a lookup tevékenység eredményének szerkezetéhez. A `count` mező a visszaadott rekordok számát jelzi. A rögzített `value` tömbmező rekordok listáját tartalmazza. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Egy async vezérlőparancs visszaadott értéke

Az aszinkron vezérlési parancsban a tevékenység lekérdezi a műveleti táblázatot a színfalak mögött, amíg az aszinkron művelet be nem fejeződik, vagy időtúlóráznem lesz. Ezért a visszaadott érték az `.show operations OperationId` adott **OperationId** tulajdonság eredményét fogja tartalmazni. Ellenőrizze az **Állapot** és **állapot** tulajdonságok értékeit a művelet sikeres befejezésének ellenőrzéséhez.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [másolhat adatokat az Azure Data Explorerbe az Azure Data Factory használatával.](data-factory-load-data.md)
* Ismerje meg, hogyan használja az [Azure Data Factory sablont az adatbázisból az Azure Data Explorerbe történő tömeges másoláshoz.](data-factory-template.md)
