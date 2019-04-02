---
title: Az Update Management kapcsolatos hibák elhárítása
description: Ismerje meg, az Update Management hibáinak elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b92ce1d5fb0e0b2b043b1bbfcb78dbaf3dde2e23
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804462"
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

### <a name="nologs"></a>Forgatókönyv: Az Azure Monitor naplóira gép nem látható a felügyeleti adatok frissítése

#### <a name="issue"></a>Probléma

Olyan gépeket azt mutatják be, mint **nincs értékelve** alatt **megfelelőségi**, de az Azure Monitor naplóira szívverési adatok jelennek meg a hibrid Runbook-feldolgozó, de nem az Update Management.

#### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó újra regisztrálni és telepíteni kell.

#### <a name="resolution"></a>Megoldás:

Kövesse a lépéseket [Windows hibrid Runbook-feldolgozó üzembe helyezése](../automation-windows-hrw-install.md) újra kell telepítenie a Windows hibrid feldolgozói vagy [üzembe helyezése egy hibrid Runbook-feldolgozója Linuxra](../automation-linux-hrw-install.md) Linux rendszeren.

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
|`0x8024402C` vagy `0x8024401C`     | Ezek a hibák a hálózati problémák léptek fel. Győződjön meg arról, hogy a gépe képes-e a megfelelő hálózati kapcsolattal a frissítéskezelés. A szakaszban [hálózattervezés](../automation-update-management.md#ports) portokat és a szükséges címek listája.        |
|`0x8024402C`     | Ha egy WSUS-kiszolgálót használ, ellenőrizze, hogy a beállításkulcs-értékeket `WUServer` és `WUStatusServer` a beállításkulcs alatt `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` megfelelő a WSUS-kiszolgálót.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Győződjön meg arról, hogy a Windows Update szolgáltatás (wuauserv) fut-e, és nincs letiltva.        |
|Bármely egyéb általános kivétel     | Keresés a lehetséges megoldásokat az interneten működnek és a helyi IT-támogatással.         |

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
