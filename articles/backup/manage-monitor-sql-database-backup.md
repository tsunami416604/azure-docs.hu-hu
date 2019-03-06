---
title: Felügyelheti és figyelheti az SQL Server-adatbázisok-beli virtuális gépen az Azure Backup biztonsági mentésének |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan állíthatja vissza, amely készül biztonsági másolat az Azure Backup és egy Azure-beli virtuális gépen futtató SQL Server-adatbázisok.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: da4264047830b21b3ac4dae723dd1fd2f9d7a8f4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432855"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Kezelése és figyelése biztonsági mentés az SQL Server-adatbázisok 


Ez a cikk ismerteti a gyakori feladatokat, a táron kezeléséhez és monitorozásához és, amelyek biztonsági mentése az Azure biztonsági mentése Recovery Services-beli virtuális gépen (VM) futtató SQL Server-adatbázisok a [Azure Backup](backup-overview.md) szolgáltatás. Megtudhatja, feladatok és riasztások figyelése, állítsa le és adatbázis védelmének folytatása, a biztonsági mentési feladatok futtatása és a biztonsági mentéseket a virtuális gép regisztrációjának törlése.


> [!NOTE]
> A az Azure Backup-beli virtuális gépen futó SQL Server-adatbázisok biztonsági mentése jelenleg nyilvános előzetes verzióban érhető el.


Ha a biztonsági mentések még nem konfigurálta, az SQL Server-adatbázisok, [Azure virtuális gépeken az SQL Server-adatbázisok biztonsági mentése](backup-azure-sql-database.md)

##  <a name="monitor-manual-backup-jobs-in-the-portal"></a>A portál manuális biztonsági mentési feladatok figyelése

Az Azure Backup az összes manuálisan aktivált feladat megjelenik a **biztonsági mentési feladatok** portálon. A feladatok akkor jelenik meg a portál Belefoglalás adatbázis felderítése és regisztrálása és biztonsági mentési és visszaállítási műveletek.

