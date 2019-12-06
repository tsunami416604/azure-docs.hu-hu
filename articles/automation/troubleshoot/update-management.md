---
title: Hibák elhárítása Update Management
description: Ismerje meg, hogyan lehet elhárítani a Update Managementével kapcsolatos problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: a42b05239ae1ddf8909e288486694bf57595b195
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849241"
---
# <a name="troubleshooting-issues-with-update-management"></a>Hibák elhárítása Update Management

Ez a cikk a Update Management használatakor felmerülhető problémák megoldásait ismerteti.

A hibrid feldolgozói ügynöknek van egy ügynök-hibakeresője, amely meghatározza a mögöttes problémát. A hibaelhárítással kapcsolatos további tudnivalókért tekintse meg a [frissítési ügynökkel kapcsolatos problémák elhárítása](update-agent-issues.md)című témakört. Az összes többi probléma esetén kövesse az alábbi hibaelhárítási útmutatót.

Ha problémák merülnek fel a megoldás virtuális gépen való beléptetése közben, tekintse meg a helyi gépen a **Operations Manager** log ( **alkalmazás és szolgáltatások naplója** ) lehetőséget az 4502-as azonosítójú eseményekkel és a **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent**azonosítójú esemény részleteivel.

A következő szakasz a konkrét hibaüzeneteket és az egyes lehetséges megoldásait ismerteti. Egyéb bevezetési problémák esetén lásd: a megoldás bevezetésének [hibaelhárítása](onboarding.md).

## <a name="nologs"></a>Forgatókönyv: a gépek nem jelennek meg a portálon Update Management

### <a name="issue"></a>Probléma

A következő tünetek jelentkeznek:

* A gép nem a virtuális gép Update Management nézetében **van konfigurálva** .

* A gépek hiányoznak a Azure Automation fiókjának Update Management nézetéről.

* Rendelkezik olyan gépekkel, amelyek nem a **megfelelőség**alapján vannak **kivizsgálva** . Azonban a Szívveréses adatAzure Monitor naplókban láthatja a hibrid Runbook-feldolgozót, de nem Update Management.

### <a name="cause"></a>Ok

Ezt a problémát a helyi konfigurációs problémák vagy a nem megfelelően konfigurált hatókör-konfiguráció okozhatja.

Előfordulhat, hogy újra kell regisztrálnia és újra kell telepítenie a hibrid Runbook-feldolgozót.

Lehet, hogy meghatározott egy kvótát a munkaterületen, amely el lett érve, és ez megakadályozza a további adattárolás megadását.

### <a name="resolution"></a>Felbontás

