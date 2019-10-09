---
title: Adatok beolvasása az Event hub-ből az Azure-ba Adatkezelő
description: Ebből a cikkből megtudhatja, hogyan végezheti el az adatok betöltését az Azure Adatkezelőba az Event hub-ból.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 102cfa81c6093ff1aeefdd8d1937143a25cf76f5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028490"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Adatok beolvasása az Event hub-ből az Azure-ba Adatkezelő

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer adatbetöltési lehetőséget tesz elérhetővé az Event Hubsból, amely egy big data-streamelési platform és eseményfeldolgozó szolgáltatás. A [Event Hubs](/azure/event-hubs/event-hubs-about) másodpercenként több millió eseményt képes feldolgozni a közel valós időben. Ebben a cikkben létrehoz egy Event hubot, csatlakozik hozzá az Azure Adatkezelő, és megtekintheti az adatfolyamot a rendszeren keresztül.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

* [Egy tesztelési fürt és adatbázis](create-cluster-database-portal.md).

* [Egy minta alkalmazás](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , amely adatokat hoz létre, és elküldi azt egy Event hub-nak. Töltse le a minta alkalmazást a rendszeren.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) a minta alkalmazás futtatásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ebben a cikkben mintaadatok létrehozásához és az Event hub-hoz való elküldéséhez. Első lépésként létre kell hoznia egy eseményközpontot. Ezt egy Azure Resource Manager-sablon használatával teheti meg az Azure Portalon.

