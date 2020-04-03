---
title: Virtuális gépek indítása és leállítása – Azure Automation –
description: Ez a cikk a virtuális gépek indítása és leállítása az Azure Automationben hibaelhárításával kapcsolatos információkat tartalmazza.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 73a9680cc570179c47b527a4844488da69193cb3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586101"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>A virtuális gépek indítása/leállítása munkaidőn kívüli megoldás sal kapcsolatos hibák elhárítása

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Eset: A virtuális gép indítási/leállítási megoldása nem megfelelően telepíthető

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

1. Már van egy automation-fiók ugyanazzal a névvel a kijelölt régióban.
2. Olyan szabályzat van érvényben, amely nem engedélyezi a virtuális gépek indítási/leállítási megoldásának telepítését.
3. A `Microsoft.OperationsManagement` `Microsoft.Insights`, `Microsoft.Automation` vagy erőforrástípusok nincsenek regisztrálva.
4. A Log Analytics-munkaterületen van egy zár rajta.
5. Az AzureRM-modulok elavult verziójával vagy a Start/Stop megoldással rendelkezik.

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi listát a probléma lehetséges megoldásaiért vagy a megkereshető helyekért:

1. Az automation-fiókoknak egyedinek kell lenniük egy Azure-régióban, még akkor is, ha különböző erőforráscsoportokban vannak. Ellenőrizze a meglévő Automation-fiókok a célrégióban.
2. Egy meglévő szabályzat megakadályozza, hogy egy erőforrás, amely szükséges a virtuális gép indítása/leállítása megoldás üzembe helyezéséhez. Nyissa meg a szabályzat-hozzárendeléseket az Azure Portalon, és ellenőrizze, hogy rendelkezik-e olyan házirend-hozzárendeléssel, amely nem engedélyezi az erőforrás üzembe helyezését. Erről a [RequestDisallowedByPolicy című témakörben](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)olvashat bővebben.
3. A virtuális gép indítási/leállítási megoldásának üzembe helyezéséhez az előfizetést regisztrálni kell a következő Azure-erőforrás-névterekbe:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Tekintse [meg az erőforrás-szolgáltató regisztrációjának hibáinak kijavítása című témakört,](../../azure-resource-manager/templates/error-register-resource-provider.md) ha többet szeretne megtudni a szolgáltatók regisztrálásasorán előforduló hibákról.
4. Ha zárolta a Log Analytics-munkaterületet, nyissa meg a munkaterületet az Azure Portalon, és távolítsa el az erőforrás zárolásait.
5. Ha a fenti megoldások nem oldják meg a problémát, kövesse [a Megoldás frissítése](../automation-solution-vm-management.md#update-the-solution) a Start/Stop megoldás újbóli üzembe helyezéséhez című útmutató utasításait.

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>Eset: Az összes virtuális gép nem indul el/nem

### <a name="issue"></a>Probléma

Konfigurálta a virtuális gép indítási/leállítási megoldását, de nem indítja el vagy állítja le az összes konfigurált virtuális gépet.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok okozhatják:

1. Az ütemezés nincs megfelelően konfigurálva
2. Lehet, hogy a RunAs-fiók nincs megfelelően konfigurálva
3. Előfordulhat, hogy egy runbook hibákba ütközhet
4. Előfordulhat, hogy a virtuális gépeket kizárták

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi listát a probléma lehetséges megoldásaiért vagy a megkereshető helyekért:

* Ellenőrizze, hogy megfelelően konfigurálta-e a virtuális gép indítási/leállítási megoldásának ütemezését. Az ütemezés konfigurálásáról az [Ütemezések](../automation-schedules.md) című cikkben olvashat.

* Ellenőrizze a [feladatstreamek,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) hogy vizsgálja meg a hibákat. A portálon nyissa meg az Automation-fiókot, és válassza a **Feladatok** lehetőséget a **Folyamatautomatizálás**csoportban. A **Feladatok** lapon keresse meg a feladatokat az alábbi runbookok egyikéből:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Ellenőrizze, hogy a [RunAs-fiók](../manage-runas-account.md) megfelelő engedélyekkel rendelkezik-e az elindítani vagy leállítani kívánt virtuális gépekhez. Ha tudni szeretné, hogyan ellenőrizheti egy erőforrás engedélyeit, olvassa el a [Rövid útmutató: Az Azure Portalon keresztül egy felhasználóhoz rendelt szerepkörök megtekintése című témakört.](../../role-based-access-control/check-access.md) Meg kell adnia az alkalmazásazonosítót a Futtatás partnerként által használt egyszerű szolgáltatáshoz. Ezt az értéket az Automation-fiók az Azure Portalon, válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások** csoportban, és kattintson a megfelelő Futtatás fiókként elemre.

* A virtuális gépek nem indítható el vagy állíthatók le, ha kifejezetten ki vannak zárva. Kizárt virtuális gépek az **Automation-fiók External_ExcludeVMNames** változójában, amelybe a megoldás telepítve van. A következő példa bemutatja, hogyan lehet lekérdezni ezt az értéket a PowerShell segítségével.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Forgatókönyv: Néhány virtuális gépem nem indul el vagy nem áll le

### <a name="issue"></a>Probléma

Konfigurálta a virtuális gép indítási és leállítási megoldását, de nem indítja el vagy állítja le a konfigurált virtuális gépek egy részét.

### <a name="cause"></a>Ok

Ezt a hibát a következő okok okozhatják:

1. Ha a szekvenciaforgatókönyvet használja, előfordulhat, hogy egy címke hiányzik vagy helytelen
2. A virtuális gép kizárható
3. Előfordulhat, hogy a RunAs-fiók nem rendelkezik elegendő engedéllyel a virtuális géphez
4. A virtuális gép lehet valami, ami megakadályozta, hogy elindul, vagy megállás

### <a name="resolution"></a>Megoldás:

Tekintse át az alábbi listát a probléma lehetséges megoldásaiért vagy a megkereshető helyekért:

* A virtuális gép indítási/leállítási forgatókönyvének munkaidőn kívüli megoldás során [történő](../automation-solution-vm-management.md) használatakor győződjön meg arról, hogy minden egyes elindítani vagy leállítani kívánt virtuális gép rendelkezik a megfelelő címkével. Győződjön meg arról, hogy a `sequencestart` virtuális gépek, hogy el szeretné `sequencestop` indítani a címkét, és a virtuális gépek le szeretné állítani a címkét. Mindkét címke pozitív egész értéket igényel. Az alábbi példához hasonló lekérdezéssel megkeresheti az összes virtuális gépet a címkékkel és azok értékeivel.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* A virtuális gépek nem indítható el vagy állíthatók le, ha kifejezetten ki vannak zárva. Kizárt virtuális gépek az **Automation-fiók External_ExcludeVMNames** változójában, amelybe a megoldás telepítve van. A következő példa bemutatja, hogyan lehet lekérdezni ezt az értéket a PowerShell segítségével.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Virtuális gépek indításához és leállításához az Automation-fiók RunAs-fiókjának megfelelő engedélyekkel kell rendelkeznie a virtuális géphez. Ha tudni szeretné, hogyan ellenőrizheti egy erőforrás engedélyeit, olvassa el a [Rövid útmutató: Az Azure Portalon keresztül egy felhasználóhoz rendelt szerepkörök megtekintése című témakört.](../../role-based-access-control/check-access.md) Meg kell adnia az alkalmazásazonosítót a Futtatás partnerként által használt egyszerű szolgáltatáshoz. Ezt az értéket az Automation-fiók az Azure Portalon, válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások** csoportban, és kattintson a megfelelő Futtatás fiókként elemre.

* Ha a virtuális gép nek problémája van a kezdési vagy az ügyletelosztással, ezt a viselkedést maga a virtuális gép problémája okozhatja. Néhány példa vagy lehetséges probléma, egy frissítés van alkalmazva, amikor megpróbálja shutdown, a szolgáltatás lefagy, és így tovább). Keresse meg a virtuális gép erőforrás, és ellenőrizze a **tevékenységnaplók,** hogy van-e hiba a naplókban. Megpróbálhat bejelentkezni a virtuális gépbe, hogy ellenőrizze, vannak-e hibák az eseménynaplókban. Ha többet szeretne megtudni a virtuális gép hibaelhárításáról, olvassa el [az Azure virtuális gépek hibaelhárítása című témakört.](../../virtual-machines/troubleshooting/index.yml)

