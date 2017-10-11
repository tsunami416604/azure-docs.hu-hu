---
title: "Erőforrás-kezelő figyelő telepített virtuális gépek biztonsági mentéseinek |} Microsoft Docs"
description: "Események és erőforrás-kezelő telepített virtuális gépek biztonsági mentéseinek riasztások figyelése. A riasztások alapján e-mail küldése."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: b9dc3f52e5fc275bc56b9964f2115833f2dde42e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Azure-beli virtuális gépek biztonsági mentésével kapcsolatos riasztások figyelése
A riasztások jelezhetik a szolgáltatásból, hogy az esemény küszöbét teljesül vagy túllépése válaszokat. Annak ismerete, ha problémák start alacsonyan tartják a költségeket üzleti kritikus fontosságú lehet. Riasztást általában nem történik meg, ütemezés szerint, és ezért fontos tudni, hogy minél hamarabb után a riasztás akkor jön létre. Például ha egy biztonsági mentési vagy helyreállítási feladat sikertelen lesz, egy riasztás a hiba öt percen belül. A tároló irányítópultjának a biztonsági riasztások csempe kritikus és a figyelmeztetési szintű eseményeket jeleníti meg. A biztonsági riasztások beállításainak megtekintheti az összes esemény. De mi a teendő, ha egy riasztás, ha egy külön probléma dolgozik? Ha nem tudja, ha a riasztás történik, akkor lehet, hogy egy kisebb kellemetlenségért, vagy az adatok biztonságát veszélyeztető. Győződjön meg arról, hogy a megfelelő személyek ismerjék riasztás - következik be, amikor konfigurálja a szolgáltatást riasztási értesítéseket e-mailben küldendő. E-mail értesítések beállításával kapcsolatos részletekért lásd: [értesítések konfigurálása](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Hogyan találhatom meg a riasztások adatai?
Tekintse meg az eseményt, amely kivételt váltott ki riasztást, meg kell nyitnia a biztonsági riasztások panel. Nyissa meg a biztonsági riasztások panel két módja van: vagy a biztonsági mentési riasztások a csempén, a tároló irányítópultjának, vagy a riasztások és események paneljéről.

A biztonsági riasztások panel megnyitása biztonsági riasztások csempén:

* A a **biztonsági mentési riasztások** a tároló irányítópult csempét, kattintson a **kritikus** vagy **figyelmeztetés** az adott súlyossági szint esetében a működési események megtekintéséhez.

    ![Biztonsági riasztások csempe](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

A biztonsági riasztások panel megnyitása a riasztások és események panel:

1. A tároló irányítópulton kattintson **összes beállítás**. ![Minden beállítások gomb](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Az a **beállítások** panelen kattintson a **riasztások és események**. ![Riasztások és események gomb](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Az a **riasztások és események** panelen kattintson a **biztonsági mentési riasztások**. ![Biztonsági riasztások gomb](./media/backup-azure-monitor-vms/backup-alerts.png)

    A **biztonsági mentési riasztások** panel nyílik meg, és a szűrt riasztásokat jeleníti meg.

    ![Biztonsági riasztások csempe](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Egy adott riasztás események, a részletes adatainak megtekintéséhez kattintson a megnyitni a **részletek** panelen.

    ![Esemény részletei](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Állítsa be az attribútumokat, megjelennek a listában, lásd: [további esemény attribútumok megtekintése](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Értesítések konfigurálása
 A riasztások az elmúlt egy órában keresztül, vagy ha az adott esemény történik előfordult e-mail értesítést a szolgáltatás konfigurálható.

A riasztások értesítő e-mailek beállítása

1. Kattintson a biztonsági mentési riasztások menü **értesítések konfigurálása**

    ![Biztonsági riasztások menü](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Ekkor megnyílik a konfigurálása értesítések panel.

    ![Értesítések panel konfigurálása](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Az értesítő e-mailek, a konfigurálás értesítések paneljén kattintson **a**.

    A címzettek és súlyosság párbeszédpanelek mellett csillag van, mert az adatszolgáltatás. Adjon meg legalább egy e-mail címet, és legalább egy súlyosságának kiválasztása.
3. Az a **címzettek (E-mail)** párbeszédpanelen írja be az e-mail címet az értesítéseket kapnak. A formátumot használja: username@domainname.com. Az e-mail címeket pontosvesszővel (;) kell elválasztani.
4. A a **értesítendő** területen válasszon **/ riasztás** értesítést küldeni, ha a megadott riasztás, vagy **óránkénti kivonatoló** összegzés küldése az elmúlt egy óra.
5. Az a **súlyossági** párbeszédpanelen válasszon egy vagy több szint, amely e-mailben értesítést aktiválják.
6. Kattintson a **Save** (Mentés) gombra.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Milyen típusú érhetők el az Azure infrastruktúra-szolgáltatási virtuális gép biztonsági mentése?
   | Riasztási szint | Értesítések küldése |
   | --- | --- |
   | Kritikus |Sikertelen biztonsági mentéshez, helyreállítási hiba |
   | Figyelmeztetés |None |
   | Tájékoztató |None |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Olyankor is előfordul, hogy nem érkezik meg az e-mail, ha az értesítések be vannak állítva?
Nincsenek olyan helyzetekben, ahol a rendszer nem küld figyelmeztetést, annak ellenére, hogy az értesítések megfelelően vannak konfigurálva. A következő helyzetekben e-mailben értesítést nem kap riasztási zaj elkerülése érdekében:

* Ha az értesítések óránkénti Digest, és a riasztás jelenik meg, és egy órán belül megoldott.
* A feladat megszakadt.
* A biztonsági mentési feladat elindul, és akkor sikertelen lesz, és folyamatban van egy másik biztonsági mentési feladat.
* A Resource Manager-kompatibilis virtuális gépek ütemezett biztonsági mentési feladat indítja el, de a virtuális gép már nem létezik.

## <a name="customize-your-view-of-events"></a>A nézet események testreszabása
A **naplók** beállítást tartalmaz egy előre definiált szűrők és oszlopok működési események adatait. Testre szabható a nézetet, hogy ha a **események** panel nyílik meg, ez azt mutatja, meg a kívánt adatokat.

1. Az a [tároló irányítópult](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), keresse meg, és kattintson a **naplók** megnyitásához a **események** panelen.

    ![Naplók](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    A **események** panelen nyitja meg a működési események csak az aktuális tárolóban vannak leszűrve.

    ![Naplózási szűrő naplók](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    A panel kritikus, hiba, figyelmeztetés és információs események a múlt hét, listáját jeleníti meg. Az időtartam az állítsa be alapértelmezett érték a **szűrő**. A **események** panel is bemutatja a dokumentumkövetést, miközben az események sávdiagram. Ha nem szeretné látni a sávdiagram az a **események** menüben kattintson a **elrejtése diagram** ki a diagram váltáshoz. Az alapértelmezett nézet események művelet, állapotáról, erőforrás, és időt információit tartalmazza. Információ az ilyen esemény további attribútumok című [eseményadatok bővülő](backup-azure-monitor-vms.md#view-additional-event-attributes).
2. Található további információ a működési események a a **művelet** oszlop, kattintson az operatív esemény a panel megnyitásához. A panel az eseményeket részletes információkat tartalmaz. Események a korrelációs azonosító és az események időtartamának történt szerint vannak csoportosítva.

    ![Művelet részletei](./media/backup-azure-monitor-vms/audit-logs-details-window.png)
3. Egy adott esemény, események, részletes adatainak megtekintéséhez kattintson a megnyitni az esemény a **részletek** panelen.

    ![Esemény részletei](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    Az esemény-szintre vonatkozó részletes információk beolvasása. Ha inkább a jelent meg minden esemény ennyi adatait, és szeretne adni ennyi adat a **események** panelen című [eseményadatok bővülő](backup-azure-monitor-vms.md#view-additional-event-attributes).

## <a name="customize-the-event-filter"></a>A szűrő testreszabása
Használja a **szűrő** módosítható, és válassza a megjelenő információk egy adott panelen. Az eseményadatok szűrése:

1. Az a [tároló irányítópult](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), keresse meg, és kattintson a **naplók** megnyitásához a **események** panelen.

    ![Naplók](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    A **események** panelen nyitja meg a működési események csak az aktuális tárolóban vannak leszűrve.

    ![Naplózási szűrő naplók](./media/backup-azure-monitor-vms/audit-logs-filter.png)
2. Az a **események** menüben kattintson a **szűrő** , hogy a panel megnyitásához.

    ![Nyissa meg a szűrő panelre](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)
3. Az a **szűrő** panelen állítsa be a **szint**, **időtartamának**, és **hívó** szűrők. A többi szűrőt óta voltak beállítva a Recovery Services-tároló az aktuális információk nem érhetők el.

    ![Naplózási naplók-lekérdezés részletei](./media/backup-azure-monitor-vms/filter-blade.png)

    Megadhatja a **szint** esemény: kritikus, hiba, figyelmeztető vagy tájékoztató. Eseményszinttel bármilyen kombinációját kiválaszthatja, de rendelkeznie kell legalább egy kijelölt szint. Váltás a szint a be- és kikapcsolása. A **időtartamának** szűrő segítségével adja meg, mennyi ideig az események rögzítésére. Ha az egyéni időtartama használja, beállíthatja a kezdő és befejező időpontja.
4. Ha készen áll a műveleti naplókat, a szűrő használatával lekérdezéséhez kattintson **frissítés**. Az eredmények megjelenítése a **események** panelen.

    ![Művelet részletei](./media/backup-azure-monitor-vms/edited-list-of-events.png)

### <a name="view-additional-event-attributes"></a>Az esemény attribútumok megtekintése
Használatával a **oszlopok** gombra, a listában megjelenő további esemény attribútumok engedélyezheti a **események** panelen. Az események alapértelmezett listáját művelet, állapotáról, erőforrás, és időt információit jeleníti meg. Ahhoz, hogy a további attribútumok:

1. Az a **események** panelen kattintson a **oszlopok**.

    ![Nyissa meg oszlopok](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    A **oszlopok kiválasztása** panel nyílik meg.

    ![Oszlopok panel](./media/backup-azure-monitor-vms/columns-blade.png)
2. Az attribútum kiválasztásához kattintson a jelölőnégyzet jelölését. Az attribútum jelölőnégyzet váltja be- és kikapcsolható.
3. Kattintson a **alaphelyzetbe** alaphelyzetbe állítja az attribútumok listáját a **események** panelen. Hozzáadása, vagy az attribútumok eltávolítása a listából, után használni **alaphelyzetbe** esemény attribútumok új listájának megtekintéséhez.
4. Kattintson a **frissítése** az adatokon az attribútumok. A következő táblázat az egyes attribútum kapcsolatos információkat.

| Oszlop neve | Leírás |
| --- | --- |
| Művelet |A művelet neve |
| Szint |A művelet szintjét, az értékek lehetnek: információs, figyelmeztetési, hiba vagy kritikus |
| status |A művelet állapotának leíró |
| Erőforrás |URL-címet, amely azonosítja az erőforrás; más néven az erőforrás-azonosítója |
| Time |Az az aktuális időponthoz képest, ha az esemény történt mért idő |
| Hívó |Ki vagy mi nevezik, és az esemény; aktivált a rendszer, vagy a felhasználó |
| időbélyeg |Az idő, amikor az esemény lett elindítva |
| Erőforráscsoport |A kapcsolódó erőforráscsoport |
| Erőforrás típusa |Az erőforrás-kezelő által használt belső erőforrástípus |
| Előfizetés azonosítója |A társított előfizetés-azonosító |
| Kategória |Az esemény kategória |
| Korrelációs azonosító |Az ezzel kapcsolatos eseményeket közös azonosítója |

## <a name="use-powershell-to-customize-alerts"></a>PowerShell segítségével testre szabhatja a riasztások
A feladatok egyéni riasztási értesítéseket kaphat a portálon. Ahhoz, hogy ezeket a feladatokat, határozza meg a műveleti naplókat események riasztási szabályok PowerShell-alapú. Használjon *PowerShell 1.3.0 verzió vagy újabb*.

Egyéni biztonsági mentési hibák riasztási értesítést megadásához használja a következő parancsfájl hasonló parancsot:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.RecoveryServices/recoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/Microsoft.RecoveryServices/vaults/trinadhVault -Actions $actionEmail
```

**ResourceId** : ResourceId lekérheti a naplókat. Az erőforrás-azonosítója a műveletnaplók erőforrás oszlopában megadott URL-cím.

**OperationName** : OperationName nem formátumú "Microsoft.RecoveryServices/recoveryServicesVault/*EventName*" ahol *EventName* lehet:<br/>

* Regisztráljon <br/>
* Regisztrálás törlése <br/>
* ConfigureProtection <br/>
* Biztonsági mentés <br/>
* Visszaállítás <br/>
* StopProtection <br/>
* DeleteBackupData <br/>
* CreateProtectionPolicy <br/>
* DeleteProtectionPolicy <br/>
* UpdateProtectionPolicy <br/>

**Állapot** : támogatott értékek a következők elindítva, sikeres vagy sikertelen.

**Erőforráscsoport** : Ez az az erőforráscsoporthoz, amelyhez az erőforrás tartozik. Az erőforráscsoport oszlop adhat hozzá a létrehozott naplók. Erőforráscsoport egyike az elérhető naplófájltípusokat események adatait.

**Név** : a riasztási szabály nevét.

**CustomEmail** : Adja meg a kívánt riasztási szóló értesítés elküldése egyéni e-mail cím

**SendToServiceOwners** : Ez a beállítás a rendszergazdák és társrendszergazdák az előfizetés riasztási értesítéseket küldő. A használat **New-AzureRmAlertRuleEmail** parancsmag

### <a name="limitations-on-alerts"></a>Riasztások korlátozásai
Eseményalapú riasztások a következő korlátozások vonatkoznak:

1. A figyelmeztetéseket a Recovery Services-tároló összes virtuális gépeken. Nem szabhatja testre a riasztás a Recovery Services-tároló virtuális gépek egy részét.
2. A funkció jelenleg előzetes verzió. [További információ](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Küldi a riasztásokat "alerts-noreply@mail.windowsazure.com". Az e-mailt olyasvalaki jelenleg nem módosíthatók.

## <a name="next-steps"></a>Következő lépések
Eseménynaplók kiváló levágást engedélyezése, és a biztonsági mentési műveletek támogatása naplózására. A rendszer naplózza a következő műveletet:

* Regisztráljon
* Regisztrálás törlése
* A védelem konfigurálása
* Biztonsági mentés (mindkettő ütemezett és igény szerinti biztonsági mentést)
* Visszaállítás
* Állítsa le a védelmet
* biztonsági mentési adatok törlése
* Házirend hozzáadása
* Szabályzat törlése
* Házirend frissítése
* Megszakítása

Események széles körű leírását, műveletek és a vizsgálati naplók az Azure szolgáltatásban, lásd: a cikk [események megtekintése és a naplók](../monitoring-and-diagnostics/insights-debugging-with-events.md).

Hozza létre újra a virtuális gép helyreállítási pontból információkért tekintse meg [visszaállítása az Azure virtuális gépek](backup-azure-restore-vms.md). Ha a virtuális gépek védelméről tájékoztatásra van szüksége, tekintse meg [először: készítsen biztonsági mentést a Recovery Services-tároló virtuális gépek](backup-azure-vms-first-look-arm.md). További tudnivalók a felügyeleti feladatokat a virtuális gép biztonsági mentésekhez, a cikkben [kezelése Azure virtuális gépek biztonsági mentéseinek](backup-azure-manage-vms.md).
