---
title: Az Azure virtuális gépek biztonsági mentési riasztások
description: Események és riasztások tartalmazzák az Azure virtuális gép biztonsági mentési feladatok figyelése A riasztások alapján e-mail küldése.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 3783014738ec4e8f185531773b1259dc63e7f49f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606307"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Azure-beli virtuális gépek biztonsági mentésével kapcsolatos riasztások figyelése
A riasztások jelezhetik a szolgáltatásból, hogy az esemény küszöbét teljesül vagy túllépése válaszokat. Annak ismerete, ha problémák start alacsonyan tartják a költségeket üzleti kritikus fontosságú lehet. Riasztást általában nem történik meg, ütemezés szerint, és ezért fontos tudni, hogy minél hamarabb után a riasztás akkor jön létre. Például ha egy biztonsági mentési vagy helyreállítási feladat sikertelen lesz, egy riasztás a hiba öt percen belül. A tároló irányítópultjának a biztonsági riasztások csempe kritikus és a figyelmeztetési szintű eseményeket jeleníti meg. A biztonsági riasztások beállításainak megtekintheti az összes esemény. De mi a teendő, ha egy riasztás, ha egy külön probléma dolgozik? Ha nem tudja, ha a riasztás történik, akkor lehet, hogy egy kisebb kellemetlenségért, vagy az adatok biztonságát veszélyeztető. Győződjön meg arról, hogy a megfelelő személyek ismerjék riasztás - következik be, amikor konfigurálja a szolgáltatást riasztási értesítéseket e-mailben küldendő. E-mail értesítések beállításával kapcsolatos részletekért lásd: [értesítések konfigurálása](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Hogyan találhatom meg a riasztások adatai?
Tekintse meg az eseményt, amely kivételt váltott ki riasztást, meg kell nyitnia a biztonsági mentési riasztások szakaszban. Nyissa meg a biztonsági mentési riasztások szakaszban két módja van: vagy a biztonsági mentési riasztások a csempén, a tároló irányítópultjának, vagy a riasztások és események szakaszából.

A biztonsági riasztások panel megnyitása biztonsági riasztások csempén:

* A a **biztonsági mentési riasztások** a tároló irányítópult csempét, kattintson a **kritikus** vagy **figyelmeztetés** az adott súlyossági szint esetében a működési események megtekintéséhez.

    ![Biztonsági riasztások csempe](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

A biztonsági riasztások panel megnyitása a riasztások és események szakaszban:

1. A tároló irányítópulton kattintson **összes beállítás**. ![Minden beállítások gomb](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Az a **beállítások** panelen kattintson a **riasztások és események**. ![Riasztások és események gomb](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Az a **riasztások és események** panelen kattintson a **biztonsági mentési riasztások**. ![Biztonsági riasztások gomb](./media/backup-azure-monitor-vms/backup-alerts.png)

    A **biztonsági mentési riasztások** szakasz megnyílik, és a szűrt riasztásokat jeleníti meg.

    ![Biztonsági riasztások csempe](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Egy adott riasztás események, a részletes adatainak megtekintéséhez kattintson a megnyitni a **részletek** szakasz.

    ![Eseményadat](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Állítsa be az attribútumokat, megjelennek a listában, lásd: [további esemény attribútumok megtekintése](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Értesítések konfigurálása
 A riasztások az elmúlt egy órában keresztül, vagy ha az adott esemény történik előfordult e-mail értesítést a szolgáltatás konfigurálható.

A riasztások értesítő e-mailek beállítása

1. Kattintson a biztonsági mentési riasztások menü **értesítések konfigurálása**

    ![Biztonsági riasztások menü](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Megnyitja a konfigurálási értesítések csoportban.

    ![Értesítések panel konfigurálása](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Az értesítő e-mailek, a konfigurálás értesítések szakaszban kattintson **a**.

    A címzettek és súlyosság párbeszédpanelek mellett csillag van, mert az adatszolgáltatás. Adjon meg legalább egy e-mail címet, és legalább egy súlyosságának kiválasztása.
3. Az a **címzettek (E-mail)** párbeszédpanelen írja be az e-mail címet az értesítéseket kapnak. A formátumot használja: username@domainname.com. Az e-mail címeket pontosvesszővel (;) kell elválasztani.
4. A a **értesítendő** területen válasszon **/ riasztás** értesítést küldeni, ha a megadott riasztás, vagy **óránkénti kivonatoló** összegzés küldése az elmúlt egy óra.
5. Az a **súlyossági** párbeszédpanelen válasszon egy vagy több szint, amely e-mailben értesítést aktiválják.
6. Kattintson a **Save** (Mentés) gombra.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Milyen típusú érhetők el az Azure infrastruktúra-szolgáltatási virtuális gép biztonsági mentése?
   | Riasztási szint | Értesítések küldése |
   | --- | --- |
   | Kritikus | a biztonsági mentési hiba, helyreállítási hiba |
   | Figyelmeztetés | a biztonsági mentési feladatok figyelmeztetésekkel fejeződött be sikeresen (például: néhány író nem tudta létrehozni a pillanatképet) |
   | Tájékoztató | jelenleg nincs információs riasztások léptek fel érhetők el az Azure virtuális gép biztonsági mentése |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Olyankor is előfordul, hogy nem érkezik meg az e-mail, ha az értesítések be vannak állítva?
Nincsenek olyan helyzetekben, ahol a rendszer nem küld figyelmeztetést, annak ellenére, hogy az értesítések megfelelően vannak konfigurálva. A következő helyzetekben e-mailben értesítést nem kap riasztási zaj elkerülése érdekében:

* Ha az értesítések óránkénti Digest, és a riasztás jelenik meg, és egy órán belül megoldott.
* A feladat megszakadt.
* A biztonsági mentési feladat elindul, és akkor sikertelen lesz, és folyamatban van egy másik biztonsági mentési feladat.
* A Resource Manager-kompatibilis virtuális gépek ütemezett biztonsági mentési feladat indítja el, de a virtuális gép már nem létezik.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>A sikeres biztonsági mentések értesítések tevékenységi naplóit segítségével

Ha szeretne értesítést kapni a biztonsági mentések sikeres követően, riasztások épülő használhatja a [tevékenységi naplóit](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) tároló.

### <a name="login-into-azure-portal"></a>Bejelentkezés az Azure-portálon
Bejelentkezés az Azure-portálon a, folytassa a megfelelő Azure Recovery Services-tároló, majd kattintson a "Tevékenységnapló" szakasz tulajdonságai.

### <a name="identify-appropriate-log"></a>Azonosítsa a megfelelő naplót

Az alábbi képen látható ellenőrzése, hogy tevékenységi naplóit sikeres biztonsági mentések fordulnak elő a szűrni. A timespan ennek megfelelően módosítsa rekordok megtekintéséhez.

![Tevékenységnaplók](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Kattintson a "JSON" szegmens további részletek és a másolás-beillesztés azt egy szövegszerkesztőbe, tekinti meg. A tároló adatait és a cikk a műveletnapló, azaz a biztonsági mentési elem kiváltó jeleníti.

Kattintson a "Napló figyelmeztetés hozzáadása" az összes ilyen naplók riasztásokat.

### <a name="add-activity-log-alert"></a>Tevékenység napló riasztások hozzáadása

"Add napló figyelmeztetés" gombra kattintva megjelenik egy olyan képernyőt alább látható módon

![Tevékenységnapló-riasztás](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
Az előfizetés és az erőforráscsoport a riasztás tárolására szolgálnak. A feltételek előre kitöltött lesz. Ellenőrizze, hogy az értékek kapcsolódik a követelmény a.

A sikeres biztonsági mentésekhez "Tájékoztató" és "Succeeded" állapotaként a "szint" van megjelölve.

Ha "erőforrás" fenti választja, a riasztás akkor jöjjön létre, amikor tevékenységi naplóit tárolja, az adott erőforrás vagy a tárolóban. Ha azt szeretné, hogy a szabály minden tárolók alkalmazandó, hagyja meg az "erőforrás" lehet üres.

### <a name="define-action-on-alert-firing"></a>Adja meg a riasztás indítási az művelet

A "művelet" csoport meghatározásához a művelet a riasztás generálása után. Rákattinthat a "Művelet típusa" tudni, hogy további információk az elérhető műveletek ilyen e-mailek/SMS/integráció a ITSM stb.

![Tevékenységcsoport napló művelet](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


Miután az OK gombra kattint, egy tevékenység napló riasztás akkor jöjjön létre, és a sikeres biztonsági mentések rögzítve későbbi tevékenységi naplóit fog érvényesítést a művelet, a művelet csoportban meghatározott.

### <a name="limitations-on-alerts"></a>Riasztások korlátozásai
Eseményalapú riasztások a következő korlátozások vonatkoznak:

1. A figyelmeztetéseket a Recovery Services-tároló összes virtuális gépeken. Nem szabhatja testre a riasztás a Recovery Services-tároló virtuális gépek egy részét.
2. Küldi a riasztásokat "alerts-noreply@mail.windowsazure.com". Az e-mailt olyasvalaki jelenleg nem módosíthatók.

## <a name="next-steps"></a>További lépések
Hozza létre újra a virtuális gép helyreállítási pontból információkért tekintse meg [visszaállítása az Azure virtuális gépek](backup-azure-arm-restore-vms.md).

Ha a virtuális gépek védelméről tájékoztatásra van szüksége, tekintse meg [először: készítsen biztonsági mentést a Recovery Services-tároló virtuális gépek](backup-azure-vms-first-look-arm.md). 

További tudnivalók a felügyeleti feladatokat a virtuális gép biztonsági mentésekhez, a cikkben [kezelése Azure virtuális gépek biztonsági mentéseinek](backup-azure-manage-vms.md).