* Ellenőrizze a [feladatstreamek,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) hogy vizsgálja meg a hibákat. A portálon nyissa meg az Automation-fiókot, és válassza a **Feladatok** lehetőséget a **Folyamatautomatizálás**csoportban.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Eset: Az egyéni runbook nem indul el, vagy leállítja a virtuális gépek

### <a name="issue"></a>Probléma

Egyéni runbookot készített, vagy letöltött egyet a PowerShell-galériából, és nem működik megfelelően.

### <a name="cause"></a>Ok

A kudarc oka lehet egy a sok közül. Nyissa meg az Automation-fiókot az Azure Portalon, és válassza a **Feladatok** lehetőséget a **Folyamatautomatizálás**területen. A **Feladatok** lapon keresse meg a feladatokat a runbookból a feladathibák megtekintéséhez.

### <a name="resolution"></a>Megoldás:

Javasoljuk, hogy a [virtuális gépek indítása/leállítása munkaidőn kívüli megoldás](../automation-solution-vm-management.md) használatával indítsa el és állítsa le a virtuális gépeket az Azure Automationben. Ezt a megoldást a Microsoft készítette. Az egyéni runbookokat a Microsoft nem támogatja. Előfordulhat, hogy megoldást talál az egyéni runbookhoz a [runbook hibaelhárítási](runbooks.md) cikkének felkeresésével. Ez a cikk általános útmutatást és hibaelhárítást tartalmaz minden típusú runbookokhoz. Ellenőrizze a [feladatstreamek,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) hogy vizsgálja meg a hibákat. A portálon nyissa meg az Automation-fiókot, és válassza a **Feladatok** lehetőséget a **Folyamatautomatizálás**csoportban.

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Eset: A virtuális gépek nem a megfelelő sorrendben indulnak el vagy állnak le

