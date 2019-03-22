---
title: Hibaelhárítás indítása és leállítása a virtuális gépek az Azure Automationnel
description: Ez a cikk nyújt információkat a hibaelhárítás indítása és leállítása virtuális gépek az Azure Automationben
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1894fc1823772c27f37829b9b27ec515dcb841b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861060"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>A gépek indítása/leállítása közben ki óra solution hibaelhárítása

## <a name="deployment-failure"></a>Forgatókönyv: A VM indítása és leállítása a megoldás megfelelő telepítése nem sikerült

### <a name="issue"></a>Probléma

Üzembe helyezésekor a [indítása és leállítása a virtuális gépek ki óra solution](../automation-solution-vm-management.md), a következő hibák egyike jelentkezik:

```
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
```

```
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

### <a name="cause"></a>Ok

Az alábbi okok egyike miatt sikertelenek lehetnek a központi telepítések:

1. Már van egy Automation-fiókot a kiválasztott régióban ugyanazzal a névvel.
2. A házirend van érvényben, amely tiltja a megoldás üzembe helyezése a virtuális gépek indítása/leállítása.
3. A `Microsoft.OperationsManagement`, `Microsoft.Insights`, vagy `Microsoft.Automation` erőforrástípus nincs regisztrálva.
4. A Log Analytics-munkaterület-zárolással rendelkezik rajta.

### <a name="resolution"></a>Megoldás:

Tekintse át a problémával vagy helyek keresse meg a lehetséges megoldások a következők:

1. Az Automation-fiókok kell egyedinek lennie az Azure-régióban, még akkor is, ha különböző erőforráscsoportokban vannak. Ellenőrizze a meglévő Automation-fiókok a célrégióban.
2. Meglévő házirend megakadályozza, hogy egy erőforrás, amely szükséges a indítása és leállítása a Virtuálisgép-megoldás üzembe helyezni. Nyissa meg a szabályzat-hozzárendelések az Azure Portalon, és ellenőrizze, hogy rendelkezik-e a központi telepítés az erőforrás letiltó szabályzat-hozzárendelés. Ezzel kapcsolatos további információkért lásd: [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. A indítása és leállítása a VM-megoldás üzembe helyezéséhez, az előfizetés regisztrálva kell lennie a következő Azure-erőforrás-névterekhez:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Látható, [ki a hibákat az erőforrás-szolgáltatói regisztrációt](../../azure-resource-manager/resource-manager-register-provider-errors.md) kapcsolatos további hibák szolgáltatók regisztrálása során.
4. Ha a zárolást a Log Analytics-munkaterületen, nyissa meg a munkaterületet az Azure Portalon, és távolítsa el az erőforrás bármilyen zárolása.

## <a name="all-vms-fail-to-startstop"></a>Forgatókönyv: Minden virtuális gép indítása és leállítása nem sikerült

### <a name="issue"></a>Probléma

Konfigurálta a indítása és leállítása a VM-megoldás, de nem indítsa el vagy állítsa le a konfigurált virtuális gépeket.

### <a name="cause"></a>Ok

Ez a hiba a következő okok valamelyike okozhatja:

1. Nincs megfelelően beállítva ütemezés
2. Előfordulhat, hogy a futtató fiók nincs megfelelően konfigurálva
3. Egy runbook előfordulhat, hogy rendelkezik hibákba
4. A virtuális gépek kizárva

### <a name="resolution"></a>Megoldás:

Tekintse át a problémával vagy helyek keresse meg a lehetséges megoldások a következők:

* Ellenőrizze, hogy megfelelően konfigurálta a indítása és leállítása a VM-megoldás ütemezését. Ismerje meg, hogyan állítson be egy ütemezést, tekintse meg a [ütemezések](../automation-schedules.md) cikk.

* Ellenőrizze a feladatstreamek keresse ki a hibákat a runbookok számára. A portálon lépjen az Automation-fiókját, és válassza **feladatok** alatt **Folyamatautomatizálás**. Az a **feladatok** lapon keresse meg a következő forgatókönyvek közül feladatok:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Ellenőrizze a [futtató fiók](../manage-runas-account.md) a virtuális gépek indítása vagy leállítása kívánt megfelelő engedélyekkel rendelkezik. Ellenőrizze az engedélyeit erőforrás kezelésével kapcsolatos információkért lásd: [a rövid útmutató: Az Azure portal használatával egy felhasználóhoz hozzárendelt szerepkörök megtekintése](../../role-based-access-control/check-access.md). Adja meg az alkalmazásazonosítót a futtató fiók által használt egyszerű szolgáltatás kell. Ezt az értéket lekérheti az Automation-fiókját az Azure Portalon a kiválasztásával **futtató fiókok** alatt **Fiókbeállítások** , és kattintson a megfelelő futtató fiókot.

* Virtuális gépek nem lehetséges, hogy elindult vagy leállt, ha azok még folyamatban van kifejezetten kizárva. Beállítva a virtuális gépek kizárt a **External_ExcludeVMNames** változó az Automation-fiókban a megoldás van telepítve. A következő példa bemutatja, hogyan kérdezheti le ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Forgatókönyv: Az egyes virtuális gépek indítása vagy leállítása sikertelen

### <a name="issue"></a>Probléma

Konfigurálta a indítása és leállítása a VM-megoldás, de nem indítása vagy leállítása konfigurált virtuális gépek némelyike.

### <a name="cause"></a>Ok

Ez a hiba a következő okok valamelyike okozhatja:

1. Ha a feladatütemezés forgatókönyvet használja, egy címke lehet hiányzik vagy helytelen
2. A virtuális Gépet ki kell zárni
3. A futtató fiók nem lehetséges, hogy nincs megfelelő jogosultsága a virtuális gépen
4. A virtuális gép lehet, hogy leállítása, indítása vagy leállítása

### <a name="resolution"></a>Megoldás:

Tekintse át a problémával vagy helyek keresse meg a lehetséges megoldások a következők:

* Használatakor a [feladatütemezési forgatókönyv](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) indítása és leállítása a virtuális gép során ki óra solution, gondoskodnia kell arról, indítása vagy leállítása kívánt minden egyes virtuális gép rendelkezik a megfelelő címkét. Ellenőrizze, hogy a virtuális gépeket, amely elindítja a rendelkezik a `sequencestart` címke és a virtuális gépek meg szeretné szüntetni a `sequencestop` címke. Mindkét címkék pozitív egész értéket kell megadni. Az alábbi példához hasonló lekérdezés segítségével keresse meg a virtuális gépek a címkéket és azok értékeit.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuális gépek nem lehetséges, hogy elindult vagy leállt, ha azok még folyamatban van kifejezetten kizárva. Beállítva a virtuális gépek kizárt a **External_ExcludeVMNames** változó az Automation-fiókban a megoldás van telepítve. A következő példa bemutatja, hogyan kérdezheti le ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Elindíthatja és leállíthatja a virtuális gépeket, hogy az Automation-fiók futtató fiókjának rendelkeznie kell a virtuális gép megfelelő engedélyekkel. Ellenőrizze az engedélyeit erőforrás kezelésével kapcsolatos információkért lásd: [a rövid útmutató: Az Azure portal használatával egy felhasználóhoz hozzárendelt szerepkörök megtekintése](../../role-based-access-control/check-access.md). Adja meg az alkalmazásazonosítót a futtató fiók által használt egyszerű szolgáltatás kell. Ezt az értéket lekérheti az Automation-fiókját az Azure Portalon a kiválasztásával **futtató fiókok** alatt **Fiókbeállítások** , és kattintson a megfelelő futtató fiókot.

* Ha a virtuális gép nem indul vagy felszabadítása hibás, ez a viselkedés a virtuális gépre a problémát okozhatja. Néhány példa vagy potenciális problémák, egy frissítését végzi, Leállítás, a szolgáltatás tett kísérlet során lefagy és egyebek). Keresse meg a virtuális gép erőforrást, és ellenőrizze a **tevékenységeket tartalmazó naplók** van-e hibák a naplók megtekintéséhez. Is megkísérelheti a jelentkezzen be a virtuális gép nem találhatók esetleges hibákat az eseménynaplóban. A virtuális gép hibaelhárítással kapcsolatos további tudnivalókért lásd: [hibaelhárítása Azure virtual machines szolgáltatásban](../../virtual-machines/troubleshooting/index.md)

## <a name="custom-runbook"></a>Forgatókönyv: Saját egyéni runbook indítása és leállítása a virtuális gépek nem

### <a name="issue"></a>Probléma

Már létrehozott egy egyéni runbook vagy letöltött egy, a PowerShell-galériából, és nem működik megfelelően.

### <a name="cause"></a>Ok

A hiba okát sok dolog lehet. Lépjen az Automation-fiókját az Azure Portalon, és válassza a **feladatok** alatt **Folyamatautomatizálás**. Az a **feladatok** lapon, keresse meg a feladatok a runbookban esetleges megtekintéséhez.

### <a name="resolution"></a>Megoldás:

Azt javasoljuk, hogy használja a [indítása és leállítása a virtuális gépek ki óra solution](../automation-solution-vm-management.md) elindíthatja és leállíthatja a virtuális gépek az Azure Automationben. Ez a megoldás a Microsoft hozta létre. Egyéni runbookjai nem támogatottak a Microsoft által. Láthatja a megoldás az egyéni runbook meglátogatják a [runbook hibaelhárítási](runbooks.md) cikk. Ez a cikk általános útmutatás és hibaelhárítás a runbookok összes típusú.

## <a name="dont-start-stop-in-sequence"></a>Forgatókönyv: Virtuális gépek ne indítsa el vagy állítsa le a megfelelő sorrendben

### <a name="issue"></a>Probléma

A megoldás konfigurált virtuális gépek ne indítsa el vagy állítsa le a megfelelő sorrendben.

### <a name="cause"></a>Ok

A virtuális gépek megfelelő címkézés okozza.

### <a name="resolution"></a>Megoldás:

Az alábbi lépéseket, győződjön meg arról, hogy a megoldás megfelelően van-e konfigurálva.

1. Győződjön meg róla rendelkezik minden virtuális gép elindítani vagy leállítani egy `sequencestart` vagy `sequencestop` címke, a helyzettől függően. Ezekkel a címkékkel pozitív egész értéket kell. Virtuális gépek feldolgozása növekvő sorrendben, ez az érték alapján történik.
2. Ellenőrizze, hogy az erőforráscsoportok indítható el, hogy a virtuális gépek vagy a rendszer leállította a `External_Start_ResourceGroupNames` vagy `External_Stop_ResourceGroupNames` változók, a helyzettől függően.
3. Tesztelheti a módosításokat úgy, hogy végrehajtja a `SequencedStartStop_Parent` runbook WHATIF paraméter igaz értékre állítva lehetővé a módosításokat.

Részletes, és további útmutatás a megoldás használatával elindíthatja és leállíthatja a virtuális gépek sorrendben: [indítása és leállítása a virtuális gépek sorrendben](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Forgatókönyv: VM indítása és leállítása feladat sikertelen lesz, 403 Tiltott állapot 

### <a name="issue"></a>Probléma

A sikertelen feladatok talál egy `403 forbidden` a gépek indítása/leállítása közben ki óra solution runbookok által jelzett hibát.

### <a name="cause"></a>Ok

A probléma okozhatja egy helytelenül konfigurált vagy lejárt futtató fiókot. A Virtuálisgép-erőforrások által az automatizálási fiók futtató fiók megfelelő engedélyei a miatt is lehet.

### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a futtató fiók megfelelően van konfigurálva, nyissa meg az Automation-fiókját az Azure Portalon, és válassza **futtató fiókok** alatt **fiókbeállításokat**. Itt láthatja az állapotát, a futtató fiókok, ha egy futtató fiókot nem megfelelően konfigurálva vagy lejárt állapotát jeleníti meg ezt.

Ha a futtató fiók [helytelenül konfigurált](../manage-runas-account.md#misconfiguration), törölje és hozza létre újra a futtató fiók.

Ha a Futtatás mint fiók a tanúsítvány lejárt, a következő lépésekkel [önaláírt tanúsítvány megújításához](../manage-runas-account.md#cert-renewal) a tanúsítvány megújításához.

A probléma okozhatja nem rendelkezik engedéllyel. Ellenőrizze az engedélyeit erőforrás kezelésével kapcsolatos információkért lásd: [a rövid útmutató: Az Azure portal használatával egy felhasználóhoz hozzárendelt szerepkörök megtekintése](../../role-based-access-control/check-access.md). Adja meg az alkalmazásazonosítót a futtató fiók által használt egyszerű szolgáltatás kell. Ezt az értéket lekérheti az Automation-fiókját az Azure Portalon a kiválasztásával **futtató fiókok** alatt **Fiókbeállítások** , és kattintson a megfelelő futtató fiókot.

## <a name="other"></a>Forgatókönyv: A probléma nem szerepel a listán felett

### <a name="issue"></a>Probléma

Virtuális gépek indítása/leállítása munkaidőn kívül megoldás, amely nem szerepel ezen a lapon használatakor egy probléma vagy a nem várt eredményt tapasztal.

### <a name="cause"></a>Ok

Sokszor hibát okozhatja a megoldás egy régi és az elavult verzióját használja.

### <a name="resolution"></a>Megoldás:

Sok hiba elhárításához, azt javasoljuk, hogy távolítsa el, és a megoldás frissítése. A megoldás frissítésével kapcsolatban lásd: [frissítése a gépek indítása/leállítása közben ki óra solution](../automation-solution-vm-management.md#update-the-solution).

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.