* Futtassa a [Windows](update-agent-issues.md#troubleshoot-offline) vagy [Linux](update-agent-issues-linux.md#troubleshoot-offline)rendszerhez készült hibakeresőt az operációs rendszertől függően.

* Győződjön meg arról, hogy a számítógép a megfelelő munkaterületre jelent jelentést. Az adott aspektus ellenőrzésével kapcsolatos útmutatásért lásd: az [ügynök kapcsolatának ellenőrzése log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Győződjön meg arról is, hogy ez a munkaterület a Azure Automation-fiókjához van csatolva. A megerősítéshez nyissa meg az Automation-fiókját, és a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület** lehetőséget.

* Győződjön meg arról, hogy a gépek megjelennek a Log Analytics munkaterületen. Futtassa az alábbi lekérdezést a Log Analytics munkaterületen, amely az Automation-fiókjához van csatolva:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Ha nem látja a gépet a lekérdezés eredményei között, azt a közelmúltban nem ellenőrizte, ami azt jelenti, hogy valószínűleg van egy helyi konfigurációs probléma, és [újra kell telepítenie az ügynököt](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Ha a gép megjelenik a lekérdezés eredményei között, ellenőriznie kell a lista következő listajeles elemében megadott hatókör-konfigurációt.

* A hatókör-konfigurációs problémák keresése. A [hatókör-konfiguráció](../automation-onboard-solutions-from-automation-account.md#scope-configuration) meghatározza, hogy mely gépek legyenek konfigurálva a megoldáshoz. Ha a gép megjelenik a munkaterületen, de nem a **Update Management** -portálon, akkor konfigurálnia kell a hatókör-konfigurációt a gépek célzásához. Ennek megismeréséhez tekintse meg [a munkaterületen található gépek](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)előkészítését ismertető témakört.

* A munkaterületen futtassa a következő lekérdezést:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Ha `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` eredményt kap, a munkaterületen meghatározott kvóta van meghatározva, és az adatok mentésének leállítása megtörtént. A munkaterületen lépjen a **használat és becsült költségek** > az **adatmennyiség-kezelés** lehetőségre, és keresse meg a kvótát, vagy távolítsa el azt.

* Ha ezek a lépések nem oldják meg a problémát, kövesse a [Windows Hybrid Runbook-feldolgozó üzembe helyezése](../automation-windows-hrw-install.md) című témakör lépéseit a hibrid feldolgozó Windows rendszerre való újratelepítéséhez. Linux rendszeren [pedig Linux hibrid Runbook-feldolgozót is üzembe helyezhet](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Forgatókönyv: nem sikerült regisztrálni az Automation erőforrás-szolgáltatót az előfizetésekhez

### <a name="issue"></a>Probléma

Ha az Automation-fiókban található megoldásokkal dolgozik, a következő hibaüzenet jelenik meg:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Ok

Az Automation erőforrás-szolgáltató nincs regisztrálva az előfizetésben.

### <a name="resolution"></a>Felbontás

Az Automation erőforrás-szolgáltató regisztrálásához kövesse az alábbi lépéseket a Azure Portalban:

1. A portál alján található Azure-szolgáltatások listájában válassza a **minden szolgáltatás**lehetőséget, majd válassza az **előfizetések** lehetőséget az általános szolgáltatás csoportban.
2. Válassza ki előfizetését.
3. A **Beállítások**területen válassza az **erőforrás-szolgáltatók**elemet.
4. Az erőforrás-szolgáltatók listájában ellenőrizze, hogy a **Microsoft. Automation** erőforrás-szolgáltató regisztrálva van-e.
5. Ha nem szerepel a felsorolásban, regisztrálja a **Microsoft. Automation** szolgáltatót az [erőforrás-szolgáltatói regisztráció](/azure/azure-resource-manager/resource-manager-register-provider-errors)során felmerülő hibák elhárítása című témakörben leírtak szerint.

## <a name="components-enabled-not-working"></a>Forgatókönyv: a Update Management-megoldás összetevői engedélyezve lettek, és most ez a virtuális gép konfigurálva van

### <a name="issue"></a>Probléma

A következő üzenetet a virtuális gépen 15 perccel a bevezetést követően továbbra is megtekintheti:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

- Az Automation-fiókkal folytatott kommunikáció blokkolása folyamatban van.
- Előfordulhat, hogy a bevezetésben lévő virtuális gép olyan klónozott gépről származik, amely nem volt telepítve a Microsoft monitoring Agent (MMA) Sysprep használatával létrehozott.

### <a name="resolution"></a>Felbontás

1. Nyissa meg a [hálózat megtervezése](../automation-hybrid-runbook-worker.md#network-planning) című témakört, amelyből megtudhatja, mely címeket és portokat kell engedélyezni a Update Management működéséhez.
2. Ha klónozott rendszerképet használ:
   1. A Log Analytics munkaterületen távolítsa el a virtuális gépet a `MicrosoftDefaultScopeConfig-Updates` hatókör-konfiguráció mentett keresésből, ha az látható. A mentett keresések a munkaterület **általános** területén találhatók.
   2. Futtassa az `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` parancsot.
   3. A `HealthService`újraindításához futtassa `Restart-Service HealthService`. Ezzel újra létrehozza a kulcsot, és létrehoz egy új UUID-t.
   4. Ha ez a megközelítés nem működik, először futtassa a sysprept a rendszerképen, majd telepítse az MMA-t.

## <a name="multi-tenant"></a>Forgatókönyv: egy másik Azure-bérlőben lévő gépekhez tartozó frissítési központi telepítés létrehozásakor a rendszer csatolt előfizetési hibát kap

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor megpróbál létrehozni egy másik Azure-bérlőben lévő gépekhez tartozó frissítési központi telepítést:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, amikor olyan frissítési központi telepítést hoz létre, amely egy másik bérlőn található Azure-beli virtuális gépekkel rendelkezik.

### <a name="resolution"></a>Felbontás

A következő megkerülő megoldással kérheti le ezeket az elemeket. A [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmagot használhatja a `-ForUpdate` kapcsolóval az ütemterv létrehozásához. Ezután használja a [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) parancsmagot, és adja át a többi bérlőn található gépeket a `-NonAzureComputer` paraméternek. Az alábbi példa bemutatja, hogyan teheti meg ezt:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Forgatókönyv: megmagyarázhatatlan újraindítások

### <a name="issue"></a>Probléma

Annak ellenére, hogy az **Újraindítás-vezérlési** lehetőséget állította be, hogy **Soha ne induljon újra**, a számítógépek még a frissítések telepítése után is újraindulnak.

### <a name="cause"></a>Ok

Windows Update több beállításkulcs is módosítható, amelyek közül bármelyik módosíthatja az újraindítási viselkedést.

### <a name="resolution"></a>Felbontás

Tekintse át az [Automatikus frissítések konfigurálása](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) szakaszban felsorolt beállításkulcsokat, és szerkessze az [újraindításhoz használt](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) beállításjegyzéket és beállításkulcsokat, és győződjön meg arról, hogy a gépek megfelelően vannak konfigurálva.

## <a name="failed-to-start"></a>Forgatókönyv: a gép a "nem sikerült elindítani" hibaüzenetet jeleníti meg egy frissítés központi telepítésében

### <a name="issue"></a>Probléma

A gép **nem tudta elindítani az** állapotot. Amikor megtekinti a gép konkrét részleteit, a következő hibaüzenet jelenik meg:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok valamelyike miatt jelentkezhet:

* A gép már nem létezik.
* A gép ki van kapcsolva, és nem érhető el.
* A gép hálózati kapcsolattal rendelkezik, ezért a hibrid feldolgozó a gépen nem érhető el.
* Frissült az MMA, amely megváltoztatta a SourceComputerId.
* A frissítési kísérlet szabályozása megtörtént, ha elér egy Automation-fiók 2 000 egyidejű feladatának korlátját. Az egyes központi telepítések feladatoknak minősülnek, és a frissítések központi telepítésének minden gépe feladatoknak számít. Az Automation-fiókban jelenleg futó egyéb automatizálási feladatok vagy frissítési üzembe helyezések az egyidejű feladatok korlátja felé mutatnak.

### <a name="resolution"></a>Felbontás

Ha alkalmazható, használjon [dinamikus csoportokat](../automation-update-management-groups.md) a frissítés központi telepítéséhez. Ezenkívül:

* Győződjön meg arról, hogy a gép még létezik, és elérhető. Ha nem létezik, szerkessze az üzemelő példányt, és távolítsa el a gépet.
* Tekintse meg a [hálózati tervezés](../automation-update-management.md#ports) szakaszt a Update Managementhoz szükséges portok és címek listájának megtekintéséhez, majd ellenőrizze, hogy a gép megfelel-e a követelményeknek.
* Futtassa a következő lekérdezést a Log Analyticsban azon gépek megkereséséhez, amelyek `SourceComputerId` megváltoztak. Keressen olyan számítógépeket, amelyek azonos `Computer` értékkel rendelkeznek, de eltérő `SourceComputerId` értékkel rendelkeznek. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Miután megtalálta az érintett gépeket, szerkessze az ezeket a gépeket tároló frissítési központi telepítéseket, majd távolítsa el, majd adja hozzá őket, hogy `SourceComputerId` a helyes értéket tükrözze.

## <a name="updates-nodeployment"></a>Forgatókönyv: a frissítések központi telepítés nélkül települnek

### <a name="issue"></a>Probléma

Ha Update Managementban regisztrál egy Windows-gépet, a frissítések telepítése nélkül települnek.

### <a name="cause"></a>Ok

Windows rendszeren a frissítések automatikusan települnek, amint elérhetők. Ez a viselkedés zavart okozhat, ha nem ütemezett frissítést a gépre való telepítéshez.

### <a name="resolution"></a>Felbontás

A `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` beállításkulcs alapértelmezett értéke 4: **automatikus letöltés és telepítés**.

Update Management ügyfelek esetében javasoljuk, hogy ezt a kulcsot 3: **automatikus letöltéssel, de ne automatikus telepítéssel**állítsa be.

További információ: az [Automatikus frissítések konfigurálása](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Forgatókönyv: a gép már regisztrálva van egy másik fiókban

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Ok

A gép már bekerült a Update Management egy másik munkaterületére.

### <a name="resolution"></a>Felbontás

1. Kövesse a következő szakaszban leírt lépéseket [a portálon a Update Management alatt](#nologs) , hogy a gép a megfelelő munkaterületre legyen bejelentve.
2. Törölje a régi összetevőket a gépen a [hibrid runbook-csoport törlésével](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), majd próbálkozzon újra.

## <a name="machine-unable-to-communicate"></a>Forgatókönyv: a gép nem tud kommunikálni a szolgáltatással

### <a name="issue"></a>Probléma

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

### <a name="cause"></a>Ok

Előfordulhat, hogy A proxy, az átjáró vagy a tűzfal blokkolja a hálózati kommunikációt.

### <a name="resolution"></a>Felbontás

Tekintse át a hálózatkezelést, és győződjön meg arról, hogy a megfelelő portok és címek engedélyezettek. A Update Management és a hibrid Runbook-feldolgozók által igényelt portok és címek listáját a [hálózati követelmények](../automation-hybrid-runbook-worker.md#network-planning) című témakörben tekintheti meg.

## <a name="unable-to-create-selfsigned-cert"></a>Forgatókönyv: nem hozható létre önaláírt tanúsítvány

### <a name="issue"></a>Probléma

A következő hibaüzenetek egyike jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Ok

A hibrid Runbook Worker nem tudott önaláírt tanúsítványt előállítani.

### <a name="resolution"></a>Felbontás

Ellenőrizze, hogy a rendszerfiók rendelkezik-e olvasási hozzáféréssel a **C:\ProgramData\Microsoft\Crypto\RSA** mappához, és próbálkozzon újra.

## <a name="mw-exceeded"></a>Forgatókönyv: az ütemezett frissítés MaintenanceWindowExceeded hibával meghiúsult

### <a name="issue"></a>Probléma

A frissítések alapértelmezett karbantartási időszaka 120 perc. A karbantartási időszakot legfeljebb 6 órára növelheti, vagy 360 percet is igénybe vehet.

### <a name="resolution"></a>Felbontás

Szerkessze a sikertelen ütemezett frissítések telepítését, és növelje a karbantartási időszakot.

További információ a karbantartási időszakokról: [Install Updates (frissítések telepítése](../automation-tutorial-update-management.md#schedule-an-update-deployment)).

## <a name="hresult"></a>Forgatókönyv: a gép "nincs kiértékelésre", és egy HResult-kivételt mutat be

### <a name="issue"></a>Probléma

* Olyan gépeket tartalmaz, amelyek **nem** **felelnek meg a megfelelőségnek**, és megjelenik egy kivételt jelző üzenet.
* Vannak olyan gépek, amelyek nem értékelték.
* Megjelenik egy HRESULT-hibakód a portálon.

### <a name="cause"></a>Ok

A frissítési ügynök (Windows Update ügynök a Windows rendszeren; a Linux-disztribúcióhoz tartozó csomagkezelő) nincs megfelelően konfigurálva. Update Management a gép frissítési ügynökére támaszkodik, hogy megadja a szükséges frissítéseket, a javítás állapotát és a telepített javítások eredményét. Ezen információk nélkül Update Management nem tud megfelelően jelentést készíteni a szükséges vagy telepített javításokról.

### <a name="resolution"></a>Felbontás

Próbálja helyileg végrehajtani a frissítéseket a gépen. Ha ez nem sikerül, az általában azt jelzi, hogy konfigurációs hiba van a frissítési ügynökkel.

Ezt a problémát gyakran a hálózati konfiguráció és a tűzfal okozta problémák okozzák. Próbálja meg a következőket:

* Linux esetén ellenőrizze a megfelelő dokumentációt, és győződjön meg arról, hogy el tudja érni a csomag tárházának hálózati végpontját.
* Windows esetén tekintse meg az ügynök konfigurációját a [frissítések nem töltik le az intranetes végpontról (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Ha a gépek Windows Updatehoz vannak konfigurálva, győződjön meg arról, hogy elérhetők a [http/proxyval kapcsolatos problémák](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)című témakörben leírt végpontok.
  * Ha a gépek Windows Server Update Services (WSUS) használatára vannak konfigurálva, győződjön meg arról, hogy elérheti a [WUServer beállításkulcs](/windows/deployment/update/waas-wu-settings)által konfigurált WSUS-kiszolgálót.

Ha megjelenik egy HRESULT, kattintson duplán a pirosban megjelenő kivételre a teljes kivétel üzenet megjelenítéséhez. Tekintse át a következő táblázatot a lehetséges megoldásokhoz vagy ajánlott műveletekhez:

|Kivétel  |Megoldás vagy művelet  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | A kivétel okának további részleteiért keresse meg a megfelelő hibakódot a [Windows Update hibakódok listájában](https://support.microsoft.com/help/938205/windows-update-error-code-list) .        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ezek hálózati kapcsolati problémákat jeleznek. Győződjön meg arról, hogy a számítógépének van hálózati kapcsolata Update Managementhoz. A szükséges portok és címek listájának megtekintéséhez tekintse meg a [hálózati tervezés](../automation-update-management.md#ports) szakaszt.        |
|`0x8024001E`| A frissítési művelet nem fejeződött be, mert a szolgáltatás vagy a rendszer leállítása megtörtént.|
|`0x8024002E`| Windows Update szolgáltatás le van tiltva.|
|`0x8024402C`     | Ha WSUS-kiszolgálót használ, győződjön meg arról, hogy az `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` beállításkulcs alatt `WUServer` és `WUStatusServer` beállításjegyzék-értékei megadják a megfelelő WSUS-kiszolgálót.        |
|`0x80072EE2`|Hálózati kapcsolati probléma vagy probléma történt a konfigurált WSUS-kiszolgálóval való kommunikáció során. Ellenőrizze a WSUS beállításait, és ellenőrizze, hogy a szolgáltatás elérhető-e az ügyfélről.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Ellenőrizze, hogy a Windows Update szolgáltatás (wuauserv) fut-e, és nincs-e letiltva.        |
|Bármely más általános kivétel     | Futtasson keresést az interneten a lehetséges megoldásokért, és működjön együtt a helyi informatikai támogatással.         |

A windowsupdate. log fájl áttekintése segíthet a lehetséges okok meghatározásában is. További információ a napló beolvasásáról: [a windowsupdate. log fájl olvasása](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Emellett letöltheti és futtathatja a [Windows Update-hibakeresőt](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) , hogy megkeresse a gépen Windows Update kapcsolatos problémákat.

> [!NOTE]
> A [Windows Update-hibakereső](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) dokumentációja azt jelzi, hogy a Windows-ügyfeleken használható, de a Windows Serveren is működik.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Forgatókönyv: a frissítés futtatása "sikertelen" állapotot ad vissza (Linux)

### <a name="issue"></a>Probléma

A frissítési kísérlet elindul, de hibákat észlel a Futtatás során.

### <a name="cause"></a>Ok

Lehetséges okok:

* A csomagkezelő állapota nem kifogástalan.
* A frissítési ügynök (WUA for Windows, disztribúció-specifikus csomagkezelő for Linux) hibásan van konfigurálva.
* A konkrét csomagok nem akadályozzák a felhőalapú javításokat.
* A gép nem érhető el.
* A frissítések nem oldották meg a függőségeket.

### <a name="resolution"></a>Felbontás

Ha a frissítés sikeres elindítása után hibák lépnek fel, ellenőrizze a Futtatás során az érintett gép [kimenetét](../manage-update-multi.md#view-results-of-an-update-deployment) . Előfordulhat, hogy olyan hibaüzeneteket talál a gépekről, amelyeken kutatást végezhet, és műveleteket hajthat végre. Update Management megköveteli, hogy a Package Manager Kifogástalan állapotba kerüljön a sikeres frissítések központi telepítéséhez.

Ha az adott javítások, csomagok vagy frissítések közvetlenül a feladatok végrehajtása előtt láthatók, kihagyhatja [azokat a](../automation-tutorial-update-management.md#schedule-an-update-deployment) következő frissítés központi telepítéséről. A naplófájlok Windows Updateból való összegyűjtéséhez lásd: [Windows Update naplófájlok](/windows/deployment/update/windows-update-logs).

Ha nem tudja feloldani a javítási problémát, készítsen másolatot a következő naplófájlról, és őrizze meg a hibaelhárítási célból, *mielőtt* a következő frissítés telepítése megkezdődik:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Nincsenek telepítve a javítások

### <a name="machines-dont-install-updates"></a>A gépek nem telepítik a frissítéseket

* Próbálja meg közvetlenül a gépen lefuttatni a frissítéseket. Ha a gép nem tudja alkalmazni a frissítéseket, tekintse [át a lehetséges hibák listáját a hibaelhárítási útmutatóban](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Ha a frissítések helyileg futnak, próbálja meg eltávolítani és újratelepíteni az ügynököt a gépen a [virtuális gép eltávolítása a Update Managementról](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)című témakör útmutatását követve.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Tudom, hogy vannak-e elérhető frissítések, de nem jelennek meg a saját gépeken

* Ez gyakran előfordul, ha a gépek WSUS vagy System Center Configuration Manager (SCCM) frissítéseinek beszerzésére vannak konfigurálva, de a WSUS és a SCCM nem hagyta jóvá a frissítéseket.
* A [jelen cikk "az automatikus frissítések konfigurálása a beállításjegyzék szerkesztésével" című szakaszában](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)megtekintheti, hogy a gépek konfigurálva vannak-e a WSUS-hez és a SCCM.
* Ha a WSUS nem jóváhagyja a frissítéseket, azok nem lesznek telepítve. A nem jóváhagyott frissítéseket Log Analytics a következő lekérdezés futtatásával tekintheti meg:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>A frissítések telepítettként jelennek meg, de nem találom a gépen

* A frissítéseket gyakran felülírják más frissítések. További információ: a [frissítés felülírva](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) a Windows Update hibaelhárítási útmutatójában.

### <a name="installing-updates-by-classification-on-linux"></a>Frissítések telepítése besorolás szerint Linuxon

* Ha Linuxon dolgozik, a frissítések besorolás szerinti („kritikus és biztonsági frissítések”) üzembe helyezésekor fontos kikötéseket kell figyelembe venni, különösen CentOS használata esetén. Ezek a korlátozások dokumentálva vannak a [Update Management áttekintés oldalon](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>A KB2267602 konzisztensen hiányzik

* A KB2267602 a [Windows Defender definíciófrissítése](https://www.microsoft.com/wdsi/definitions). Naponta frissül.

## <a name="next-steps"></a>Következő lépések

Ha nem találta meg a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, valamint a válaszok, a támogatás és a szakértők összekapcsolásával csatlakozhat a [@AzureSupporthoz ](https://twitter.com/azuresupport), a hivatalos Microsoft Azure a felhasználói élmény fokozásához.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
