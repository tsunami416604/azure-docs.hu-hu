---
title: Az Update Management kapcsolatos hibák elhárítása
description: Ismerje meg, az Update Management hibáinak elhárítása
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 23139755af812f99bce8c2c255805eaf9e30b2da
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477058"
---
# <a name="troubleshooting-issues-with-update-management"></a>Az Update Management kapcsolatos hibák elhárítása

Ez a cikk ismerteti a megoldásokat, amelyek találkozhat az Update Management használatakor problémák megoldásához.

Van egy ügynök hibaelhárító hibrid feldolgozó ügynök határozza meg a hibát kiváltó problémát. A hibaelhárító kapcsolatos további információkért lásd: [hibaelhárítás frissítési ügynök problémák](update-agent-issues.md). A többi probléma, a részletes információinak megtekintéséhez alábbi lehetséges okokat.

## <a name="general"></a>Általános kérdések

### <a name="components-enabled-not-working"></a>Forgatókönyv: Az "Update Management" megoldás összetevői engedélyezve van, és most már a virtuális gép konfigurálása

#### <a name="issue"></a>Probléma

A következő üzenet jelenik a virtuális gépen 15 perc, az előkészítés után továbbra is:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Ok

Ez a hiba oka lehet a következő okok miatt:

1. Térjen vissza az Automation-fiók kommunikációja blokkolva van folyamatban.
2. A virtuális gép típusától függenek előfordulhat, hogy a klónozott gép, amelynek nem a Microsoft Monitoring Agent telepítése a Sysprep használatával előkészített kell származnia.

#### <a name="resolution"></a>Megoldás:

