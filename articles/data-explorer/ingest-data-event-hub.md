---
title: 'Rövid útmutató: Adatok betöltése az Event Hubsból az Azure Data Explorerbe'
description: Ebből a rövid útmutatóból megtudhatja, hogyan tölthet be adatokat az Azure Data Explorerbe az Event Hubsból.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 3350c222cced036af6319cee166c53da0b14f2a9
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210448"
---
# <a name="quickstart-ingest-data-from-event-hub-into-azure-data-explorer"></a>Rövid útmutató: Adatok betöltése az Event Hubsból az Azure Data Explorerbe

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer adatbetöltési lehetőséget tesz elérhetővé az Event Hubsból, amely egy big data-streamelési platform és eseményfeldolgozó szolgáltatás. Az Event Hubs másodpercenként több millió esemény feldolgozására képes, valós időben. Ebben a rövid útmutatóban létrehozunk egy eseményközpontot, csatlakozunk hozzá az Azure Data Explorerből, és megfigyeljük az adatok a rendszeren keresztüli áramlását.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez az Azure-előfizetés mellett szüksége lesz a következőkre is:

* [Egy tesztfürt és -adatbázis](create-cluster-database-portal.md)

* [Egy mintaalkalmazás](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), amely adatokat állít elő, és elküldi azokat egy eseményközpontnak

* A [Visual Studio 2017 szoftver 15.3.2-es vagy újabb verziója](https://www.visualstudio.com/vs/) a mintaalkalmazás futtatásához

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

A rövid útmutatóban mintaadatokat állítunk elő, és elküldjük azokat egy eseményközpontnak. Első lépésként létre kell hoznia egy eseményközpontot. Ezt egy Azure Resource Manager-sablon használatával teheti meg az Azure Portalon.

1. Az üzembe helyezés indításához kattintson a következő gombra. Javasoljuk, hogy a hivatkozást egy új lapon vagy ablakban nyissa meg, hogy könnyebben követhesse a cikk további lépéseit.

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
    | Előfizetés | Az Ön előfizetése | Válassza ki az eseményközponthoz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | *test-hub-rg* | Hozzon létre egy új erőforráscsoportot. |
    | Hely | *USA nyugati régiója* | Ebben a rövid útmutatóban válassza az *USA nyugati régióját*. Éles üzemben az igényeinek leginkább megfelelő régiót válassza.
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

1. Másolja be a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Létrehozási lekérdezés futtatása](media/ingest-data-event-hub/run-create-query.png)

1. Másolja be a következő parancsot az ablakba, és válassza a **Futtatás** lehetőséget.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```
    A parancs a bejövő JSON-adatokat leképezi a táblában (TestTable) szereplő oszlopnevekre és adattípusokra.

## <a name="connect-to-the-event-hub"></a>Csatlakozás az eseményközponthoz

Most csatlakozzon az eseményközponthoz az Azure Data Explorerből. Ha ez a kapcsolat létrejött, az eseményközpontba érkező adatok a cikk korábbi részében létrehozott teszttáblába kerülnek.

1. Az eszközsáv **Értesítések** elemének kiválasztásával győződjön meg arról, hogy az eseményközpont üzembe helyezése sikeresen megtörtént.

1. A létrehozott fürt alatt válassza az **Adatbázisok**, majd a **TestDatabase** lehetőséget.

    ![Tesztadatbázis kiválasztása](media/ingest-data-event-hub/select-test-database.png)

1. Válassza az **Adatbevitel**, majd az **Adatkapcsolat hozzáadása** lehetőséget.

    ![Adatfeldolgozás](media/ingest-data-event-hub/data-ingestion-create.png)

1. Adja meg az alábbi adatokat az űrlapon, majd válassza a **Létrehozás** lehetőséget.

    ![Eseményközpont-kapcsolat](media/ingest-data-event-hub/event-hub-connection.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Adatkapcsolat neve | *test-hub-connection* | Az Azure Data Explorerben létrehozni kívánt kapcsolat neve.|
    | Eseményközpont-névtér | A névtér egyedi neve | A korábban a névtér azonosításához választott név. |
    | Eseményközpont | *test-hub* | A létrehozott eseményközpont. |
    | Fogyasztói csoport | *test-group* | A létrehozott eseményközponton definiált fogyasztói csoport. |
    | Céltábla | Hagyja **A saját adatok útválasztási információt tartalmaznak** lehetőséget bejelöletlenül. | Az útvonalválasztás esetében két lehetőség érhető el: a *statikus* és a *dinamikus*. Ebben a rövid útmutatóban statikus útválasztást alkalmazunk (ez az alapértelmezett), amelyben megadjuk a táblanevet, a fájlformátumot és a leképezést. Dinamikus útválasztás is alkalmazható, ha a saját adatok tartalmazzák a szükséges útválasztási információkat. |
    | Tábla | *TestTable* | A **TestDatabase** adatbázisban létrehozott tábla. |
    | Adatformátum | *JSON* | A JSON és a CSV formátum támogatott. |
    | Oszlopleképezés | *TestMapping* | A **TestDatabase** adatbázisban létrehozott leképezés, amely a bejövő JSON-adatokat leképezi a **TestTable** tábla esetében használt oszlopnevekre és adattípusokra.|
    | | |

## <a name="copy-the-connection-string"></a>A kapcsolati sztring másolása

Amikor elindítja az Előfeltételek között szereplő [mintaalkalmazást](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), szüksége lesz az eseményközpont névterének kapcsolati sztringjére.

1. A létrehozott eseményközpont-névtér alatt válassza a **Megosztott elérési szabályzatok**, majd a **RootManageSharedAccessKey** lehetőséget.

    ![Megosztott elérési házirendek](media/ingest-data-event-hub/shared-access-policies.png)

1. Másolja ki a **kapcsolati sztring elsődleges kulcsát**. A következő szakaszban kell beillesztenie.

    ![Kapcsolati sztring](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Most, hogy az Azure Data Explorer és az eseményközpont között létrejött a kapcsolat, a letöltött [mintaalkalmazással](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) előállíthatja az adatokat.

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

1. Lépjen vissza a mintaalkalmazáshoz, és állítsa le, amint az üzenetek száma eléri a 99-et.

1. A következő lekérdezés a tesztadatbázison való futtatásával ellenőrizze, hogy hány üzenet került át eddig a pillanatig az adatbázisba.

    ```Kusto
    TestTable
    | count
    ```

1. Az üzenetek tartalmának megtekintéséhez futtassa az alábbi lekérdezést.

    ```Kusto
    TestTable
    ```

    Az eredményhalmaznak a következőhöz hasonlóan kell kinéznie.

    ![Üzenetek eredményhalmaza](media/ingest-data-event-hub/message-result-set.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi, hogy továbbra is használja, a költségek elkerülése érdekében törölje a **test-hub-rg** erőforráscsoportot.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen, majd a létrehozott erőforráscsoport.  

    Ha a bal oldali menü össze van csukva, kattintson a ![Kibontás gombra](media/ingest-data-event-hub/expand.png) a kinyitásához.

   ![A törölni kívánt erőforráscsoport kiválasztása](media/ingest-data-event-hub/delete-resources-select.png)

1. A **test-resource-group** alatt válassza az **Erőforráscsoport törlése** elemet.

1. Az új ablakban írja be a törölni kívánt erőforráscsoport nevét (*test-hub-rg*), majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
