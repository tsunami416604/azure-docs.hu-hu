---
title: SQL Server-db-ek kezelése és figyelése Azure-beli virtuális gépen
description: Ez a cikk ismerteti, hogyan kezelheti és figyelheti az Azure virtuális gépen futó SQL Server-adatbázisok.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 14e3a4797fe60a3d1857f1e6d947fa0c669bdcfe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537304"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Biztonsági másolattal rendelkező SQL Server-adatbázisok kezelése és monitorozása

Ez a cikk ismerteti az Azure virtuális gépen (VM) futó ÉS az Azure Backup Recovery Services tárolójában az [Azure Backup](backup-overview.md) szolgáltatás által biztonsági mentésre készült SQL Server-adatbázisok kezelésével és figyelésével kapcsolatos gyakori feladatokat. Megtudhatja, hogyan figyelheti a feladatokat és riasztásokat, leállíthatja és folytathatja az adatbázis-védelmet, futtathat biztonsági mentési feladatokat, és hogyan szüntetheti meg a virtuális gép biztonsági mentések ből való regisztrációját.

Ha még nem konfigurálta az SQL Server-adatbázisok biztonsági másolatait, olvassa el [az SQL Server-adatbázisok biztonsági mentése az Azure virtuális gépeken című témakört.](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Kézi biztonsági mentési feladatok figyelése a portálon

Az Azure Backup megjeleníti az összes manuálisan aktivált feladatok a **biztonsági mentési feladatok** portálon. A portálon látható feladatok közé tartozik az adatbázis felderítése és regisztrálása, valamint a biztonsági mentési és visszaállítási műveletek.

![A Biztonsági mentési feladatok portál](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> A **Biztonsági mentési feladatok** portálon nem jelennek meg az ütemezett biztonsági mentési feladatok. Az SQL Server Management Studio használatával figyelheti az ütemezett biztonsági mentési feladatokat, ahogy azt a következő szakasz ismerteti.
>

A figyelési forgatókönyvekkel kapcsolatos részletekért nyissa meg [a Figyelés az Azure Portalon](backup-azure-monitoring-built-in-monitor.md) és az [Azure Monitor használatával című figyelés című](backup-azure-monitoring-use-azuremonitor.md)részt.  

## <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

Mivel a naplóbiztonsági mentések 15 percenként történnek, a biztonsági mentési feladatok figyelése fárasztó lehet. Az Azure Backup megkönnyíti a figyelést az e-mail-értesítések küldésével. Az e-mailben küldött értesítések a következők:

- Minden biztonsági mentési hiba esetén aktiválódik.
- Az adatbázis szintjén hibakóddal összesítve.
- Csak az adatbázis első biztonsági mentési hibája miatt küldve.

Adatbázis-biztonsági mentési riasztások figyelése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tároló irányítópultján válassza a **Riasztások és események**lehetőséget.

   ![Figyelmeztetések és események kiválasztása](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. A **Riasztások és események csoportban**válassza a Biztonsági másolat **készítési riasztások**lehetőséget.

   ![Biztonsági mentési riasztások kiválasztása](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server adatbázis védelmének leállítása

Az SQL Server-adatbázisok biztonsági mentését többféleképpen is leállíthatja:

- Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törölje az összes helyreállítási pontot.
- Állítsa le az összes jövőbeli biztonsági mentési feladatot, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy elhagyja a helyreállítási pontokat, tartsa szem előtt az alábbi részleteket:

- Minden helyreállítási pont örökre érintetlen marad, minden metszés nek meg kell állnia a stop védelem ben az adatok megőrzésével.
- A védett példányért és a felhasznált tárterületért díjat számítunk fel. További információ: [Azure Backup díjszabás.](https://azure.microsoft.com/pricing/details/backup/)
- Ha a biztonsági mentések leállítása nélkül töröl egy adatforrást, az új biztonsági mentések sikertelenek lesznek. A régi helyreállítási pontok a házirendnek megfelelően lejárnak, de egy utolsó helyreállítási pont mindig megmarad, amíg le nem állítja a biztonsági mentéseket, és nem törli az adatokat.

Adatbázis védelmének leállítása:

1. A tároló irányítópultján válassza a **Biztonsági másolat elemei lehetőséget.**

2. A **Biztonsági másolat kezelése típusa csoportban**válassza az SQL lehetőséget az Azure **VM-ben.**

    ![Sql kiválasztása az Azure Virtuális gépben](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Jelölje ki azt az adatbázist, amelynek védelmét le szeretné állítani.

    ![A védelem leállításához kijelölt adatbázis kijelölése](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Az adatbázis menüben válassza a **Biztonsági mentés leállítása parancsot.**

    ![Válassza a Biztonsági mentés leállítása lehetőséget](./media/backup-azure-sql-database/stop-db-button.png)

5. A **Biztonsági másolat leállítása** menüben adja meg, hogy meg szeretné-e őrizni vagy törölni szeretné-e az adatokat. Ha szeretné, adjon meg egy okot és megjegyzést.

    ![Adatok megőrzése vagy törlése a Biztonsági másolat leállítása menüben](./media/backup-azure-sql-database/stop-backup-button.png)

6. Válassza **a Biztonsági mentés leállítása**lehetőséget.

> [!NOTE]
>
>Az adatok törlésével kapcsolatos további információkért tekintse meg az alábbi GYIK-et:
>
>- [Ha egy automatikusan védett példányból törlök egy adatbázist, mi történik a biztonsági másolatokkal?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Ha leállítom egy automatikusan védett adatbázis biztonsági mentési műveletét, mi lesz a viselkedése?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>SQL-adatbázis védelmének folytatása

Ha leállítja az SQL-adatbázis védelmét, ha a Biztonsági másolat adatai nak **beállítását választja,** később folytathatja a védelmet. Ha nem őrzi meg a biztonsági mentési adatokat, nem folytathatja a védelmet.

SQL-adatbázis védelmének folytatása:

1. Nyissa meg a biztonsági másolat elemet, és válassza **a Biztonsági másolat folytatása**lehetőséget.

    ![Az adatbázis-védelem folytatásához válassza a Biztonsági mentés folytatása lehetőséget.](./media/backup-azure-sql-database/resume-backup-button.png)

2. A **Biztonsági másolat házirend menüjében** válasszon egy házirendet, majd kattintson a **Mentés parancsra.**

## <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

Különböző típusú igény szerinti biztonsági mentéseket futtathat:

- Teljes biztonsági mentés
- Csak másolás teljes biztonsági mentés
- Különbségi biztonsági mentés
- Naplóalapú biztonsági mentés

Bár meg kell adnia a megőrzési időtartam csak másolása teljes biztonsági mentés, az igény szerinti teljes biztonsági mentés megőrzési tartománya automatikusan 45 napra lesz állítva az aktuális időtől.

További információt az [SQL Server biztonsági mentési típusai](backup-architecture.md#sql-server-backup-types)című témakörben talál.

## <a name="unregister-a-sql-server-instance"></a>SQL Server-példány regisztrációjának megszüntetése

Törölje az SQL Server-példány regisztrációját a védelem letiltása után, de a tároló törlése előtt:

1. A tároló irányítópultján a **Kezelés**csoportban válassza a **Biztonsági másolat infrastruktúrája**lehetőséget.  

   ![Biztonsági mentési infrastruktúra kiválasztása](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. A **Felügyeleti kiszolgálók csoportban**válassza a **Védett kiszolgálók lehetőséget.**

   ![Védett kiszolgálók kiválasztása](./media/backup-azure-sql-database/protected-servers.png)

3. A **Védett kiszolgálók csoportban**jelölje ki a törölni kívánt kiszolgálót. A tároló törléséhez törölnie kell az összes kiszolgáló regisztrációjának törlését.

4. Kattintson a jobb gombbal a védett kiszolgálóra, és válassza **a Regisztráció megszüntetése parancsot.**

   ![Törlés kijelölése](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Házirend módosítása

Módosítsa a házirendet a biztonsági mentés gyakoriságának vagy adatmegőrzési tartományának módosításához.

> [!NOTE]
> A megőrzési időszak bármilyen változását visszamenőleges hatállyal alkalmazzák az újakon kívül az összes régebbi helyreállítási pontra.

A tároló irányítópultján nyissa meg a Biztonsági **mentési házirendek kezelése** > **lapot,** és válassza ki a szerkesztni kívánt házirendet.

  ![Biztonsági mentési házirend kezelése](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Biztonsági mentési házirend módosítása](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

A házirend módosítása hatással lesz az összes kapcsolódó biztonsági mentési elemre, és elindítja a megfelelő **konfigurálási védelmi** feladatokat.

### <a name="inconsistent-policy"></a>Inkonzisztens házirend

Előfordulhat, hogy egy módosítási házirend-művelet egyes biztonsági másolat elemeinek **inkonzisztens** házirend-verziójához vezethet. Ez akkor fordul elő, ha a biztonsági másolat szolgáltatás a biztonsági mentési elem megfelelő **konfigurálása** sikertelen lesz, miután egy módosítási házirend-művelet aktiválódik. A biztonsági másolat elemnézetében a következőképpen jelenik meg:

  ![Inkonzisztens házirend](./media/backup-azure-sql-database/inconsistent-policy.png)

Az érintett elemek házirend-verzióját egyetlen kattintással javíthatja:

  ![Inkonzisztens házirend javítása](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Bővítmény újbóli regisztrálása az SQL Server virtuális gépén

Néha a számítási feladatok bővítménya a virtuális gép előfordulhat, hogy hatással van az egyik vagy a másik ok miatt. Ilyen esetekben a virtuális gépen aktivált összes művelet sikertelen lesz. Ezután szükség lehet újra regisztrálni a bővítményt a virtuális gép. **Újraregisztrálási** művelet újratelepíti a számítási feladatok biztonsági mentési bővítményt a virtuális gép a műveletek folytatásához.

Óvatosan használja ezt a beállítást; ha egy már kifogástalan állapotú bővítményt aktivál, ez a művelet a bővítmény újraindítását eredményezi. Ez azt eredményezheti, hogy az összes folyamatban lévő feladat sikertelen lesz. Kegyes, ellenőrizze egy vagy több [tünetet,](backup-sql-server-azure-troubleshoot.md#re-registration-failures) mielőtt újra regisztrálna.

## <a name="next-steps"></a>További lépések

További információt az [SQL Server adatbázis biztonsági másolatainak elhárítása](backup-sql-server-azure-troubleshoot.md)című témakörben talál.
