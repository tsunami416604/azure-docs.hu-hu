---
title: Az Azure Data Explorer megjelenítése a Redash segítségével
description: Ebben a cikkben megtudhatja, hogyan vizualizálhatja az adatokat az Azure Data Explorer ben a Redash natív összekötő.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773956"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Adatok megjelenítése az Azure Data Explorerből a Redash alkalmazásban

[A Redash](https://redash.io/) összekapcsolja és lekérdezi az adatforrásokat, irányítópultokat hoz létre az adatok megjelenítéséhez és a társakkal való megosztásához. Ebben a cikkben megtudhatja, hogyan állíthatja be az Azure Data Exploreradatforrásként a Redash adatforrását, majd jelenítheti meg az adatokat.

## <a name="prerequisites"></a>Előfeltételek

1. [Fürt és adatbázis létrehozása](create-cluster-database-portal.md).
1. Az adatok betöltése az [Azure Data Explorer betöltési mintaadatainak magyarázata szerint.](ingest-sample-data.md) További betöltési beállításokért lásd: [Betöltési áttekintés.](ingest-data-overview.md)

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Azure Data Explorer-összekötő létrehozása a Redash programban 

1. Jelentkezzen be a [Redash be.](https://www.redash.io/) Fiók létrehozásához válassza az **Első lépések** lehetőséget.
1. A **Kezdés**lehetőséget csoportban válassza **az Adatforrás csatlakoztatása**lehetőséget.

    ![Adatforrás csatlakoztatása](media/redash/connect-data-source.png)

1. Az **Új adatforrás létrehozása** ablakban válassza az Azure Data Explorer **(Kusto)** lehetőséget, majd a **Létrehozás lehetőséget.** 

    ![Válassza az Azure Data Explorer adatforrását](media/redash/select-adx-data-source.png)

1. Az **Azure Data Explorer (Kusto)** ablakban töltse ki az alábbi űrlapot, és válassza a **Létrehozás gombot.**

    ![Az Azure Data Explorer (Kusto) beállítási ablaka](media/redash/adx-settings-window.png)

1. A **Beállítások** ablakban válassza **a Mentés** és a Kapcsolat **tesztelése** lehetőséget az **Azure Data Explorer (Kusto)** adatforrás-kapcsolat ának teszteléséhez.

## <a name="create-queries-in-redash"></a>Lekérdezések létrehozása a Redash-ben

1. Az Újravonaltól balra kattintson a**Lekérdezés** **létrehozása gombra.** >  Kattintson az **Új lekérdezés gombra,** és nevezze át a lekérdezést.

    ![Lekérdezés létrehozása](media/redash/create-query.png)

1. Írja be a lekérdezést a felső szerkesztőablakba, és válassza a **Mentés** és **végrehajtás lehetőséget.** Válassza **a Közzététel** lehetőséget a lekérdezés későbbi használatához történő közzétételéhez.

    ![Lekérdezés mentése és végrehajtása](media/redash/save-and-execute-query.png)

    A bal oldali ablaktáblában a legördülő menüben láthatja az adatforrás-kapcsolat nevét **(Github-összekötő** a folyamatban) és a kijelölt adatbázis tábláit. 

1. A lekérdezés eredményeinek megtekintése az alsó középső ablaktáblán. Hozzon létre egy vizualizációt a lekérdezéshez az **Új vizualizáció** gomb kiválasztásával.

    ![Új képi megjelenítés](media/redash/new-visualization.png)

1. A vizualizációs képernyőn jelölje ki a **Vizualizáció típusa** és a megfelelő mezőket, például **az X oszlop** és az Y oszlop **mezőt.** **Mentse** a vizualizációt.

    ![Képi megjelenítés konfigurálása és mentése](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Lekérdezés létrehozása paraméter használatával

1. **Lekérdezés létrehozása** > **Query** új lekérdezés létrehozásához. Adjon hozzá egy paramétert a {{}} göndör zárójelek használatával. A **Paraméter hozzáadása** ablak megnyitásához válassza a **{{}}** lehetőséget. A *beállítások ikonra* is kiválaszthatja egy meglévő paraméter attribútumainak módosítását, és megnyithatja a **<parameter_name>** ablakban. 

    ![paraméter beszúrása](media/redash/insert-parameter.png)

1. Nevezze el a paramétert. Válassza **a Típus**: **Lekérdezésalapú legördülő lista lehetőséget** a legördülő menüből. Kattintson az **OK** gombra.

    ![lekérdezésalapú legördülő lista](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > A lekérdezés több értéket használ, ezért a `| where Type in ((split('{{Type}}', ',')))`következő szintaxist kell tartalmaznia. További információ: [operator](/azure/kusto/query/inoperator). Ez [több lekérdezési paraméterbeállítást](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io) eredményez a redash alkalmazásban

## <a name="create-a-dashboard-in-redash"></a>Irányítópult létrehozása a Redash-ben

1. Az irányítópult létrehozásához hozza létre az Irányítópult **létrehozása című.** > **Dashboard** Másik lehetőségként válassza ki a meglévő **irányítópultokat, az Irányítópultokat** > válassza ki az irányítópultot a listából.

    ![Irányítópult létrehozása](media/redash/create-dashboard.png)

1. Az **Új irányítópult** ablakban nevezze el az irányítópultot, és válassza a Mentés **gombot.** Az Dashboard_name **>Dashboard_name ablakban** válassza<a **Widget hozzáadása** lehetőséget új widget létrehozásához. 

1. A **Widget hozzáadása** ablakban válassza a lekérdezés nevét, a Megjelenítés **és**a **Paraméterek lehetőséget.** Válassza **a Hozzáadás az irányítópulthoz lehetőséget**

   ![Képi megjelenítések kiválasztása és hozzáadás az irányítópulthoz](media/redash/add-widget-window.png)

1. Válassza a **Kész szerkesztés lehetőséget** az irányítópult létrehozásának befejezéséhez.

1.  Az irányítópult szerkesztési módjában válassza az **Irányítópultszint-szűrők használata lehetőséget** a korábban definiált **Típus** paraméter használatához.

    ![Az irányítópult létrehozása teljes](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)


