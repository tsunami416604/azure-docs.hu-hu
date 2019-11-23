---
title: Az Azure Adatkezelő vezérlési parancsainak használata Azure Data Factory
description: Ebben a témakörben az Azure Adatkezelő Control parancsait használja Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264486"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Azure Data Factory Command tevékenység használata az Azure Adatkezelő Control parancsok futtatásához

A [Azure Data Factory](/azure/data-factory/) (ADF) egy felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi tevékenységek kombinációjának végrehajtását az adatokon. Az ADF használatával adatvezérelt munkafolyamatokat hozhat létre az adatáthelyezés és az adatátalakítás előkészítéséhez és automatizálásához. A Azure Data Factory **azure adatkezelő parancsának** tevékenysége lehetővé teszi az [Azure adatkezelő-vezérlési parancsok](/azure/kusto/concepts/#control-commands) futtatását egy ADF-munkafolyamaton belül. Ebből a cikkből megtudhatja, hogyan hozhat létre egy olyan folyamatot, amely egy Azure Adatkezelő Command tevékenységet tartalmazó keresési tevékenységgel és ForEach tevékenységgel rendelkezik.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Azure Adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md)
* Adatforrás.
* [Egy adatfeldolgozó](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Új folyamat létrehozása

1. Válassza ki a **szerzői** ceruza eszközt. 
1. Hozzon létre egy új folyamatot a **+** kiválasztásával, majd válassza a **folyamat** lehetőséget a legördülő menüből.

   ![új folyamat létrehozása](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Keresési tevékenység létrehozása

A [keresési tevékenység](/azure/data-factory/control-flow-lookup-activity) bármilyen Azure Data Factory által támogatott adatforrásból lekérhet egy adatkészletet. A keresési tevékenység kimenete ForEach vagy más tevékenységben is használható.

1. A **tevékenységek** ablaktábla **általános**területén válassza ki a **keresési** tevékenységet. Húzza át a fő vászonra a jobb oldalon.
 
    ![keresési tevékenység kiválasztása](media/data-factory-command-activity/select-activity.png)

1. A vászon most már tartalmazza a létrehozott keresési tevékenységet. A vászon alatti lapokat a megfelelő paraméterek módosításához használhatja. **Általában**nevezze át a tevékenységet. 

    ![keresési tevékenység szerkesztése](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Kattintson az üres vászonra a folyamat tulajdonságainak megtekintéséhez. A folyamat átnevezéséhez használja az **általános** lapot. A folyamat neve: *pipeline-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Azure Adatkezelő adatkészlet létrehozása keresési tevékenységben

1. A **Beállítások**területen válassza ki az előre létrehozott Azure adatkezelő **forrás-adatkészletet**, vagy válassza az **+ új** lehetőséget egy új adatkészlet létrehozásához.
 
    ![adatkészlet hozzáadása a keresési beállításokban](media/data-factory-command-activity/lookup-settings.png)

1. Válassza ki az **Azure adatkezelő (Kusto)** adatkészletet az **új adatkészlet** ablakból. Válassza a **Folytatás** lehetőséget az új adatkészlet hozzáadásához.

   ![új adatkészlet kiválasztása](media/data-factory-command-activity/select-new-dataset.png) 

1. Az új Azure Adatkezelő adatkészlet-paraméterek a **beállításokban**láthatók. A paraméterek frissítéséhez válassza a **Szerkesztés**lehetőséget.

    ![keresési beállítások az Azure Adatkezelő adatkészlettel](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Megnyílik a **AzureDataExplorerTable** új lap a fő vásznon. 
    * Válassza az **általános** lehetőséget, és szerkessze az adatkészlet nevét. 
    * Válassza a **Kapcsolódás** lehetőséget az adatkészlet tulajdonságainak szerkesztéséhez. 
    * Válassza ki a **társított szolgáltatást** a legördülő menüből, vagy válassza az **+ új** lehetőséget egy új társított szolgáltatás létrehozásához.

    ![Az Azure Adatkezelő adatkészlet tulajdonságainak szerkesztése](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Új társított szolgáltatás létrehozásakor megnyílik az **új társított szolgáltatás (Azure adatkezelő)** lap:

    ![ADX új társított szolgáltatás](media/data-factory-command-activity/adx-new-linked-service.png)

   * Válassza ki az Azure Adatkezelő társított szolgáltatás **nevét** . Szükség esetén adja hozzá a **leírást** .
   * A **Csatlakozás integrációs**modulon keresztül beállításnál módosítsa az aktuális beállításokat, ha szükséges. 
   * A **fiók kiválasztása módszernél** válassza ki a fürtöt a két módszer egyikének használatával: 
        * Válassza az **Azure-előfizetések** választógombot, és válassza ki az **Azure-előfizetési** fiókját. Ezután válassza ki a **fürtöt**. Vegye figyelembe, hogy a legördülő lista csak a felhasználóhoz tartozó fürtöket sorolja fel.
        * Ehelyett válassza a **manuális megadása** választógombot, és adja meg a **végpontot** (a fürt URL-címét).
    * A **bérlő**meghatározása.
    * Adja meg az **egyszerű szolgáltatásnév azonosítóját**. A résztvevő AZONOSÍTÓjának megfelelő engedélyekkel kell rendelkeznie a parancs által igényelt jogosultsági szintnek megfelelően.
    * Válassza ki az **egyszerű szolgáltatásnév** gombot, és adja meg az **egyszerű szolgáltatásnév kulcsát**.
    * Válassza ki az **adatbázist** a legördülő menüből. Másik lehetőségként válassza a **Szerkesztés** jelölőnégyzetet, és adja meg az adatbázis nevét.
    * Válassza a **kapcsolat tesztelése** lehetőséget a létrehozott társított szolgáltatás kapcsolatának teszteléséhez. Ha csatlakozni tud a telepítőhöz, akkor a zöld pipa- **kapcsolat sikeresen** megjelenik.
    * Válassza a **Befejezés** lehetőséget a társított szolgáltatás létrehozásának befejezéséhez.

1. Miután beállított egy társított szolgáltatást, a **AzureDataExplorerTable** > **kapcsolat**területen adja hozzá a **Táblanév** nevet. Válassza az **előnézeti**adatforrások lehetőséget, hogy meggyőződjön arról, hogy az adott adatmegjelenítés megfelelően jelenik meg.

   Az adatkészlet most már készen áll, és továbbra is szerkesztheti a folyamatát.

### <a name="add-a-query-to-your-lookup-activity"></a>Lekérdezés hozzáadása a keresési tevékenységhez

1. A (z) **-4 – docs** > **beállításaiban** adjon hozzá egy lekérdezést a **lekérdezés** szövegmezőben, például:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Szükség szerint módosítsa a **lekérdezés időtúllépését** vagy a **csonkítás** és az **első sor csak** tulajdonságokat. Ebben a folyamatban megtartjuk az alapértelmezett **lekérdezési időkorlátot** , és töröljük a jelölőnégyzeteket. 

    ![Keresési tevékenység végső beállításai](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Minden tevékenység létrehozása 

A [for-each](/azure/data-factory/control-flow-for-each-activity) tevékenység használatával megismételhető egy gyűjtemény, és a megadott tevékenységek egy hurokban hajthatók végre. 

1. Most adjon hozzá egy for-each tevékenységet a folyamathoz. Ez a tevékenység a keresési tevékenység által visszaadott adatok feldolgozását fogja feldolgozni. 
    * A **tevékenységek** ablaktáblán az **iteráció & a feltételesség**területen válassza ki a **foreach** tevékenységet, és húzza a vászonra.
    * Rajzoljon egy vonalat a keresési tevékenység kimenete és a ForEach tevékenység bemenete között a vásznon a kapcsolódáshoz.

        ![ForEach tevékenység](media/data-factory-command-activity/for-each-activity.png)

1.  Válassza ki a ForEach tevékenységet a vásznon. Az alábbi **Beállítások** lapon:
    * A keresési eredmények szekvenciális feldolgozásához jelölje be a **szekvenciális** jelölőnégyzetet, vagy hagyja, hogy a rendszer törölje a jelet a párhuzamos feldolgozás létrehozásához.
    * Adja meg a **kötegek darabszámát**.
    * Az **elemek**területen adja meg a következő kimeneti értékre mutató hivatkozást: *@activity("Lookup1"). output. Value*

       ![ForEach tevékenység beállításai](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Azure Adatkezelő parancs-tevékenység létrehozása a ForEach tevékenységen belül

1. Kattintson duplán a vászon ForEach tevékenységére, hogy megnyissa azt egy új vásznon a ForEach belüli tevékenységek megadásához.
1. A **tevékenységek** ablaktábla **Azure adatkezelő**területén válassza ki az **Azure adatkezelő parancs** tevékenységét, és húzza a vászonra.

    ![Azure Adatkezelő parancs tevékenység](media/data-factory-command-activity/adx-command-activity.png)

1.  A **kapcsolat** lapon válassza ki ugyanazt a társított szolgáltatást, amelyet korábban hozott létre.

    ![Az Azure adatkezelő parancs tevékenységének kapcsolatai lap](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. A **parancs** lapon adja meg a következő parancsot:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    A **parancs** arra utasítja az Azure Adatkezelőot, hogy tömörített formátumban exportálja egy adott lekérdezés eredményét egy blob-tárolóba. Aszinkron módon fut (az aszinkron módosító használatával).
    A lekérdezés a keresési tevékenység eredményének egyes sorainak adatbázis oszlopát kezeli. A **parancs időtúllépése** változatlan marad.

    ![Parancs tevékenység](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > A parancs tevékenységének korlátai a következők:
    > * Méretkorlát: 1 MB válasz mérete
    > * Időkorlát: 20 perc (alapértelmezett), 1 óra (maximum).
    > * Ha szükséges, fűzze hozzá az eredményhez egy lekérdezést az [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands)használatával az eredményül kapott méret/idő csökkentése érdekében.

1.  A folyamat most már készen áll. A folyamat nevére kattintva visszatérhet a fő folyamat nézetéhez.

    ![Azure Adatkezelő parancssori folyamat](media/data-factory-command-activity/adx-command-pipeline.png)

1. A folyamat közzététele előtt válassza a **hibakeresés** lehetőséget. A folyamat előrehaladását a **kimenet** lapon lehet figyelni.

    ![Az Azure adatkezelő parancs tevékenységének kimenete](media/data-factory-command-activity/command-activity-output.png)

1. **Közzéteheti az összes** , majd a **trigger hozzáadásával** is futtathatja a folyamatot. 

## <a name="control-command-outputs"></a>Parancs kimenetének vezérlése

A parancs tevékenység kimenetének szerkezete alább látható. Ezt a kimenetet a folyamat következő tevékenysége használhatja.

### <a name="returned-value-of-a-non-async-control-command"></a>Nem aszinkron vezérlési parancs visszaadott értéke

A nem aszinkron vezérlési parancsokban a visszaadott érték szerkezete hasonló a keresési tevékenység eredményének struktúrájához. A `count` mező a visszaadott rekordok számát jelzi. A rögzített tömb mező `value` a rekordok listáját tartalmazza. 

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
 
### <a name="returned-value-of-an-async-control-command"></a>Aszinkron vezérlő parancs visszaadott értéke

Egy aszinkron vezérlési parancsban a tevékenység lekérdezi az operatív táblázatot a jelenetek mögött, amíg az aszinkron művelet be nem fejeződik, vagy időtúllépés történik. Ezért a visszaadott érték a megadott **OperationId** tulajdonság `.show operations OperationId` eredményét fogja tartalmazni. Ellenőrizze az **állapot** és **állapot** tulajdonságok értékeit a művelet sikeres befejezésének ellenőrzéséhez.

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

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [másolhat adatait az Azure Adatkezelő Azure Data Factory használatával](data-factory-load-data.md).
* További információ az [adatbázisból az adatkezelő Azure-ba való tömeges másoláshoz Azure Data Factory sablon](data-factory-template.md)használatával.
