---
title: Azure-beli virtuális gépeken lévő SAP HANA adatbázisok biztonsági mentésének kezelése
description: Ebből a cikkből megtudhatja, hogyan kezelheti és figyelheti az Azure-beli virtuális gépeken futó SAP HANA adatbázisok felügyeletére és figyelésére vonatkozó általános feladatokat.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: f76054c7c78c55a9754975267ee4fa3caab968a3
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288346"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>SAP HANA adatbázisok biztonsági mentésének kezelése és figyelése

Ez a cikk az Azure-beli virtuális gépen (VM) futó SAP HANA adatbázisok felügyeletének és figyelésének általános feladatait ismerteti, amelyek biztonsági mentése a [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) szolgáltatás által Azure Backup Recovery Services-tárolóba történik. Megismerheti a feladatok és a riasztások figyelését, az igény szerinti biztonsági mentés elindítását, a házirendek szerkesztését, az adatbázis-védelem leállítását és folytatását, valamint a virtuális gépek biztonsági másolatokból való regisztrációját.

Ha még nem konfigurálta a biztonsági mentéseket a SAP HANA adatbázisokhoz, tekintse [meg a SAP HANA adatbázisok biztonsági mentése Azure virtuális gépeken](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)című témakört.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Manuális biztonsági mentési feladatok figyelése a portálon

Azure Backup megjeleníti az összes manuálisan aktivált feladatot a Azure Portal **biztonsági mentési feladatok** szakaszában.

![Biztonsági mentési feladatok szakasz](./media/sap-hana-db-manage/backup-jobs.png)

A portálon megjelenő feladatok közé tartozik az adatbázis-felderítés és a regisztrálás, valamint a biztonsági mentési és visszaállítási műveletek. Az ütemezett feladatok, beleértve a naplók biztonsági mentését, nem jelennek meg ebben a szakaszban. A SAP HANA natív ügyfelektől (Studio/cockpit/DBA pilótafülke) manuálisan indított biztonsági mentések itt nem jelennek meg.

![Biztonsági mentési feladatok listája](./media/sap-hana-db-manage/backup-jobs-list.png)

A figyeléssel kapcsolatos további információkért tekintse meg [a Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) és a [figyelés a Azure monitor használatával](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)című témakört.

## <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

A riasztások a SAP HANA adatbázisok biztonsági mentésének egyszerű figyelését jelentik. A riasztások segítséget nyújtanak a lehető legtöbbet a biztonsági másolatok által generált események sokaságának elvesztése nélkül. Azure Backup lehetővé teszi a riasztások beállítását, és a következőképpen figyelhetők:

* Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
* A tároló irányítópultján válassza a **biztonsági mentési riasztások**lehetőséget.

  ![Biztonsági mentési riasztások a tároló irányítópultján](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Ekkor megtekintheti a riasztásokat:

  ![Biztonsági mentési riasztások listája](./media/sap-hana-db-manage/backup-alerts-list.png)

* További részletekért kattintson a riasztásokra:

  ![Riasztás részletei](./media/sap-hana-db-manage/alert-details.png)

Napjainkban Azure Backup lehetővé teszi a riasztások e-mailben történő küldését. Ezek a riasztások a következők:

* Az összes biztonsági mentési hiba miatt aktiválódik.
* Az adatbázis szintjén összesítve hibakód jelenik meg.
* Csak az adatbázis első biztonsági mentési hibája miatt lett elküldve.

ToTo további információ a figyelésről [: a Azure Portal figyelése](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) és [figyelése Azure monitor használatával](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Felügyeleti műveletek

A Azure Backup egy biztonsági másolattal rendelkező SAP HANA adatbázis felügyeletét teszi egyszerűbbé az általa támogatott felügyeleti műveletek sokaságával. A következő szakaszokban részletesebben ismertetjük ezeket a műveleteket.

### <a name="run-an-ad-hoc-backup"></a>Alkalmi biztonsági mentés futtatása

A biztonsági mentések a szabályzat ütemezésével összhangban futnak. Az igény szerinti biztonsági mentést az alábbi módon futtathatja:

1. A tároló menüjében kattintson a **biztonsági másolati elemek elemre**.
2. A **biztonsági másolati elemek**területen válassza ki a SAP HANA adatbázist futtató virtuális gépet, majd kattintson a **biztonsági mentés**elemre.
3. A **biztonsági mentés most**a Calendar (naptár) vezérlőelem használatával válassza ki azt az utolsó napot, ameddig a helyreállítási pontot meg kell őrizni. Ezután kattintson az **OK** gombra.
4. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján követheti nyomon > a **biztonsági mentési feladatok** > **folyamatban**van. Az adatbázis méretétől függően a kezdeti biztonsági mentés hosszabb időt is igénybe vehet.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Natív ügyfél biztonsági mentésének futtatása az Azure Backup szolgáltatást használó adatbázison SAP HANA

Ha helyi biztonsági mentést szeretne készíteni (a HANA Studio/cockpit használatával) egy olyan adatbázisról, amelyről biztonsági mentés készül Azure Backupsal, tegye a következőket:

1. Várjon, amíg befejeződik az adatbázis teljes vagy naplózott biztonsági mentése. Az állapot ellenõrzése SAP HANA Studio/pilótafülkében.
2. Tiltsa le a naplók biztonsági mentését, és állítsa a biztonsági mentési katalógust a fájlrendszerre a megfelelő adatbázishoz.
3. Ehhez kattintson duplán a **systemdb** > **konfiguráció** > válassza az **adatbázis** > **szűrő (napló)** lehetőséget.
4. A **enable_auto_log_backup** beállítása **nem**értékre.
5. **Log_backup_using_backint** beállítása **hamis**értékre.
6. Készítsen ad hoc teljes biztonsági mentést az adatbázisról.
7. Várjon, amíg befejeződik a teljes biztonsági mentés és a katalógus biztonsági mentése.
8. A korábbi beállítások visszaállítása az Azure-ba:
   * Állítsa a Enable_auto_log_backup **értéket igen**értékre.
   * A **log_backup_using_backint** beállítása **igaz**értékre.

### <a name="edit-underlying-policy"></a>Alapul szolgáló szabályzat szerkesztése

Házirend módosítása a biztonsági mentési gyakoriság vagy a megőrzési tartomány módosításához:

* A tároló irányítópultján lépjen a > **biztonsági mentési szabályzatok** **kezelése** elemre.

  ![Biztonsági mentési szabályzatok a tároló irányítópultján](./media/sap-hana-db-manage/backup-policies-dashboard.png)

* Válassza ki a szerkeszteni kívánt szabályzatot:

  ![Biztonsági mentési szabályzatok listája](./media/sap-hana-db-manage/backup-policies-list.png)

  ![Biztonsági mentési szabályzat részletei](./media/sap-hana-db-manage/backup-policy-details.png)

>[!NOTE]
> A megőrzési időtartam változásai visszamenőlegesen lesznek alkalmazva az újakon kívül az összes korábbi helyreállítási pontra.
>
> A növekményes biztonsági mentési szabályzatok nem használhatók SAP HANA adatbázisokhoz. Ezen adatbázisok esetében a növekményes biztonsági mentés jelenleg nem támogatott.

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA-adatbázis védelmének leállítása

Több módon is leállíthatja a SAP HANA adatbázisok védelmét:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törölje az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy kihagyja a helyreállítási pontokat, tartsa szem előtt az alábbi adatokat:

* Az összes helyreállítási pont érintetlen marad, és az összes törlés leáll a védelem leállításakor az adatmegőrzés során.
* A védett példány és a felhasznált tárterület után díjat számítunk fel. További információ: [Azure Backup díjszabása](https://azure.microsoft.com/pricing/details/backup/).
* Ha töröl egy adatforrást a biztonsági mentések leállítása nélkül, az új biztonsági mentések sikertelenek lesznek.

Az adatbázis védelmének leállítása:

* A tároló irányítópultján válassza a **biztonsági másolati elemek**lehetőséget.
* A **biztonsági mentési felügyelet típusa**területen válassza **a SAP HANA lehetőséget az Azure virtuális gépen**

  ![SAP HANA kiválasztása az Azure-beli virtuális gépen](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Válassza ki azt az adatbázist, amelynek a védelmét le szeretné állítani:

  ![Válassza ki az adatbázist a védelem leállításához](./media/sap-hana-db-manage/select-database.png)

* Az adatbázis menüben válassza a **biztonsági mentés leállítása**lehetőséget.

  ![Válassza a biztonsági mentés leállítása lehetőséget.](./media/sap-hana-db-manage/stop-backup.png)

* A **biztonsági mentés leállítása** menüben válassza ki, hogy szeretné-e megőrizni vagy törölni az adattárolást. Ha szeretné, adjon meg egy okot és egy megjegyzést.

  ![Az adatmegőrzés vagy-törlés kiválasztása](./media/sap-hana-db-manage/retain-backup-data.png)

* Válassza a **biztonsági mentés leállítása**lehetőséget.

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA-adatbázis védelmének folytatása

Ha leállítja a SAP HANA-adatbázis védelmét, ha bejelöli a **biztonsági mentési adat megőrzése** beállítást, később folytathatja a védelmet. Ha nem őrzi meg a biztonsági másolatban szereplő adatait, nem fogja tudni folytatni a védelmet.

SAP HANA-adatbázis védelmének folytatása:

* Nyissa meg a biztonsági mentési elemet, és válassza a **biztonsági mentés folytatása**lehetőséget.

   ![Válassza a biztonsági mentés folytatása lehetőséget.](./media/sap-hana-db-manage/resume-backup.png)

* A **biztonsági mentési házirend** menüben válasszon ki egy házirendet, majd kattintson a **Mentés**gombra.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Frissítés SAP HANA 1,0 – 2,0

Megtudhatja, hogyan folytathatja a biztonsági mentést egy SAP HANA adatbázisról a [1,0-2,0-es verzióra való SAP HANA frissítés után](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Frissítés SID-változás nélkül

Megtudhatja, hogyan folytathatja a SAP HANA-adatbázis biztonsági mentését, amelynek SID-je [a frissítés után nem módosult](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-database"></a>SAP HANA adatbázis regisztrációjának törlése

A védelem letiltása, de a tár törlése előtt törölje a SAP HANA példány regisztrációját:

* A tároló irányítópultjának **kezelés**területén válassza a **biztonsági mentési infrastruktúra**elemet.

   ![Biztonsági mentési infrastruktúra kiválasztása](./media/sap-hana-db-manage/backup-infrastructure.png)

* A **biztonsági mentési felügyelet típusának** kiválasztása **Munkaterhelésként az Azure-beli virtuális gépen**

   ![A biztonsági mentési felügyelet típusának kiválasztása Munkaterhelésként az Azure-beli virtuális gépen](./media/sap-hana-db-manage/backup-management-type.png)

* A **védett kiszolgálók**lapon válassza ki a regisztrálni kívánt példányt. A tár törléséhez meg kell szüntetnie az összes kiszolgáló/példány regisztrációját.

* Kattintson a jobb gombbal a védett példányra, majd válassza a **Regisztráció törlése**lehetőséget.

   ![Regisztráció törlése](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogy miként lehet [elhárítani a SAP HANA adatbázisok biztonsági mentése során felmerülő gyakori problémákat.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)

