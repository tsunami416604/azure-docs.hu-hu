---
title: A virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldással – hibaelhárítás
description: Ez a cikk a virtuális gép elindítása és leállítása a munkaidőn kívüli megoldás során történő hibaelhárításával kapcsolatos információkat tartalmaz.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 611e8441fab56114ca010d0b555c9ed156ae9d40
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855055"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>A virtuális gépek indítása és leállítása a munkaidőn kívüli megoldással – problémamegoldás

Ez a cikk azokat a hibaelhárítási problémákat ismerteti, amelyek akkor merülnek fel, amikor a virtuális gépeket a munkaidőn kívüli megoldásban Azure Automation.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). A Azure Automation fiókjában a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Forgatókönyv: a virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldás nem sikerül megfelelően telepíteni

### <a name="issue"></a>Probléma

Ha a [virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldás alatt](../automation-solution-vm-management.md)történik, a következő hibák valamelyike jelenik meg:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Ok

A központi telepítések a következő okok egyike miatt sikertelenek lehetnek:

- Már van ilyen nevű Automation-fiók a kiválasztott régióban.
- Egy házirend nem engedélyezi a virtuális gépek indítási és leállítási idejének kikapcsolását a munkaidőn kívüli megoldásban.
- A `Microsoft.OperationsManagement`, `Microsoft.Insights`vagy `Microsoft.Automation` az erőforrástípus nincs regisztrálva.
- A Log Analytics munkaterület zárolva van.
- A AzureRM modulok elavult verziója vagy a virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldásban.

### <a name="resolution"></a>Megoldás:

Tekintse át a következő javításokat a problémával kapcsolatos lehetséges megoldásokhoz:

