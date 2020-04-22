---
title: A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás hibáinak elhárítása
description: Ez a cikk a virtuális gép indítási/leállítása megoldás hibaelhárításával kapcsolatos információkat tartalmaz.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679161"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás sal kapcsolatos hibák elhárítása

Ez a cikk a virtuális gépek indítási/leállítási munka közben munkaidőn kívüli megoldás közben fellépő hibaelhárítási problémákról nyújt tájékoztatást.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Eset: A virtuális gépek indítása/leállítása munkaidőn kívül megoldás nem megfelelően telepíthető

### <a name="issue"></a>Probléma

A virtuális [gépek indítása/leállítása munkaidőn kívüli megoldás során](../automation-solution-vm-management.md)az alábbi hibaüzenetek egyike jelenik meg:

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

A központi telepítések a következő okok valamelyike miatt sikertelenek lehetnek:

1. Már van egy Automation-fiók ugyanazzal a névvel a kijelölt régióban.
2. A szabályzat nem engedélyezi a virtuális gépek üzembe helyezését munkaidőn kívüli megoldás során.
3. A `Microsoft.OperationsManagement` `Microsoft.Insights`, `Microsoft.Automation` vagy az erőforrástípus nincs regisztrálva.
4. A Log Analytics-munkaterület zárolva van.
5. Az AzureRM-modulok vagy a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás elavult verziójával rendelkezik.

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi javításokat a probléma lehetséges megoldásaiért:

