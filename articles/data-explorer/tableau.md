---
title: Adatok vizualizálása a Tableau megnyitott adatbázis-kapcsolat (ODBC) kapcsolat az Adatkezelőbe az Azure használatával
description: Ebből a cikkből elsajátíthatja egy megnyitott adatbázis-kapcsolat (ODBC) kapcsolatot az adatkezelőt az Azure-kapcsolat használata az adatok vizualizálása a Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499084"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Azure Data Explorer, a Tableau adatainak megjelenítése

 [A tableau](https://www.tableau.com/) az üzleti intelligencia vizuális elemzési platform. Csatlakozás Azure adatkezelő a Tableau és a egy minta-fürtből származó adatokat betöltheti használja az SQL Server megnyitott adatbázis kapcsolat (ODBC) illesztőprogram. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a következőkre lesz szüksége:

* [Csatlakozhat az Azure Data Explorer ODBC](connect-odbc.md) az SQL Server ODBC-illesztőprogramját használja a csatlakozás az Azure Data Explorer a Tableau. 

* A tableau Desktop teljes körű, vagy [próbaverziós](https://www.tableau.com/products/desktop/download) verzió.

* A StormEvents mintaadatokat tartalmazó fürt. További információkért lásd: [hozzon létre egy Azure Data Explorer fürt és az adatbázis](create-cluster-database-portal.md) és [mintaadatok betöltése az Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Adatok megjelenítése a Tableau 

Végzett ODBC konfigurálása, ha a mintaadatokat, Tableau is tenné.

1. A Tableau Desktop, a bal oldali menüben válassza **más adatbázisok ODBC**.

    ![Kapcsolódás ODBC-vel](media/tableau/connect-odbc.png)

1. A **DSN**, jelölje ki az adatforrást, az ODBC számára létrehozott, majd válassza ki **bejelentkezés**.

    ![ODBC-bejelentkezés](media/tableau/odbc-sign-in.png)

1. A **adatbázis**, válassza ki például az adatbázist a mintául szolgáló fürtön *TestDatabase*. A **séma**válassza *dbo*, és a **tábla**, jelölje be a *StormEvents* mintatáblát.

    ![Adatbázis és tábla kijelölése](media/tableau/select-database-table.png)

1. A tableau most a mintaadatok sémáját jeleníti meg. Válassza ki **frissítés most** ahhoz, hogy az adatokat, a Tableau.

    ![Adatok frissítése](media/tableau/update-data.png)

    Az adatok importálásakor a Tableau, az alábbi képhez hasonló látható sornyi adatot.

    ![Eredményhalmaz](media/tableau/result-set.png)

1. Most Tableau Azure Data Explorer beillesztette adatokon alapuló vizualizációkat hozhat létre. További információkért lásd: [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)