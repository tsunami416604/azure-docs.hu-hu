---
title: Hibák elhárítása Update Management
description: Ismerje meg, hogyan lehet elhárítani a Update Management kapcsolatos problémákat
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 48d2463eee2caeaae36118bf736d00eed84c897a
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186225"
---
# <a name="troubleshooting-issues-with-update-management"></a>Hibák elhárítása Update Management

Ez a cikk a Update Management használatakor esetlegesen futtatott problémák megoldását ismerteti.

A hibrid feldolgozói ügynöknek van egy ügynök-hibakeresője, amely meghatározza a mögöttes problémát. A hibaelhárítással kapcsolatos további tudnivalókért tekintse meg a [frissítési ügynökkel kapcsolatos problémák elhárítása](update-agent-issues.md)című témakört. Az összes többi probléma esetén tekintse meg az alábbi részletes információkat a lehetséges problémákkal kapcsolatban.

## <a name="general"></a>Általános

### <a name="rp-register"></a>Forgatókönyv Nem lehet regisztrálni az Automation erőforrás-szolgáltatót az előfizetésekhez

#### <a name="issue"></a>Probléma

Az Automation-fiókban található megoldások használatakor a következő hibaüzenet jelenhet meg.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

#### <a name="cause"></a>Ok

Az Automation erőforrás-szolgáltató nincs regisztrálva az előfizetésben.

#### <a name="resolution"></a>Megoldás:

Az Automation erőforrás-szolgáltatókat az alábbi lépések végrehajtásával regisztrálhatja a Azure Portalban:

1. Az alsó Azure-szolgáltatás listában kattintson az **összes szolgáltatás** lehetőségre , majd válassza az előfizetések lehetőséget az _általános_ szolgáltatás csoportban.
2. Válassza ki előfizetését.
3. Kattintson az **erőforrás-szolgáltatók** elemre a _Beállítások_területen.
4. Az erőforrás-szolgáltatók listájából ellenőrizze, hogy a **Microsoft. Automation** erőforrás-szolgáltató regisztrálva van-e.
5. Ha a szolgáltató nem szerepel a listáján, regisztrálja a **Microsoft. Automation** -szolgáltatót [ ](/azure/azure-resource-manager/resource-manager-register-provider-errors)a következő szakaszban ismertetett lépésekkel:.

### <a name="mw-exceeded"></a>Forgatókönyv A frissítés kezelése nem sikerült – a hiba MaintenanceWindowExceeded

#### <a name="issue"></a>Probléma

A frissítések alapértelmezett karbantartási időszaka 120 perc. A karbantartási időszakot legfeljebb hat (6) órára növelheti, vagy 360 percet is igénybe vehet.

#### <a name="resolution"></a>Megoldás:

Szerkessze a sikertelen ütemezett frissítések telepítését, és növelje a karbantartási időszakot.

