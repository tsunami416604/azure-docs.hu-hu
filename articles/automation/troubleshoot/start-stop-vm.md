---
title: Virtuális gépek indítására és leállítására vonatkozó hibák – Azure Automation
description: Ez a cikk a virtuális gépek Azure Automation-ban való indításával és leállításával kapcsolatos hibaelhárítási tudnivalókat tartalmaz.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97ea98fc38fc8d06dc1bc65ee057241da6f15488
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851382"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>A virtuális gépek indítása és leállítása a munkaidőn kívüli megoldással – problémamegoldás

## <a name="deployment-failure"></a>Forgatókönyv: a virtuális gép indítása/leállítása nem sikerül megfelelően telepíteni

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

1. Már van ilyen nevű Automation-fiók a kiválasztott régióban.
2. Olyan házirend van érvényben, amely nem teszi lehetővé a virtuális gépek indítási/leállítási megoldásának telepítését.
3. A `Microsoft.OperationsManagement`-, `Microsoft.Insights`-vagy `Microsoft.Automation`-erőforrástípusok nincsenek regisztrálva.
4. A Log Analytics munkaterület zárolva van.
5. A AzureRM modulok elavult verziója vagy a Start/Stop megoldás.

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi listát a lehetséges megoldásokról a problémára vagy a helyekre:

1. Az Automation-fiókoknak egyedinek kell lenniük egy Azure-régión belül, még akkor is, ha azok különböző erőforráscsoportok. Keresse meg a meglévő Automation-fiókokat a célhelyen.
2. Egy meglévő szabályzat megakadályozza a virtuálisgép-megoldás üzembe helyezésének megkezdéséhez/leállításához szükséges erőforrást. Lépjen a Azure Portal házirend-hozzárendeléseire, és győződjön meg arról, hogy rendelkezik-e olyan házirend-hozzárendeléssel, amely nem engedélyezi az erőforrás központi telepítését. További információ: [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
3. A virtuálisgép-indítási és leállítási megoldás üzembe helyezéséhez regisztrálnia kell az előfizetést a következő Azure-beli erőforrás-névterekben:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Az [erőforrás-szolgáltató regisztrálásával kapcsolatos hibák elhárításával](../../azure-resource-manager/templates/error-register-resource-provider.md) kapcsolatos további információkért tekintse meg a szolgáltatók regisztrálásakor fellépő hibákat.
4. Ha zárolva van a Log Analytics munkaterületen, lépjen a munkaterületre a Azure Portalban, és távolítsa el az erőforrás összes zárolását.
5. Ha a fenti állásfoglalások nem oldják meg a problémát, kövesse a [megoldás frissítése](../automation-solution-vm-management.md#update-the-solution) című témakör útmutatását a Start/Stop megoldás újbóli üzembe helyezéséhez.

## <a name="all-vms-fail-to-startstop"></a>Forgatókönyv: az összes virtuális gép elindítása/leállítása sikertelen

### <a name="issue"></a>Probléma

Konfigurálta a virtuálisgép-indítási és-leállítási megoldást, de az nem indul el vagy nem állítja le az összes konfigurált virtuális GÉPET.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok egyike okozhatta:

1. Egy ütemterv nincs megfelelően konfigurálva
2. Lehetséges, hogy a futtató fiók helytelenül van konfigurálva.
3. Lehetséges, hogy egy runbook hibákba ütközik
4. Lehetséges, hogy a virtuális gépek ki lettek zárva

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi listát a lehetséges megoldásokról a problémára vagy a helyekre:

* Győződjön meg arról, hogy megfelelően konfigurálta az ütemtervet a virtuálisgép-indítási és-leállítási megoldáshoz. Az ütemtervek konfigurálásának megismeréséhez tekintse meg az [ütemterveket](../automation-schedules.md) ismertető cikket.

* Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) . A portálon nyissa meg az Automation-fiókját, és válassza a **feladatok** **automatizálása**lehetőséget. A **feladatok** lapon keresse meg a következő runbookok egyikét:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Győződjön meg arról, hogy a futtató [fiók](../manage-runas-account.md) megfelelő engedélyekkel rendelkezik az elindítani vagy leállítani kívánt virtuális gépekhez. Az erőforrásokra vonatkozó engedélyek vizsgálatával kapcsolatban lásd: gyors útmutató [: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md). Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjába, majd a **fiók beállításai** területen a **futtató fiókok** elemre, majd a megfelelő futtató fiókra.

* Előfordulhat, hogy a virtuális gépeket nem lehet elindítani vagy leállítani, ha explicit módon ki vannak zárva. Kizárt virtuális gépek az Automation-fiók **External_ExcludeVMNames** változójában, amelyet a megoldás üzembe helyez. Az alábbi példa bemutatja, hogyan kérdezheti le ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Forgatókönyv: a virtuális gépek némelyike nem indul el vagy leáll

### <a name="issue"></a>Probléma

Konfigurálta a virtuálisgép-indítási és-leállítási megoldást, de nem indul el vagy nem állítja le a konfigurált virtuális gépek némelyikét.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok egyike okozhatta:

1. Ha a sorozatot használja, a címke hiányozhat vagy helytelen lehet.
2. Lehetséges, hogy a virtuális gép ki van zárva
3. Előfordulhat, hogy a futtató fiók nem rendelkezik elegendő jogosultsággal a virtuális gépen
4. Előfordulhat, hogy a virtuális gép nem indul el vagy leáll

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi listát a lehetséges megoldásokról a problémára vagy a helyekre:

* Ha [a virtuális](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) gép elindítása/leállítása a munkaidőn kívüli megoldásban az indítás/leállítás lehetőséget használja, meg kell győződnie arról, hogy az összes elindítani vagy leállítani kívánt virtuális gép megfelelő címkével rendelkezik. Győződjön meg arról, hogy az elindítani kívánt virtuális gépek rendelkeznek a `sequencestart` címkével és a leállítani kívánt virtuális gépekkel `sequencestop` címkével. Mindkét címkéhez pozitív egész értéket kell megadni. Az alábbi példához hasonló lekérdezéssel megkeresheti az összes virtuális gépet a címkékkel és azok értékeivel.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Előfordulhat, hogy a virtuális gépeket nem lehet elindítani vagy leállítani, ha explicit módon ki vannak zárva. Kizárt virtuális gépek az Automation-fiók **External_ExcludeVMNames** változójában, amelyet a megoldás üzembe helyez. Az alábbi példa bemutatja, hogyan kérdezheti le ezt az értéket a PowerShell használatával.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* A virtuális gépek indításához és leállításához az Automation-fiókhoz tartozó futtató fióknak megfelelő engedélyekkel kell rendelkeznie a virtuális géphez. Az erőforrásokra vonatkozó engedélyek vizsgálatával kapcsolatban lásd: gyors útmutató [: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md). Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjába, majd a **fiók beállításai** területen a **futtató fiókok** elemre, majd a megfelelő futtató fiókra.

* Ha a virtuális gépnek probléma van a megkezdése vagy felszabadítása során, akkor ezt a viselkedést a virtuális gép hibája okozhatja. Néhány példa vagy lehetséges probléma: a leállítási kísérlet, a szolgáltatás leállása és egyebek esetén frissítés kerül alkalmazásra. Navigáljon a virtuálisgép-erőforráshoz, és ellenőrizze a **tevékenységek naplóit** , hogy vannak-e hibák a naplókban. Megpróbálhatja bejelentkezni a virtuális gépre, hogy ellenőrizze, van-e hiba az eseménynaplókban. További információ a virtuális gép hibaelhárításáról: Azure-beli [virtuális gépek hibaelhárítása](../../virtual-machines/troubleshooting/index.yml)

* Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) . A portálon nyissa meg az Automation-fiókját, és válassza a **feladatok** **automatizálása**lehetőséget.

