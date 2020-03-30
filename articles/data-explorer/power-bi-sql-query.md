---
title: Adatok megjelenítése az Azure Data Explorerből Egy Power BI SQL-lekérdezéssel
description: 'Ebben a cikkben megtudhatja, hogyan használhatja az adatok megjelenítésére a Power BI-ban: egy Azure Data Explorer-fürt elleni SQL-lekérdezés t.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: d402d4c1ee77d0f97d2a5c3bdf43d0cc62aac096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560473"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Adatok megjelenítése az Azure Data Explorerből SQL-lekérdezéssel a Power BI-ban

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket.

Az Azure Data Explorer három lehetőséget biztosít az adatokhoz való csatlakozáshoz a Power BI-ban: használja a beépített összekötőt, importáljon egy lekérdezést az Azure Data Explorerből, vagy használjon SQL-lekérdezést. Ez a cikk bemutatja, hogyan használhat SQL-lekérdezést adatok lehívására és megjelenítésére egy Power BI-jelentésben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez a következőkre van szükség:

* Egy szervezeti e-mail fiók, amely az Azure Active Directory tagja, így csatlakozhat az [Azure Data Explorer súgófürthöz.](https://dataexplorer.azure.com/clusters/help/databases/samples)

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (válasz: **LETÖLTÉS INGYENES)**

## <a name="get-data-from-azure-data-explorer"></a>Adatok bekéselése az Azure Data Explorerből

Először csatlakozzon az Azure Data Explorer súgófürthöz, majd behozza a *StormEvents* tábla adatainak egy részét. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Általában a natív lekérdezési nyelvet használja az Azure Data Explorer, de ez is támogatja az SQL-lekérdezések, amelyeket itt fog használni. Az Azure Data Explorer az SQL-lekérdezést natív lekérdezéssé fordítja.

1. A Power BI Desktop **Kezdőlap** lapján válassza az **Adatok beszerezni,** majd az **Egyebek**lehetőséget.

    ![Adatok lekérése](media/power-bi-sql-query/get-data-more.png)

1. Keressen az *Azure SQL Database ,* válassza az Azure SQL **Database,** majd **a Connect**lehetőséget.

    ![Adatok keresése és lekérése](media/power-bi-sql-query/search-get-data.png)

1. Az **SQL Server adatbázis** képernyőjén töltse ki az űrlapot a következő információkkal.

    ![Adatbázis, tábla, lekérdezési beállítások](media/power-bi-sql-query/database-table-query.png)

    **Beállítás** | **Érték** | **Mező leírása**
    |---|---|---|
    | Kiszolgáló | *help.kusto.windows.net* | A súgófürt URL-címe *(https://* nélkül). Más fürtök esetén az URL-cím * \<ClusterName\>formában\< van. Régió\>.kusto.windows.net*. |
    | Adatbázis | *Minták* | A mintaadatbázis, amely azon a fürtön található, amelyhez csatlakozik. |
    | Adatkapcsolati mód | *Importálás* | Azt határozza meg, hogy a Power BI importálja-e az adatokat, vagy közvetlenül kapcsolódik-e az adatforráshoz. Ezzel az összekötővel bármelyik lehetőséget használhatja. |
    | Parancs időmeghosszabbítása | Hagyja üresen | Mennyi ideig fut a lekérdezés, mielőtt időtúllépési hibát hoz. |
    | SQL utasítás | A tábla alatti lekérdezés másolása | Az Azure Data Explorer által natív lekérdezéssé fordított SQL-utasítás. |
    | Egyéb lehetőségek | Kilépés alapértelmezett értékként | A beállítások nem vonatkoznak az Azure Data Explorer-fürtökre. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Ha még nem rendelkezik kapcsolattal a súgófürthöz, jelentkezzen be. Jelentkezzen be Microsoft-fiókkal, majd válassza a **Csatlakozás**lehetőséget.

    ![Bejelentkezés](media/power-bi-sql-query/sign-in.png)

1. A **help.kusto.windows.net: Minták** képernyőn válassza a **Betöltés**lehetőséget.

    ![Adatok betöltése](media/power-bi-sql-query/load-data.png)

    A tábla a Power BI fő ablakában, jelentésnézetben nyílik meg, ahol jelentéseket hozhat létre a mintaadatok alapján.

## <a name="visualize-data-in-a-report"></a>Adatok megjelenítése a jelentésben

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a cikkhez létrehozott jelentésre, törölje a Power BI Desktop (.pbix) fájlt.

## <a name="next-steps"></a>További lépések

[Adatok megjelenítése az Azure Data Explorer-összekötő használatával a Power BI-hoz](power-bi-connector.md)