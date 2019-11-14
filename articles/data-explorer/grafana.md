---
title: Adatok megjelenítése az Azure Adatkezelő Grafana használatával
description: Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure Adatkezelőt a Grafana adatforrásaként, majd hogyan jelenítheti meg az adatait egy minta fürtből.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038045"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Adatok megjelenítése az Azure Adatkezelő a Grafana-ben

A Grafana egy elemzési platform, amely lehetővé teszi az adatlekérdezést és megjelenítést, majd irányítópultok létrehozását és megosztását a vizualizációk alapján. A Grafana egy Azure Adatkezelő *beépülő modult*biztosít, amely lehetővé teszi az azure-Adatkezelőhoz való kapcsolódást és az adatok megjelenítését. Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure Adatkezelőt a Grafana adatforrásaként, majd hogyan jelenítheti meg az adatait egy minta fürtből.

A következő videó segítségével megismerheti, hogyan használhatja a Grafana Azure Adatkezelő beépülő modulját, hogyan állíthatja be az Azure Adatkezelőt adatforrásként a Grafana, majd megjelenítheti az információkat. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Másik lehetőségként [konfigurálhatja az adatforrást](#configure-the-data-source) , és [megjelenítheti az információt](#visualize-data) az alábbi cikkben leírtak szerint.

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következőkre lesz szüksége:

* Az operációs rendszer [Grafana-verziójának 5.3.0 vagy újabb verziója](https://docs.grafana.org/installation/)

* A Grafana készült [Azure adatkezelő beépülő modul](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)

* Egy fürt, amely tartalmazza a StormEvents. További információ: gyors útmutató [: azure adatkezelő-fürt és-adatbázis létrehozása](create-cluster-database-portal.md) és [mintaadatok begyűjtése az Azure Adatkezelőba](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Tulajdonságok megadása és a kapcsolatok tesztelése

A *megjelenítői* szerepkörhöz hozzárendelt egyszerű szolgáltatással a Grafana-példányban megadhatja a tulajdonságokat, és tesztelheti az Azure Adatkezelőhoz való kapcsolódást.

1. A Grafana bal oldali menüjében kattintson a fogaskerék ikonra, majd az **adatforrások**elemre.

    ![Adatforrások](media/grafana/data-sources.png)

1. Válassza **az adatforrás hozzáadása**lehetőséget.

1. Az **adatforrások/új** lapon adja meg az adatforrás nevét, majd válassza ki az **Azure adatkezelő DataSource**típust.

    ![A kapcsolatok neve és típusa](media/grafana/connection-name-type.png)

1. Adja meg a fürt nevét a https://{ClusterName} formában. {Region}. kusto. Windows. net. Adja meg a Azure Portal vagy a parancssori felület többi értékét. A leképezéshez az alábbi ábrán látható táblázatban talál további információt.

    ![Kapcsolat tulajdonságai](media/grafana/connection-properties.png)

    | Grafana UI | Azure Portal | Azure CLI |
    | --- | --- | --- |
    | Előfizetési azonosító | ELŐFIZETÉS AZONOSÍTÓJA | SubscriptionId |
    | Bérlő azonosítója | Könyvtár azonosítója | tenant |
    | Ügyfél-azonosító | Alkalmazásazonosító | appId |
    | Titkos ügyfélkulcs | Jelszó | jelszó |
    | | | |

1. Válassza a **mentés & teszt**lehetőséget.

    Ha a teszt sikeres, ugorjon a következő szakaszra. Ha bármilyen probléma merül fel, ellenőrizze a Grafana megadott értékeket, és tekintse át az előző lépéseket.

## <a name="visualize-data"></a>Adatok vizualizációja

Most, hogy befejezte az Azure Adatkezelő konfigurálását a Grafana adatforrásaként, itt az ideje, hogy megjelenítse az adatmegjelenítést. Itt egy alapszintű példát mutatunk be, de sokkal többet tehetünk. Javasoljuk, hogy az [Azure-adatkezelő írási lekérdezéseit](write-queries.md) tekintse át a minta adatkészleten való futtatásra vonatkozó példákat.

1. A Grafana bal oldali menüjében válassza a plusz ikont, majd az **irányítópultot**.

    ![Irányítópult létrehozása](media/grafana/create-dashboard.png)

1. A **Hozzáadás** lapon válassza a **Graph**lehetőséget.

    ![Gráf hozzáadása](media/grafana/add-graph.png)

1. A Graph panelen válassza a **panel title** ( **Szerkesztés**) lehetőséget.

    ![Panel szerkesztése](media/grafana/edit-panel.png)

1. A panel alján válassza az **adatforrás** lehetőséget, majd válassza ki a konfigurált adatforrást.

    ![Adatforrás kiválasztása](media/grafana/select-data-source.png)

1. A lekérdezés ablaktáblán másolja a vágólapra a következő lekérdezést, majd válassza a **Futtatás**lehetőséget. A lekérdezés a mintavételi adatkészletek napi eseményeinek számát gyűjtőre állítja.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![A lekérdezés futtatása](media/grafana/run-query.png)

1. A gráf nem jeleníti meg az eredményeket, mert a hatóköre alapértelmezés szerint az elmúlt hat órában lévő adatokra vonatkozik. A felső menüben válassza az **utolsó 6 óra**lehetőséget.

    ![Az elmúlt hat órában](media/grafana/last-six-hours.png)

1. Adja meg a 2007-es és a StormEvents-minta adatkészletben szereplő évet magában foglaló egyéni tartományt. Kattintson az **Alkalmaz** gombra.

    ![Egyéni dátumtartomány](media/grafana/custom-date-range.png)

    A gráf mostantól a 2007-tól kezdve az adatok napi összegyűjtését mutatja.

    ![Befejezett gráf](media/grafana/finished-graph.png)

1. A felső menüben válassza a Mentés ikont: ![Mentés ikon](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Riasztások létrehozása

1. Új értesítési csatorna létrehozásához a Kezdőlap irányítópultján válassza a **riasztás** > **értesítési csatornák** lehetőséget.

    ![értesítési csatorna létrehozása](media/grafana/create-notification-channel.png)

1. Hozzon létre egy új **értesítési csatornát**, majd **mentse**.

    ![Új értesítési csatorna létrehozása](media/grafana/new-notification-channel-adx.png)

1. Az **irányítópulton**válassza a **Szerkesztés** lehetőséget a legördülő listából.

    ![Szerkesztés kiválasztása az irányítópulton](media/grafana/edit-panel-4-alert.png)

1. Válassza a riasztási harang ikont a **riasztás** panel megnyitásához. Válassza a **riasztás létrehozása**lehetőséget. Hajtsa végre a következő tulajdonságokat a **riasztás** ablaktáblán.

    ![riasztás tulajdonságai](media/grafana/alert-properties.png)

1. A módosítások mentéséhez kattintson az **irányítópult mentése** ikonra.

## <a name="next-steps"></a>Következő lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)