## <a name="custom-runbook"></a>Forgatókönyv: az egyéni runbook nem sikerül elindítani vagy leállítani a virtuális gépeket

### <a name="issue"></a>Probléma

Létrehozott egy egyéni runbook, vagy letöltött egyet a PowerShell-galériaból, és nem működik megfelelően.

### <a name="cause"></a>Ok

A hiba oka lehet a sok dolog egyike. Nyissa meg az Automation-fiókját a Azure Portalon, és válassza a **folyamatok automatizálása**alatt a **feladatok** lehetőséget. A **feladatok** lapon keresse meg a runbook feladatait, és tekintse meg a feladatok hibáit.

### <a name="resolution"></a>Megoldás:

Azt javasoljuk, hogy a virtuális gépek [elindítása/leállítása a munkaidőn kívüli megoldásnál](../automation-solution-vm-management.md) a Azure Automationban lévő virtuális gépek elindításához és leállításához. Ezt a megoldást a Microsoft készítette el. A Microsoft nem támogatja az egyéni runbookok használatát. Előfordulhat, hogy az egyéni runbook megoldást talál a [runbook hibaelhárítási](runbooks.md) cikkében. Ez a cikk általános útmutatást és hibaelhárítást biztosít az összes típusú runbookok kapcsolatban. Ellenőrizze, hogy vannak-e hibák a [feladatok streamben](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) . A portálon nyissa meg az Automation-fiókját, és válassza a **feladatok** **automatizálása**lehetőséget.

## <a name="dont-start-stop-in-sequence"></a>Forgatókönyv: a virtuális gépek nem indulnak el vagy leállnak a megfelelő sorozatban

### <a name="issue"></a>Probléma

A megoldásban konfigurált virtuális gépek nem indulnak el vagy nem állnak le a megfelelő sorozatban.

