---
title: Az Azure Adatkezelő ODBC-összekötő használata a Tablói adatmegjelenítéshez
description: Ebből a cikkből megtudhatja, hogyan használható a nyílt adatbázis-kapcsolat (ODBC) kapcsolat az Azure Adatkezelő kapcsolattal az adatmegjelenítéshez a Tablóval.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562445"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Adatok megjelenítése az Azure Adatkezelő a Tablóban

 A [tabló](https://www.tableau.com/) az üzleti intelligencia vizuális elemzési platformja. Ha az Azure Adatkezelő-hoz szeretne csatlakozni a tablóból, és egy minta fürt adatait szeretné bevezetni, használja a SQL Server Open Database Connectivity (ODBC) illesztőprogramot. 

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következőkre lesz szüksége:

* [Kapcsolódjon az azure adatkezelő az ODBC-vel](connect-odbc.md) az SQL Server ODBC-illesztővel, és kapcsolódjon az Azure Adatkezelőhoz a tablóból. 

* A tabló asztali, teljes vagy [próbaverziós](https://www.tableau.com/products/desktop/download) verziója.

* Egy fürt, amely tartalmazza a StormEvents. További információ: [azure adatkezelő-fürt és-adatbázis létrehozása](create-cluster-database-portal.md) és [mintaadatok begyűjtése az Azure Adatkezelőba](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Adatgyűjtés a Tablóban 

Miután befejezte az ODBC konfigurálását, a mintaadatok beállíthatók a Tablóba.

1. A tabló asztal bal oldali menüjében válassza a **más adatbázisok (ODBC)** elemet.

    ![Kapcsolódás ODBC-vel](media/tableau/connect-odbc.png)

1. Az **DSN**esetében válassza ki az ODBC-hez létrehozott adatforrást, majd válassza a **Bejelentkezés**lehetőséget.

    ![ODBC-bejelentkezés](media/tableau/odbc-sign-in.png)

1. Az **adatbázis**lapon válassza ki a fürtben található adatbázist, például *TestDatabase*. A **séma**területen válassza a *dbo*lehetőséget, majd a **táblánál**válassza ki a *StormEvents* -minta táblát.

    ![Adatbázis és tábla kiválasztása](media/tableau/select-database-table.png)

1. A tabló most a mintaadatok sémáját jeleníti meg. Válassza a **Frissítés most** lehetőséget az adatgyűjtés a tablóba való bekapcsolásához.

    ![Adatok frissítése](media/tableau/update-data.png)

    Az adatok importálásakor a tabló az alábbi képhez hasonló adatsorokat jelenít meg.

    ![Eredményhalmaz](media/tableau/result-set.png)

1. Most már létrehozhat vizualizációkat a Tablóban az Azure Adatkezelőból bevitt adatok alapján. További információ: a [tabló megismerése](https://www.tableau.com/learn).

## <a name="next-steps"></a>További lépések

* [Lekérdezések írása az Azure Data Explorerhez](write-queries.md)