![A biztonsági mentési feladatok portál](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> A **biztonsági mentési feladatok** portál nem jelenik meg az ütemezett biztonsági mentési feladatot. Az SQL Server Management Studio figyelése ütemezett biztonsági mentési feladatok, a következő szakaszban leírtak szerint.
>

## <a name="monitor-scheduled-backup-jobs-in-sql-server-management-studio"></a>Az SQL Server Management Studio ütemezett biztonsági mentési feladatok figyelése 

Az Azure Backup minden biztonsági mentési műveletek az SQL natív API-kat használ. A natív API-k segítségével kéri le az összes feladat adatait a [SQL biztonságimásolat-készletet létrehozták tábla](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) az msdb adatbázisban.

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

Naplóalapú biztonsági mentések 15 percenként történik, mert fárasztó lehet biztonsági mentési feladatok figyelése. Az Azure Backup megkönnyíti a figyelés küldése e-mailes riasztásokhoz. A rendszer e-mailes riasztásokhoz:

- Aktivált az összes biztonsági mentési hibáit.
- Az adatbázis szintjén leltároz hibakód.
- A rendszer csak a egy adatbázis első biztonsági mentés sikertelen. 

Adatbázis biztonsági mentésekkel kapcsolatos riasztások figyelése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a tároló irányítópultjának **riasztások és események**.

   ![Válassza ki a riasztások és események](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

1. A **riasztások és események**válassza **biztonsági mentési riasztások**.

   ![Válassza ki a biztonsági mentési riasztások](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server-adatbázisok védelmének megszüntetése

Többféle módon az SQL Server-adatbázis biztonsági másolatának állíthatja le:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törli az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és a helyreállítási pontok érintetlenül hagyja.

Ha hagyja meg a helyreállítási pontok, tartsa szem előtt ezeket az adatokat:

* Hagyja helyreállítási pontot a biztonsági mentési házirendnek megfelelően lesznek törölve. 
* Mindaddig, amíg az összes helyreállítási pont törölve lettek, hogy a védett példány és a felhasznált tárolási díjat kell fizetnie. További információkért lásd: [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).
* Az Azure Backup mindig biztosítja, hogy egy, a legutóbbi helyreállítási pont, amíg nem törli a biztonsági mentési adatokat. 
* Ha töröl egy adatforrást biztonsági mentés leállítása nélkül, akkor az új biztonsági mentések sikertelenek lesznek. 
* Az adatbázis autoprotection engedélyezve van, kivéve, ha letiltja a autoprotection állítsa le nem biztonsági mentések.

Egy adatbázis védelmének megszüntetése:

1. A tároló irányítópultjának alatt **használati**válassza **biztonsági másolati elemek**.

1. A **biztonságimásolat-felügyeleti típussal**válassza **SQL Azure-beli virtuális gépen**.

    ![Válassza ki az SQL Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)


1. Válassza ki az adatbázist, amelynek védelmét le szeretné.

    ![Válassza ki az adatbázis védelmének megszüntetése](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


1. Válassza ki az adatbázis menü **biztonsági mentés leállítása**.

    ![Válassza ki a biztonsági mentés leállítása](./media/backup-azure-sql-database/stop-db-button.png)


1. Az a **biztonsági mentés leállítása** menüben válassza ki, hogy megtartja vagy törli az adatokat. Ha azt szeretné, adja meg az okát, és megjegyzést.

    ![Megtartja vagy törli az adatokat a biztonsági mentés leállítása menü](./media/backup-azure-sql-database/stop-backup-button.png)

1. Válassza ki **biztonsági mentés leállítása**.

  

## <a name="resume-protection-for-a-sql-database"></a>Az SQL-adatbázis védelmének folytatása

Amikor leállítja az SQL database, ha kiválasztja a **biztonsági másolatok adatainak megőrzése** lehetőség, később folytathatja a védelmet. Ha nem megőrzi a biztonsági mentési adatokat, védelem nem folytatható.

SQL-adatbázishoz a védelem folytatásához:

1. Nyissa meg a biztonsági mentési elemet, és válassza ki **biztonsági mentés folytatása**.

    ![Válassza ki a biztonsági mentés folytatása adatbázis védelmének folytatása](./media/backup-azure-sql-database/resume-backup-button.png)

2. Az a **biztonsági mentési szabályzat** menüben válasszon ki egy szabályzatot, és válassza **mentése**.

## <a name="run-an-on-demand-backup"></a>Egy igény szerinti biztonsági mentés futtatása

Igény szerinti biztonsági mentések különböző típusairól futtathatja:

* Teljes biztonsági mentés
* Csak másolatot teljes biztonsági mentés
* Különbségi biztonsági mentés
* Naplóalapú biztonsági mentés

További információkért lásd: [SQL Server biztonsági mentési típusok](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>SQL Server-példány regisztrációjának törlése

SQL Server-példány regisztrációját a védelem letiltása után, de a tároló törlése előtt:

1. A tároló irányítópultjának alatt **kezelés**válassza **biztonsági mentési infrastruktúra**.  

   ![Válassza ki a biztonsági mentési infrastruktúrára](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. A **felügyeleti kiszolgálók**válassza **védett kiszolgálók**.

   ![Válassza ki a védett kiszolgálók](./media/backup-azure-sql-database/protected-servers.png)


3. A **védett kiszolgálók**, válassza ki a kiszolgáló regisztrációjának törlése. A tároló törléséhez az összes kiszolgáló kell regisztrációját.

4. Kattintson a jobb gombbal a védett kiszolgálón, és válassza ki **törlése**.

   ![Válassza a Törlés](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>További lépések

További információkért lásd: [hibáinak elhárítása az SQL Server-adatbázis biztonsági mentések](backup-sql-server-azure-troubleshoot.md).
