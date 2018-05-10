---
title: SQL Server Management Studio Azure Blockchain munkaterület adatok használata
description: 'Útmutató: Azure Blockchain munkaterület SQL-adatbázis SQL Server Management Studio való kapcsolódáshoz.'
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1c9ee1e94ba1195db027c3edfab2e2a96f181ca3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Azure Blockchain munkaterület adatokat az SQL Server Management Studio használatával

Microsoft SQL Server Management Studio lehetővé teszi a gyors fejlesztésére, és Azure Blockhain munkaterület SQL DB lekérdezéseket tesztelésére. Ez a szakasz egy részletes útmutató arról, hogyan kell Azure Blockchain munkaterület SQL adatbázishoz az SQL Server Management Studio.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio Azure Blockchain munkaterület-adatokhoz való kapcsolódásról

1. Az SQL Server Management Studio megnyitásához, és válassza ki **Connect**.
2. Válassza ki **adatbázis-motor**.

    ![Adatbázismotor](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a kiszolgáló nevét és az adatbázis hitelesítő adatai.

    Ha az Azure Blockchain munkaterület központi telepítési folyamat során létrehozott hitelesítő adatok használata esetén a felhasználónév lesz **dbadmin** és a jelszó lesz a telepítés során megadottal.

    ![SQL-hitelesítő adatainak megadása](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio megjeleníti az adatbázisok adatbázis-nézetek és tárolt eljárások az Azure Blockchain munkaterület-adatbázis.

    ![Az adatbázisok listája](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. Az adatbázis nézetek kapcsolódó adatok megjelenítése, automatikusan a select utasítással, az alábbi lépéseket követve hozhat létre.
6. Kattintson a jobb gombbal az Object Explorer adatbázis nézetek.
7. Válassza ki **nézetként parancsfájl**.
8. Válasszon **BEJELÖLÉSÉVEL**.
9. Válassza ki **új Lekérdezésszerkesztő ablak**.
10. Egy új lekérdezést hozhat létre kiválasztásával **új lekérdezés**.

## <a name="next-steps"></a>További lépések

* [Adatbázis nézetekhez Azure Blockchain munkaterület](blockchain-workbench-database-views.md)