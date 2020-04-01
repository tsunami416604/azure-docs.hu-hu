---
title: Adatok betöltése az Event Hubról az Azure Data Explorerbe
description: Ebben a cikkben megtudhatja, hogyan töltheti be (töltheti be) az adatokat az Azure Data Explorerbe az Event Hubról.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: bce92eeed669628fa1b6318abd6b0c13f7e84848
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411207"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Adatok betöltése az Event Hubról az Azure Data Explorerbe

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-hub-resource-manager.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer adatbetöltési lehetőséget tesz elérhetővé az Event Hubsból, amely egy big data-streamelési platform és eseményfeldolgozó szolgáltatás. [Az Event Hubs](/azure/event-hubs/event-hubs-about) másodpercenként több millió eseményt képes feldolgozni közel valós időben. Ebben a cikkben hozzon létre egy eseményközpontot, csatlakozzon hozzá az Azure Data Explorerből, és tekintse meg a rendszeren keresztüli adatáramlást.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Tesztfürt és adatbázis](create-cluster-database-portal.md).
* [Egy mintaalkalmazás,](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) amely adatokat hoz létre, és elküldi azokat egy eseményközpontba. Töltse le a mintaalkalmazást a rendszerére.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) a mintaalkalmazás futtatásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ebben a cikkben példát hoz létre, és küldje el őket egy eseményközpontba. Első lépésként létre kell hoznia egy eseményközpontot. Ezt egy Azure Resource Manager-sablon használatával teheti meg az Azure Portalon.