### <a name="issue"></a>Probléma

A megoldásban konfigurált virtuális gépek nem a megfelelő sorrendben indulnak el vagy állnak le.

### <a name="cause"></a>Ok

Ezt a virtuális gépek helytelen címkézése okozza.

### <a name="resolution"></a>Megoldás:

A megoldás megfelelő konfigurálásához tegye a következő lépéseket.

1. Győződjön meg arról, hogy `sequencestart` az `sequencestop` összes elindítandó vagy leállítandó virtuális gép nek van egy vagy címke, a helyzettől függően. Ezeknek a címkéknek pozitív egész számra van szükségük értékként. A virtuális gépek feldolgozása növekvő sorrendben van feldolgozva ezen az értéken alapulva.
2. Győződjön meg arról, hogy a virtuális gépek `External_Start_ResourceGroupNames` elindításához vagy leállításához szánt erőforráscsoportok a helyzettől függően a vagy `External_Stop_ResourceGroupNames` változókban vannak.
3. Tesztelje a módosításokat `SequencedStartStop_Parent` a runbook végrehajtásával a WHATIF paraméter true értékre állítva a módosítások előnézetének megtekintéséhez.

A virtuális gépek [indítása/leállítása című témakörben](../automation-solution-vm-management.md)részletesebb és további útmutatást talál arról, hogyan használhatja a megoldást a virtuális gépek egymás után történő indítására és leállítására.

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>Eset: A virtuális gép indítása/leállítása 403-as tiltott állapottal sikertelen