### <a name="cause"></a>Ok

Ezt a virtuális gépek helytelen címkézése okozta.

### <a name="resolution"></a>Megoldás:

A következő lépések végrehajtásával győződjön meg arról, hogy a megoldás megfelelően van konfigurálva.

1. Győződjön meg arról, hogy az összes elindítható vagy leállított virtuális gép `sequencestart` vagy `sequencestop` címkével rendelkezik, a helyzettől függően. Ezeknek a címkéknek pozitív egész számnak kell lennie az értékként. A virtuális gépek az érték alapján növekvő sorrendben lesznek feldolgozva.
2. Győződjön meg arról, hogy a virtuális gépek elindításához vagy leállításához szükséges erőforráscsoportok az adott helyzettől függően `External_Start_ResourceGroupNames` vagy `External_Stop_ResourceGroupNames` változóban vannak.
3. Tesztelje a módosításokat úgy, hogy végrehajtja a `SequencedStartStop_Parent` runbook, és a WHATIF paraméter értéke TRUE (igaz) értékre van állítva a módosítások megtekintéséhez.

A virtuális gépek egymás utáni indítására és leállítására szolgáló megoldás használatával kapcsolatos részletes és további útmutatásért lásd: [virtuális gépek indítása/leállítása a sorozatban](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Forgatókönyv: a virtuális gép indítása/leállítása feladattal meghiúsul, 403 Tiltott állapottal

### <a name="issue"></a>Probléma

Olyan feladatokat talál, amelyek sikertelenek voltak, `403 forbidden` hiba történt a virtuális gépek indítási és leállítási ideje alatt a megoldás runbookok.

### <a name="cause"></a>Ok

Ezt a problémát egy nem megfelelően konfigurált vagy lejárt futtató fiók okozhatja. Az is előfordulhat, hogy az Automation-fiókok futtató fiókja nem rendelkezik megfelelő engedélyekkel a virtuális gépek erőforrásaihoz.

### <a name="resolution"></a>Megoldás:

A futtató fiók megfelelő konfigurálásához lépjen a Azure Portal Automation-fiókjába, és válassza a **fiók beállításai**alatt a **futtató fiókok** elemet. Itt láthatja a futtató fiókok állapotát, ha a futtató fiók helytelenül van konfigurálva vagy lejárt, és ez az állapot jelenik meg.

Ha a futtató fiók helytelenül van konfigurálva, törölje, majd hozza létre újra a futtató fiókot. Lásd: [Azure Automation futtató fiókok kezelése](../manage-runas-account.md).

Ha a tanúsítvány lejárt a futtató fiókhoz, kövesse az [önaláírt tanúsítvány megújításakor](../manage-runas-account.md#cert-renewal) felsorolt lépéseket a tanúsítvány megújításához.

Lehetséges, hogy a problémát a hiányzó engedélyek okozzák. Az erőforrásokra vonatkozó engedélyek vizsgálatával kapcsolatban lásd: gyors útmutató [: a felhasználóhoz rendelt szerepkörök megtekintése a Azure Portal használatával](../../role-based-access-control/check-access.md). Meg kell adnia a futtató fiók által használt szolgáltatásnév alkalmazás-AZONOSÍTÓját. Ezt az értéket lekérheti a Azure Portal Automation-fiókjába, majd a **fiók beállításai** területen a **futtató fiókok** elemre, majd a megfelelő futtató fiókra.

## <a name="other"></a>Forgatókönyv: a probléma nem szerepel a fentiekben

### <a name="issue"></a>Probléma

Az ezen a lapon nem szereplő Start/Stop VMs during off-hours megoldás használatakor probléma vagy váratlan eredmény tapasztalható.

### <a name="cause"></a>Ok

Sokszor a megoldás régi és elavult változata is okozhatja a hibákat.

> [!NOTE]
> A Start/Stop VMs during off-hours megoldás az Automation-fiókba importált Azure-modulokkal lett tesztelve a megoldás telepítésekor. A megoldás jelenleg nem működik az Azure-modul újabb verzióival. Ez csak az Start/Stop VMs during off-hours megoldás futtatásához használt Automation-fiókra vonatkozik. Továbbra is használhatja az Azure-modul újabb verzióit a többi Automation-fiókban a következő témakörben ismertetett módon: [Azure PowerShell modulok frissítése Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Megoldás:

Sok hiba elhárításához javasolt a megoldás eltávolítása és frissítése. A megoldás frissítéséről a [virtuális gépek indítása és leállítása a munkaidőn kívüli megoldásban](../automation-solution-vm-management.md#update-the-solution)című témakörben olvashat bővebben. Emellett a [feladatok adatfolyamait](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) is megtekintheti a hibák kereséséhez. A portálon nyissa meg az Automation-fiókját, és válassza a **feladatok** **automatizálása**lehetőséget.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
