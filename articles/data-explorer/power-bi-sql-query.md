---
title: Adatok megjelenítése az Azure Adatkezelő egy SQL-lekérdezés használatával Power BI
description: 'Ebből a cikkből megtudhatja, hogyan használhatja a három lehetőség egyikét az Power BI: SQL-lekérdezés az Azure Adatkezelő-fürtön való megjelenítéséhez.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: e4e7858a54f3002a511269a2519135d5ac24ed68
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024085"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Adatok megjelenítése az Azure Adatkezelő egy SQL-lekérdezés használatával Power BI

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. A Power BI egy üzleti elemzési megoldás, amellyel megjelenítheti adatait, és a teljes szervezettel megoszthatja az eredményeket.

Az Azure Adatkezelő három lehetőséget biztosít az adatokhoz való kapcsolódáshoz Power BI: a beépített összekötő használata, lekérdezés importálása az Azure-ból Adatkezelő vagy SQL-lekérdezés használata. Ebből a cikkből megtudhatja, hogyan kérhet le és jelenítheti meg az SQL-lekérdezéseket egy Power BI jelentésekben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következőkre lesz szüksége:

* Egy olyan szervezeti e-mail fiók, amely az Azure Active Directory tagja, így kapcsolódhat az [azure adatkezelő Súgó fürthöz](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power bi Desktop](https://powerbi.microsoft.com/get-started/) (válassza az **ingyenes letöltés**lehetőséget)

## <a name="get-data-from-azure-data-explorer"></a>Adatok beolvasása az Azure Adatkezelőból

Először kapcsolódjon az Azure Adatkezelő Súgó fürthöz, majd a *StormEvents* tábla adatainak egy részhalmazát adja meg. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Általában a natív lekérdezési nyelvet használja az Azure Adatkezelő, de támogatja az SQL-lekérdezéseket is, amelyeket itt fog használni. Az Azure Adatkezelő egy natív lekérdezésre fordítja le az SQL-lekérdezést.

1. Power BI Desktop a **Kezdőlap** lapon válassza az **adatlekérdezés** , majd a **továbbiak**lehetőséget.

    ![Adatok lekérése](media/power-bi-sql-query/get-data-more.png)

1. Keresse meg a *Azure SQL Database*, válassza a **Azure SQL Database** majd a **kapcsolat**lehetőséget.

    ![Adatok keresése és lekérése](media/power-bi-sql-query/search-get-data.png)

1. Az **SQL Server adatbázis** képernyőn töltse ki az űrlapot a következő információkkal.

    ![Adatbázis, tábla, lekérdezési beállítások](media/power-bi-sql-query/database-table-query.png)

    **Beállítás** | **Érték** | **Mező leírása**
    |---|---|---|
    | Kiszolgáló | *help.kusto.windows.net* | A Súgó fürt URL-címe ( *https://* nélkül). Más fürtök esetében az URL-cím a következő formában található *\<ClusterName\>.\<régió\>. kusto.Windows.net*. |
    | Adatbázis | *Példák* | A fürtön tárolt mintaadatbázis, amelyhez csatlakozik. |
    | Adatkapcsolati mód | *Importálás* | Meghatározza, hogy Power BI importálja-e az adatimportálást, vagy közvetlenül kapcsolódik-e az adatforráshoz. Ezzel az összekötővel bármelyik lehetőséget használhatja. |
    | Parancs időkorlátja | Hagyja üresen | Mennyi ideig fut a lekérdezés, mielőtt időtúllépési hibát jelez. |
    | SQL-utasítás | A lekérdezés másolása a táblázat alá | Az Azure által Adatkezelő natív lekérdezésre fordított SQL-utasítás. |
    | Egyéb beállítások | Kilépés alapértelmezett értékként | A beállítások nem vonatkoznak az Azure Adatkezelő-fürtökre. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Ha még nem rendelkezik a Súgó fürthöz való kapcsolódással, jelentkezzen be. Jelentkezzen be egy Microsoft-fiók, majd válassza a **kapcsolat**lehetőséget.

    ![Bejelentkezés](media/power-bi-sql-query/sign-in.png)

1. A **help.kusto.Windows.net: Samples** képernyőn válassza a **Betöltés**lehetőséget.

    ![Adatok betöltése](media/power-bi-sql-query/load-data.png)

    A tábla a jelentés nézet fő Power BI ablakában nyílik meg, ahol jelentéseket hozhat létre a mintaadatok alapján.

## <a name="visualize-data-in-a-report"></a>Az adatjelentések megjelenítése

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a cikkhez létrehozott jelentésre, törölje a Power BI Desktop (. pbix) fájlt.

## <a name="next-steps"></a>Következő lépések

[Adatmegjelenítés az Azure Adatkezelő Connector használatával Power BI](power-bi-connector.md)