---
title: Az Azure Blockchain Workbench-beli SQL-adatbázis használata
description: Megtudhatja, hogy hogyan csatlakozhat az Azure Blockchain Workbench SQL Database-adatbázisához az SQL Server Management Studióból.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 3a734c09b3a74fbe4165f4de25ce450fd3fbde1d
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286726"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Azure Blockchain Workbench-adatok használata az SQL Server Management Studióval

A Microsoft SQL Server Management Studio képes gyorsan írni és tesztelni az Azure Blockchain Workbench SQL DB-vel kapcsolatos lekérdezéseket. Ez a szakasz egy lépésenkénti útmutatót tartalmaz, amely bemutatja, hogyan csatlakozhat az Azure Blockchain Workbench SQL Database a SQL Server Management Studioon belülről.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) alkalmazást.

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Az SQL Server Management Studio csatlakoztatása az Azure Blockchain Workbench adataihoz

1. Nyissa meg az SQL Server Management Studio alkalmazást, és válassza a **Connect** (Csatlakozás) lehetőséget.
2. Válassza a **Database Engine** (Adatbázismotor) lehetőséget.

    ![Adatbázismotor](./media/data-sql-management-studio/database-engine.png)

3. A **Connect to Server** (Csatlakozás kiszolgálóhoz) párbeszédpanelen adja meg az adatbázisa hitelesítő adatait.

    Ha az Azure Blockchain Workbench telepítési folyamata során létrehozott hitelesítő adatokat használja, akkor a felhasználónév **dbadmin**, a jelszó pedig a telepítés során megadott jelszó.

    ![SQL-adatbázis hitelesítő adatainak megadása](./media/data-sql-management-studio/sql-creds.png)

   1. Az SQL Server Management Studio képes megjeleníteni az adatbázisokat, adatbázisnézeteket és tárolt eljárásokat az Azure Blockchain Workbench adatbázisából.

      ![Adatbázisok listája](./media/data-sql-management-studio/db-list.png)

5. Az adatbázisnézetekhez tartozó adatok megtekintéséhez automatikusan létrehozhat egy SELECT utasítást az alábbi lépések végrehajtásával.
6. Kattintson a jobb gombbal bármelyik adatbázis-nézetre a Object Explorer.
7. Válassza a **Script View as** (Megtekintési szkript) lehetőséget.
8. Válassza a **SELECT to** (SELECT cél) lehetőséget.
9. Válassza a **New Query Editor Window** (Új Lekérdezésszerkesztő ablak) lehetőséget.
10. Új lekérdezést a **New Query** (Új lekérdezés) lehetőség választásával hozhat létre.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)