* Az automation-fiókoknak egyedinek kell lenniük egy Azure-régióban, még akkor is, ha különböző erőforráscsoportokban vannak. Ellenőrizze a meglévő Automation-fiókokat a célrégióban.
* Egy meglévő szabályzat megakadályozza, hogy egy erőforrás, amely szükséges a start/stop virtuális gépek munkaidőn kívüli megoldás üzembe helyezéséhez. Nyissa meg a szabályzat-hozzárendeléseket az Azure Portalon, és ellenőrizze, hogy rendelkezik-e olyan házirend-hozzárendeléssel, amely nem engedélyezi az erőforrás üzembe helyezését. Erről a [RequestDisallowedByPolicy című témakörben](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)olvashat bővebben.
* A virtuális gépek indítási/leállítási megoldásának üzembe helyezéséhez az előfizetést regisztrálni kell a következő Azure-erőforrás-névterekbe:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Az [erőforrás-szolgáltató regisztrációjának hibáinak kijavítása](../../azure-resource-manager/templates/error-register-resource-provider.md) című témakörben olvashat bővebben a szolgáltatók regisztrálása során előforduló hibákról.
* Ha zárolta a Log Analytics-munkaterületet, nyissa meg a munkaterületet az Azure Portalon, és távolítsa el az erőforrás zárolásait.
* Ha a fenti megoldások nem oldják meg a problémát, kövesse [a Megoldás frissítése](../automation-solution-vm-management.md#update-the-solution) a Start/Stop megoldás újbóli üzembe helyezéséhez című útmutató utasításait.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Eset: Az összes virtuális gép nem indul el vagy nem áll le

### <a name="issue"></a>Probléma

Konfigurálta a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során, de nem indul el vagy nem állítja le az összes virtuális gépet.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok okozhatják:

1. Az ütemezés nincs megfelelően konfigurálva.
2. Előfordulhat, hogy a Futtatás másként fiók nincs megfelelően konfigurálva.
3. Előfordulhat, hogy egy runbook hibákba ütközik.
4. Előfordulhat, hogy a virtuális gépek ki lettek zárva.

### <a name="resolution"></a>Megoldás:

A probléma lehetséges megoldásait az alábbi listában találja:

* Ellenőrizze, hogy megfelelően konfigurálta-e a virtuális gépek indítási/leállítási ütemezését munkaidőn kívüli megoldás során. Az ütemezés konfigurálásáról az [Ütemezések](../automation-schedules.md) című cikkben olvashat.

* Ellenőrizze a [feladatstreamek,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) hogy vizsgálja meg a hibákat. Keressen feladatokat az alábbi runbookok egyikéből:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Ellenőrizze, hogy a [Futtatás másként fiók](../manage-runas-account.md) rendelkezik-e a megfelelő engedélyekkel az elindítani vagy leállítani kívánt virtuális gépekhez. Ha tudni szeretné, hogyan ellenőrizheti egy erőforrás engedélyeit, olvassa el a [Rövid útmutató: Az Azure Portalon keresztül egy felhasználóhoz rendelt szerepkörök megtekintése című témakört.](../../role-based-access-control/check-access.md) Meg kell adnia az alkalmazásazonosítót a Futtatás másként fiók által használt egyszerű szolgáltatáshoz. Ezt az értéket az Automation-fiók az Azure Portalon, válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások**csoportban, és kattintson a megfelelő Futtatás másként fiókra.

* A virtuális gépek nem indítható el vagy állíthatók le, ha kifejezetten ki vannak zárva. A kizárt virtuális gépek `External_ExcludeVMNames` az Automation-fiókban lévő változóban vannak beállítva, amelyre a megoldás telepítve van. A következő példa bemutatja, hogyan lehet lekérdezni ezt az értéket a PowerShell segítségével.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Forgatókönyv: Néhány virtuális gépem nem indul el vagy nem áll le

### <a name="issue"></a>Probléma

Konfigurálta a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során, de nem indul el vagy nem áll le a konfigurált virtuális gépek némelyike.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok okozhatják:

1. A szekvenciaforgatókönyvben előfordulhat, hogy egy címke hiányzik vagy helytelen.
2. Előfordulhat, hogy a virtuális gép ki van zárva.
3. A Futtatás másként fiók előfordulhat, hogy nem rendelkezik elegendő engedéllyel a virtuális gép.
4. A virtuális gép lehet egy probléma, amely leállította indítása vagy leállítása.

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi listát a probléma lehetséges megoldásaiért vagy a megkereshető helyekért:

* A virtuális gépek indítási/leállítási forgatókönyvének munkaidőn kívüli megoldás során [történő](../automation-solution-vm-management.md) használatakor győződjön meg arról, hogy minden egyes elindítani vagy leállítani kívánt virtuális gép rendelkezik a megfelelő címkével. Győződjön meg arról, hogy a `sequencestart` virtuális gépek, hogy el szeretné `sequencestop` indítani a címkét, és a virtuális gépek le szeretné állítani a címkét. Mindkét címke pozitív egész értéket igényel. Az alábbi példához hasonló lekérdezéssel megkeresheti az összes virtuális gépet a címkékkel és azok értékeivel.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Előfordulhat, hogy a virtuális gépek nem indulnak el vagy nem állíthatók le, ha kifejezetten ki vannak zárva. A kizárt virtuális gépek `External_ExcludeVMNames` az Automation-fiókban lévő változóban vannak beállítva, amelyre a megoldás telepítve van. A következő példa bemutatja, hogyan lehet lekérdezni ezt az értéket a PowerShell segítségével.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* A virtuális gépek indításához és leállításához az Automation-fiók Futtatás másként fiókjának megfelelő engedélyekkel kell rendelkeznie a virtuális géphez. Ha tudni szeretné, hogyan ellenőrizheti egy erőforrás engedélyeit, olvassa el a [Rövid útmutató: Az Azure Portalon keresztül egy felhasználóhoz rendelt szerepkörök megtekintése című témakört.](../../role-based-access-control/check-access.md) Meg kell adnia az alkalmazásazonosítót a Futtatás másként fiók által használt egyszerű szolgáltatáshoz. Ezt az értéket az Automation-fiók az Azure Portalon, válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások** csoportban, és kattintson a megfelelő Futtatás másként fiókra.

* Ha a virtuális gép nek problémája van a kezdési vagy a deallocating, lehet, hogy a probléma a virtuális gép is. Például egy frissítést alkalmaznak, amikor a virtuális gép megpróbálja leállítani, egy szolgáltatás lefagy, és így tovább. Keresse meg a virtuális gép erőforrás, és ellenőrizze a **tevékenységnaplók,** hogy van-e hiba a naplókban. Előfordulhat, hogy megpróbálhat bejelentkezni a virtuális gépbe, hogy ellenőrizze, vannak-e hibák az eseménynaplókban. Ha többet szeretne megtudni a virtuális gép hibaelhárításáról, olvassa el [az Azure virtuális gépek hibaelhárítása című témakört.](../../virtual-machines/troubleshooting/index.yml)

* Ellenőrizze a [feladatstreamek,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) hogy vizsgálja meg a hibákat. A portálon nyissa meg az Automation-fiókot, és válassza a **Feladatok** lehetőséget a **Folyamatautomatizálás**területen.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Eset: Az egyéni runbook nem indul el, vagy leállítja a virtuális gépek

### <a name="issue"></a>Probléma

Egyéni runbookot készített, vagy letöltött egyet a PowerShell-galériából, és nem működik megfelelően.

### <a name="cause"></a>Ok

A kudarcnak számos oka lehet. Nyissa meg az Automation-fiókot az Azure Portalon, és válassza a **Feladatok** lehetőséget a **Folyamatautomatizálás**területen. A Feladatok lapon keresse meg a feladatokat a runbookból a feladathibák megtekintéséhez.

### <a name="resolution"></a>Megoldás:

Javasoljuk, hogy:

* Használja a [virtuális gépek indítása/leállítása munkaidőn kívüli megoldás](../automation-solution-vm-management.md) indítása és leállítása virtuális gépek az Azure Automationben. Ezt a megoldást a Microsoft készítette. 

* Ne feledje, hogy a Microsoft nem támogatja az egyéni runbookokat. Előfordulhat, hogy megoldást talál az egyéni runbookhoz a [runbook hibaelhárításából.](runbooks.md) Ellenőrizze a [feladatstreamek,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) hogy vizsgálja meg a hibákat. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Eset: A virtuális gépek nem a megfelelő sorrendben indulnak el vagy állnak le

### <a name="issue"></a>Probléma

A megoldásban konfigurált virtuális gépek nem a megfelelő sorrendben indulnak el vagy állnak le.

### <a name="cause"></a>Ok

Ezt a problémát a virtuális gépek helytelen címkézése okozza.

### <a name="resolution"></a>Megoldás:

A megoldás megfelelő konfigurálásához tegye a következő lépéseket.

1. Győződjön meg arról, hogy `sequencestart` az `sequencestop` összes elindítandó vagy leállítandó virtuális gép nek van egy vagy címke, a helyzettől függően. Ezeknek a címkéknek pozitív egész számra van szükségük értékként. A virtuális gépek feldolgozása növekvő sorrendben van feldolgozva ezen az értéken alapulva.
2. Győződjön meg arról, hogy a virtuális gépek `External_Start_ResourceGroupNames` elindításához vagy leállításához szánt erőforráscsoportok a helyzettől függően a vagy `External_Stop_ResourceGroupNames` változókban vannak.
3. Tesztelje a módosításokat `SequencedStartStop_Parent` a runbook végrehajtásával a `WHATIF` paraméterek True értékre állítva a módosítások előnézetének megtekintéséhez.
4. A virtuális gépek [indítása/leállítása a](../automation-solution-vm-management.md)virtuális gépek sorrendben történő indításáról és leállításáról további tudnivalókat.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Eset: A virtuális gépek indítása/leállítása munkaidőn kívül 403-as tiltott hibával sikertelen

### <a name="issue"></a>Probléma

Olyan feladatokat talál, `403 forbidden` amelyek a virtuális gépek indítási/leállítási hibaával sikertelenek voltak a munkaidőn kívüli megoldás runbookok során.

### <a name="cause"></a>Ok

Ezt a problémát okozhatja egy nem megfelelően konfigurált vagy lejárt Futtatás másként fiók. Ez is lehet, mert a nem megfelelő engedélyeket a virtuális gép erőforrásait a Futtatás másként fiók.

### <a name="resolution"></a>Megoldás:

A Futtatás másként fiók megfelelő konfigurálásának ellenőrzéséhez nyissa meg az Automation-fiókot az Azure Portalon, és válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások**csoportban. Ha a Futtatás másként fiók helytelenül van konfigurálva vagy lejárt, az állapot a feltételt jeleníti meg.

Ha a Futtatás másként fiók helytelenül van konfigurálva, törölje és hozza létre újra a Futtatás másként fiókot. Lásd: [Azure Automation Futtatás másként fiókok kezelése.](../manage-runas-account.md)

Ha a tanúsítvány lejárt a Futtatás másként fiókhoz, olvassa el az [Önaláírt tanúsítvány megújításának](../manage-runas-account.md#cert-renewal) lépéseit a tanúsítvány megújításához.

Ha hiányoznak az engedélyek, olvassa [el a Rövid útmutató: Az Azure Portalon egy felhasználóhoz rendelt szerepkörök megtekintése című témakört.](../../role-based-access-control/check-access.md) Meg kell adnia az alkalmazásazonosítót a Futtatás másként fiók által használt egyszerű szolgáltatáshoz. Ezt az értéket az Automation-fiók az Azure Portalon, válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások**csoportban, és kattintson a megfelelő Futtatás másként fiókra.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Eset: A probléma nem szerepel a fenti felsorolásban

### <a name="issue"></a>Probléma

Problémát vagy váratlan eredményt tapasztal, ha a virtuális gépek indítása/leállítása olyan munkaidőn kívüli megoldás használata, amely nem szerepel ezen az oldalon.

### <a name="cause"></a>Ok

Sokszor hibákat okozhat a megoldás régi és elavult verziójának használata.

> [!NOTE]
> A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás tesztelése megtörtént az Automation-fiókba importált Azure-modulokkal a megoldás üzembe helyezésekor. A megoldás jelenleg nem működik az Azure-modul újabb verzióival. Ez csak az Automation-fiók, amely a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás futtatásához használt. Továbbra is használhatja az Azure-modul újabb verzióit a többi Automation-fiókban, ahogyan az [Azure PowerShell-modulok frissítése](../automation-update-azure-modules.md) az Azure Automationben című részben leírtak szerint

### <a name="resolution"></a>Megoldás:

Sok hiba megoldásához ajánlott eltávolítani és [frissíteni a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás során.](../automation-solution-vm-management.md#update-the-solution) Emellett ellenőrizheti a [feladatstreameket,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) hogy keresse meg a hibákat. 

## <a name="next-steps"></a>További lépések

Ha a fenti probléma nem jelenik meg, vagy nem tudja megoldani a problémát, próbálkozzon az alábbi csatornák egyikével további támogatásért:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**