1. Eseményközpont létrehozásához használja a következő gombot a központi telepítés elindításához. Kattintson a jobb gombbal, és válassza **a Megnyitás új ablakban**parancsot, így a cikk ben ismertetett további lépéseket követheti.

    [![Üzembe helyezés az Azure-ban](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Az **Üzembe helyezés az Azure-ban** gombra kattintva megnyílik az Azure Portal, ahol egy üzembehelyezési űrlapot kell kitöltenie.

    ![Üzembe helyezés az Azure-ban](media/ingest-data-event-hub/deploy-to-azure.png)

1. Válassza ki azt az előfizetést, amelyben az eseményközpontot létre kívánja hozni, és hozzon létre egy *test-hub-rg* nevű erőforráscsoportot.

    ![Erőforráscsoport létrehozása](media/ingest-data-event-hub/create-resource-group.png)

1. Adja meg az alábbi adatokat az űrlapon.

    ![Üzembehelyezési űrlap](media/ingest-data-event-hub/deployment-form.png)

    Az alábbi táblázatban fel nem sorolt beállítások esetében használja az alapértelmezett értéket.

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki az eseményközponthoz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *test-hub-rg* | Hozzon létre egy új erőforráscsoportot. |
    | Hely | *USA nyugati régiója* | A cikkhez válassza a *Nyugat-USA* lehetőséget. Éles üzemben az igényeinek leginkább megfelelő régiót válassza. Hozza létre az eseményközpont névtere ugyanazon a helyen, mint a Kusto-fürt a legjobb teljesítmény érdekében (a legfontosabb a nagy átviteli sebességű eseményközpont-névterek esetében).
    | Névtér neve | A névtér egyedi neve | Válasszon egy egyedi nevet a névtér azonosításához. Például: *mytestnamespace*. A rendszer hozzáfűzi a *servicebus.windows.net* tartománynevet a megadott névhez. A név csak betűket, számokat és kötőjelet tartalmazhat. A névnek betűvel kell kezdődnie, és betűvel vagy számmal kell végződnie. Az érték 6 és 50 karakter közötti hosszúságú lehet.
    | Event Hubs neve | *test-hub* | Az eseményközpont a névtéren belül helyezkedik el, ami egy egyedi hatókörkezelési tárolóként szolgál. Az eseményközpont nevének egyedinek kell lennie a névtéren belül. |
    | Fogyasztói csoport neve | *test-group* | A fogyasztói csoportokkal több fogyasztói alkalmazás is rendelkezhet az eseménystream külön nézetével. |
    | | |

1. Válassza a **Vásárlás** lehetőséget, amivel megerősíti, hogy erőforrásokat kíván létrehozni az előfizetésben.

1. Az eszköztáron válassza az **Értesítések** elemet az üzembehelyezési folyamat nyomon követéséhez. A sikeres üzembe helyezés igénybe vehet néhány percet, azonban addig is folytathatja a következő lépéssel.

    ![Értesítések](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Céltábla létrehozása az Azure Data Explorerben

Most létrehozunk egy táblát az Azure Data Explorerben, amelyre az Event Hubs az adatokat továbbítja majd. A táblát az **Előfeltételek** szakaszban lefoglalt fürtön és adatbázisban hozzuk létre.

1. Az Azure Portalon keresse meg a fürtöt, majd válassza a **Lekérdezés** elemet.

    ![Alkalmazáshivatkozás lekérdezése](media/ingest-data-event-hub/query-explorer-link.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget a bevitt adatokat fogadó tábla (TestTable) létrehozásához.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Létrehozási lekérdezés futtatása](media/ingest-data-event-hub/run-create-query.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget, ha a bejövő JSON-adatokat a tábla oszlopnevéhez és adattípusához (TestTable) szeretné leképezni.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp", "Properties": {"Path": "$.timeStamp"}},{"column":"Name", "Properties": {"Path":"$.name"}} ,{"column":"Metric", "Properties": {"Path":"$.metric"}}, {"column":"Source", "Properties": {"Path":"$.source"}}]'
    ```

## <a name="connect-to-the-event-hub"></a>Csatlakozás az eseményközponthoz

Most csatlakozzon az eseményközponthoz az Azure Data Explorerből. Ha ez a kapcsolat létrejött, az eseményközpontba érkező adatok a cikk korábbi részében létrehozott teszttáblába kerülnek.

1. Az eszközsáv **Értesítések** elemének kiválasztásával győződjön meg arról, hogy az eseményközpont üzembe helyezése sikeresen megtörtént.

1. A létrehozott fürt alatt válassza az **Adatbázisok**, majd a **TestDatabase** lehetőséget.

    ![Tesztadatbázis kiválasztása](media/ingest-data-event-hub/select-test-database.png)

1. Válassza **az Adatbetöltés** és **az Adatkapcsolat hozzáadása**lehetőséget. Ezután töltse ki az űrlapot a következő információkkal. Ha végzett, válassza a **Létrehozás** gombot.

    ![Eseményközpont-kapcsolat](media/ingest-data-event-hub/event-hub-connection.png)

    **Adatforrás:**

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatkapcsolat neve | *test-hub-connection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | Eseményközpont-névtér | A névtér egyedi neve | A korábban a névtér azonosításához választott név. |
    | Eseményközpont | *test-hub* | A létrehozott eseményközpont. |
    | Fogyasztói csoport | *test-group* | A létrehozott eseményközponton definiált fogyasztói csoport. |
    | Az eseményrendszer tulajdonságai | Válassza ki a megfelelő tulajdonságokat | Az [Event Hub rendszer tulajdonságai](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Ha eseményüzenetenként több rekord van, a rendszertulajdonságok hozzáadódnak az elsőhöz. Rendszertulajdonságok hozzáadásakor [hozzon létre](/azure/kusto/management/create-table-command) vagy [frissítsen](/azure/kusto/management/alter-table-command) táblasémát, és [képezképezést](/azure/kusto/management/mappings) a kijelölt tulajdonságokhoz. |
    | Tömörítés | *Nincs* | Az Event Hub üzenetek hasznos adatának tömörítési típusa. Támogatott tömörítési típusok: *Nincs, GZip*.|
    | | |

    **Céltábla:**

    A bevitt adatok útválasztása két lehetőség közül választhat: *statikus* és *dinamikus.* 
    Ebben a cikkben statikus útválasztást kell használnia, ahol megadhatja a tábla nevét, adatformátumát és leképezését. Ezért hagyja a **Saját adatok útválasztási adatait** bejelölve.

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *TestTable* | A **TestDatabase** adatbázisban létrehozott tábla. |
    | Adatformátum | *JSON* | Támogatott formátumok: Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE, TXT, ORC és PARQUET. |
    | Oszlopleképezés | *TestMapping* | A **TestDatabase**alkalmazásban létrehozott [leképezés](/azure/kusto/management/mappings) , amely a bejövő JSON-adatokat a TestTable oszlopnevéhez és adattípusához **rendeli hozzá.** JSON vagy MULTILINE JSON esetén, más formátumok esetén pedig nem kötelező.|
    | | |

    > [!NOTE]
    > * Válassza **a Saját adatok között szerepelnek útválasztási adatok** dinamikus útválasztás, ahol az adatok tartalmazzák a szükséges útválasztási információkat, mint a [minta alkalmazás](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) megjegyzéseket. Ha mind a statikus, mind a dinamikus tulajdonságok be vannak állítva, a dinamikus tulajdonságok felülírják a statikus tulajdonságokat. 
    > * Csak az adatkapcsolat létrehozása után várólistára helyezett események kerülnek betöltésre.
    > * A tömörítés típusát a mintaalkalmazásban látható dinamikus tulajdonságokon keresztül is [beállíthatja.](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)
    > * Avro, ORC és PARQUET formátumok, valamint az eseményrendszer tulajdonságai nem támogatottak a GZip tömörítési hasznos adattal.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="copy-the-connection-string"></a>A kapcsolati sztring másolása

Amikor elindítja az Előfeltételek között szereplő [mintaalkalmazást](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), szüksége lesz az eseményközpont névterének kapcsolati sztringjére.

1. A létrehozott eseményközpont-névtér alatt válassza a **Megosztott elérési szabályzatok**, majd a **RootManageSharedAccessKey** lehetőséget.

    ![Megosztott elérési házirendek](media/ingest-data-event-hub/shared-access-policies.png)

1. **Kapcsolati karakterlánc**másolása - elsődleges kulcs . A következő szakaszban kell beillesztenie.

    ![Kapcsolati sztring](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Használja a letöltött [mintaalkalmazást](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) az adatok létrehozásához.

1. Nyissa meg a mintaalkalmazást a Visual Studióban.

1. A *program.cs* fájlban cserélje le a `connectionString` konstanst az eseményközpont-névtérből kimásolt kapcsolati sztringre.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Hozza létre és futtassa az alkalmazást. Az alkalmazás üzeneteket küld az eseményközpontba, és 10 másodpercenként megjeleníti az állapotot.

1. Miután az alkalmazás már küldött néhány üzenetet, lépjen tovább a következő lépésre: tekintse át az adatok az eseményközpontba, majd a teszttáblába való áramlását.

## <a name="review-the-data-flow"></a>Az adatfolyam áttekintése

Most, hogy az alkalmazás adatokat állít elő, láthatja, ahogy ezek az adatok az eseményközpontból a fürtön található táblába áramlanak.

1. Az Azure Portalon az eseményközpont alatt megfigyelheti a tevékenységcsúcsot, amíg az alkalmazás fut.

    ![Eseményközpont diagramja](media/ingest-data-event-hub/event-hub-graph.png)

1. A következő lekérdezés a tesztadatbázison való futtatásával ellenőrizze, hogy hány üzenet került át eddig a pillanatig az adatbázisba.

    ```Kusto
    TestTable
    | count
    ```

1. Az üzenetek tartalmának megtekintéséhez futtassa a következő lekérdezést:

    ```Kusto
    TestTable
    ```

    Az eredményhalmaznak a következőkre kell hasonlítania:

    ![Üzenetek eredményhalmaza](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Az Azure Data Explorer rendelkezik egy aggregációs (kötegelési) szabályzat adatbetöltési, célja, hogy optimalizálja a betöltési folyamat. A házirend alapértelmezés szerint 5 percre vagy 500 MB-ra van konfigurálva, így késésléphet. Az [batching policy](/azure/kusto/concepts/batchingpolicy) összesítési beállításokat a kötegelési házirendben láthatja. 
    > * Az Event Hub betöltése 10 másodperces vagy 1 MB-os eseményközpont-válaszidőt tartalmaz. 
    > * Állítsa be a táblát, hogy támogassa a streamelést, és távolítsa el a válaszidő késését. Lásd a [streamelési házirendet.](/azure/kusto/concepts/streamingingestionpolicy) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi, hogy továbbra is használja, a költségek elkerülése érdekében törölje a **test-hub-rg** erőforráscsoportot.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen, majd a létrehozott erőforráscsoport.  

    Ha a bal oldali menü össze van csukva, kattintson a ![Kibontás gombra](media/ingest-data-event-hub/expand.png) a kinyitásához.

   ![A törölni kívánt erőforráscsoport kiválasztása](media/ingest-data-event-hub/delete-resources-select.png)

1. A **test-resource-group** alatt válassza az **Erőforráscsoport törlése** elemet.

1. Az új ablakban írja be a törölni kívánt erőforráscsoport nevét (*test-hub-rg*), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
