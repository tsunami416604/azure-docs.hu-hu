---
title: Tableau-adatok megjelenítéséhez használja az Azure Data Explorer ODBC-összekötőjét
description: Ebben a cikkben megtudhatja, hogyan használhatja az Open Database Connectivity (ODBC) kapcsolatot az Azure Data Explorer-kapcsolattal az adatok tablóval való megjelenítéséhez.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562445"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Az Azure Data Explorer adatainak megjelenítése a Tablóban

 [A Tableau](https://www.tableau.com/) az üzleti intelligencia vizuális elemzési platformja. Ha a Tableau-ból szeretne csatlakozni az Azure Data Explorerhez, és adatokat szeretne behozni egy mintafürtből, használja az SQL Server Open Database Connectivity (ODBC) illesztőprogramot. 

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez a következőkre van szükség:

* [Csatlakozzon az Azure Data Explorerhez](connect-odbc.md) az ODBC használatával az SQL Server ODBC illesztőprogramjával, és csatlakozzon az Azure Data Explorerhez a Tableau-ból. 

* Tableau Desktop, teljes vagy [próbaverzió.](https://www.tableau.com/products/desktop/download)

* A StormEvents mintaadatokat tartalmazó fürt. További információ: [Create an Azure Data Explorer cluster and database](create-cluster-database-portal.md) and [Ingest sample data into Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Adatok megjelenítése a Tablóban 

Miután befejezte az ODBC konfigurálását, mintaadatokat hozhat a Tablóba.

1. A Tableau Desktop bal oldali menüjében válassza az **Egyéb adatbázisok (ODBC)** lehetőséget.

    ![Kapcsolódás ODBC-vel](media/tableau/connect-odbc.png)

1. A **DSN**esetében jelölje ki az ODBC-hez létrehozott adatforrást, majd válassza **a Bejelentkezés**lehetőséget.

    ![ODBC bejelentkezés](media/tableau/odbc-sign-in.png)

1. Adatbázis **esetén**jelölje ki az adatbázist a mintafürtön, például *a TestDatabase*. A **Séma**, válassza *a dbo*, és a **tábla,** válassza ki a *StormEvents* mintatábla.

    ![Adatbázis és tábla kijelölése](media/tableau/select-database-table.png)

1. A Tabló most a mintaadatok sémáját jeleníti meg. Válassza **a Frissítés most** lehetőséget, ha az adatokat a Tablóba szeretné hozni.

    ![Adatok frissítése](media/tableau/update-data.png)

    Az adatok importálásakor a Tabló az alábbi képhez hasonló adatsorokat jelenít meg.

    ![Eredményhalmaz](media/tableau/result-set.png)

1. Most már létrehozhat vizualizációkat a Tablóban az Azure Data Explorerből behozott adatok alapján. További információ: [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)