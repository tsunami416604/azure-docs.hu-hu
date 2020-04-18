---
title: Az Azure Automation indítási/leállítási virtuális gépeket konfigurálja munkaidőn kívüli megoldás közben
description: Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gépek kezdési/leállítási munkaidőn kívüli megoldás különböző használati esetek vagy forgatókönyvek.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604764"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>A virtuális gépek indítási/leállítási beállítása munkaidőn kívüli megoldás során

A **virtuális gépek indítása/leállítása munkaidőn kívüli megoldással** a következőket teheti:

- [A virtuális gépek indításának és leállításának ütemezése](#schedule).
- Ütemezze a virtuális gépeket, hogy növekvő sorrendben induljanak el és állítsanak le [az Azure-címkék használatával](#tags) (nem támogatott a klasszikus virtuális gépek).
- Automatikus leállítási virtuális gépek alacsony [CPU-használat](#cpuutil)alapján.

Ez a cikk azt ismerteti, hogyan lehet sikeresen konfigurálni a megoldást, hogy támogassa ezeket a forgatókönyveket. Megtudhatja, hogyan hajthat végre más gyakori konfigurációs beállításokat a megoldáshoz, például:

* [E-mail-értesítések konfigurálása](#configure-email-notifications)

* [Virtuális gép hozzáadása](#add-a-vm)

* [Virtuális gép kizárása](#exclude-a-vm)

* [Az indítási és leállítási ütemezés módosítása](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>1. forgatókönyv: Virtuális gépek indítása/leállítása ütemezés szerint

Ez a forgatókönyv az alapértelmezett konfiguráció a megoldás első üzembe helyezésekor. Beállíthatja például, hogy állítsa le az összes virtuális gépet az előfizetésben, amikor este elhagyja a munkát, és reggel kezdi el őket, amikor újra az irodában van. Az **ütemezett-startvm** és az **ütemezett-stopvm** ütemezések konfigurálásakor a központi telepítés során elindulnak és leállítják a célzott virtuális gépeket. A megoldás konfigurálása a virtuális gépek leállításához támogatott, olvassa [el az indítási és leállítási ütemezések módosítása című témakört,](#modify-the-startup-and-shutdown-schedules) amelyből megtudhatja, hogyan konfigurálhat egyéni ütemezést.

> [!NOTE]
> Az időzóna az aktuális időzóna az ütemezési idő paraméter konfigurálásakor. Azonban az Azure Automation-ben UTC formátumban tárolja. Nem kell semmilyen időzóna-átalakítást végeznie, mivel ez a központi telepítés során történik.

A következő változók konfigurálásával szabályozhatja, hogy mely virtuális gépek vannak hatókörben: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**és **External_ExcludeVMNames.**

Engedélyezheti a művelet célzását egy előfizetés és egy erőforráscsoport ellen, vagy a virtuális gépek egy adott listáját, de mindkettőt nem.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és leállítási műveletek célzása előfizetési és erőforráscsoporthoz

1. Konfigurálja `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` a és a változókat a cél virtuális gépek megadásához.

2. Engedélyezze és frissítse az **ütemezett StartVM** és **az Ütemezett stopvm** ütemezéseket.

3. Futtassa a **ScheduledStartStop_Parent** runbookot **úgy,** hogy az ACTION paramétermező **elindul,** és a **WHATIF** paramétermező igaz értékre van állítva a módosítások előnézetének megtekintéséhez.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>A kezdési és leállítási művelet célzása a virtuális gépek listájával

1. Futtassa a **ScheduledStartStop_Parent** runbookot **az ACTION** **beállítással,** adja hozzá a virtuális gépek vesszővel tagolt listáját a **VMList** paramétermezőben, majd állítsa a **WHATIF** paramétermezőt True értékre. Tekintse meg a módosítások előnézetét.

2. Konfigurálja `External_ExcludeVMNames` a változót a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3).

3. Ez a forgatókönyv `External_Start_ResourceGroupNames` nem `External_Stop_ResourceGroupnames` felel meg a változóknak. Ebben a forgatókönyvben létre kell hoznia a saját Automation-ütemezés. További információt a [Runbook ütemezése az Azure Automationben című témakörben](../automation/automation-schedules.md)talál.

    > [!NOTE]
    > A **célerőforrás-csoportnevek** értéke a és `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`a értéke ként is tárolódik. További részletesség érdekében módosíthatja ezeket a változókat a különböző erőforráscsoportok megcélzásához. A művelet elindításához `External_Start_ResourceGroupNames`használja `External_Stop_ResourceGroupNames` a és a stop műveletet. A virtuális gépek automatikusan hozzáadódnak a kezdési és leállítási ütemezésekhez.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>2. forgatókönyv: VMS indítása/leállítása egymás után címkék használatával

Olyan környezetben, amely két vagy több összetevőt tartalmaz több virtuális gépen, amelyek támogatják az elosztott számítási feladatokat, fontos az összetevők indítási és leállításás sorrendjének támogatása. Ezt a forgatókönyvet a következő lépések végrehajtásával valósíthatja meg:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és leállítási műveletek célzása előfizetési és erőforráscsoporthoz

1. Adjon `sequencestart` hozzá `sequencestop` egy és egy pozitív egész értéket tartalmazó címkét a `External_Start_ResourceGroupNames` megcélzott virtuális gépekhez és `External_Stop_ResourceGroupNames` változókhoz. A kezdési és leállítási műveletek növekvő sorrendben történnek. A virtuális gépek címkézésének módjáról az [Azure-beli Windows-virtuális gép címkézése](../virtual-machines/windows/tag.md) és [az Azure-beli Linux-virtuális gép címkézése.](../virtual-machines/linux/tag.md)

2. Módosítsa az **ütemezések Szveszvenált-StartVM** és **szekvenált-StopVM** a dátum és az idő, amely megfelel a követelményeknek, és lehetővé teszi az ütemezés.

3. Futtassa a **SequencedStartStop_Parent** runbookot **úgy,** hogy az ACTION készen áll az **indításra,** és a **WhatIF** értéke True a módosítások előnézetének megtekintéséhez.

4. Tekintse meg a műveletet, és a szükséges módosításokat, mielőtt az éles virtuális gépek en implementálása. Ha készen áll, manuálisan hajtsa végre a runbookot `Sequenced-StopVM` a **Hamis**paraméter beállítással, vagy hagyja, hogy az Automatizálás ütemezése és `Sequenced-StartVM` automatikusan fusson az előírt ütemezés szerint.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>A kezdési és leállítási művelet célzása a virtuális gépek listájával

1. Adjon `sequencestart` hozzá `sequencestop` egy és egy pozitív egész értéket tartalmazó címkét `VMList` a paraméterhez hozzáadni kívánt virtuális gépekhez.

2. Futtassa a **SequencedStartStop_Parent** runbook **ot az ACTION** beállítással, adja hozzá a virtuális gépek vesszővel tagolt listáját a **VMList** paramétermezőben, majd állítsa a **start** **WHATIF értéket** True értékre. Tekintse meg a módosítások előnézetét.

3. Konfigurálja `External_ExcludeVMNames` a változót a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3).

4. Ez a forgatókönyv `External_Start_ResourceGroupNames` nem `External_Stop_ResourceGroupnames` felel meg a változóknak. Ebben a forgatókönyvben létre kell hoznia a saját Automation-ütemezés. További információt a [Runbook ütemezése az Azure Automationben című témakörben](../automation/automation-schedules.md)talál.

5. Tekintse meg a műveletet, és a szükséges módosításokat, mielőtt az éles virtuális gépek en implementálása. Ha készen áll, manuálisan hajtsa végre a **figyelés-diagnosztika/monitoring-action-groupsrunbook** paraméter értéke **Hamis**. Másik lehetőségként hagyja, `Sequenced-StartVM` `Sequenced-StopVM` hogy az Automatizálás ütemezze és automatikusan fusson az előírt ütemezés szerint.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>3. forgatókönyv: Indítás/leállítás automatikusan a PROCESSZOR kihasználtsága alapján

Ez a megoldás segíthet az Azure Resource Manager és a klasszikus virtuális gépek futtatásának költségeinek kezelésében az előfizetésben, kiértékelve a nem csúcsidőszakokban, például munkaidő utáni használatban használt virtuális gépeket, és automatikusan leállíthatja őket, ha a processzorkihasználtság kisebb, mint egy megadott százalék.

Alapértelmezés szerint a megoldás előre konfigurálva van, hogy kiértékelje a százalékos CPU-metrikát, hogy lássa, az átlagos kihasználtság 5 százalék vagy annál kevesebb.Default the solution is pre-configured to evaluate the percentage CPU metric to see if average kihasználtság 5 százalék vagy kevesebb. Ezt a forgatókönyvet a következő változók szabályozzák, és módosítható, ha az alapértelmezett értékek nem felelnek meg a követelményeknek:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Engedélyezheti és célozhatja meg a műveletet egy előfizetési és erőforráscsoport, vagy a virtuális gépek egy adott listáját célozza meg.

A **AutoStop_CreateAlert_Parent** runbook futtatásakor ellenőrzi, hogy a célzott előfizetés, erőforráscsoport(ok) és virtuális gépek léteznek-e. Ha a virtuális gépek léteznek, a runbook majd meghívja a **AutoStop_CreateAlert_Child** runbook minden ellenőrzött virtuális gép a szülő runbook. Ez a gyermek runbook a következőműveleteket hajtja végre:

* Metrika riasztási szabályt hoz létre minden ellenőrzött virtuális géphez.

* Elindítja a **AutoStop_VM_Child** runbook egy adott virtuális gép, ha a processzor alá csökken a megadott időintervallumban a beállított küszöbérték alá. Ez a runbook ezután megpróbálja leállítani a virtuális gép.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Az automatikus leállítási művelet célzása az előfizetésben lévő összes virtuális gépre

1. Győződjön `External_Stop_ResourceGroupNames` meg arról, hogy a változó üres vagy * (helyettesítő karakter) értékre van állítva.

2. [Nem kötelező lépés] Ha ki szeretne zárni néhány virtuális gépet az automatikus leállításból, hozzáadhat egy vesszővel tagolt virtuálisgép-névlistát a `External_ExcludeVMNames` változóhoz.

3. Engedélyezze `Schedule_AutoStop_CreateAlert_Parent` az ütemezés futtatásához a szükséges virtuális gép-metrika-riasztási szabályok létrehozásához az előfizetésben lévő összes virtuális géphez. Az ilyen típusú ütemezés futtatásával új metrikariasztási szabályokat hozhat létre, ahogy új virtuális gépek kerülnek az előfizetésbe.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Az automatikus leállítási művelet célzása egy erőforráscsoport ban vagy több erőforráscsoportban lévő összes virtuális gépre

1. Adja hozzá az erőforráscsoportnevek vesszővel tagolt `External_Stop_ResourceGroupNames` listáját a változóhoz.

2. Ha ki szeretné zárni a virtuális gépek egy részét az automatikus leállításból, hozzáadhat egy virtuális `External_ExcludeVMNames` gépnevek et vesszővel tagolt listát a változóhoz.

3. Engedélyezze a **Schedule_AutoStop_CreateAlert_Parent** ütemezés futtatásához a szükséges virtuálisgép-metrika-riasztási szabályok létrehozásához az erőforráscsoportok ban lévő összes virtuális géphez. A művelet ütemezés sel való futtatása lehetővé teszi új metrikariasztási szabályok létrehozását, mivel új virtuális gépek kerülnek az erőforráscsoport(ok)hoz.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Az automatikus leállítási művelet célzása a virtuális gépek listájára

1. Hozzon létre egy új [ütemezést,](shared-resources/schedules.md#creating-a-schedule) és csatolja a **AutoStop_CreateAlert_Parent** runbook, a `VMList` virtuális gépnevek vesszővel tagolt listájának hozzáadásával a paraméterhez.

2. Ha ki szeretne zárni néhány virtuális gépet az automatikus leállításból, hozzáadhat egy virtuálisgép-nevek et `External_ExcludeVMNames` vesszővel tagolt listát a változóhoz.

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

Ha módosítani szeretné az e-mail értesítéseket a megoldás telepítése után, módosítsa a telepítés során létrehozott műveletcsoportot.  

> [!NOTE]
> Az Azure Government Cloud előfizetései nem támogatják a megoldás e-mail funkcióit.

1. Az Azure Portalon keresse meg a **Figyelés**, majd **a műveletcsoportok**. Jelölje ki a **StartStop_VM_Notication**nevű műveletcsoportot.

    ![Automation Update Management megoldás lap](media/automation-solution-vm-management/azure-monitor.png)

2. A **StartStop_VM_Notification** lapon kattintson a **Részletek szerkesztése** gombra a **Részletek csoportban.** Ezzel megnyitja az **E-mail/SMS/Push/Voice** oldalt. Frissítse az e-mail címet, és kattintson az **OK** gombra a módosítások mentéséhez.

    ![Automation Update Management megoldás lap](media/automation-solution-vm-management/change-email.png)

    Másik lehetőségként további műveleteket is hozzáadhat a műveletcsoporthoz, ha többet szeretne megtudni a műveletcsoportokról, olvassa el a [műveletcsoportokat.](../azure-monitor/platform/action-groups.md)

Az alábbiakban egy példa e-mailt, amely akkor küldi el, amikor a megoldás leállítja a virtuális gépek.

![Automation Update Management megoldás lap](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Virtuális gépek hozzáadása/kizárása

A megoldás lehetővé teszi, hogy a megoldás által megcélzott virtuális gépek et adjon hozzá, vagy kifejezetten kizárja a gépeket a megoldásból.

### <a name="add-a-vm"></a>Virtuális gép hozzáadása

Két lehetőség, amely segítségével győződjön meg arról, hogy a virtuális gép szerepel a Start/Stop megoldás futtatásakor.

* A megoldás minden szülő [runbookja](automation-solution-vm-management.md#runbooks) rendelkezik egy `VMList` paraméterrel. A virtuális gépnevek vesszővel tagolt listáját átadhatja ennek a paraméternek, amikor a megfelelő szülő runbookot ütemezi a helyzethez, és ezek a virtuális gépek a megoldás futtatásakor lesznek.

* Több virtuális gép kijelöléséhez állítsa be, `External_Start_ResourceGroupNames` és `External_Stop_ResourceGroupNames` az erőforráscsoport neveit tartalmazó virtuális gépek et szeretne indítani vagy leállítani. A változókat is beállíthatja `*` egy értékre, hogy a megoldás az előfizetés összes erőforráscsoportján fusson.

### <a name="exclude-a-vm"></a>Virtuális gép kizárása

Ha ki szeretne zárni egy virtuális gép a `External_ExcludeVMNames` megoldásból, hozzáadhatja azt a változóhoz. Ez a változó egy vesszővel tagolt lista az egyes virtuális gépek, hogy kizárják a Start/Stop megoldás. Ez a lista legfeljebb 140 virtuális gépre korlátozódik. Ha 140-nél több virtuális gépet ad hozzá ehhez a vesszővel tagolt listához, előfordulhat, hogy a kizárt virtuális gépek véletlenül elindulnak vagy leállnak.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Az indítási és leállítási ütemezés módosítása

Az indítási és leállítási ütemezések kezelése ebben a megoldásban ugyanazokat a lépéseket követi, mint amelyeket a [Runbook ütemezése](automation-schedules.md)az Azure Automationben című dokumentumban ismertetett. A virtuális gépek indításához és leállításához külön ütemezésre van szükség.

A megoldás konfigurálása, hogy csak állítsa le a virtuális gépek egy bizonyos időpontban támogatott. Ebben a forgatókönyvben csak hozzon létre egy **stop** ütemezést, és nincs megfelelő **kezdési** ütemezés. Ehhez a következőket kell tennie:

1. Győződjön meg arról, hogy hozzáadta az `External_Stop_ResourceGroupNames` erőforráscsoportokat a virtuális gépek leállításához a változóban.

2. Hozzon létre saját ütemezést a virtuális gépek leállításának idejére vonatkozóan.

3. Nyissa meg a **ScheduledStartStop_Parent** a runbookot, és kattintson **az Ütemezés gombra.** Ez lehetővé teszi az előző lépésben létrehozott ütemezés kiválasztását.

4. Válassza a **Paraméterek lehetőséget, és futtassa a beállításokat,** és állítsa az **ACTION** mezőt **Leállítás**ra.

5. A módosítások mentéséhez kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

* A virtuális gépek munkaidőn kívüli indítási/leállítási hibáinak elhárításáról a [Virtuális gépek indítási/leállítási hibáinak elhárítása](troubleshoot/start-stop-vm.md)című témakörben olvashat.

* [Tekintse át](automation-solution-vm-management-logs.md) az Azure Monitor naplókba írt Automation-rekordokat és a példa naplókeresési lekérdezéseket az Automation runbook-feladatok állapotának elemzéséhez a virtuális gépek indítási/leállítási szolgáltatásából.
