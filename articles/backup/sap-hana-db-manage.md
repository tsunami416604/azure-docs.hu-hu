---
title: SAP HANA-adatbázisok kezelése az Azure virtuális gépein
description: Ebben a cikkben ismerje meg az Azure virtuális gépeken futó SAP HANA-adatbázisok kezelésével és figyelésével kapcsolatos gyakori feladatokat.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480062"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Biztonsági másolattal rendelkező SAP HANA-adatbázisok kezelése és monitorozása

Ez a cikk ismerteti az Azure-virtuális gépen (VM) futó SAP HANA-adatbázisok kezelésével és figyelésével kapcsolatos gyakori feladatokat, amelyekről az [Azure Backup Service](https://docs.microsoft.com/azure/backup/backup-overview) szolgáltatás biztonsági másolatot készít. Megtudhatja, hogyan figyelheti a feladatokat és riasztásokat, hogyan indíthat el igény szerinti biztonsági mentést, szerkesztheti a szabályzatokat, leállíthatja és folytathatja az adatbázis-védelmet, és hogyan szüntetheti meg a virtuális gépek regisztrációját a biztonsági mentésekből.

Ha még nem konfigurálta az SAP HANA-adatbázisok biztonsági másolatait, olvassa el [az SAP HANA-adatbázisok biztonsági mentése az Azure virtuális gépeken.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Kézi biztonsági mentési feladatok figyelése a portálon

Az Azure Backup az Azure Portal **biztonsági mentési feladatok** szakaszában jeleníti meg az összes manuálisan aktivált feladatot.

![Biztonsági mentési feladatok szakasz](./media/sap-hana-db-manage/backup-jobs.png)

A portálon látható feladatok közé tartozik az adatbázis felderítése és regisztrálása, valamint a biztonsági mentési és visszaállítási műveletek. Az ütemezett feladatok, beleértve a naplóbiztonsági mentéseket is, nem jelennek meg ebben a szakaszban. Manuálisan aktivált biztonsági mentések az SAP HANA natív ügyfelek (Studio / Cockpit / DBA Cockpit) is nem jelennek meg itt.

![Biztonsági mentési feladatok listája](./media/sap-hana-db-manage/backup-jobs-list.png)

Ha többet szeretne megtudni a figyelésről, olvassa el [a Figyelés az Azure Portalon](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) és az [Azure Monitor figyelése](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)című részt.

## <a name="view-backup-alerts"></a>Biztonsági mentési riasztások megtekintése

Riasztások egy egyszerű eszköz az SAP HANA-adatbázisok biztonsági mentései figyelése. A riasztások segítségével a legfontosabb eseményekre összpontosíthat anélkül, hogy elveszne a biztonsági mentés által generált események sokaságában. Az Azure Backup lehetővé teszi a riasztások beállítását, és a következőképpen figyelhetők:

* Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
* A tároló irányítópultján válassza a **Biztonsági mentési riasztások**lehetőséget.

  ![Biztonsági mentési riasztások a tároló irányítópultján](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Látni fogja a riasztásokat:

  ![Biztonsági mentési riasztások listája](./media/sap-hana-db-manage/backup-alerts-list.png)

* További részletekért kattintson a riasztásokra:

  ![Riasztás részletei](./media/sap-hana-db-manage/alert-details.png)

Ma az Azure Backup lehetővé teszi a riasztások küldését e-mailben. Ezek a riasztások a következők:

* Minden biztonsági mentési hiba esetén aktiválódik.
* Az adatbázis szintjén hibakóddal összesítve.
* Csak az adatbázis első biztonsági mentési hibája miatt küldve.

Ha többet szeretne megtudni a figyelésről, olvassa el [a Figyelés az Azure Portalon](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) és a [Figyelés az Azure Monitor használatával című részt.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)

## <a name="management-operations"></a>Kezelési műveletek

Az Azure Backup megkönnyíti a biztonsági másolatban található SAP HANA-adatbázis kezelését az általa támogatott felügyeleti műveletek bőségével. Ezeket a műveleteket a következő szakaszok részletesebben tárgyaljuk.

### <a name="run-an-on-demand-backup"></a>Igény szerinti biztonsági mentés futtatása

A biztonsági mentések a házirend-ütemezésnek megfelelően futnak. Igény szerint futtathat biztonsági másolatot az alábbiak szerint:

1. A tároló menüjében kattintson a **Biztonsági másolat parancsra.**
2. A **Biztonsági másolat elemei ben**jelölje ki az SAP HANA-adatbázist futtató virtuális gépét, majd kattintson a Biztonsági mentés **gombra.**
3. A **Biztonsági másolat most**eszközben a naptár vezérlővel jelölje ki azt az utolsó napot, amikor a helyreállítási pontot meg kell őrizni. Ezt követően kattintson az **OK** gombra.
4. A portál értesítéseinek figyelése. A feladat előrehaladását a tároló irányítópultján figyelheti > folyamatban lévő **biztonsági mentési feladatok** > **.** Az adatbázis méretétől függően a kezdeti biztonsági mentés létrehozása eltarthat egy ideig.

### <a name="hana-native-client-integration"></a>HANA natív ügyfélintegráció

Most a HANA natív ügyfelek bármelyikéből indított teljes biztonsági mentések teljes biztonsági másolatként jelennek meg a **Biztonsági mentés elemek** lapon.

![Az utolsó biztonsági mentések futtatása](./media/sap-hana-db-manage/last-backups.png)

Ezek az ad-hoc teljes biztonsági mentések is megjelennek a visszaállítási pontok listájában a visszaállításhoz.

![Visszaállítási pontok listája](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA natív ügyfél biztonsági másolatának futtatása olyan adatbázison, amelyen engedélyezve van az Azure biztonsági mentés

Ha helyi biztonsági mentést szeretne készíteni egy olyan adatbázisról (a HANA Studio / Cockpit használatával), amelyről az Azure Backup biztonsági másolatkészül, tegye a következőket:

1. Várja meg, amíg az adatbázis befejeződik a teljes vagy naplós biztonsági mentésre. Ellenőrizze az SAP HANA Studio/ Cockpit állapotát.
2. Tiltsa le a naplóbiztonsági mentéseket, és állítsa a biztonsági másolat katalógusát a megfelelő adatbázis fájlrendszerére.
3. Ehhez kattintson duplán **a systemdb** > **Configuration** > **Select adatbázisszűrőre** > **(napló)**.
4. Állítsa **enable_auto_log_backup** **Nem**beállításra.
5. A **log_backup_using_backint** értéke **Hamis**.
6. Igény szerinti teljes biztonsági mentést készítsen az adatbázisról.
7. Várja meg, amíg befejeződik a teljes biztonsági mentés és a katalógus biztonsági mentése.
8. Az előző beállítások visszaállítása az Azure-beállításokra:
   * A **enable_auto_log_backup** **beállítása Igen**.
   * Állítsa **log_backup_using_backint** **Igaz**értékre.

### <a name="change-policy"></a>Házirend módosítása

Módosíthatja az SAP HANA biztonsági mentési elem alapjául szolgáló házirendet.

* A tároló irányítópultján nyissa meg a **Biztonsági másolat elemeit:**

  ![Biztonsági másolat elemeinek kijelölése](./media/sap-hana-db-manage/backup-items.png)

* Válassza az **SAP HANA-t az Azure virtuális gépében**

  ![Válassza az SAP HANA-t az Azure virtuális gépében](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Válassza ki azt a biztonságimásolat-elemet, amelynek alapjául szolgáló házirendet módosítani szeretné
* Kattintson a meglévő biztonsági mentési házirendre

  ![Meglévő biztonsági mentési házirend kiválasztása](./media/sap-hana-db-manage/existing-backup-policy.png)

* Módosítsa a házirendet, válasszon a listából. [Szükség esetén hozzon létre egy új biztonsági mentési házirendet.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy)

  ![Házirend kiválasztása legördülő listából](./media/sap-hana-db-manage/choose-backup-policy.png)

* A módosítások mentése

  ![A módosítások mentése](./media/sap-hana-db-manage/save-changes.png)

* A házirend módosítása hatással lesz az összes kapcsolódó biztonsági mentési elemre, és elindítja a megfelelő **konfigurálási védelmi** feladatokat.

>[!NOTE]
> A megőrzési időszak bármilyen változását visszamenőleges hatállyal alkalmazzák az újakon kívül az összes régebbi helyreállítási pontra.
>
> Növekményes biztonsági mentési szabályzatok nem használhatók AZ SAP HANA-adatbázisokhoz. A növekményes biztonsági mentés jelenleg nem támogatott ezeknél az adatbázisoktól.

### <a name="modify-policy"></a>Házirend módosítása

Módosítsa a házirendet a biztonsági másolat típusának, gyakoriságának és megőrzési tartományának módosításához.

>[!NOTE]
>A megőrzési időszak bármilyen változása visszamenőleges hatállyal az újakon kívül az összes régebbi helyreállítási pontra visszamenőleg alkalmazandó.

1. A tároló irányítópultján nyissa meg **> biztonsági mentési szabályzatok kezelése,** és válassza ki a szerkesztni kívánt házirendet.

   ![A szerkesztandó házirend kiválasztása](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Válassza **a Módosítás**lehetőséget.

   ![Módosítás kiválasztása](./media/sap-hana-db-manage/modify-policy.png)

1. Válassza ki a biztonsági mentési típusok gyakoriságát.

   ![A biztonsági mentés gyakoriságának kiválasztása](./media/sap-hana-db-manage/choose-frequency.png)

A házirend módosítása hatással lesz az összes kapcsolódó biztonsági mentési elemre, és elindítja a megfelelő **konfigurálási védelmi** feladatokat.

### <a name="inconsistent-policy"></a>Inkonzisztens házirend

Esetenként egy módosító házirend-művelet egyes biztonsági másolat **elemeinek inkonzisztens** házirend-verziójához vezethet. Ez akkor fordul elő, ha a biztonsági másolat szolgáltatás a biztonsági mentési elem megfelelő **konfigurálása** sikertelen lesz, miután egy módosítási házirend-művelet aktiválódik. A biztonsági másolat elemnézetében a következőképpen jelenik meg:

![Inkonzisztens házirend](./media/sap-hana-db-manage/inconsistent-policy.png)

Az érintett elemek házirend-verzióját egyetlen kattintással javíthatja:

![Házirend-verzió javítása](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA-adatbázis védelmének leállítása

Az SAP HANA-adatbázisok védelme több féleképpen leállíthatja:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törölje az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy elhagyja a helyreállítási pontokat, tartsa szem előtt az alábbi részleteket:

* Minden helyreállítási pont örökre érintetlen marad, minden metszés nek meg kell állnia a stop védelem ben az adatok megőrzésével.
* A védett példányért és a felhasznált tárterületért díjat számítunk fel. További információ: [Azure Backup díjszabás.](https://azure.microsoft.com/pricing/details/backup/)
* Ha a biztonsági mentések leállítása nélkül töröl egy adatforrást, az új biztonsági mentések sikertelenek lesznek.

Adatbázis védelmének leállítása:

* A tároló irányítópultján válassza a **Biztonsági másolat elemei lehetőséget.**
* A **Biztonsági másolat kezelése típusa csoportban**válassza az SAP **HANA elemet az Azure virtuális gépben**

  ![Válassza ki az SAP HANA-t az Azure virtuális gépében](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Válassza ki azt az adatbázist, amelyen le szeretné állítani a védelmet:

  ![A védelem leállításához kijelölt adatbázis kiválasztása](./media/sap-hana-db-manage/select-database.png)

* Az adatbázis menüben válassza a **Biztonsági mentés leállítása parancsot.**

  ![Biztonsági mentés leállítása](./media/sap-hana-db-manage/stop-backup.png)

* A **Biztonsági másolat leállítása** menüben adja meg, hogy meg szeretné-e őrizni vagy törölni szeretné-e az adatokat. Ha szeretné, adjon meg egy okot és megjegyzést.

  ![Adatok megőrzése vagy törlése kijelölése](./media/sap-hana-db-manage/retain-backup-data.png)

* Válassza **a Biztonsági mentés leállítása**lehetőséget.

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA-adatbázis védelmének folytatása

Ha leállítja az SAP HANA-adatbázis védelmét, ha a **Biztonsági másolat adatainak megtartása beállítást választja,** később folytathatja a védelmet. Ha nem őrzi meg a biztonsági másolatot, nem tudja folytatni a védelmet.

SAP HANA-adatbázis védelmének folytatása:

* Nyissa meg a biztonsági másolat elemet, és válassza **a Biztonsági másolat folytatása**lehetőséget.

   ![Válassza a folytatás biztonsági mentését](./media/sap-hana-db-manage/resume-backup.png)

* A **Biztonsági másolat házirend menüjében** válasszon egy házirendet, majd kattintson a **Mentés parancsra.**

### <a name="upgrading-from-sap-hana-10-to-20"></a>Frissítés SAP HANA 1.0-ról 2.0-ra

Ismerje meg, hogyan folytathatja a biztonsági mentést egy SAP HANA [adatbázisról az SAP HANA 1.0-ról 2.0-ra való frissítés után.](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20)

### <a name="upgrading-without-a-sid-change"></a>Frissítés sid-módosítás nélkül

Megtudhatja, hogyan folytathatja egy SAP HANA-adatbázis biztonsági mentését, amelynek [SID-je nem változott a frissítés után.](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change)

### <a name="unregister-an-sap-hana-instance"></a>SAP HANA-példány regisztrációjának megszüntetése

Törölje az SAP HANA-példány regisztrációjának regisztrációt a védelem letiltása után, de a tároló törlése előtt:

* A tároló irányítópultján a **Kezelés**csoportban válassza a **Biztonsági másolat infrastruktúrája**lehetőséget.

   ![Biztonsági mentési infrastruktúra kiválasztása](./media/sap-hana-db-manage/backup-infrastructure.png)

* Válassza ki a **Biztonsági mentés kezelése típust** **számítási feladatként az Azure Virtuális gépben**

   ![Válassza ki a Biztonsági mentés kezelése típust számítási feladatként az Azure Virtuális gépben](./media/sap-hana-db-manage/backup-management-type.png)

* A **Védett kiszolgálók területen**válassza ki a regisztráció megszüntetéséhez kívánt példányt. A tároló törléséhez törölnie kell az összes kiszolgáló/példány regisztrációját.

* Kattintson a jobb gombbal a védett példányra, és válassza **a Regisztráció megszüntetése parancsot.**

   ![Regisztráció megszüntetése kijelölése](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Bővítmény újbóli regisztrálása az SAP HANA-kiszolgáló virtuális gépén

Előfordulhat, hogy a virtuális gép számítási feladatbővítménye érintett lehet valamelyik vagy másik ok miatt. Ilyen esetekben a virtuális gépen aktivált összes művelet sikertelen lesz. Ezután szükség lehet újra regisztrálni a bővítményt a virtuális gép. Újraregisztrálási művelet újratelepíti a számítási feladatok biztonsági mentési bővítményt a virtuális gép a műveletek folytatásához.

Körültekintően használja ezt a beállítást: ha egy már kifogástalan állapotú bővítményt használó virtuális gépen aktiválódik, ez a művelet a bővítmény újraindítását okozza. Ez az összes folyamatban lévő feladat sikertelensedéséhez vezethet. Az újraregisztrálási művelet életbe léptetése előtt ellenőrizze, hogy van-e egy vagy több [tünet.](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [hárítsa el a gyakori problémákat az SAP HANA-adatbázisok biztonsági mentésekor.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
