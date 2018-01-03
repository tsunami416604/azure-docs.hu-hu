---
title: "Állítsa vissza az Azure SQL Data Warehouse (Azure-portál) |} Microsoft Docs"
description: "Az Azure portál feladatokat az Azure SQL-adatraktár a visszaállításakor."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 6be187784a68174b63f7fc068c772826a0db52c6
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Állítsa vissza az Azure SQL Data Warehouse (portál)
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Portál][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
Ön ebből a cikkből megtudhatja, hogyan Azure SQL Data Warehouse visszaállítása az Azure-portál használatával.

## <a name="before-you-begin"></a>Előkészületek
**A DTU-kapacitásának ellenőrzése.** Az SQL Data Warehouse-példányokhoz egy SQL server (például myserver.database.windows.net), amely alapértelmezett adatok átviteli egységek (DTU) tartozó kvóta üzemelteti. Az SQL Data Warehouse próbál visszaállítani, győződjön meg arról, hogy az SQL server rendelkezik-e az adatbázishoz, amely éppen visszaállítása elég fennmaradó DTU-kvótát. A DTU-kvótát kiszámításához, vagy kérjen további dtu-k, lásd: [DTU-kvótát Módosítás kérése][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Az aktív vagy szüneteltetett adatbázis visszaállítása
Egy adatbázis visszaállításához:

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A bal oldali panelen válassza ki a **Tallózás**, majd válassza ki **SQL Server-kiszolgálók**.

    ![Válassza ki a Tallózás > SQL Server-kiszolgálók](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. A kiszolgáló található, és állítsa be azt.

    ![Válassza ki a kiszolgálót](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Az SQL Data Warehouse szeretné visszaállítani kívánt példány, és állítsa be azt.

    ![Válassza ki a példány az SQL Data Warehouse visszaállítása](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. A Data Warehouse panel felső részén válassza ki a **visszaállítása**.

    ![Válassza ki a visszaállítási](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Adjon meg egy új **adatbázisnév**.
7. Válassza ki a legújabb **visszaállítási pont**.

   Győződjön meg arról, hogy a legutóbbi helyreállítási pontot választja. Visszaállítási pontok jelennek meg az egyezményes világidő (UTC), mert az alapértelmezett beállítás nem feltétlenül a legutóbbi helyreállítási pontot.

      ![Válasszon ki egy helyreállítási pontot](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Kattintson az **OK** gombra.
9. Az adatbázis visszaállítási folyamat megkezdődik, és használhatja **értesítések** használatával figyelheti a folyamatot.

> [!NOTE]
> A visszaállítás befejeződését követően a helyreállított adatbázis követve konfigurálhatja [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
A törölt adatbázisok visszaállítása:

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A bal oldali panelen válassza ki a **Tallózás**, majd válassza ki **SQL Server-kiszolgálók**.

    ![Válassza ki a Tallózás > SQL Server-kiszolgálók](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. A kiszolgáló található, és állítsa be azt.

    ![Válassza ki a kiszolgálót](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Görgessen le a **műveletek** szakaszt, a kiszolgálójuk paneljéről.
5. Válassza ki a **adatbázisait törölte a** csempére.

    ![Válassza ki a törölt adatbázisok csempére](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Válassza ki a visszaállítani kívánt törölt adatbázis.

    ![Válasszon ki egy adatbázist visszaállítása](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Adjon meg egy új **adatbázisnév**.

    ![Adja hozzá az adatbázis nevét](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Kattintson az **OK** gombra.
9. Az adatbázis visszaállítási folyamat megkezdődik, és használhatja **értesítések** használatával figyelheti a folyamatot.

> [!NOTE]
> A visszaállítás befejezése után az adatbázis konfigurálásához lásd: [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
>
>

## <a name="next-steps"></a>További lépések
Az üzleti folytonosságot biztosító szolgáltatásokat az Azure SQL Database kiadásainak kapcsolatban, olvassa el a [Azure SQL Database üzleti folytonosság – áttekintés][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
