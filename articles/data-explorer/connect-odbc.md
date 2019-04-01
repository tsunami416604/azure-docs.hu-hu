---
title: Csatlakozás az Azure Data Explorer ODBC
description: Ebben az útmutatóban megismerheti, hogyan az Adatkezelőbe az Azure egy ODBC-kapcsolat beállításához, majd a kapcsolat használata az adatok vizualizálása a Tableau.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 0ec1c2f4fc412ca6c81e179d0ad22f781b896357
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757627"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Csatlakozás az Azure Data Explorer ODBC

Nyissa meg az adatbázis-kapcsolat ([ODBC](/sql/odbc/reference/odbc-overview)) van egy széles körben elfogadott alkalmazásprogramozási felületet (API) az adatbázis eléréséhez. ODBC segítségével csatlakozzon az Adatkezelőbe az Azure-alkalmazásokból, amelyek nem rendelkeznek egy dedikált összekötőt.

A színfalak mögött alkalmazások hívja az ODBC-felületén, függvények, amelyek az adatbázis-specifikus modulokat, vagyis úgynevezett megvalósított *illesztőprogramok*. Az Azure Data Explorer támogatja az SQL Server kommunikációs protokoll egy részét ([MS-TDS](/azure/kusto/api/tds/)); ezért használhat az ODBC-illesztőprogram SQL Serverhez.

Ebből a cikkből megismerheti, hogyan kell használni az Azure Data Explorer bármely olyan alkalmazásból, amely támogatja a ODBC csatlakozhat az SQL Server ODBC-illesztőprogramját. Majd igény szerint csatlakozhat Azure adatkezelő a Tableau, és egy minta-fürtből származó adatok beolvasása.

## <a name="prerequisites"></a>Előfeltételek

Ez annak a következőkre lesz szüksége:

* [Microsoft ODBC-illesztő az SQL Server 17.2.0.1 verzió vagy újabb](/sql/connect/odbc/download-odbc-driver-for-sql-server) , az operációs rendszernek.

* Ha azt szeretné, kövesse a Tableau példában, akkor is szüksége lesz:

  * A tableau asztali teljes vagy [próbaverziós](https://www.tableau.com/products/desktop/download) verzió.

  * A StormEvents mintaadatokat tartalmazó fürt. További információkért lásd: [a rövid útmutató: Hozzon létre egy Azure Data Explorer fürt és az adatbázis](create-cluster-database-portal.md) és [mintaadatok betöltése az Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>ODBC-adatforrás konfigurálása

Kövesse az alábbi lépéseket az ODBC-illesztő használatával SQL Serverhez készült ODBC-adatforrás konfigurálása.

1. A Windows, keresse meg *ODBC adatforrások*, és nyissa meg a ODBC adatforrások asztali alkalmazást.

1. Válassza a **Hozzáadás** lehetőséget.

    ![Adatforrás hozzáadása](media/connect-odbc/add-data-source.png)

1. Válassza ki **ODBC-illesztőprogram 17 az SQL Server** majd **Befejezés**.

    ![Illesztőprogram kiválasztása](media/connect-odbc/select-driver.png)

1. Adjon meg egy nevet és leírást a kapcsolat és a fürt, amelyhez csatlakozni kíván, majd válassza ki **tovább**. A fürt URL-címet kell lennie a képernyő  *\<ClusterName\>.\< Régió\>. kusto.windows.net*.

    ![Kiszolgáló kiválasztása](media/connect-odbc/select-server.png)

1. Válassza ki **Active Directory integrált** majd **tovább**.

    ![Active Directoryba integrált](media/connect-odbc/active-directory-integrated.png)

1. Ezután válassza ki az adatbázist a mintaadatok **tovább**.

    ![Alapértelmezett adatbázis váltása](media/connect-odbc/change-default-database.png)

1. Minden beállítás a következő képernyőn, hagyja, mert alapértelmezés szerint, majd válassza ki **Befejezés**.

1. Válassza ki **tesztelése adatforrás**.

    ![Teszt adatforrás](media/connect-odbc/test-data-source.png)

1. Győződjön meg arról, hogy a teszt sikeres volt-e majd **OK**. Ha a teszt nem sikerült, ellenőrizze az előző lépésben megadott értékeket, és ellenőrizze, hogy megfelelő engedélyekkel a fürthöz való csatlakozáshoz.

    ![Teszt sikerült](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Adatok megjelenítése a Tableau (nem kötelező)

Most végzett ODBC konfigurálása, a mintaadatokat, Tableau is tenné.

1. A Tableau Desktop, a bal oldali menüben válassza **más adatbázisok ODBC**.

    ![Kapcsolódás ODBC-vel](media/connect-odbc/connect-odbc.png)

1. A **DSN**, jelölje ki az adatforrást, az ODBC számára létrehozott, majd válassza ki **bejelentkezés**.

    ![ODBC-bejelentkezés](media/connect-odbc/odbc-sign-in.png)

1. A **adatbázis**, válassza ki például az adatbázist a mintául szolgáló fürtön *TestDatabase*. A **séma**válassza *dbo*, és a **tábla**, jelölje be a *StormEvents* mintatáblát.

    ![Adatbázis és tábla kijelölése](media/connect-odbc/select-database-table.png)

1. A tableau most a mintaadatok sémáját jeleníti meg. Válassza ki **frissítés most** ahhoz, hogy az adatokat, a Tableau.

    ![Adatok frissítése](media/connect-odbc/update-data.png)

    Az adatok importálásakor a Tableau, az alábbi képhez hasonló látható sornyi adatot.

    ![Eredményhalmaz](media/connect-odbc/result-set.png)

1. Most Tableau Azure Data Explorer beillesztette adatokon alapuló vizualizációkat hozhat létre. További információkért lásd: [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>További lépések

[Lekérdezések írása az Azure Data Explorerhez](write-queries.md)

[Oktatóanyag: A Power bi-ban az Azure Data Explorer adatainak megjelenítése](visualize-power-bi.md)