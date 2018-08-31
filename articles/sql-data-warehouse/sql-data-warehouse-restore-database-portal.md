---
title: Állítsa vissza az Azure SQL Data Warehouse (az Azure portal) |} A Microsoft Docs
description: Azure-portál feladatok az Azure SQL Data Warehouse visszaállításához.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 8752bc1977bac75928651a62576272ffc673ee7e
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306300"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Állítsa vissza az Azure SQL Data Warehouse (portál)
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
Ebben a cikkben megtudhatja hogyan állíthatja vissza az Azure SQL Data Warehouse az Azure portal használatával.

## <a name="before-you-begin"></a>Előkészületek
**A dtu-k kapacitásának ellenőrzése.** Az SQL Data Warehouse minden példánya egy SQL Servert (például a myserver.database.windows.net) rendelkezik egy alapértelmezett data átviteli egységek (DTU) kvóta üzemelteti. Az SQL Data Warehouse a visszaállításhoz, győződjön meg arról, hogy az SQL-kiszolgáló rendelkezik-e elég fennmaradó DTU-kvótával az adatbázis, amely már visszaállítása. DTU-kvóta kiszámítása vagy kérjen további dtu-k kezelésével kapcsolatos információkért lásd: [DTU-kvóta módosítási kérése][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Egy aktív vagy szüneteltetett adatbázis visszaállítása
Adatbázis visszaállítása:

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A bal oldali panelen válassza ki a **Tallózás**, majd válassza ki **SQL Server-kiszolgálók**.

    ![Válassza a Tallózás > SQL Server-kiszolgálók](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Keresse meg a kiszolgáló, és válassza ki azt.

    ![Válassza ki a kiszolgálót](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Keresse meg az SQL Data Warehouse, a visszaállítani kívánt példányát, és válassza ki azt.

    ![Válassza ki az SQL Data warehouse visszaállítása](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Válassza ki a Data Warehouse panel tetején lévő **visszaállítása**.

    ![Válassza ki a visszaállítás](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Adja meg egy új **adatbázisnév**.
7. Válassza ki a legújabb **visszaállítási pont**.

   Ellenőrizze, hogy a legutóbbi helyreállítási pontot választja. Visszaállítási pontok jelenjenek meg az egyezményes világidő (UTC), mert az alapértelmezett beállítás a nem a legújabb visszaállítási pont.

      ![Egy visszaállítási pont kijelölése](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Kattintson az **OK** gombra.
9. Az adatbázis-visszaállítási folyamat elindul, és használhatja **értesítések** a folyamat megfigyeléséhez.

> [!NOTE]
> A visszaállítás befejezése után, a helyreállított adatbázis konfigurálhatja az alábbi [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
Törölt adatbázis visszaállítása:

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A bal oldali panelen válassza ki a **Tallózás**, majd válassza ki **SQL Server-kiszolgálók**.

    ![Válassza a Tallózás > SQL Server-kiszolgálók](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Keresse meg a kiszolgáló, és válassza ki azt.

    ![Válassza ki a kiszolgálót](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Görgessen le a **műveletek** szakasz az a kiszolgáló panelen.
5. Válassza ki a **törölt adatbázisok** csempére.

    ![A törölt adatbázisok csempére](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Válassza ki a visszaállítani kívánt törölt adatbázisok.

    ![Válassza ki az adatbázis visszaállítása](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Adja meg egy új **adatbázisnév**.

    ![Adja hozzá az adatbázis nevét](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Kattintson az **OK** gombra.
9. Az adatbázis-visszaállítási folyamat elindul, és használhatja **értesítések** a folyamat megfigyeléséhez.

> [!NOTE]
> A visszaállítás befejezése után az adatbázis beállítása: [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
>
>

## <a name="next-steps"></a>További lépések
Az üzletmenet-folytonossági funkciókat az Azure SQL Database-kiadás kapcsolatos további információkért olvassa el a [Azure SQL Database üzletmenet folytonossága – áttekintés][Azure SQL Database business continuity overview].

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
