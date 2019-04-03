---
title: Felügyelheti és figyelheti az SQL Server-adatbázisok-beli virtuális gépen az Azure Backup biztonsági mentésének |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan kezelhet és figyelhet egy Azure virtuális Gépen futó SQL Server-adatbázisok.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: ea5495867d5f453db014e000e01d533d049dc628
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849602"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Kezelése és figyelése biztonsági mentés az SQL Server-adatbázisok

Ez a cikk ismerteti a gyakori feladatokat, a táron kezeléséhez és monitorozásához és, amelyek biztonsági mentése az Azure biztonsági mentése Recovery Services-beli virtuális gépen (VM) futtató SQL Server-adatbázisok a [Azure Backup](backup-overview.md) szolgáltatás. Megtudhatja, feladatok és riasztások figyelése, állítsa le és adatbázis védelmének folytatása, a biztonsági mentési feladatok futtatása és a biztonsági mentéseket a virtuális gép regisztrációjának törlése.

Ha a biztonsági mentések még nem konfigurálta, az SQL Server-adatbázisok, [Azure virtuális gépeken az SQL Server-adatbázisok biztonsági mentése](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>A portál manuális biztonsági mentési feladatok figyelése

Az Azure Backup az összes manuálisan aktivált feladat megjelenik a **biztonsági mentési feladatok** portálon. A feladatok akkor jelenik meg a portál Belefoglalás adatbázis felderítése és regisztrálása és biztonsági mentési és visszaállítási műveletek.

![A biztonsági mentési feladatok portál](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> A **biztonsági mentési feladatok** portál nem jelenik meg az ütemezett biztonsági mentési feladatot. Az SQL Server Management Studio figyelése ütemezett biztonsági mentési feladatok, a következő szakaszban leírtak szerint.
>

Figyelés forgatókönyvek részletes ismertetéséért tekintse [Monitorozás az Azure Portal](backup-azure-monitoring-built-in-monitor.md) és [figyelése az Azure Monitor használatával](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

Naplóalapú biztonsági mentések 15 percenként történik, mert fárasztó lehet biztonsági mentési feladatok figyelése. Az Azure Backup megkönnyíti a figyelés küldése e-mailes riasztásokhoz. A rendszer e-mailes riasztásokhoz:

- Aktivált az összes biztonsági mentési hibáit.
- Az adatbázis szintjén leltároz hibakód.
- A rendszer csak a egy adatbázis első biztonsági mentés sikertelen.

Adatbázis biztonsági mentésekkel kapcsolatos riasztások figyelése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki a tároló irányítópultjának **riasztások és események**.

   ![Válassza ki a riasztások és események](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. A **riasztások és események**válassza **biztonsági mentési riasztások**.

   ![Válassza ki a biztonsági mentési riasztások](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server-adatbázisok védelmének megszüntetése

Többféle módon az SQL Server-adatbázis biztonsági másolatának állíthatja le:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törli az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és a helyreállítási pontok érintetlenül hagyja.

Ha hagyja meg a helyreállítási pontok, tartsa szem előtt ezeket az adatokat:

* Összes helyreállítási pont örökre változatlanok maradnak, minden törlési kell állni a stop-védelem és az adatok megőrzése mellett.
* Meg kell fizetni a védett példány és a felhasznált tárterület mérete. További információkért lásd: [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).
* Ha töröl egy adatforrást biztonsági mentés leállítása nélkül, akkor az új biztonsági mentések sikertelenek lesznek.

Egy adatbázis védelmének megszüntetése:

1. Válassza ki a tároló irányítópultjának **biztonsági másolati elemek**.

2. A **biztonságimásolat-felügyeleti típussal**válassza **SQL Azure-beli virtuális gépen**.

    ![Válassza ki az SQL Azure-beli virtuális gépen](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Válassza ki az adatbázist, amelynek védelmét le szeretné.

    ![Válassza ki az adatbázis védelmének megszüntetése](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Válassza ki az adatbázis menü **biztonsági mentés leállítása**.

    ![Válassza ki a biztonsági mentés leállítása](./media/backup-azure-sql-database/stop-db-button.png)


5. Az a **biztonsági mentés leállítása** menüben válassza ki, hogy megtartja vagy törli az adatokat. Ha azt szeretné, adja meg az okát, és megjegyzést.

    ![Megtartja vagy törli az adatokat a biztonsági mentés leállítása menü](./media/backup-azure-sql-database/stop-backup-button.png)

6. Válassza ki **biztonsági mentés leállítása**.


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

Adja meg a megőrzési időtartam csak másolatot teljes biztonsági mentésre van szüksége, míg más biztonsági mentés esetén a megőrzési időtartam értéke automatikusan 30 napig aktuális időponthoz képest. <br/>
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

## <a name="re-register-extension-on-the-sql-server-vm"></a>Regisztrálja újra az SQL Server rendszerű virtuális gép futtatására szolgáló bővítmény

Egyes esetekben a számítási feladatok futtatására szolgáló bővítmény a virtuális gép több okból, vagy a másik előfordulhat, hogy első negatív. Ezekben az esetekben a virtuális gép által aktivált összes művelet megkezdődik, sikertelen lesz. Ezután szükség lehet újraregisztrálni a bővítményt a virtuális gépen. **Regisztrálja újra az** művelet újratelepíti a számítási feladatok biztonsági mentési bővítményt a virtuális gép számára is megfelelően működjön.  <br>

Használja ezt a beállítást körültekintéssel; javasolt létrehozzon egy virtuális gépen már megfelelő kiterjesztéssel együtt, ez a művelet hatására a bővítmény beszerzése indítani. Emiatt előfordulhat, hogy az összes folyamatban lévő feladat sikertelen. Egy vagy több, jelölje a [tünetek](backup-sql-server-azure-troubleshoot.md#symptoms) a regisztrálja újra a művelet elindítása előtt.

## <a name="next-steps"></a>További lépések

További információkért lásd: [hibáinak elhárítása az SQL Server-adatbázis biztonsági mentések](backup-sql-server-azure-troubleshoot.md).