További információ a karbantartási időszakokról: [Install Updates (frissítések telepítése](../automation-update-management.md#install-updates)).

### <a name="components-enabled-not-working"></a>Forgatókönyv A "Update Management" megoldás összetevői engedélyezve lettek, és most már konfigurálva van a virtuális gép.

#### <a name="issue"></a>Probléma

A következő üzenetet a virtuális gépen 15 perccel a bevezetést követően továbbra is megtekintheti:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Ok

Ezt a hibát a következő okok okozhatják:

1. A rendszer blokkolja az Automation-fiókkal való kommunikációt.
2. Előfordulhat, hogy a bevezetésben lévő virtuális gép olyan klónozott gépről származott, amely nem volt telepítve a Microsoft monitoring Agent Sysprep használatával létrehozott.

#### <a name="resolution"></a>Megoldás:

1. Látogasson el a [Network Planning](../automation-hybrid-runbook-worker.md#network-planning) webhelyre, ahol megtudhatja, mely címeket és portokat kell engedélyezni a Update Management működéséhez.
2. Klónozott rendszerkép használata esetén:
   1. A log Analytics munkaterületen távolítsa el a virtuális gépet a hatókör-konfiguráció `MicrosoftDefaultScopeConfig-Updates` mentett keresésével, ha az megjelenik. A mentett keresések a munkaterület **általános** területén találhatók.
   2. Futtassa a `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` parancsot.
   3. Futtassa `Restart-Service HealthService` újra a `HealthService`parancsot. Ezzel újra létrehozza a kulcsot, és létrehoz egy új UUID-t.
   4. Ha ez nem működik, a Sysprep először a rendszerképet telepíti, és a tény után telepíti az MMA-ügynököt.

### <a name="multi-tenant"></a>Forgatókönyv Egy másik Azure-bérlőben lévő gépekhez tartozó frissítési központi telepítés létrehozásakor a rendszer csatolt előfizetési hibát kap.

#### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor egy másik Azure-bérlőben lévő gépekhez próbál frissíteni központi telepítést:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, amikor olyan frissítési központi telepítést hoz létre, amely egy másik bérlőn található Azure-beli virtuális gépekkel rendelkezik, és egy frissítés üzemel.

#### <a name="resolution"></a>Megoldás:

A következő megkerülő megoldást kell használnia az ütemezett beszerzéséhez. A [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmag `-ForUpdate` használatával létrehozhat egy ütemtervet, és használhatja a [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) parancsmagot, és átadhatja a gépeket a másikban a `-NonAzureComputer` (z) paraméter bérlője. A következő példa egy példát mutat be erre:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="updates-nodeployment"></a>Forgatókönyv Frissítések telepítés nélküli telepítése

### <a name="issue"></a>Probléma

Ha Update Managementban regisztrál egy Windows rendszerű gépet, a frissítések telepítés nélkül is megjelenhetnek.

### <a name="cause"></a>Ok

Windows rendszeren a frissítések automatikusan települnek, amint elérhetők. Ez zavart okozhat, ha nem ütemezett frissítést a gépre való központi telepítéshez.

### <a name="resolution"></a>Megoldás:

A Windows beállításkulcs `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` alapértelmezett értéke a "4" – **automatikus letöltés és telepítés**.

Update Management ügyfelek esetében javasoljuk, hogy ezt a kulcsot "3" – **automatikus letöltéssel, de ne automatikus telepítéssel**állítsa be.

További információ: az [Automatikus frissítések konfigurálása](https://docs.microsoft.com/en-us/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

### <a name="nologs"></a>Forgatókönyv A gépek nem jelennek meg a portálon Update Management

#### <a name="issue"></a>Probléma

A következő helyzetekben lehet futtatni:

* A gép nem a virtuális gép Update Management nézetében **van konfigurálva**

* A gépek hiányoznak az Automation-fiók Update Management nézetéről

* Olyan gépeket tartalmaz, amelyek **nem** felelnek mega megfelelőségnek, de a szívverési adat a hibrid Runbook-feldolgozó Azure monitor naplófájljaiban jelenik meg, de nem Update Management.

#### <a name="cause"></a>Ok

Ezt okozhatja a lehetséges helyi konfigurációs problémák vagy a nem megfelelően konfigurált hatókör-konfiguráció.

Előfordulhat, hogy a hibrid Runbook-feldolgozót újra kell regisztrálni és újra kell telepíteni.

Lehet, hogy meghatározott egy kvótát a munkaterületen, amely elérte az adatok tárolását, és leállította azokat.

#### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a gép a megfelelő munkaterületre jelent jelentést. Ellenőrizze, hogy a számítógép mely munkaterületen jelent jelentést. Ennek ellenőrzésével kapcsolatos útmutatásért lásd: az [ügynök kapcsolatának ellenőrzése log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Ezt követően győződjön meg arról, hogy ez az a munkaterület, amely a Azure Automation-fiókjához van csatolva. Ennek megerősítéséhez navigáljon az Automation-fiókjához, és kattintson a **kapcsolódó erőforrások**elemre a **csatolt munkaterület** lehetőségre.

* Győződjön meg arról, hogy a gépek megjelennek a Log Analytics munkaterületen. Futtassa az alábbi lekérdezést az Log Analytics munkaterületen, amely az Automation-fiókjához van csatolva. Ha nem látja a gépet a lekérdezés eredményei között, a gép nem Szívveréses, ami azt jelenti, hogy a probléma valószínűleg helyi konfigurációval kapcsolatos. Az operációs rendszertől függően futtathatja a [Windows](update-agent-issues.md#troubleshoot-offline) -vagy [Linux](update-agent-issues-linux.md#troubleshoot-offline) -hibakeresőt, vagy újratelepítheti [az ügynököt](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)is. Ha a gép megjelenik a lekérdezés eredményei között, akkor az alábbi felsorolásban megadott hatókör-konfigurációt kell használnia.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* A hatókör-konfigurációs problémák keresése. A [hatókör-konfiguráció](../automation-onboard-solutions-from-automation-account.md#scope-configuration) meghatározza, hogy mely gépek legyenek konfigurálva a megoldáshoz. Ha a gép megjelenik a munkaterületen, de nem jelenik meg, akkor konfigurálnia kell a hatókör-konfigurációt a gépek célzásához. Ennek megismeréséhez tekintse meg [a munkaterületen található gépek](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)előkészítését ismertető témakört.

* Ha a fenti lépések nem oldják meg a problémát, kövesse a [Windows hibrid Runbook](../automation-windows-hrw-install.md) -feldolgozó telepítése a hibrid feldolgozó újratelepítéséhez vagy a Linux [hibrid Runbook-feldolgozójának üzembe helyezése](../automation-linux-hrw-install.md) című témakör lépéseit.

* A munkaterületen futtassa a következő lekérdezést. Ha úgy látja `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` , hogy a munkaterületen olyan kvóta van meghatározva, amely el lett érve, és leállította az adatok mentését. A munkaterületen navigáljon a **használat és becsült költségek** > adatmennyiség-**kezelés** elemre, és tekintse meg a kvótát, vagy távolítsa el a kvótát.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Ha problémák merülnek fel a megoldás virtuális gépen való beléptetése során, tekintse meg az **Operations Manager** eseménynaplót a helyi gépen az **alkalmazás és szolgáltatások naplóban** , a **4502** -es azonosítójú eseménnyel és az esemény üzenettel a **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**tartalmazza.

A következő szakasz a konkrét hibaüzeneteket és az egyes lehetséges megoldásait ismerteti. Más bevezetési problémák esetén lásd: [megoldás](onboarding.md)bevezetésének hibaelhárítása.

### <a name="machine-already-registered"></a>Forgatókönyv A gép már regisztrálva van egy másik fiókban

#### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Ok

A gép már be van vezetve a Update Management egy másik munkaterületére.

#### <a name="resolution"></a>Megoldás:

Végezze el a régi összetevők tisztítását a gépen a [hibrid runbook törlésével](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , majd próbálkozzon újra.

### <a name="machine-unable-to-communicate"></a>Forgatókönyv A gép nem tud kommunikálni a szolgáltatással

#### <a name="issue"></a>Probléma

A következő hibaüzenetek egyike jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Ok

Lehetséges, hogy a proxy, az átjáró vagy a tűzfal blokkolja a hálózati kommunikációt.

#### <a name="resolution"></a>Megoldás:

Tekintse át a hálózatkezelést, és győződjön meg arról, hogy a megfelelő portok és címek engedélyezettek. Tekintse meg a [hálózati követelmények](../automation-hybrid-runbook-worker.md#network-planning)című témakört, amely a Update Management és a hibrid Runbook-feldolgozók által igényelt portok és címek listáját tartalmazza.

### <a name="unable-to-create-selfsigned-cert"></a>Forgatókönyv Nem hozható létre önaláírt tanúsítvány

#### <a name="issue"></a>Probléma

A következő hibaüzenetek egyike jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Ok

A hibrid Runbook Worker nem tudott önaláírt tanúsítványt előállítani

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a rendszerfiók rendelkezik-e olvasási hozzáféréssel a **C:\ProgramData\Microsoft\Crypto\RSA** mappához, és próbálkozzon újra.

### <a name="failed-to-start"></a>Forgatókönyv Egy gép azt mutatja, hogy a frissítés központi telepítése nem indult el

#### <a name="issue"></a>Probléma

Egy gép állapota **nem indult el** a gépen. Amikor megtekinti a gép konkrét részleteit, a következő hibaüzenet jelenik meg:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Ok

Ez a hiba az alábbi okok egyike miatt fordulhat elő:

* A gép már nem létezik.
* A gép ki van kapcsolva, és nem érhető el.
* A gép hálózati kapcsolattal rendelkezik, és a gép hibrid feldolgozója nem érhető el.
* A SourceComputerId módosított Microsoft monitoring Agent frissítése megtörtént
* Előfordulhat, hogy a frissítési menet szabályozása megtörtént, ha az Automation-fiók 2 000 egyidejű feladataira vonatkozó korlátot adott meg. Az egyes központi telepítések feladatoknak minősülnek, és a frissítések központi telepítésének minden gépe feladatainak számítanak. Az Automation-fiókban jelenleg futó egyéb automatizálási feladatok vagy frissítési üzembe helyezések száma az egyidejű feladatokra vonatkozó korlát felé mutat.

#### <a name="resolution"></a>Megoldás:

Ha alkalmazható, [dinamikus csoportokat](../automation-update-management.md#using-dynamic-groups) használ a frissítés központi telepítéséhez.

* Ellenőrizze, hogy a gép továbbra is létezik-e, és elérhető-e. Ha nem létezik, szerkessze az üzemelő példányt, és távolítsa el a gépet.
* Tekintse meg a [hálózat](../automation-update-management.md#ports) megtervezése című szakaszt a Update Managementához szükséges portok és címek listájának megtekintéséhez, és ellenőrizze, hogy a gép megfelel-e a követelményeknek.
* Futtassa a következő lekérdezést a log Analyticsban azon gépek megkereséséhez, `SourceComputerId` amelyek módosultak a környezetben. Keressen olyan számítógépeket, amelyek azonos `Computer` értékkel rendelkeznek, `SourceComputerId` de eltérő értékkel rendelkeznek. Miután megtalálta az érintett gépeket, szerkesztenie kell a gépeket megcélzó frissítési központi telepítéseket, és el kell távolítania és újra `SourceComputerId` hozzá kell adnia a gépeket, hogy az tükrözze a megfelelő értéket.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Forgatókönyv A gép nem vizsgált és HResult kivételt mutat

#### <a name="issue"></a>Probléma

Olyan gépeket tartalmaz, amelyek **nem** felelnek mega megfelelőségnek, és megjelenik egy kivételt jelző üzenet.

#### <a name="cause"></a>Ok

A Windows Update vagy a WSUS nincs megfelelően konfigurálva a gépen. Update Management az Windows Update vagy a WSUS szolgáltatásnak a szükséges frissítések biztosítására, a javítás állapotára és az üzembe helyezett javítások eredményeire támaszkodik. Ezen információk nélkül Update Management nem tud megfelelően jelentést készíteni a szükséges vagy telepített javításokról.

#### <a name="resolution"></a>Megoldás:

Kattintson duplán a piros színnel megjelenő kivételre, és tekintse meg a teljes kivételt jelző üzenetet. Tekintse át a következő táblázatot a lehetséges megoldásokról vagy műveletekről:

|Kivétel  |Megoldás vagy művelet  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | A kivétel okának meghatározásához keresse meg a megfelelő hibakódot a [Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) -hibakódok listájában.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ezek a hibák hálózati kapcsolati problémák. Győződjön meg arról, hogy a számítógépe megfelelő hálózati kapcsolattal rendelkezik a Update Managementhoz. A szükséges portok és címek listáját a [hálózati tervezés](../automation-update-management.md#ports) című szakaszban találja.        |
|`0x8024001E`| A frissítési művelet nem fejeződött be, mert a szolgáltatás vagy a rendszer leállítása megtörtént.|
|`0x8024002E`| Windows Update szolgáltatás le van tiltva.|
|`0x8024402C`     | Ha WSUS-kiszolgálót használ, győződjön meg arról `WUServer` , hogy a beállításjegyzék-kulcs `WUStatusServer` `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` és a beállításkulcs alatt a megfelelő WSUS-kiszolgáló található.        |
|`0x80072EE2`|Hálózati kapcsolati probléma vagy a konfigurált WSUS-kiszolgálóval való kommunikációra adott probléma. Ellenőrizze a WSUS beállításait, és győződjön meg arról, hogy az az ügyféltől elérhető.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Győződjön meg arról, hogy a Windows Update szolgáltatás (wuauserv) fut, és nincs letiltva.        |
|Bármely más általános kivétel     | Végezzen keresést az interneten a lehetséges megoldásokhoz, és működjön együtt a helyi informatikai támogatással.         |

A `windowsupdate.log` segítségével megtekintheti a lehetséges okokat is. A napló beolvasásával kapcsolatos további információkért lásd: [a windowsupdate. log fájl olvasása](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Emellett letöltheti és futtathatja a [Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) -hibakeresőt annak vizsgálatához, hogy van-e probléma a gépen a Windows Update.

> [!NOTE]
> A [Windows Update-hibakereső](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) azokat a Windows-ügyfeleket is támogatja, amelyeken a Windows Server is működik.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Forgatókönyv: A frissítés futtatása nem indul el

#### <a name="issue"></a>Probléma

Egy frissítés futtatása nem indul el Linux rendszerű gépen.

#### <a name="cause"></a>Ok

A Linux Hybrid Worker nem kifogástalan állapotú.

#### <a name="resolution"></a>Megoldás:

Készítsen másolatot a következő naplófájlról, és őrizze meg a hibaelhárítási célokat:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Forgatókönyv: A frissítési Futtatás elindul, de hibákat észlel

#### <a name="issue"></a>Probléma

A frissítési kísérlet elindul, de a Futtatás közben hibákat észlel.

#### <a name="cause"></a>Ok

Lehetséges okok:

* A Package Manager sérült
* Bizonyos csomagok zavarhatják a felhőalapú javítást
* Egyéb okok

#### <a name="resolution"></a>Megoldás:

Ha egy frissítés futtatása után hibák történnek a Linux rendszeren való sikeres indítás után, akkor ellenőrizze a Futtatás során az érintett gépről érkező feladatok kimenetét. Előfordulhat, hogy bizonyos hibaüzeneteket talál a számítógép csomagkezelő, melyen kutatást végezhet, és műveleteket végezhet. Update Management megköveteli, hogy a Package Manager Kifogástalan állapotba kerüljön a sikeres frissítések központi telepítéséhez.

Bizonyos esetekben a csomagok frissítései zavarhatják Update Management a frissítések befejezésének megakadályozását. Ha ezt látja, akkor ki kell zárnia ezeket a csomagokat a jövőbeli frissítési futtatásokból, vagy manuálisan kell telepítenie őket.

Ha nem tudja feloldani a javítási problémát, készítsen másolatot a következő naplófájlról, és őrizze meg a következő frissítés központi telepítésének megkezdése **előtt** hibaelhárítási célból:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-are-not-installed"></a>Nincsenek telepítve a javítások

### <a name="machines-do-not-install-updates"></a>A gépek nem telepítik a frissítéseket

* Próbálja meg közvetlenül a gépen lefuttatni a frissítéseket. Ha a gép nem frissül, akkor tekintse át a [lehetséges hibák listáját a hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Ha a frissítések helyszíni futtatással működnek, akkor távolítsa el és telepítse újra az ügynököt a gépen a [VM Update Managementből történő eltávolításával](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management) foglalkozó témakör alapján.

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>Tudom, hogy vannak elérhető frissítések, de nem jelennek meg a gépeken

* Ez gyakran előfordul, ha a gépek úgy vannak konfigurálva, hogy a WSUS/SCCM-ből kérjék le a frissítéseket, de a WSUS/SCCM még nem hagyta azokat jóvá.
* Azt, hogy a gépek a WSUS/SCCM-re vannak-e konfigurálva, úgy ellenőrizheti, hogy [kereszthivatkozza a „UseWUServer” beállításkulcsot a jelen dokumentum „Automatikus frissítések konfigurálása a beállításjegyzék szerkesztésével” szakaszában](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s) található beállításkulcsokkal.

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>**A frissítések telepítettként jelennek meg, de nem találom őket a számítógépen**

* A frissítéseket gyakran felülírják más frissítések. További információért tekintse meg [a felülírt frissítésekkel foglalkozó szakaszt a Windows Update hibaelhárítási útmutatójában](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="installing-updates-by-classification-on-linux"></a>**Frissítések telepítése besorolás szerint Linuxon**

* Ha Linuxon dolgozik, a frissítések besorolás szerinti („kritikus és biztonsági frissítések”) üzembe helyezésekor fontos kikötéseket kell figyelembe venni, különösen CentOS használata esetén. Ezeket [a korlátozásokat megtalálja az Update Management áttekintő oldalán](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently--missing"></a>**A KB2267602 folyamatosan hiányzik**

* A KB2267602 a [Windows Defender definíciófrissítése](https://www.microsoft.com/wdsi/definitions). Naponta frissül.

## <a name="other"></a>Forgatókönyv A probléma nem szerepel a fenti felsorolásban

### <a name="issue"></a>Probléma

Olyan problémáról van szó, amelyet a felsorolt egyéb forgatókönyvek nem oldottak meg.

### <a name="cause"></a>Ok

A helytelenül konfigurált vagy hiányzó beállításkulcsok problémákat okozhatnak a Update Management.

### <a name="resolution"></a>Megoldás:

Törölje a beállításkulcsot `HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` , és indítsa újra a **HealthService**.

A következő PowerShell-parancsokat is használhatja.

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási](https://azure.microsoft.com/support/options/) webhelyét, és válassza a **támogatás kérése**lehetőséget.