* Az Automation-fiókoknak egyedi Azure-régión belül kell lenniük, még akkor is, ha különböző erőforráscsoportok. Keresse meg a meglévő Automation-fiókokat a célhelyen.
* Egy meglévő szabályzat megakadályozza, hogy a virtuális gépek indítása és leállítása a kikapcsolási idő megoldásában az üzembe helyezés ideje alatt megkövetelt erőforrást. Lépjen a Azure Portal házirend-hozzárendeléseire, és győződjön meg arról, hogy rendelkezik-e olyan szabályzat-hozzárendeléssel, amely nem engedélyezi az erőforrás központi telepítését. További információ: [RequestDisallowedByPolicy hiba](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* A virtuális gépek indítása és leállítása megoldás üzembe helyezéséhez az előfizetést regisztrálni kell a következő Azure-beli erőforrás-névterekben:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Ha többet szeretne megtudni a szolgáltatók regisztrálásával kapcsolatos hibákról, tekintse meg [az erőforrás-szolgáltatói regisztráció hibáinak elhárítása](../../azure-resource-manager/templates/error-register-resource-provider.md)című témakört.
* Ha zárolva van a Log Analytics munkaterületen, lépjen a munkaterületre a Azure Portalban, és távolítsa el az erőforrás összes zárolását.
* Ha ezek a megoldások nem oldják meg a problémát, kövesse a [megoldás frissítése](../automation-solution-vm-management.md#update-the-solution) részben található utasításokat a virtuális gépek elindítása és leállítása a munkaidőn kívüli megoldásban című témakörben leírtak szerint.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Forgatókönyv: az összes virtuális gép elindítása vagy leállítása sikertelen

### <a name="issue"></a>Probléma

A virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldásként történt, de nem indul el vagy nem állítja le az összes virtuális gépet.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok egyike okozhatta:

- Az ütemterv nincs megfelelően konfigurálva.
- Lehet, hogy a futtató fiók nem megfelelően van konfigurálva.
- Lehetséges, hogy egy runbook hibák léptek fel.
- Lehetséges, hogy a virtuális gépek ki lettek zárva.

### <a name="resolution"></a>Megoldás:

Tekintse át a következő listát a problémával kapcsolatos lehetséges megoldásokról:

* Győződjön meg arról, hogy megfelelően konfigurálta az ütemtervet a virtuális gépek indításához és leállításához a munkaidőn kívüli megoldásban. Az ütemtervek konfigurálásának megismeréséhez lásd: [ütemtervek](../automation-schedules.md).

* Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#job-statuses) . Keressen feladatokat a következő runbookok egyikéről:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Ellenőrizze, hogy a [futtató fiókja](../manage-runas-account.md) rendelkezik-e megfelelő engedélyekkel az elindítani vagy leállítani kívánt virtuális gépekhez. Az erőforrásokra vonatkozó engedélyek vizsgálatával kapcsolatban lásd: gyors útmutató [: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md). Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjával. Válassza a **fiók beállításai**területen a **futtató fiókok** lehetőséget, majd válassza ki a megfelelő futtató fiókot.

* Előfordulhat, hogy a virtuális gépeket nem lehet elindítani vagy leállítani, ha explicit módon ki vannak zárva. A kizárt virtuális gépeket abban az `External_ExcludeVMNames` Automation-fiókban lévő változóban kell beállítani, amelyre a megoldás telepítve van. Az alábbi példa bemutatja, hogyan kérdezheti le ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Forgatókönyv: a virtuális gépek némelyike nem indul el vagy leáll

### <a name="issue"></a>Probléma

A virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldásként van konfigurálva, de nem indul el vagy nem állítja le a konfigurált virtuális gépek némelyikét.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok egyike okozhatta:

- A Sequence forgatókönyvben előfordulhat, hogy a címke hiányzik vagy helytelen.
- Lehet, hogy a virtuális gép ki van zárva.
- Előfordulhat, hogy a futtató fiók nem rendelkezik megfelelő engedélyekkel a virtuális gépen.
- A virtuális gép rendelkezhet olyan hibával, amely leállt az indítástól vagy a leállítástól.

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi listát a lehetséges megoldásokról a problémára vagy a helyekre:

* Ha a virtuális gépek indítása/leállítása a munkaidőn kívüli megoldásban [szakaszának a Sequence forgatókönyvét](../automation-solution-vm-management.md) használja, meg kell győződnie arról, hogy minden egyes elindítani vagy leállítani kívánt virtuális gép rendelkezik a megfelelő címkével. Győződjön meg arról, hogy az elindítani kívánt virtuális gépek rendelkeznek `sequencestart` a címkével és a leállítani `sequencestop` kívánt virtuális gépekkel. Mindkét címkéhez pozitív egész értéket kell megadni. Az alábbi példához hasonló lekérdezéssel megkeresheti az összes virtuális gépet a címkékkel és azok értékeivel.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Előfordulhat, hogy a virtuális gépeket nem lehet elindítani vagy leállítani, ha explicit módon ki vannak zárva. A kizárt virtuális gépeket abban az `External_ExcludeVMNames` Automation-fiókban lévő változóban kell beállítani, amelyre a megoldás telepítve van. Az alábbi példa bemutatja, hogyan kérdezheti le ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* A virtuális gépek indításához és leállításához az Automation-fiókhoz tartozó futtató fióknak megfelelő engedélyekkel kell rendelkeznie a virtuális géphez. Az erőforrásokra vonatkozó engedélyek vizsgálatával kapcsolatban lásd: gyors útmutató [: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md). Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjával. Válassza a **fiók beállításai** területen a **futtató fiókok** lehetőséget, majd válassza ki a megfelelő futtató fiókot.
* Ha a virtuális gép a probléma megkezdése vagy felszabadítása során problémákba ütközik, előfordulhat, hogy a virtuális gépen probléma merül fel. Ilyenek például egy olyan frissítés, amely akkor lép életbe, amikor a virtuális gép le van állítva, és a szolgáltatás leáll. Nyissa meg a virtuális gép erőforrását, **és ellenőrizze, hogy vannak-e** hibák a naplókban. Előfordulhat, hogy megpróbál bejelentkezni a virtuális gépre, hogy ellenőrizze, van-e hiba az eseménynaplókban. A virtuális gép hibaelhárításával kapcsolatos további tudnivalókért tekintse meg az Azure-beli [virtuális gépek hibaelhárítása](../../virtual-machines/troubleshooting/index.yml)című témakört.
* Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#job-statuses) . A portálon nyissa meg az Automation-fiókját, és válassza a **feladatok** **automatizálása**lehetőséget.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Forgatókönyv: az egyéni runbook nem sikerül elindítani vagy leállítani a virtuális gépeket

### <a name="issue"></a>Probléma

Létrehozott egy egyéni runbook, vagy letöltött egyet a PowerShell-galériaból, és nem működik megfelelően.

### <a name="cause"></a>Ok

A hiba számos oka lehet. Nyissa meg az Automation-fiókját a Azure Portal, és válassza a **feladatok** **automatizálása**lehetőséget. A **feladatok** lapon keresse meg a runbook feladatait, és tekintse meg a feladatok hibáit.

### <a name="resolution"></a>Megoldás:

A következő megoldást javasoljuk:

* A virtuális gépek [elindítása és leállítása a munkaidőn kívüli megoldásban](../automation-solution-vm-management.md) a Azure Automationban lévő virtuális gépek elindításához és leállításához. Ezt a megoldást a Microsoft készítette el. 
* Vegye figyelembe, hogy a Microsoft nem támogatja az egyéni runbookok. Előfordulhat, hogy az egyéni runbook megoldást talál a [runbook hibaelhárítása](runbooks.md)során. Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#job-statuses) . 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Forgatókönyv: a virtuális gépek nem indulnak el vagy leállnak a megfelelő sorozatban

### <a name="issue"></a>Probléma

A megoldásban konfigurált virtuális gépek nem indulnak el vagy nem állnak le a megfelelő sorozatban.

### <a name="cause"></a>Ok

Ezt a problémát a virtuális gépek helytelen címkézése okozza.

### <a name="resolution"></a>Megoldás:

A következő lépésekkel ellenőrizheti, hogy a megoldás megfelelően van-e konfigurálva.

1. Győződjön meg arról, hogy az összes elindítható `sequencestart` vagy `sequencestop` leállított virtuális gép rendelkezik a helyzettől függően a vagy a címkével. Ezeknek a címkéknek pozitív egész számnak kell lennie az értékként. A virtuális gépek az érték alapján növekvő sorrendben lesznek feldolgozva.
1. Győződjön meg arról, hogy a virtuális gépek elindításához vagy leállításához szükséges `External_Start_ResourceGroupNames` erőforráscsoportok `External_Stop_ResourceGroupNames` a helyzettől függően a vagy a változóban vannak.
1. Tesztelje a módosításokat úgy, hogy `SequencedStartStop_Parent` a Runbook `WHATIF` az igaz értékre állítja, és megtekinti a módosításokat.

További információ arról, hogyan használható a megoldás a virtuális gépek indítására és leállítására a következő helyen: [virtuális gépek indítása/leállítása a sorozatban](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Forgatókönyv: a virtuális gépek elindítása/leállítása a munkaidőn kívüli feladatban sikertelen, 403 Tiltott hiba

### <a name="issue"></a>Probléma

Olyan `403 forbidden` feladatokat talál, amelyek a virtuális gépek indítására és leállítására vonatkozó hiba miatt sikertelenek voltak a megoldás runbookok.

### <a name="cause"></a>Ok

Ezt a problémát egy nem megfelelően konfigurált vagy lejárt futtató fiók okozhatja. Az is előfordulhat, hogy a futtató fiók nem rendelkezik megfelelő engedélyekkel a virtuális gép erőforrásaihoz.

### <a name="resolution"></a>Megoldás:

Annak ellenőrzéséhez, hogy a futtató fiók megfelelően van-e konfigurálva, lépjen a Azure Portal Automation-fiókjába, és válassza a **fiók beállításai**alatt a **futtató fiókok** elemet. Ha egy futtató fiók helytelenül van konfigurálva vagy lejárt, az állapot megjeleníti a feltételt.

Ha a futtató fiók helytelenül van konfigurálva, törölje és hozza létre újra a futtató fiókot. További információ: [Azure Automation futtató fiókok kezelése](../manage-runas-account.md).

Ha a tanúsítvány lejárt a futtató fiókhoz, kövesse az [önaláírt tanúsítvány megújításának](../manage-runas-account.md#cert-renewal) lépéseit a tanúsítvány megújításához.

Ha hiányoznak az engedélyek, tekintse [meg a rövid útmutató: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md)című témakört. Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjával. Válassza a **fiók beállításai**területen a **futtató fiókok** lehetőséget, majd válassza ki a megfelelő futtató fiókot.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Forgatókönyv: a probléma nem szerepel a listán

### <a name="issue"></a>Probléma

Probléma vagy váratlan eredmény tapasztalható, ha a virtuális gépek elindítása/leállítása a nem ezen a lapon nem szereplő megoldással történik.

### <a name="cause"></a>Ok

Sokszor a megoldás régi és elavult változata is okozhatja a hibákat.

> [!NOTE]
> A virtuális gépek elindítása/leállítása a munkaidőn kívüli megoldásban az Automation-fiókba importált Azure-modulokkal lett tesztelve a megoldás telepítésekor. A megoldás jelenleg nem működik az Azure-modul újabb verzióival. Ez a korlátozás csak azt az Automation-fiókot érinti, amelyet a virtuális gépek indítása/leállítása a munkaidőn kívüli megoldás futtatásakor használ. Továbbra is használhatja az Azure-modul újabb verzióit a többi Automation-fiókban a következő témakörben leírtak szerint: [Azure PowerShell-modulok frissítése Azure Automationban](../automation-update-azure-modules.md).

### <a name="resolution"></a>Megoldás:

Számos hiba megoldásához távolítsa el és [frissítse a virtuális gépek indítása és leállítása a munkaidőn kívüli megoldás alatt](../automation-solution-vm-management.md#update-the-solution). A [feladatok adatfolyamait](../automation-runbook-execution.md#job-statuses) is megtekintheti, ha hibákat keres. 

## <a name="next-steps"></a>További lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport)a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése**lehetőséget.