1. Az Event hub létrehozásához használja a következő gombot a központi telepítés elindításához. Kattintson a jobb gombbal, és válassza a **Megnyitás új ablakban**lehetőséget, így követheti a cikk további lépéseit.

    [![Üzembe helyezés az Azure-ban](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Az **Üzembe helyezés az Azure-ban** gombra kattintva megnyílik az Azure Portal, ahol egy üzembehelyezési űrlapot kell kitöltenie.

    ![Üzembe helyezés az Azure-ban](media/ingest-data-event-hub/deploy-to-azure.png)

1. Válassza ki azt az előfizetést, amelyben az eseményközpontot létre kívánja hozni, és hozzon létre egy *test-hub-rg* nevű erőforráscsoportot.

    ![Hozzon létre egy erőforráscsoportot](media/ingest-data-event-hub/create-resource-group.png)

1. Adja meg az alábbi adatokat az űrlapon.

    ![Üzembehelyezési űrlap](media/ingest-data-event-hub/deployment-form.png)

    Az alábbi táblázatban fel nem sorolt beállítások esetében használja az alapértelmezett értéket.

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Subscription | Az Ön előfizetése | Válassza ki az eseményközponthoz használni kívánt Azure-előfizetést.|
    | Resource group | *test-hub-rg* | Hozzon létre egy új erőforráscsoportot. |
    | Location | *USA nyugati régiója* | A cikkhez válassza az *USA nyugati* régiója lehetőséget. Éles üzemben az igényeinek leginkább megfelelő régiót válassza. Hozza létre az Event hub-névteret ugyanabban a helyen, mint a Kusto-fürtöt a legjobb teljesítmény érdekében (amely a nagy átviteli sebességű Event hub-névterek esetében fontos
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

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** elemet a betöltött adatot fogadó tábla (TestTable) létrehozásához.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Létrehozási lekérdezés futtatása](media/ingest-data-event-hub/run-create-query.png)

1. Másolja a következő parancsot az ablakba, és válassza a **Futtatás** elemet a bejövő JSON-adattípusok a tábla oszlopnevek és adattípusai (TestTable) szerinti leképezéséhez.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>Csatlakozás az eseményközponthoz

Most csatlakozzon az eseményközponthoz az Azure Data Explorerből. Ha ez a kapcsolat létrejött, az eseményközpontba érkező adatok a cikk korábbi részében létrehozott teszttáblába kerülnek.

1. Az eszközsáv **Értesítések** elemének kiválasztásával győződjön meg arról, hogy az eseményközpont üzembe helyezése sikeresen megtörtént.

1. A létrehozott fürt alatt válassza az **Adatbázisok**, majd a **TestDatabase** lehetőséget.

    ![Tesztadatbázis kiválasztása](media/ingest-data-event-hub/select-test-database.png)

1. Válassza **az adatfeldolgozás** lehetőséget, és **adja hozzá az adatkapcsolatok**elemet. Ezután töltse ki az űrlapot a következő információkkal. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    ![Eseményközpont-kapcsolat](media/ingest-data-event-hub/event-hub-connection.png)

    Adatforrás:

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatkapcsolat neve | *test-hub-connection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | Eseményközpont-névtér | A névtér egyedi neve | A korábban a névtér azonosításához választott név. |
    | Eseményközpont | *test-hub* | A létrehozott eseményközpont. |
    | Fogyasztói csoport | *test-group* | A létrehozott eseményközponton definiált fogyasztói csoport. |
    | Eseményvezérelt rendszerek tulajdonságai | Válassza ki a megfelelő tulajdonságokat | Az [Event hub rendszertulajdonságai](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations) Ha az eseményen több rekord van, akkor a rendszer tulajdonságai hozzáadódnak az elsőhöz. A Rendszertulajdonságok hozzáadásakor [hozzon létre](/azure/kusto/management/tables#create-table) vagy [frissítsen](/azure/kusto/management/tables#alter-table-and-alter-merge-table) egy tábla sémáját és [hozzárendelését](/azure/kusto/management/mappings) a kiválasztott tulajdonságok belefoglalásához. |
    | | |

    Céltábla:

    Két lehetőség van a betöltött adatmennyiség útválasztására: *statikus* és *dinamikus*. 
    Ebben a cikkben statikus útválasztást használ, ahol megadhatja a tábla nevét, az adatformátumot és a leképezést. Ezért hagyja, hogy az adatok között ne legyenek kiválasztva **az útválasztási adatok** .

     **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Tábla | *TestTable* | A **TestDatabase** adatbázisban létrehozott tábla. |
    | Adatformátum | *JSON* | A támogatott formátumok a következők: Avro, CSV, JSON, többsoros JSON, PSV, SOHSV, SCSV, TSV, TSVE és TXT. Támogatott tömörítési beállítások: GZip |
    | Oszlopleképezés | *TestMapping* | A **TestDatabase**-ben létrehozott [leképezés](/azure/kusto/management/mappings) , amely leképezi a bejövő JSON-adattípusokat a **TestTable**tartozó oszlopnevek és adattípusok számára. JSON-, többsoros JSON-vagy AVRO szükséges, és más formátumokhoz nem kötelező.|
    | | |

    > [!NOTE]
    > * Válassza a **saját adatok: útválasztási információ** lehetőséget a dinamikus útválasztás használatához, ahol az adatok tartalmazzák a szükséges útválasztási információkat a [minta alkalmazás](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) megjegyzésében látható módon. Ha a statikus és a dinamikus tulajdonságok is be vannak állítva, a dinamikus tulajdonságok felülbírálják a statikus fájlokat. 
    > * A rendszer csak az adatkapcsolatok létrehozását követően várólistán lévő eseményeket.

## <a name="copy-the-connection-string"></a>A kapcsolati sztring másolása

Amikor elindítja az Előfeltételek között szereplő [mintaalkalmazást](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), szüksége lesz az eseményközpont névterének kapcsolati sztringjére.

1. A létrehozott eseményközpont-névtér alatt válassza a **Megosztott elérési szabályzatok**, majd a **RootManageSharedAccessKey** lehetőséget.

    ![Megosztott elérési házirendek](media/ingest-data-event-hub/shared-access-policies.png)

1. Másolja ki a **kapcsolati sztring elsődleges kulcsát**. A következő szakaszban kell beillesztenie.

    ![Kapcsolati sztring](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Használja a letöltött [minta alkalmazást](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) az adatlétrehozáshoz.

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

    Az eredményhalmaz a következőhöz hasonlóan néz ki:

    ![Üzenetek eredményhalmaza](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Az Azure Adatkezelő a betöltési folyamat optimalizálására szolgáló összesítési (batch-) szabályzattal rendelkezik az adatfeldolgozáshoz. A házirend alapértelmezés szerint 5 percre vagy 500 MB-ra van konfigurálva, így késést tapasztalhat. Lásd: az összesítési beállításokra vonatkozó [kötegelt házirend](/azure/kusto/concepts/batchingpolicy) . 
    > * Az Event hub betöltésének része az Event hub válaszideje 10 másodperc vagy 1 MB. 
    > * Konfigurálja a táblázatot a folyamatos átvitel támogatásához, és távolítsa el a késést a válaszadás időpontjában. Lásd: [streaming Policy](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi, hogy továbbra is használja, a költségek elkerülése érdekében törölje a **test-hub-rg** erőforráscsoportot.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen, majd a létrehozott erőforráscsoport.  

    Ha a bal oldali menü össze van csukva, kattintson a ![Kibontás gombra](media/ingest-data-event-hub/expand.png) a kinyitásához.

   ![A törölni kívánt erőforráscsoport kiválasztása](media/ingest-data-event-hub/delete-resources-select.png)

1. A **test-resource-group** alatt válassza az **Erőforráscsoport törlése** elemet.

1. Az új ablakban írja be a törölni kívánt erőforráscsoport nevét (*test-hub-rg*), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Az Azure Adatkezelő lekérdezése](web-query-data.md)
