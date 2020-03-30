---
title: Adatok megjelenítése az Azure Data Explorerből a Grafana használatával
description: Ebben a cikkben megtudhatja, hogy az Azure Data Explorer a Grafana adatforrásaként, majd a mintafürt ből származó adatok megjelenítése.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038045"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Adatok megjelenítése az Azure Data Explorerből Grafanában

A Grafana egy elemzési platform, amely lehetővé teszi az adatok lekérdezését és megjelenítését, majd irányítópultok létrehozását és megosztását a vizualizációk alapján. A Grafana egy Azure Data Explorer *beépülő modult*biztosít, amely lehetővé teszi az Azure Data Explorer adataihoz való csatlakozást és azok megjelenítését. Ebben a cikkben megtudhatja, hogy az Azure Data Explorer a Grafana adatforrásaként, majd a mintafürt ből származó adatok megjelenítése.

Az alábbi videó segítségével megtudhatja, hogyan használhatja a Grafana Azure Data Explorer beépülő modulját, hogyan állíthatja be az Azure Data Explorert a Grafana adatforrásaként, majd jelenítheti meg az adatokat. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Másik lehetőségként [konfigurálhatja az adatforrást,](#configure-the-data-source) és az alábbi cikkben részletezett módon jelenítheti meg az [adatokat.](#visualize-data)

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez a következőkre van szükség:

* [Grafana 5.3.0-s vagy újabb verzió](https://docs.grafana.org/installation/) az operációs rendszerhez

* A Grafana [Azure Data Explorer beépülő modulja](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)

* A StormEvents mintaadatokat tartalmazó fürt. További információ: [Gyorsútmutató: Azure Data Explorer-fürt és-adatbázis létrehozása,](create-cluster-database-portal.md) valamint [mintaadatok betöltése az Azure Data Explorerbe című témakörben.](ingest-sample-data.md)

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Tulajdonságok megadása és a kapcsolat tesztelése

A *megtekintők* szerepkörhöz rendelt egyszerű szolgáltatással mostantól meg kell adnia a Grafana példányában a tulajdonságokat, és tesztelheti a kapcsolatot az Azure Data Explorerrel.

1. A Grafana bal oldali menüjében válassza a fogaskerék ikont, majd **az Adatforrásokat**.

    ![Adatforrások](media/grafana/data-sources.png)

1. Válassza **az Adatforrás hozzáadása**lehetőséget.

1. Az **Adatforrások / Új** lapon adja meg az adatforrás nevét, majd válassza ki az **Azure Data Explorer adatforrás**típusát.

    ![Kapcsolat neve és típusa](media/grafana/connection-name-type.png)

1. Írja be a fürt nevét a https://{ClusterName} űrlapon. {Region}.kusto.windows.net. Adja meg a többi értéket az Azure Portalon vagy a CLI-ben. A leképezést az alábbi táblázatban talál.

    ![Kapcsolat tulajdonságai](media/grafana/connection-properties.png)

    | Grafana Felhasználói felület | Azure portál | Azure CLI |
    | --- | --- | --- |
    | Előfizetés azonosítója | ELŐFIZETÉS AZONOSÍTÓJA | SubscriptionId |
    | Bérlőazonosítója | Címtár azonosítója | Bérlő |
    | Ügyfélazonosító | Alkalmazásazonosító | appId |
    | Titkos ügyfélkulcs | Jelszó | jelszó |
    | | | |

1. Válassza **a &-teszt mentése lehetőséget.**

    Ha a teszt sikeres, lépjen a következő szakaszra. Ha bármilyen probléma merül fel, ellenőrizze a Grafanában megadott értékeket, és tekintse át az előző lépéseket.

## <a name="visualize-data"></a>Adatok vizualizációja

Most, hogy befejezte az Azure Data Explorer konfigurálását a Grafana adatforrásaként, itt az ideje az adatok megjelenítésének. Mutatunk egy alapvető példát itt, de sokkal többet tehet. Azt javasoljuk, hogy az [Azure Data Explorer írási lekérdezések](write-queries.md) című részét a mintaadatkészleten futtatandó egyéb lekérdezések példáinak lekérdezései ből vizsgálja meg.

1. A Grafana bal oldali menüjében válassza a plusz ikont, majd a **Dashboard parancsot.**

    ![Irányítópult létrehozása](media/grafana/create-dashboard.png)

1. A **Hozzáadás** lapon válassza a **Diagram**lehetőséget.

    ![Diagram hozzáadása](media/grafana/add-graph.png)

1. A diagrampanelen válassza a **Panel címe** lehetőséget, majd **a Szerkesztés**lehetőséget.

    ![Szerkesztés panel](media/grafana/edit-panel.png)

1. A panel alján válassza az **Adatforrás** lehetőséget, majd jelölje ki a konfigurált adatforrást.

    ![Adatforrás kiválasztása](media/grafana/select-data-source.png)

1. A lekérdezés ablaktábláján másolja a másolatot a következő lekérdezésbe, majd válassza a **Futtatás lehetőséget.** A lekérdezés a mintaadatkészlet eseményeinek napi számát gyűjti.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![A lekérdezés futtatása](media/grafana/run-query.png)

1. A diagram nem jelenít meg eredményeket, mert a hatóköre alapértelmezés szerint az elmúlt hat óra adataihoz van elszámolva. A felső menüben válassza az **Utolsó 6 óra**lehetőséget.

    ![Az elmúlt hat órában](media/grafana/last-six-hours.png)

1. Adjon meg egy egyéni tartományt, amely lefedi a 2007-es, az év szerepel a StormEvents minta adatkészletben. Kattintson az **Alkalmaz** gombra.

    ![Egyéni dátumtartomány](media/grafana/custom-date-range.png)

    Most a grafikon a 2007-es adatokat mutatja, napról napra.

    ![Kész grafikon](media/grafana/finished-graph.png)

1. A felső menüben válassza a Mentés ikont: ![Mentés ikon](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Riasztások létrehozása

1. Az Otthoni irányítópulton válassza az**Értesítési csatornák** **riasztása** > lehetőséget új értesítési csatorna létrehozásához

    ![értesítési csatorna létrehozása](media/grafana/create-notification-channel.png)

1. Hozzon létre egy új **értesítési csatornát**, majd mentse a **.**

    ![Új értesítési csatorna létrehozása](media/grafana/new-notification-channel-adx.png)

1. Az **irányítópulton**válassza a **Szerkesztés** lehetőséget a legördülő menüből.

    ![szerkesztés kiválasztása az irányítópulton](media/grafana/edit-panel-4-alert.png)

1. A **Riasztás** ablaktábla megnyitásához válassza a riasztási csengő ikonját. Válassza **a Riasztás létrehozása**lehetőséget. Hajtsa végre az alábbi tulajdonságokat a **Riasztás** ablaktáblán.

    ![riasztási tulajdonságok](media/grafana/alert-properties.png)

1. A módosítások mentéséhez válassza az **Irányítópult mentése** ikont.

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)
