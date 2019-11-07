---
title: Azure-Adatkezelő megjelenítése a redash segítségével
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg az Azure Adatkezelőban tárolt adatmegjelenítést a natív összekötővel.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8bb8711bc4a6134ec740a55d9f5d5794b2de77ca
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588589"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Adatok megjelenítése az Azure Adatkezelő a redash-ben

A [redash](https://www.redash.io/) összekapcsolja és lekérdezi az adatforrásokat, irányítópultokat hoz létre az adatmegjelenítéshez és a társaikkal való megosztásához. Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure Adatkezelőt adatforrásként a redash számára, majd megjelenítheti az adatmegjelenítést.

## <a name="prerequisites"></a>Előfeltételek

1. [Hozzon létre egy fürtöt és egy adatbázist](create-cluster-database-portal.md).
1. A mintaadatok betöltését az [Azure Adatkezelőba](ingest-sample-data.md)a beolvasott adatmennyiséggel. További betöltési lehetőségekért lásd: a betöltés [áttekintése](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Azure Adatkezelő-összekötő létrehozása a redash-ben 

1. Jelentkezzen be a [redash](https://www.redash.io/)-be. Válassza az első **lépések** lehetőséget a fiók létrehozásához.
1. Az első **lépések**szakaszban válassza az **adatforrás összekapcsolását**.

    ![Adatforrás összekötése](media/redash/connect-data-source.png)

1. Az **új adatforrás létrehozása** ablakban válassza az **Azure adatkezelő (Kusto)** lehetőséget, majd válassza a **Létrehozás**lehetőséget. 

    ![Válassza ki az Azure Adatkezelő adatforrást](media/redash/select-adx-data-source.png)

1. Az **Azure adatkezelő (Kusto)** ablakban végezze el a következő űrlapot, és válassza a **Létrehozás**lehetőséget.

    ![Azure Adatkezelő (Kusto) beállítások ablak](media/redash/adx-settings-window.png)

1. A **Beállítások** ablakban válassza a **Save (Mentés** ) lehetőséget, és **tesztelje** az **Azure adatkezelő (Kusto)** adatforrás-kapcsolódását.

## <a name="create-queries-in-redash"></a>Lekérdezések létrehozása a redash-ben

1. A redash bal felső részén válassza az > **lekérdezés** **létrehozása** lehetőséget. Kattintson az **Új lekérdezés** elemre, és nevezze át a lekérdezést.

    ![Lekérdezés létrehozása](media/redash/create-query.png)

1. Írja be a lekérdezést a felső szerkesztési ablaktáblában, majd kattintson a **Mentés** és **végrehajtás**gombra. Válassza a **Közzététel** lehetőséget a lekérdezés közzétételéhez későbbi használatra.

    ![Lekérdezés mentése és végrehajtása](media/redash/save-and-execute-query.png)

    A bal oldali panelen megtekintheti az adatforrás-kapcsolatok nevét (a flow-ban lévő**GitHub-összekötőt** ) a legördülő menüben, valamint a kiválasztott adatbázis tábláiban. 

1. Tekintse meg a lekérdezés eredményeit az alsó középső ablaktáblán. Hozzon létre egy vizualizációt a lekérdezéssel való ugráshoz az **új vizualizáció** gombra kattintva.

    ![Új vizualizáció](media/redash/new-visualization.png)

1. A vizualizáció képernyőn válassza ki a **vizualizáció típusát** és a megfelelő mezőket (például **X oszlop** és **Y oszlop**). **Mentse** a vizualizációt.

    ![Vizualizáció konfigurálása és mentése](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Lekérdezés létrehozása paraméter használatával

1. **Hozzon létre** > **lekérdezést** új lekérdezés létrehozásához. Adjon hozzá egy paramétert a (z) {{}} kapcsos zárójelek használatával. Válassza a **{{}}** lehetőséget a **paraméter hozzáadása** ablak megnyitásához. A *Beállítások ikont* választva módosíthatja a meglévő paraméterek attribútumait, és megnyithatja a **< parameter_name >** ablakot. 

    ![paraméter beszúrása](media/redash/insert-parameter.png)

1. Nevezze el a paramétert. Válassza a **típus**: **lekérdezési alapú legördülő lista** lehetőséget a legördülő menüből. Kattintson az **OK** gombra.

    ![lekérdezésen alapuló legördülő lista](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > A lekérdezés több értéket használ, ezért a következő szintaxist kell tartalmaznia `| where Type in ((split('{{Type}}', ',')))`. További információ: [az operátor](/azure/kusto/query/inoperator). Ennek eredményeképpen a [redash alkalmazásban több lekérdezési paraméter is elérhető](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io) .

## <a name="create-a-dashboard-in-redash"></a>Irányítópult létrehozása a redash-ben

1. Az irányítópult létrehozásához **hozzon létre** > **irányítópultot**. Másik lehetőségként válassza a meglévő irányítópultot, az **irányítópultok** > a listából válassza ki az irányítópultot.

    ![Irányítópult létrehozása](media/redash/create-dashboard.png)

1. Az **új irányítópult** ablakban nevezze el az irányítópultot, és kattintson a **Mentés**gombra. **< Dashboard_name >** ablakban válassza a **widget hozzáadása** lehetőséget az új widget létrehozásához. 

1. A **widget hozzáadása** ablakban válassza a lekérdezés neve lehetőséget, majd a **vizualizáció**és a **Paraméterek**elemet. **A Hozzáadás az irányítópulthoz** lehetőség kiválasztása

   ![Vizualizációk kiválasztása és Hozzáadás az irányítópulthoz](media/redash/add-widget-window.png)

1. Válassza a **Szerkesztés kész** lehetőséget az irányítópultok létrehozásának befejezéséhez.

1.  Az irányítópult szerkesztési módjában válassza az **irányítópult szintű szűrők használata** **lehetőséget a korábban definiált típusparaméter használatához** .

    ![Irányítópult-létrehozás befejezése](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)


