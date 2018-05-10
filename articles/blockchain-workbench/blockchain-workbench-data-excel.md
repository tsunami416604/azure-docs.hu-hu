---
title: A Microsoft Excel Azure Blockchain munkaterület adatok használata
description: Ismerje meg, hogyan tölthetők be, és a Microsoft Excel Azure Blockchain munkaterület SQL-adatbázis adatainak megtekintéséhez.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 70297bd0af6322d0f3ac2c719d1827e4bc5898cd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>A Microsoft Excel Azure Blockchain munkaterület adatok megtekintése

A Microsoft Excel Azure Blockchain munkaterület SQL-adatbázis adatainak megtekintéséhez használhatja. A cikkben a lépéseket:

* Csatlakozás a Blockchain munkaterület-adatbázis a Microsoft Excel
* Nézze meg Blockchain munkaterület-adatbázis táblák és nézetek
* Blockchain munkaterület nézet adatok betöltése az Excel programhoz

## <a name="connect-to-the-blockchain-workbench-database"></a>Csatlakozás a Blockchain munkaterület-adatbázis

Adatbázishoz való kapcsolódáshoz a Blockchain munkaterület:

1. Nyissa meg a Microsoft Excel.
2. Az a **adatok** lapra, majd **adatok beolvasása**.
3. Válassza ki **az Azure** majd **az Azure SQL Database**.

   ![Kapcsolódás az Azure SQL Database szolgáltatáshoz](media/blockchain-workbench-data-excel/connect-sql-db.png)

4. Az a **SQL Server-adatbázis** párbeszédpanel:

    * A **Server**, adja meg a Blockchain munkaterület-kiszolgáló nevét.
    * A **(nem kötelező) adatbázis**, adja meg az adatbázis nevét.

   ![Adja meg az adatbázis-kiszolgáló és adatbázis](media/blockchain-workbench-data-excel/provide-server-db.png)

5. Az a **SQL Server-adatbázis** párbeszédpanel navigációs sáv, jelölje be **adatbázis**. Adja meg a **felhasználónév** és **jelszó**, majd válassza ki **Connect**.

    > [!NOTE]
    > Az Azure Blockchain munkaterület központi telepítése során létrehozott hitelesítő adatok használata a **felhasználónév** van `dbadmin`. A **jelszó** egy, létrejön a Blockchain munkaterület üzembe helyezésekor.
    
   ![Adja meg az adatbázis eléréséhez szükséges hitelesítő adatokat](media/blockchain-workbench-data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Nézze meg adatbázis-táblák és nézetek

Miután csatlakozott az adatbázishoz, megnyílik az Excel Navigator párbeszédpanel. A Navigátor segítségével tekintse meg a táblák és nézetek az adatbázisban. A nézetek jelentéskészítéshez, és a nevük fűzve előtagként **vw**.

   ![Excel Navigator előnézete nézet](media/blockchain-workbench-data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Nézet adatok betöltése az Excel-munkafüzet

A következő példa bemutatja, hogyan akkor is adatok betöltése az Excel-munkafüzet megtekintésében.

1. Az a **Navigator** görgetősáv, jelölje be a **vwContractAction** nézet. A **vwContractAction** mutatja. a szerződés az Blockchain munkaterület-adatbázis a műveleteket.
2. Válassza ki **terhelés** beolvasni az összes adatot a nézetben, és elhelyezi az Excel-munkafüzetet.

   ![Az adatok betöltése a nézetből](media/blockchain-workbench-data-excel/view-data.png)

Most, hogy az adatok betöltése, a Excel szolgáltatások segítségével létrehozhatja a saját jelentéseit az Azure Blockchain munkaterület-adatbázis metaadatok és a tranzakciók adatait használja.

## <a name="next-steps"></a>További lépések

* [Adatbázis nézetekhez Azure Blockchain munkaterület](blockchain-workbench-database-views.md)