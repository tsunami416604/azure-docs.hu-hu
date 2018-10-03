---
title: Azure Blockchain Workbench-adatok használata az SQL Server Management Studióval
description: Megtudhatja, hogy hogyan csatlakozhat az Azure Blockchain Workbench SQL Database-adatbázisához az SQL Server Management Studióból.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 405104554b4ee8e773b6d2e39966a262f98beb6d
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48243144"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Azure Blockchain Workbench-adatok használata az SQL Server Management Studióval

A Microsoft SQL Server Management Studióban gyorsan írhat lekérdezéseket az Azure Blockhain Workbench SQL-adatbázisához, és azonnal tesztelheti is őket. Ebből a szakaszból részletesen megismerheti, hogy hogyan csatlakozhat az Azure Blockchain Workbench SQL Database-adatbázisához az SQL Server Management Studióból.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) alkalmazást.

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Az SQL Server Management Studio csatlakoztatása az Azure Blockchain Workbench adataihoz

1. Nyissa meg az SQL Server Management Studio alkalmazást, és válassza a **Connect** (Csatlakozás) lehetőséget.
2. Válassza a **Database Engine** (Adatbázismotor) lehetőséget.

    ![Adatbázismotor](./media/data-sql-management-studio/database-engine.png)

3. A **Connect to Server** (Csatlakozás kiszolgálóhoz) párbeszédpanelen adja meg az adatbázisa hitelesítő adatait.

    Ha az Azure Blockchain Workbench telepítési folyamata során létrehozott hitelesítő adatokat használja, akkor a felhasználónév **dbadmin** lesz, a jelszó pedig a telepítés során megadott jelszó.

    ![SQL-adatbázis hitelesítő adatainak megadása](./media/data-sql-management-studio/sql-creds.png)

 4. Az SQL Server Management Studio képes megjeleníteni az adatbázisokat, adatbázisnézeteket és tárolt eljárásokat az Azure Blockchain Workbench adatbázisából.

    ![Adatbázisok listája](./media/data-sql-management-studio/db-list.png)

5. Az adatbázisnézetekhez tartozó adatok megtekintéséhez automatikusan létrehozhat egy SELECT utasítást az alábbi lépések végrehajtásával.
6. Kattintson a jobb gombbal egy tetszőleges adatbázisnézetre az Object Explorerben.
7. Válassza a **Script View as** (Megtekintési szkript) lehetőséget.
8. Válassza a **SELECT to** (SELECT cél) lehetőséget.
9. Válassza a **New Query Editor Window** (Új Lekérdezésszerkesztő ablak) lehetőséget.
10. Új lekérdezést a **New Query** (Új lekérdezés) lehetőség választásával hozhat létre.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)