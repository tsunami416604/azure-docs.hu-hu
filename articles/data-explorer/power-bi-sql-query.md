---
title: SQL-lekérdezést használja a Power bi-ban Azure adatkezelő adatainak megjelenítése
description: 'Ebből a cikkből elsajátíthatja, hogyan használja a következő három lehetőség közül használatával megjelenítheti az adatokat a Power bi-ban: egy Azure Data Explorer-fürtöt egy SQL-lekérdezést.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 13cec67a690239204bfc52d04b7cf03fd571148a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399269"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Adatok megjelenítése a Power bi-hoz az Azure Data Explorer-összekötő használatával

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket.

A Power BI-adatokhoz való csatlakozásról három lehetőséget kínál a Azure Data Explorer: használja a beépített összekötő, importálhat egy lekérdezést az Azure Data Explorer vagy egy SQL-lekérdezést. Ez a cikk bemutatja, hogyan használható SQL-lekérdezés adatok lekérése, és jelenítheti meg azokat a Power BI-jelentésekben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a következőkre lesz szüksége:

* Szervezeti e-mail-fiókkal, amely tagja az Azure Active directory, így kapcsolódhat a [Azure adatkezelő súgófürtben](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (válasszon **ingyenes letöltés**)

## <a name="get-data-from-azure-data-explorer"></a>Adatok lekérése az Azure Data Explorer

Először csatlakozik az Azure Data Explorer súgófürtben, majd származó adatok egy részét a tenné a *StormEvents* tábla. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Általában a natív lekérdezési nyelvet használja az Azure Data Explorer használatával, de azt is támogatja az SQL-lekérdezések, amelyek itt fogja használni. Az adatkezelő az Azure az SQL-lekérdezést fordítja, a natív lekérdezés.

1. A Power BI Desktopban a a **kezdőlap** lapon jelölje be **adatok lekérése** majd **további**.

    ![Adatok lekérése](media/power-bi-sql-query/get-data-more.png)

1. Keresse meg *Azure SQL Database*válassza **Azure SQL Database** majd **Connect**.

    ![Adatok keresése és lekérése](media/power-bi-sql-query/search-get-data.png)

1. Az a **SQL Server-adatbázis** lapon töltse ki az űrlapot a következő információkat.

    ![Adatbázis, a tábla, a lekérdezés beállításai](media/power-bi-sql-query/database-table-query.png)

    **Beállítás** | **Érték** | **Mező leírása**
    |---|---|---|
    | Kiszolgáló | *help.kusto.windows.net* | A súgófürtben URL-CÍMÉT (nélkül *https://* ). Egyéb fürtök esetében az URL-cím szerepel az űrlap  *\<ClusterName\>.\< Régió\>. kusto.windows.net*. |
    | Adatbázis | *Példák* | A minta a fürtön futó adatbázis, amelyhez csatlakozik. |
    | Adatkapcsolati mód | *Importálás* | Meghatározza, hogy a Power BI importálta az adatokat, vagy közvetlenül csatlakozik az adatforráshoz. Mindkét módszerhez ezzel az összekötővel. |
    | Parancs időkorlátja | Hagyja üresen | Mennyi ideig a lekérdezés előtt fut, egy időtúllépési hibát jelez. |
    | SQL-utasítás | A lekérdezés a táblázat alatti másolása | Az SQL-utasítást, amely az Azure Data Explorer azt jelenti, hogy egy natív lekérdezés. |
    | Egyéb beállítások | Hagyja az alapértelmezett értékek | Azure Data Explorer fürtök beállítások nem vonatkoznak. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Ha már nincs a súgófürtben csatlakozni, jelentkezzen be. A Microsoft-fiókkal, majd válassza a **Connect**.

    ![Bejelentkezés](media/power-bi-sql-query/sign-in.png)

1. Az a **help.kusto.windows.net: A minták** képernyőn válassza ki **terhelés**.

    ![Adatok betöltése](media/power-bi-sql-query/load-data.png)

    A tábla megnyitása a Power BI főablakban, a jelentés nézetben, ahol a mintaadatok alapján jelentéseket hozhat létre.

## <a name="visualize-data-in-a-report"></a>A jelentésben szereplő adatok megjelenítése

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott jelentés ebben a cikkben, törölje a Power BI Desktop (.pbix) fájlt.

## <a name="next-steps"></a>További lépések

[Adatok megjelenítése Power BI-ban az importált lekérdezés használatával](power-bi-connector.md)