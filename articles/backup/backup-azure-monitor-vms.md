---
title: Azure-beli virtuális gépek biztonsági mentési riasztások figyelése
description: Események és riasztások az Azure virtuális gép biztonsági mentési feladatok monitorozása. E-mailt a riasztások alapján.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: 886c41f669d0b0363f4484b24ba3b9975904d9f7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492215"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Azure-beli virtuális gépek biztonsági mentésével kapcsolatos riasztások figyelése

Riasztások a szolgáltatást, hogy egy teljesítményesemény küszöbértéke teljesül vagy túllépése érkező válaszokat is. Hogy mikor problémák kezdő kritikus fontosságú üzleti dokumentumkezelésben lehet. Riasztások általában nem fordulhat elő, ütemezés szerint, és így hasznos lehet tudni, hogy minél hamarabb után kapcsolatos riasztások történnek. Ha például egy biztonsági mentési vagy visszaállítási feladat sikertelen lesz, ha egy riasztás a hiba öt percen belül. A tároló irányítópultján a biztonsági riasztások csempén kritikus és figyelmeztetési szintű eseményeket jeleníti meg. A biztonsági mentési riasztások beállításai az összes eseményt megtekintheti. De mi a következő lépés Ha egy riasztás történik, amikor éppen dolgozik egy külön probléma? Ha nem tudja, ha a riasztás történik, előfordulhat, hogy egy kisebb elnézését kérjük, vagy veszélyeztethetik a data. Győződjön meg arról, hogy a megfelelő személyek ismeri - riasztás esetén a konfigurálnia a e-mailen keresztül riasztási értesítések küldéséhez. E-mail-értesítések beállításával kapcsolatos részletekért lásd: [értesítések konfigurálása](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Hogyan találhatom meg a riasztások adatai?

Az esemény által kiváltott riasztást kapcsolatos információk megtekintéséhez, meg kell nyitnia a biztonsági mentési riasztások szakaszban. Nyissa meg a biztonsági mentési riasztások szakaszban két módja van: az a biztonsági riasztások csempén a tároló irányítópultján, vagy a riasztások és események szakaszban.

A biztonsági riasztások panel megnyitásához a biztonsági riasztások csempén:

* A a **biztonsági mentési riasztások** csempére a tároló irányítópultjának, **kritikus** vagy **figyelmeztetés** az adott súlyossági szint esetében a működési események megtekintéséhez.

    ![Biztonsági riasztások csempe](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

A biztonsági riasztások panel megnyitásához a riasztások és események szakaszban:

1. A tároló irányítópultján kattintson **minden beállítás**. ![Az összes beállítások gomb](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Az a **beállítások** panelen kattintson a **riasztások és események**. ![Riasztások és események gomb](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Az a **riasztások és események** panelen kattintson a **biztonsági mentési riasztások**. ![Biztonsági mentési riasztások gomb](./media/backup-azure-monitor-vms/backup-alerts.png)

    A **biztonsági mentési riasztások** szakasz megnyílik, és a szűrt riasztásokat jeleníti meg.

    ![Biztonsági riasztások csempe](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Egy adott, az események, a riasztás részletes adatainak megtekintéséhez kattintson a riasztásra nyissa meg a **részletek** szakaszban.

    ![Eseményadat](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Testre szabhatja az attribútumokat, megjelennek a listában, lásd: [további esemény attribútumok megtekintése](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Értesítések konfigurálása

 Beállíthatja, hogy a szolgáltatás, amely az elmúlt egy órában, vagy bizonyos típusú események bekövetkezésekor történt a riasztásokról szóló e-mail értesítések küldéséhez.

Riasztásokhoz kapcsolódó e-mail értesítések beállítása

1. Kattintson a biztonsági mentési riasztások menü **értesítések konfigurálása**

    ![Biztonsági mentési riasztások menüből](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    A konfigurálás értesítések szakaszban nyílik meg.

    ![Értesítések panel konfigurálása](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Az E-mail értesítések, a konfigurálás értesítések szakaszban kattintson **a**.

    A címzettek és súlyosság párbeszédpanelek egy csillag mellettük rendelkezik, mivel ezt az információt kötelező megadni. Adjon meg legalább egy e-mail-címet, és válassza ki legalább egy súlyosságot.
3. Az a **címzettek (E-mail)** párbeszédpanelen írja be az e-mail-címét az értesítéseket kapnak. Használja a következő formátumot: username@domainname.com. Az e-mail címeket pontosvesszővel (;) válassza el.
4. Az a **értesítendő** területen válassza a **Riasztásonként** értesítést küldeni, ha a megadott riasztás történik, vagy **óránkénti összefoglaló** összefoglaló küldése az elmúlt egy óra.
5. Az a **súlyossági** párbeszédpanelen válasszon egy vagy több szint, amelyet szeretne e-mailben értesítést vált.
6. Kattintson a **Save** (Mentés) gombra.

### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Milyen típusú riasztásokról érhetők el az Azure IaaS virtuális gép biztonsági mentése

   | Riasztási szint | Elküldött riasztások |
   | --- | --- |
   | Kritikus | biztonsági mentés sikertelen, a sikertelen helyreállítás |
   | Figyelmeztetés | a biztonsági mentési feladatok sikeres volt, figyelmeztetésekkel fejeződött be (például: néhány írók nem sikerült a pillanatkép létrehozása közben) |
   | Tájékoztató | jelenleg nincsenek tájékoztató riasztások érhetők el az Azure virtuális gép biztonsági mentése |

### <a name="situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Olyan helyzetekben, ahol nem érkezik e-mail, még akkor is, ha az értesítések beállítása

Nincsenek olyan helyzetekben, ahol a rendszer nem küld figyelmeztetést, annak ellenére, hogy az értesítéseket megfelelően legyenek konfigurálva. Az alábbi helyzetekben e-mail értesítések nem érkeznek riasztási zaj elkerülése érdekében:

* Ha az értesítések óránkénti összegzésre vannak konfigurálva, és a egy riasztás kiadása és feloldása egy órán belül.
* A feladat megszakítása.
* Biztonsági mentési feladat aktiválódik, akkor sikertelen lesz, és a egy másik biztonsági mentési művelet folyamatban van.
* A Resource Managert használó virtuális gép ütemezett biztonsági mentési feladat elindul, de a virtuális gép már nem létezik.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Értesítés a sikeres biztonsági mentések tevékenységeket tartalmazó naplók segítségével

> [!NOTE]
> A Recovery Services-tárolók az Azure Backup műveletnaplóinak főfenékvízcsövön új modell áthelyeztük. Sajnos ez a független Felhőkben Azure vizsgálati naplók létrehozása hatással volt. Azure szuverén felhő felhasználók létrehozása vagy nincs konfigurálva minden olyan riasztások tevékenységnaplókból itt említett Azure monitoron keresztül, ha, akkor nem aktiválódik. Ebben az esetben javasoljuk volna az ilyen felhasználók a diagnosztikai beállítások és LA munkaterület vagy [Power bi jelentéskészítő megoldás](backup-azure-configure-reports.md) a vonatkozó információkat lekérni. Emellett minden nyilvános Azure-régióban, ha egy felhasználó gyűjti Recovery Services-Tevékenységnaplók egy Log Analytics-munkaterületre említettek szerint a [Itt](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity), ezek a naplók még nem jelent.

Ha szeretne értesítést kap, ha a biztonsági mentések sikeres, alapuló riasztásokat is használhatja a [tevékenységeket tartalmazó naplók](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) tár.

### <a name="login-into-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az Azure portal és a kapcsolódó Azure Recovery Services-tároló továbbléphet, és kattintson a Tulajdonságok "Activity log" című szakaszában.

### <a name="identify-appropriate-log"></a>Azonosítsa a megfelelő naplót

A alkalmazni a szűrőket, győződjön meg arról, hogy azért küldtük Önnek, a sikeres biztonsági mentések Tevékenységnaplók az alábbi képen is látható. Az időtartomány ennek megfelelően módosítsa rekordok megtekintéséhez.

![Tevékenységnaplók](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Kattintson a "JSON" szegmens további részletekért és a másolás-beillesztés azt egy szövegszerkesztőben az alakzatot tekinti meg. Ekkor megjelenik a tároló részleteit és az elem, amely a tevékenységnapló, azaz a biztonsági másolati elemek.

Kattintson a "Tevékenységnapló-riasztás hozzáadása" az összes napló riasztásokat generálni.

### <a name="add-activity-log-alert"></a>Tevékenységnapló-riasztás hozzáadása

"Tevékenységnapló-riasztás hozzáadása" gombra kattintva megjelenik egy képernyő alább látható módon

![Tevékenységnapló-riasztás](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png) az előfizetésben és erőforráscsoportban a riasztás tárolására szolgálnak. A feltételek előre kitöltött lesz. Ellenőrizze, hogy a követelmény a megfelelő összes értékét.

A sikeres biztonsági mentések a "szint" van megjelölve, a "Tájékoztatási szintű" és "Sikeres" állapotot.

Ha "fent egy erőforrást" lehetőséget választja, a riasztást hoz létre tevékenységeket tartalmazó naplók az adott erőforrás vagy a tárban vannak rögzítve. Ha azt szeretné, hogy a szabály is alkalmazható legyen minden tároló, hagyja meg az "erőforrás" üresnek kell lenniük.

### <a name="define-action-on-alert-firing"></a>A riasztási elsőre művelet megadása

Használja a "művelet"csoport generált riasztás esetén a művelet meghatározására. Kattintson a "Művelettípus" megismernie rendelkezésre álló műveletek ilyen e-mailben vagy SMS/integrációs az ITSM-stb.

![Tevékenység log műveletcsoport](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)

Miután az OK gombra kattint, egy tevékenységnapló-riasztás generál, és a rendszer rögzíti a sikeres biztonsági mentések későbbi tevékenységeket tartalmazó naplók a művelet akkor aktiválódik, ahogyan az a műveletcsoport.

### <a name="limitations-on-alerts"></a>Korlátozások a riasztásokat

Eseményalapú értesítéssel a következő korlátozások vonatkoznak:

1. A figyelmeztetéseket a helyreállítási tárban lévő összes virtuális gépeken. Nem szabhatja testre a riasztás egy Recovery Services-tárolót a virtuális gépek egy részhalmaza számára.
2. Küldi a riasztásokat "alerts-noreply@mail.windowsazure.com". Az e-mailt küldő jelenleg nem módosítható.

## <a name="next-steps"></a>További lépések

Hozza létre újra a virtuális gép helyreállítási pontból információkért tekintse meg [visszaállítása az Azure virtuális gépek](backup-azure-arm-restore-vms.md).

Ha a virtuális gépek védelme információra van szüksége, tekintse meg [első lépések: Recovery Services-tároló virtuális gépek biztonsági mentése](backup-azure-vms-first-look-arm.md).

További információ a felügyeleti tevékenységek a cikk a virtuális gép biztonsági mentéseinek [kezelése az Azure virtuális gépek biztonsági mentéseinek](backup-azure-manage-vms.md).