### <a name="issue"></a>Probléma

Olyan feladatokat talál, `403 forbidden` amelyek a virtuális gépek indítási/leállítási hibaával sikertelenek voltak a munkaidőn kívüli megoldás runbookok során.

### <a name="cause"></a>Ok

Ezt a problémát okozhatja egy nem megfelelően konfigurált vagy lejárt Futtatás mint fiók. Ez is lehet, mert a virtuális gép erőforrásaihoz az Automation-fiókok futtatása fiókként nem megfelelő engedélyekkel rendelkezik.

### <a name="resolution"></a>Megoldás:

A Futtatás másként fiók megfelelő konfigurálásához nyissa meg az Automation-fiókot az Azure Portalon, és válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások csoportban.** Itt láthatja a futtatás állapotát fiókként, ha a Futtatás másként fiók helytelenül van konfigurálva vagy lejárt, az állapot ezt fogja megjeleníteni.

Ha a Futtatás másként fiók helytelenül van konfigurálva, törölje, majd hozza létre újra a Futtatás másként fiókot. Lásd: [Azure Automation Futtatás másként fiókok kezelése.](../manage-runas-account.md)

Ha a tanúsítvány lejárt a Futtatás másként fiókesetében, a tanúsítvány megújításához kövesse az [Önaláírt tanúsítvány megújítása](../manage-runas-account.md#cert-renewal) című témakörben leírt lépéseket.

A problémát a hiányzó engedélyek okozhatták. Ha tudni szeretné, hogyan ellenőrizheti egy erőforrás engedélyeit, olvassa el a [Rövid útmutató: Az Azure Portalon keresztül egy felhasználóhoz rendelt szerepkörök megtekintése című témakört.](../../role-based-access-control/check-access.md) Meg kell adnia az alkalmazásazonosítót a Futtatás partnerként által használt egyszerű szolgáltatáshoz. Ezt az értéket az Automation-fiók az Azure Portalon, válassza a **Futtatás fiókként** lehetőséget a **Fiókbeállítások** csoportban, és kattintson a megfelelő Futtatás fiókként elemre.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Eset: A probléma nem szerepel a fenti felsorolásban

### <a name="issue"></a>Probléma

Problémát vagy váratlan eredményt tapasztal, amikor a virtuális gépek indítása/leállítása olyan munkaidőn kívüli megoldás használata, amely nem szerepel ezen az oldalon.

### <a name="cause"></a>Ok

Sokszor hibákat okozhat a megoldás régi és elavult verziójának használata.

> [!NOTE]
> A kezdő/leállító virtuális gépek munkaidőn kívüli megoldás tesztelése megtörtént az Automation-fiókba importált Azure-modulokkal a megoldás üzembe helyezésekor. A megoldás jelenleg nem működik az Azure-modul újabb verzióival. Ez csak az Automation-fiók, amely a virtuális gépek indítása/leállítása munkaidőn kívüli megoldás futtatásához használt. Továbbra is használhatja az Azure-modul újabb verzióit a többi Automation-fiókban, ahogyan az [Azure PowerShell-modulok frissítése](../automation-update-azure-modules.md) az Azure Automationben című részben ismerteti.

### <a name="resolution"></a>Megoldás:

Sok hiba megoldásához ajánlott eltávolítani és frissíteni a megoldást. A megoldás frissítésének módjáról [a Virtuális gépek indítása/leállítása munkaidőn kívüli megoldás című témakörben](../automation-solution-vm-management.md#update-the-solution)olvashat. Emellett ellenőrizheti a [feladatstreameket,](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) hogy keresse meg a hibákat. A portálon nyissa meg az Automation-fiókot, és válassza a **Feladatok** lehetőséget a **Folyamatautomatizálás**csoportban.

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba – a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Ha további segítségre van szüksége, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
