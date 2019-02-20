---
title: Felügyelheti és figyelheti az SQL Server-adatbázisok az Azure virtuális gép biztonsági mentése az Azure backuppal |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan készül biztonsági másolat az Azure Backup-beli virtuális gépen futó SQL Server adatbázisok visszaállítása
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430800"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Kezelése és figyelése biztonsági mentés az SQL Server-adatbázisok 


Ez a cikk ismerteti a gyakori feladatokat, a táron kezelése és biztonsági mentése az Azure biztonsági mentése Recovery Services-beli virtuális gépen futó SQL Server adatbázisok figyelése a [Azure Backup](backup-overview.md) szolgáltatás. A műveletek többek között a figyelési feladatok és riasztások, leállítása és adatbázis-védelem folytatása, futó biztonsági mentési feladatok és regisztrációjának megszüntetése egy virtuális Gépet biztonsági másolatból.


> [!NOTE]
> Az Azure Backup-beli virtuális gépen futó SQl Server-adatbázisok biztonsági mentése jelenleg nyilvános előzetes verzióban érhető el.


Még nem konfigurált biztonsági mentés az SQL Server-adatbázisok, kövesse az utasításokat, mégis TIf [Ez a cikk](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs"></a>Biztonsági mentési feladatok figyelése

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>A portálon az alkalmi feladatok figyelése

Az Azure Backup az összes manuálisan aktivált feladat megjelenik a **biztonsági mentési feladatok** portál, beleértve a felderítése és regisztrálása, adatbázisok és a biztonsági mentési és visszaállítási műveletek.

![Biztonsági mentési feladatok portál](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Ütemezett biztonsági mentési feladatok nem láthatók a **biztonsági mentési feladatok** portálon. Az SQL Server Management Studio figyelése ütemezett biztonsági mentési feladatok, a következő szakaszban leírtak szerint.
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>Biztonsági mentési feladatok a figyelő az SQL Server Management Studióval 

Az Azure Backup minden biztonsági mentési műveletek az SQL natív API-kat használ.

A natív API-k segítségével kéri le az összes feladat adatait a [SQL biztonságimásolat-készletet létrehozták tábla](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) az msdb adatbázisban.

Az alábbi példa egy lekérdezést, amely beolvassa az összes biztonsági mentési feladat nevű adatbázishoz **DB1**. A lekérdezés a speciális monitorozás testreszabása.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

Naplóalapú biztonsági mentések 15 percenként történik, mert fárasztó lehet biztonsági mentési feladatok figyelése. Az Azure Backup megkönnyíti a figyelés az e-mailes riasztásokhoz.

- A figyelmeztetéseket az összes biztonsági mentési hibák.
- Riasztások az adatbázis szintjén vannak konszolidált hibakód alapján.
- Értesítő e-mail érkezik, csak az első biztonsági mentés sikertelen egy adatbázis esetében. 

Biztonsági mentésekkel kapcsolatos riasztások figyelése:

1. Jelentkezzen be az Azure-előfizetését a [az Azure portal](https://portal.azure.com) adatbázisokkal kapcsolatos riasztások figyelése.

2. Válassza ki a tároló irányítópultjának **riasztások és események**.

   ![Válassza ki a riasztások és események](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. A **riasztások és események**válassza **biztonsági mentési riasztások**.

   ![Válassza ki a biztonsági mentési riasztások](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server-adatbázisok védelmének megszüntetése

Többféle módon az SQL Server-adatbázis biztonsági másolatának állíthatja le:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törli az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, de a helyreállítási pontok érintetlenül hagyja.

Vegye figyelembe:

Ha a helyreállítási pontok hagyja, a pontokat törlődnek, biztonsági mentési szabályzatának megfelelően. Díj terheli a védett példány és a felhasznált tárterület mérete, amíg az összes helyreállítási pont törlődnek. [További](https://azure.microsoft.com/pricing/details/backup/) információ a díjszabásról.
- Bár a lejárat után a adatmegőrzési szabályzatának megfelelően nem sérültek, hagyja helyreállítási pontokat, amikor Azure Backup mindig megőrzi egy legutóbbi helyreállítási pont mindaddig, amíg explicit módon biztonsági mentési adatok törlése.
- Ha töröl egy adatforrást leállításával biztonsági mentés nélkül, új biztonsági mentései fog kezdenek. Ismét házirend szerint régi helyreállítási pontok érvényessége lejár, de egy, a legutóbbi helyreállítási pont mindig megőrzi a rendszer mindaddig, amíg a biztonsági mentés leállítása, és törölheti az adatokat.
- Biztonsági mentés mindaddig, amíg az automatikus védelem le van tiltva az automatikus védelemhez, engedélyezett egy adatbázis nem állítható le.

Egy adatbázis védelmének megszüntetése:

1. A tároló irányítópultján alatt **használati**válassza **biztonsági másolati elemek**.

    ![A biztonsági másolati elemek menü megnyitásához](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. A **biztonságimásolat-felügyeleti típussal**válassza **SQL Azure-beli virtuális gépen**.

    ![Válassza ki az SQL Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. Válassza ki az adatbázist, amelynek védelmét le szeretné.

    ![Válassza ki az adatbázis védelmének megszüntetése](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. Válassza ki az adatbázis menü **biztonsági mentés leállítása**.

    ![Válassza ki a biztonsági mentés leállítása](./media/backup-azure-sql-database/stop-db-button.png)


6. A **biztonsági mentés leállítása** menüben válassza ki, hogy megtartja vagy törli az adatokat. Megadhat egy OK és megjegyzés.

    ![Állítsa le a biztonsági mentés menü](./media/backup-azure-sql-database/stop-backup-button.png)

7. Kattintson a **biztonsági mentés leállítása** .

  

### <a name="resume-protection-for-a-sql-database"></a>Az SQL-adatbázis védelmének folytatása

Ha a **biztonsági másolatok adatainak megőrzése** lehetőséget választotta, amikor az SQL-adatbázis védelmét le lett állítva, folytathatja a védelmet. Ha a biztonsági mentési adatok nem őrződnek meg, a védelem nem folytatható.

1. Az SQL-adatbázis védelmének visszaállításához, nyissa meg a biztonsági mentési elemet, és válassza **biztonsági mentés folytatása**.

    ![Válassza ki a biztonsági mentés folytatása adatbázis védelmének folytatása](./media/backup-azure-sql-database/resume-backup-button.png)

2. Az a **biztonsági mentési szabályzat** menüben válasszon ki egy szabályzatot, és válassza **mentése**.

## <a name="run-an-on-demand-backup"></a>Egy igény szerinti biztonsági mentés futtatása

Igény szerinti biztonsági mentések különböző típusairól futtathatja:

* Teljes biztonsági mentés
* Csak másolatot teljes biztonsági mentés
* Különbségi biztonsági mentés
* Naplóalapú biztonsági mentés

[További](backup-architecture.md#sql-server-backup-types) tudnivalók az SQL Server biztonsági mentési típusok.

## <a name="unregister-a-sql-server-instance"></a>SQL Server-példány regisztrációjának törlése

SQL Server-példány regisztrációját, miután védelmet letiltotta, de a tároló törlése előtt:

1. A tároló irányítópultjának alatt **kezelés**válassza **biztonsági mentési infrastruktúra**.  

   ![Válassza ki a biztonsági mentési infrastruktúrára](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. A **felügyeleti kiszolgálók**válassza **védett kiszolgálók**.

   ![Válassza ki a védett kiszolgálók](./media/backup-azure-sql-database/protected-servers.png)


3. A **védett kiszolgálók**, válassza ki a kiszolgáló regisztrációjának törlése. A tároló törléséhez az összes kiszolgáló kell regisztrációját.

4. Kattintson a jobb gombbal a védett kiszolgáló > **törlése**.

   ![Válassza a Törlés](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>További lépések

[Felülvizsgálat](backup-sql-server-azure-troubleshoot.md) hibaelhárítási információkat az SQL Server-adatbázis biztonsági mentését.
