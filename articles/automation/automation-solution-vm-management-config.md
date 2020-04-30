---
title: Azure Automation a virtuális gépek indításának és leállításának beállítása a munkaidőn kívüli megoldásban
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Start/Stop VMs during off-hours megoldás a különböző használati esetek és forgatókönyvek támogatásához.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604764"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Virtuális gépek indításának és leállításának beállítása a munkaidőn kívüli megoldásban

A **virtuális gépek indítása és leállítása a munkaidőn kívüli** megoldásban a következőket teheti:

- [Virtuális gépek indítása és leállítása](#schedule).
- A virtuális gépeket úgy ütemezhet, hogy az Azure- [címkék használatával](#tags) növekvő sorrendben induljon el és álljon le (a klasszikus virtuális gépek nem támogatottak).
- Virtuális gépek automatikus leállítása [alacsony CPU-használat](#cpuutil)alapján.

Ez a cikk azt ismerteti, hogyan lehet sikeresen konfigurálni a megoldást ezen forgatókönyvek támogatásához. Azt is megtudhatja, hogyan hajthat végre más gyakori konfigurációs beállításokat a megoldáshoz, például:

* [E-mail-értesítések konfigurálása](#configure-email-notifications)

* [Virtuális gép hozzáadása](#add-a-vm)

* [Virtuális gép kizárása](#exclude-a-vm)

* [Az indítási és leállítási ütemtervek módosítása](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>1. forgatókönyv: virtuális gépek indítása/leállítása ütemterv szerint

Ez a forgatókönyv az alapértelmezett konfiguráció a megoldás első telepítésekor. Beállíthatja például, hogy leállítsa az összes virtuális gépet egy előfizetésen belül, amikor az esti munkát elhagyja, és reggel, amikor visszatért az irodába. Ha az üzembe helyezés során ütemezi a **ütemezett StartVM** és az **ütemezett StopVM** , a rendszer elindítja és leállítja a célként megadott virtuális gépeket. Ha ezt a megoldást csak a virtuális gépek leállítására szeretné konfigurálni, tekintse meg [az indítási és leállítási ütemtervek módosítása](#modify-the-startup-and-shutdown-schedules) témakört, amelyből megtudhatja, hogyan konfigurálhat egyéni

> [!NOTE]
> Az időzóna az aktuális időzóna, amikor konfigurálja az ütemterv időpontja paramétert. A Azure Automation azonban UTC formátumban van tárolva. Nem szükséges időzóna-átalakítást végeznie, mivel ezt a rendszer az üzembe helyezés során kezeli.

A következő változók konfigurálásával szabályozhatja, hogy mely virtuális gépek tartoznak a hatókörbe: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**és **External_ExcludeVMNames**.

Engedélyezheti a műveletet egy előfizetéshez és az erőforráscsoporthoz, vagy megcélozhatja a virtuális gépek egy adott listáját, de mindkettőt nem.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és a leállítási műveletek célzása egy előfizetéshez és az erőforráscsoporthoz

1. Konfigurálja a `External_Stop_ResourceGroupNames` és `External_ExcludeVMNames` a változókat a cél virtuális gépek megadásához.

2. Az **ütemezett StartVM** és az **ütemezett StopVM** ütemezések engedélyezése és frissítése.

3. Futtassa a **ScheduledStartStop_Parent** Runbook a **művelet** paraméterrel a **Start** értékre, és a **WHATIF** paraméter értéke TRUE (igaz), hogy megtekintse a módosításokat.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>A Start és a stop művelet megcélzása virtuálisgép-lista alapján

1. Futtassa a **ScheduledStartStop_Parent** runbook a következő **művelettel** : **Start**értékre, adja hozzá a virtuális gépek vesszővel tagolt listáját a **VMList** paraméter mezőben, majd állítsa a **WHATIF** paramétert igaz értékre. A módosítások előnézete.

2. Konfigurálja a `External_ExcludeVMNames` változót a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3).

3. Ez a forgatókönyv nem tartja `External_Start_ResourceGroupNames` tiszteletben `External_Stop_ResourceGroupnames` a és változókat. Ebben a forgatókönyvben létre kell hoznia a saját automatizálási ütemtervét. Részletekért lásd: [Runbook ütemezése Azure Automationban](../automation/automation-schedules.md).

    > [!NOTE]
    > A célként megadott **ResourceGroup** `External_Start_ResourceGroupNames` -nevek értékét a és `External_Stop_ResourceGroupNames`a értékének értékeként tárolja a rendszer. A további részletesség érdekében módosíthatja ezeket a változókat a különböző erőforráscsoportok megcélzásához. Az indítási művelethez használja `External_Start_ResourceGroupNames`a parancsot, `External_Stop_ResourceGroupNames` és használja a stop művelethez. A virtuális gépek automatikusan hozzáadódnak az indítási és leállítási ütemtervekhez.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>2. forgatókönyv: virtuális gépek indítása és leállítása címkék használatával

Egy olyan környezetben, amely több, elosztott munkaterhelést támogató virtuális gépen található kettő vagy több összetevőt tartalmaz, és amely támogatja az összetevők indításának és leállításának sorrendjét. Ezt a forgatókönyvet a következő lépések végrehajtásával hajthatja végre:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és a leállítási műveletek célzása egy előfizetéshez és az erőforráscsoporthoz

1. Adjon hozzá `sequencestart` egy pozitív `sequencestop` egész értékkel rendelkező és egy olyan címkét, amely a `External_Start_ResourceGroupNames` és `External_Stop_ResourceGroupNames` változókat célozza meg. A kezdési és a leállítási műveletek növekvő sorrendben lesznek végrehajtva. A virtuális gépek címkézésével kapcsolatos további információkért lásd: [Windows rendszerű virtuális gép címkézése az Azure-ban](../virtual-machines/windows/tag.md) , és Linux rendszerű [virtuális gép címkézése az Azure-ban](../virtual-machines/linux/tag.md).

2. Módosítsa az ütemezett **StartVM** és a **sorozatos StopVM** az igényeinek megfelelő dátumra és időpontra, és engedélyezze az ütemtervet.

3. A módosítások előnézetének megtekintéséhez futtassa az **SequencedStartStop_Parent** **runbook a** következővel: **Start** és **WHATIF** True értékre állítva.

4. Tekintse meg a műveletet, és végezze el a szükséges módosításokat az éles virtuális gépeken való megvalósítás előtt. Ha készen áll, manuálisan hajtsa végre a runbook a paraméterrel **hamis**értékre állítva, `Sequenced-StartVM` vagy `Sequenced-StopVM` hagyja meg az automatizálási ütemtervet, és futtassa automatikusan az előírt ütemtervet követve.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>A Start és a stop művelet megcélzása virtuálisgép-lista alapján

1. Adja hozzá `sequencestart` a és `sequencestop` a címkét pozitív egész értékkel a `VMList` paraméterhez hozzáadni kívánt virtuális gépekhez.

2. Futtassa a **SequencedStartStop_Parent** runbook a következő **művelettel** : **Start**értékre, adja hozzá a virtuális gépek vesszővel tagolt listáját a **VMList** paraméter mezőben, majd állítsa a **WHATIF** értéket True értékre. A módosítások előnézete.

3. Konfigurálja a `External_ExcludeVMNames` változót a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3).

4. Ez a forgatókönyv nem tartja `External_Start_ResourceGroupNames` tiszteletben `External_Stop_ResourceGroupnames` a és változókat. Ebben a forgatókönyvben létre kell hoznia a saját automatizálási ütemtervét. Részletekért lásd: [Runbook ütemezése Azure Automationban](../automation/automation-schedules.md).

5. Tekintse meg a műveletet, és végezze el a szükséges módosításokat az éles virtuális gépeken való megvalósítás előtt. Ha elkészült, manuálisan hajtsa végre a **figyelés és diagnosztika/figyelés – művelet – groupsrunbook** paramétert a **false**értékre. Azt is megteheti, `Sequenced-StartVM` hogy `Sequenced-StopVM` az automatizálási ütemtervet és az előírt ütemtervet követően automatikusan futtatja azt.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>3. forgatókönyv: automatikusan indítás/leállítás a CPU-kihasználtság alapján

Ez a megoldás segít kezelni a Azure Resource Manager és a klasszikus virtuális gépek előfizetésében való futtatásának költségeit, ha olyan virtuális gépeket értékel ki, amelyeket nem a nem csúcsidőben használ, például órák után, és automatikusan leáll, ha a processzor kihasználtsága kisebb, mint a megadott százalék.

Alapértelmezés szerint a megoldás előre konfigurálva van a százalékos CPU-metrika kiértékeléséhez, hogy az átlagos kihasználtság 5 százalék vagy kevesebb legyen. Ezt a forgatókönyvet a következő változók vezérlik, és módosíthatók, ha az alapértelmezett értékek nem felelnek meg a követelményeknek:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Engedélyezheti és megcélozhatja a műveletet egy előfizetéshez és az erőforráscsoporthoz, vagy megcélozhatja a virtuális gépek egy adott listáját.

A **AutoStop_CreateAlert_Parent** runbook futtatásakor ellenőrzi, hogy a célként megadott előfizetés, erőforráscsoport (ok) és virtuális gépek léteznek-e. Ha a virtuális gépek léteznek, a runbook ezután meghívja az összes ellenőrzött virtuális gép **AutoStop_CreateAlert_Child** runbook a szülő runbook. A gyermek runbook a következőket hajtja végre:

* Metrikai riasztási szabályt hoz létre minden ellenőrzött virtuális géphez.

* Egy adott virtuális gép **AutoStop_VM_Child** runbook indítja el, ha a processzor a megadott időtartam alatt csökken a beállított küszöbérték alá. Ez a runbook ezután megkísérli leállítani a virtuális gépet.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Az automatikus leállítás művelet megcélzása az előfizetésben lévő összes virtuális gépen

1. Győződjön meg arról `External_Stop_ResourceGroupNames` , hogy a változó üres, vagy a * (helyettesítő karakter) értékre van állítva.

2. [Nem kötelező lépés] Ha ki szeretne zárni néhány virtuális GÉPET az automatikus leállítás alól, hozzáadhat egy vesszővel tagolt listát a `External_ExcludeVMNames` változóhoz.

3. Engedélyezze az `Schedule_AutoStop_CreateAlert_Parent` ütemezett futtatást, hogy létrehozza a szükséges leállítás virtuálisgép-metrika riasztási szabályait az előfizetésben található összes virtuális géphez. Az ilyen típusú ütemezés futtatásával új metrikai riasztási szabályokat hozhat létre, mivel új virtuális gépeket adnak hozzá az előfizetéshez.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Az automatikus leállítás művelet megcélzása egy erőforráscsoport vagy több erőforráscsoport összes virtuális gépe esetében

1. Adja hozzá az erőforráscsoportok neveinek vesszővel tagolt listáját a `External_Stop_ResourceGroupNames` változóhoz.

2. Ha a virtuális gépek némelyikét ki szeretné zárni az automatikus leállításból, a virtuálisgép-nevek vesszővel tagolt listáját felveheti a `External_ExcludeVMNames` változóba.

3. Engedélyezze a **Schedule_AutoStop_CreateAlert_Parent** ütemezett futtatását, hogy létrehozza a szükséges leállítás virtuálisgép-metrika riasztási szabályait az erőforráscsoportok összes virtuális gépén. A művelet egy adott időpontban történő futtatása lehetővé teszi új metrikai riasztási szabályok létrehozását, mivel új virtuális gépek kerülnek be az erőforráscsoport (ok) ba.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Az autostop művelet megcélzása a virtuális gépek listájára

1. Hozzon létre egy új [ütemtervet](shared-resources/schedules.md#creating-a-schedule) , és kapcsolja össze a **AutoStop_CreateAlert_Parent** runbook, és adja hozzá a virtuális gépek neveinek `VMList` vesszővel tagolt listáját a paraméterhez.

2. Ha bizonyos virtuális gépeket ki szeretne zárni az automatikus leállításból, hozzáadhat egy vesszővel elválasztott virtuális gép nevét a `External_ExcludeVMNames` változóhoz.

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

Ha módosítani szeretné az e-mail-értesítéseket a megoldás üzembe helyezése után, módosítsa az üzembe helyezés során létrehozott műveleti csoportot.  

> [!NOTE]
> A Azure Government-felhőben lévő előfizetések nem támogatják a megoldás e-mail-funkcióit.

1. A Azure Portal navigáljon a **figyelés**, majd a **műveleti csoportok**elemre. Válassza ki a **StartStop_VM_Notication**nevű műveleti csoportot.

    ![Automation Update Management megoldás lapja](media/automation-solution-vm-management/azure-monitor.png)

2. A **StartStop_VM_Notification** **lapon kattintson a részletek** elemre. **Details** Ekkor megnyílik az **e-mail/SMS/leküldés/hang** lap. Frissítse az e-mail-címet, és kattintson **az OK** gombra a módosítások mentéséhez.

    ![Automation Update Management megoldás lapja](media/automation-solution-vm-management/change-email.png)

    Másik lehetőségként további műveleteket is hozzáadhat a műveleti csoporthoz, hogy többet tudjon meg a műveleti csoportokról, lásd: [műveleti csoportok](../azure-monitor/platform/action-groups.md)

A következő példa egy olyan e-mailt küld, amelyet a megoldás a virtuális gépek leállításakor küld.

![Automation Update Management megoldás lapja](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Virtuális gépek hozzáadása/kizárása

A megoldás lehetővé teszi, hogy a megoldás által célzott virtuális gépeket vagy kifejezetten a megoldásból kizárja a gépeket.

### <a name="add-a-vm"></a>Virtuális gép hozzáadása

Két lehetőség közül választhat, amelyekkel gondoskodhat arról, hogy a futtatásakor a rendszer a virtuális gépet tartalmazza a Start/Stop megoldásban.

* A megoldás minden szülő [runbookok](automation-solution-vm-management.md#runbooks) rendelkezik egy `VMList` paraméterrel. A virtuális gépek neveinek vesszővel tagolt listáját átadhatja erre a paraméterre, ha ütemezi a megfelelő fölérendelt runbook az adott helyzetben, és ezek a virtuális gépek a megoldás futtatásakor szerepelni fognak.

* Több virtuális gép kiválasztásához `External_Start_ResourceGroupNames` állítson be és azokat az erőforráscsoport- `External_Stop_ResourceGroupNames` nevekkel, amelyek tartalmazzák az elindítani vagy leállítani kívánt virtuális gépeket. A változókat `*` beállíthatja úgy is, hogy a megoldás az előfizetésben lévő összes erőforráscsoport esetében fusson.

### <a name="exclude-a-vm"></a>Virtuális gép kizárása

Ha ki szeretne zárni egy virtuális gépet a megoldásból, adja hozzá a `External_ExcludeVMNames` változóhoz. Ez a változó a Start/Stop megoldásból kizárandó adott virtuális gépek vesszővel tagolt listája. Ez a lista 140 virtuális gépre korlátozódik. Ha több mint 140 virtuális gépet ad hozzá ehhez a vesszővel tagolt listához, a kizárni kívánt virtuális gépeket a rendszer véletlenül elindíthatja vagy leállíthatja.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Az indítási és leállítási ütemtervek módosítása

Az indítási és leállítási ütemezések kezelése ebben a megoldásban ugyanazokat a lépéseket követi, mint az [Runbook ütemezése Azure Automationban](automation-schedules.md). A virtuális gépek elindításához és leállításához külön ütemtervre van szükség.

Ha a megoldást úgy konfigurálja, hogy a virtuális gépeket csak bizonyos időben állítsa le, a rendszer támogatja. Ebben a forgatókönyvben csak egy **leállítási** ütemtervet hoz létre, és nem megfelelő **kezdési** ütemtervet. Ehhez a következőket kell tennie:

1. Győződjön meg arról, hogy hozzáadta a virtuális gépekhez a `External_Stop_ResourceGroupNames` változóban leállítani kívánt erőforráscsoportot.

2. Hozzon létre saját ütemtervet a virtuális gépek leállításához szükséges időpontra.

3. Navigáljon a **ScheduledStartStop_Parent** runbook, és kattintson az **ütemterv**gombra. Ez lehetővé teszi az előző lépésben létrehozott ütemterv kiválasztását.

4. Válassza a paraméterek lehetőséget, **és futtassa a beállításokat** , és állítsa a **művelet** mezőt **Leállítás**értékre.

5. A módosítások mentéséhez kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

* A Start/Stop VMs during off-hours hibaelhárításával kapcsolatos további információkért lásd: a [virtuális gépek indításának és leállításának hibaelhárítása](troubleshoot/start-stop-vm.md).

* [Tekintse át](automation-solution-vm-management-logs.md) a Azure monitor naplókba írt Automation-rekordokat, valamint a naplóbeli keresési lekérdezéseket, hogy elemezze az Automation runbook-feladatok állapotát a Start/Stop virtuális gépekről.