1. Látogasson el, [hálózattervezés](../automation-hybrid-runbook-worker.md#network-planning) további információt arról, hogy mely címeket és portokat engedélyezni kell, az Update Management működjön.
2. Ha egy klónozott rendszerkép használatával:
   1. A Log Analytics munkaterületén távolítsa el a virtuális gép számára a hatókör-konfigurációt a mentett keresés `MicrosoftDefaultScopeConfig-Updates` ez-e meg. Mentett keresések területen található **általános** a munkaterületén.
   2. Futtassa a `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` parancsot.
   3. Futtatás `Restart-Service HealthService` újraindítani a `HealthService`. Ez hozza létre újra a kulcsot, és hozzon létre egy új UUID azonosítója.
   4. Ha ez sem működik, a sysprep lemezkép az első és az MMA-ügynök telepítése után az a tény.

### <a name="multi-tenant"></a>Forgatókönyv: A társított előfizetés hibaüzenetet kapja, amikor a gépek a frissítéstelepítések létrehozásához egy másik Azure-bérlőhöz.

#### <a name="issue"></a>Probléma

Egy másik Azure-bérlőhöz frissítéstelepítés gépek létrehozására tett kísérlet közben a következő hibaüzenetet kapja:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Ok

Ez akkor fordul elő, amikor létrehoz egy központi telepítést, amely rendelkezik az Azure-beli virtuális gépek szerepelnek a frissítéstelepítés egy másik bérlőben.

#### <a name="resolution"></a>Megoldás:

Az alábbi megkerülő megoldást használja, hogy az ütemezett kell. Használhatja a [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmag és a kapcsoló `-ForUpdate` ütemezés létrehozása és használata a [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) parancsmag paraméterével a a többi bérlő számára a gépek a `-NonAzureComputer` paraméter. Az alábbi példa bemutatja egy példa, hogyan teheti ezt meg:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Forgatókönyv: Gépek nem jelennek meg az Update Management portál

#### <a name="issue"></a>Probléma

A következő esetekben találkozhat:

* A gép látható **nincs konfigurálva** az Update Management nézetből a virtuális gépek

* A gépek hiányoznak az Update Management nézetből, az Automation-fiók

* Olyan gépeket azt mutatják be, mint **nincs értékelve** alatt **megfelelőségi**, de az Azure Monitor naplóira szívverési adatok jelennek meg a hibrid Runbook-feldolgozó, de nem az Update Management.

#### <a name="cause"></a>Ok

Ez olyankor fordulhat elő, vagy helytelenül konfigurált hatókör-konfiguráció által potenciális helyi konfigurációs problémákat.

A hibrid Runbook-feldolgozó újra regisztrálni és telepíteni kell.

Előfordulhat, hogy meghatározott kvótát, amelynek már elérte és leállítása adatokat tárolna a rendszer a munkaterület.

#### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, a gép a megfelelő munkaterületet jelent. Ellenőrizze, milyen jelent, hogy a gép a munkaterületen. Ennek az utasításokért lásd: [ellenőrizheti az ügynök csatlakozását a Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Ezt követően ügyeljen arra, ez a munkaterület, amely kapcsolódik az Azure Automation-fiók. Ennek ellenőrzéséhez keresse meg az Automation-fiókját, és kattintson **csatolt munkaterület** alatt **kapcsolódó erőforrások**.

* Ellenőrizze, hogy a Log Analytics-munkaterület megjelennek a gépek. Futtassa a következő lekérdezést a Log Analytics-munkaterület, amely az Automation-fiókhoz van csatolva. Ha nem látja, akkor a gép az a lekérdezés eredményeit, a gép nem bocsát ki szívveréseket, ami azt jelenti, nagy valószínűséggel van a helyi konfigurációs probléma. A hibaelhárító futtatása [Windows](update-agent-issues.md#troubleshoot-offline) vagy [Linux](update-agent-issues-linux.md#troubleshoot-offline) is attól függően, az operációs rendszer, vagy [telepítse újra a házirendügynök](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Ha a gép megjelenik-e a lekérdezés eredményeit, akkor kell nagyon a következő felsorolás a megadott hatókör-konfigurációt.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Ellenőrizze a hatókör-konfigurációs problémákat. [A hatókör beállításainak](../automation-onboard-solutions-from-automation-account.md#scope-configuration) meghatározza, mely a megoldás első konfigurált. Ha a gép a munkaterületen jelenik meg, de nem jelenik meg, hogy a hatókör-konfigurációs, amelyekre a gépek konfigurálnia kell. Ezzel kapcsolatban lásd: [előkészítheti a gépeket a munkaterületen](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Ha a fenti lépések nem oldják meg a problémát, kövesse a lépéseket [Windows hibrid Runbook-feldolgozó üzembe helyezése](../automation-windows-hrw-install.md) újra kell telepítenie a Windows hibrid feldolgozói vagy [üzembe helyezése egy hibrid Runbook-feldolgozója Linuxra](../automation-linux-hrw-install.md) Linux rendszeren.

* A munkaterületen a következő lekérdezés futtatásával. Ha az eredmény megjelenítéséhez `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` definiálva a munkaterület, amely mentésének leállt, és elérte a kvóta tartozik. Lépjen a munkaterületén **felhasználás és becsült költségek** > **adatmennyiség-kezelés** és ellenőrizze a kvótát, vagy rendelkezik a kvóta eltávolításához.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Ha problémák merülnek fel a megoldás virtuális gépen való felvétele közben, ellenőrizze a **az Operations Manager** eseménynaplóban **alkalmazás- és szolgáltatásnaplók** események a helyi gépen Eseményazonosító **4502** és esemény üzenetet tartalmazó **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

A következő szakasz az egyes konkrét hibaüzeneteket és a egy lehetséges megoldás emeli ki. Egyéb regisztrációs problémák megtekintéséhez [hibaelhárítása a megoldás bevezetése](onboarding.md).

### <a name="machine-already-registered"></a>Forgatókönyv: Gép már regisztrálva van egy másik fiókkal

#### <a name="issue"></a>Probléma

A következő hibaüzenet jelenhet meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Ok

A gép már egy másik munkaterület az Update Management előkészítve.

#### <a name="resolution"></a>Megoldás:

Hajtsa végre a régi összetevők tisztítását által a gépen [a hibrid runbook-csoport törlése](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , és próbálkozzon újra.

### <a name="machine-unable-to-communicate"></a>Forgatókönyv: Gép nem tud kommunikálni a szolgáltatás

#### <a name="issue"></a>Probléma

A következő hibaüzeneteket egyikét kapja:

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

Egy proxy, átjáró vagy egy tűzfal blokkolja a hálózati kommunikációt lehet.

#### <a name="resolution"></a>Megoldás:

Tekintse át a hálózati, és győződjön meg, hogy a megfelelő portok és a címek használata engedélyezett. Lásd: [hálózati követelmények](../automation-hybrid-runbook-worker.md#network-planning), portokat és az Update Management és a hibrid Runbook-feldolgozók által igényelt-címek listáját.

### <a name="unable-to-create-selfsigned-cert"></a>Forgatókönyv: Nem sikerült önaláírt tanúsítvány létrehozása

#### <a name="issue"></a>Probléma

A következő hibaüzeneteket egyikét kapja:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó nem tudta önaláírt tanúsítvány létrehozása

#### <a name="resolution"></a>Megoldás:

Ellenőrizze a rendszer fiók olvasási hozzáféréssel rendelkezik mappába **C:\ProgramData\Microsoft\Crypto\RSA** , és próbálkozzon újra.

### <a name="failed-to-start"></a>Forgatókönyv: Egy gép jeleníti meg a frissítéstelepítés indítása sikertelen.

#### <a name="issue"></a>Probléma

A gép állapota **nem sikerült elindítani a** gép. A gép részleteit megtekintheti a következő hiba jelenik meg:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, az alábbi okok egyike miatt:

* A gép már nem létezik.
* A gép és nem érhető el, ki van kapcsolva.
* A gép rendelkezik hálózati probléma, és a hibrid feldolgozó a gépen nem érhető el.
* Hiba történt a Microsoft Monitoring Agent, amelyek módosultak a SourceComputerId frissítése
* A frissítési menetet előfordulhat, hogy szabályozva lettek Ha eléri a 2000 egyidejű feladat korlátot, az Automation-fiók. Minden egyes üzembe helyezési számít egy feladat és a egy frissítés üzemelő példányok száma az egyes gépek feladatként. Bármely más automatizálási feladat vagy a frissítés központi telepítése jelenleg fut az Automation-fiók száma az egyidejű feladat korlát felé a.

#### <a name="resolution"></a>Megoldás:

Ha érvényes feltételek [dinamikus csoportok](../automation-update-management.md#using-dynamic-groups) a frissítési telepítés céljából.

* Ellenőrizze a gép még létezik, és elérhető-e. Ha még nem létezik, módosítsa a telepítését, és távolítsa el a gépet.
* A szakaszban [hálózattervezés](../automation-update-management.md#ports) portokat és a címeket, amelyek szükségesek az Update Management, és ellenőrizze a gép megfelel-e ezek a követelmények listáját.
* Futtassa a következő lekérdezést a Log Analyticsben található gépek a környezetében azon `SourceComputerId` megváltozott. Keresse meg az azonos rendelkező számítógépek `Computer` érték, de különböző `SourceComputerId` értéket. Miután megtalálta az érintett számítógépeken, szerkesztenie kell a frissítés-központitelepítést cél ezeken a gépeken, és távolítsa el, és adja hozzá újra a gépek tehát a `SourceComputerId` tükrözi a megfelelő értéket.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Forgatókönyv: Számítógép nincs értékelve, és megjeleníti a kivétel HResult jelenik meg

#### <a name="issue"></a>Probléma

Olyan gépeket azt mutatják be, mint **nincs értékelve** alatt **megfelelőségi**, és a egy alatta kivétel üzenet jelenik meg.

#### <a name="cause"></a>Ok

Windows Update vagy a WSUS nem megfelelően van konfigurálva a gépen. Az Update Management támaszkodik a Windows Update vagy a WSUS szolgáltatást adja meg a szükséges, a frissítések állapota a javítást, és javítások vannak telepítve az eredményeket. Ezen adatok nélkül az Update Management is nem megfelelően jelentik a szükséges vagy telepített javítások.

#### <a name="resolution"></a>Megoldás:

Kattintson duplán a kivételt a kivétel teljes üzenet jelenik meg a vörös színnel jelenik meg. Tekintse át a következő táblázat a lehetséges megoldások vagy műveleteket tartalmazza:

|Kivétel  |Megoldás vagy művelet  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Keresse meg a megfelelő hibakód [Windows update kód Hibalista](https://support.microsoft.com/help/938205/windows-update-error-code-list) megtalálja a további részleteket a kivétel okát.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ezek a hibák a hálózati problémák léptek fel. Győződjön meg arról, hogy a gépe képes-e a megfelelő hálózati kapcsolattal a frissítéskezelés. A szakaszban [hálózattervezés](../automation-update-management.md#ports) portokat és a szükséges címek listája.        |
|`0x8024001E`| A frissítési művelet nem fejeződött be, mert a szolgáltatás vagy a rendszer leállt.|
|`0x8024002E`| Windows Update szolgáltatás le van tiltva.|
|`0x8024402C`     | Ha egy WSUS-kiszolgálót használ, ellenőrizze, hogy a beállításkulcs-értékeket `WUServer` és `WUStatusServer` a beállításkulcs alatt `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` megfelelő a WSUS-kiszolgálót.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Győződjön meg arról, hogy a Windows Update szolgáltatás (wuauserv) fut-e, és nincs letiltva.        |
|Bármely egyéb általános kivétel     | Keresés a lehetséges megoldásokat az interneten működnek és a helyi IT-támogatással.         |

Tekintse át a `windowsupdate.log` állapítható meg, valamint a lehetséges ok nyújt segítséget. A naplófájl olvasása. További információkért lásd: [a Windowsupdate.log fájl olvasása](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Emellett töltse le és futtassa a [Windows frissítési hibaelhárító](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) ellenőrizze, hogy vannak-e a gépen Windows Update szolgáltatással kapcsolatos problémák.

> [!NOTE]
> A [Windows frissítési hibaelhárító](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) állapotok a Windows-ügyfelek de is működik Windows Server rendszeren.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Forgatókönyv: A frissítési menetet nem indul el

#### <a name="issue"></a>Probléma

Egy frissítés futtatások Linuxos gépen indítása sikertelen.

#### <a name="cause"></a>Ok

A Linux hibrid feldolgozó állapota nem megfelelő.

#### <a name="resolution"></a>Megoldás:

Készítsen másolatot a következő naplófájl, és megőrizheti azokat hibaelhárítás céljából:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Forgatókönyv: A frissítési menetet elindul, de hibát észlel

#### <a name="issue"></a>Probléma

A frissítési menetet elindul, de a futtatás során hibát észlel.

#### <a name="cause"></a>Ok

Lehetséges okai lehetnek:

* A Package manager állapota nem megfelelő
* Adott csomagok zavarhatják felhőalapú javítása
* Egyéb okok

#### <a name="resolution"></a>Megoldás:

Ha egy frissítés futtatása után sikeresen elindul a linuxon futó hiba fordul elő, ellenőrizze a feladat kimenete a Futtatás a fertőzött gép. Azt tapasztalhatja, hogy hibaüzeneteket a kutatás és a művelet végrehajtása a számítógépe Csomagkezelő. Az Update Management a Csomagkezelőt a sikeres telepítések állapota megfelelő lesz szükséges.

Bizonyos esetekben a csomagfrissítéseket zavarhatja megakadályozza, hogy befejezze a frissítéstelepítések frissítéskezelési. Ha látni, hogy kell ezeket a csomagokat kizárása későbbi frissítési menetek, vagy telepítse manuálisan saját magának.

Ha mégsem sikerül megoldani a javítási problémát, készítsen másolatot a következő naplófájlt, és megőrizheti azokat **előtt** a következő központi telepítési elindítja a hibaelhárítás céljából:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.
