---
title: A hibrid Runbook-feldolgozók hibaelhárítása Azure Automation
description: Ez a cikk a hibrid Runbook-feldolgozók Azure Automation hibaelhárításához nyújt információt.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679338"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hibrid Runbook-feldolgozók hibáinak megoldása

Ez a cikk a hibrid Runbook-feldolgozókkal kapcsolatos hibák elhárításával kapcsolatos információkat tartalmaz.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="general"></a>Általános kérdések

A hibrid Runbook Worker egy ügynöktől függ, hogy hogyan regisztrálja az Automation-fiókját a feldolgozó, a Runbook-feladatok fogadása és a jelentés állapotának regisztrálásához. Windows esetén ez az ügynök a Windows Log Analytics ügynöke. Linux esetén ez az Log Analytics-ügynök Linux rendszerhez.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Forgatókönyv: a Runbook végrehajtása sikertelen

#### <a name="issue"></a>Probléma

A Runbook végrehajtása sikertelen, és a következő hibaüzenet jelenik meg.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

A runbook röviddel azután fel van függesztve, hogy háromszor próbálkozik a végrehajtással. Vannak olyan feltételek, amelyek megszakítják a runbook befejezését. Előfordulhat, hogy a kapcsolódó hibaüzenet nem tartalmaz további információkat.

#### <a name="cause"></a>Ok

A lehetséges okok a következők:

* A runbookok nem tud hitelesíteni a helyi erőforrásokkal.
* A hibrid feldolgozó proxy vagy tűzfal mögött van.
* A hibrid Runbook-feldolgozó futtatására konfigurált számítógép nem felel meg a minimális hardverkövetelmények követelményeinek.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a ***. Azure-Automation.net** a 443-es porton.

A hibrid Runbook-feldolgozót futtató számítógépeknek meg kell felelniük a minimális hardverkövetelmények, mielőtt a feldolgozó konfigurálva lenne a szolgáltatás üzemeltetésére. A runbookok és az általuk használt háttérbeli folyamat okozhatja, hogy a rendszer felhasználható, és runbook a feladatok késését vagy időtúllépését okozhatja.

Győződjön meg arról, hogy a hibrid Runbook Worker szolgáltatás futtatására szolgáló számítógép megfelel a minimális hardverkövetelmények követelményeinek. Ha igen, figyelje a CPU-t és a memóriát a hibrid Runbook-munkavégző folyamatok és a Windows teljesítménye közötti korreláció megállapítása érdekében. A memória vagy a CPU nyomása az erőforrások frissítésének szükségességét jelezheti. Kiválaszthat egy másik számítási erőforrást is, amely támogatja a minimális követelményeket és a méretezést, ha a munkaterhelési igények azt jelzik, hogy növelésre van szükség.

A megfelelő esemény leírását `Win32 Process Exited with code [4294967295]`a **Microsoft-SMA** eseménynaplóban találja. Ennek a hibának az az oka, hogy nem konfigurálta a hitelesítést a runbookok, vagy megadta a hibrid Runbook Worker csoport futtató hitelesítő adatait. Tekintse át a runbook engedélyeit a [hibrid runbook-feldolgozón futó runbookok](../automation-hrw-run-runbooks.md) , és ellenőrizze, hogy helyesen konfigurálta-e a hitelesítést a runbookok.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Forgatókönyv: 15011-es esemény a hibrid Runbook-feldolgozóban

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozó a 15011-es eseményt kapja, ami azt jelzi, hogy a lekérdezés eredménye érvénytelen. A következő hiba jelenik meg, amikor a feldolgozó megpróbál megnyitni egy kapcsolódást a [jelző kiszolgálóval](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó nincs megfelelően konfigurálva az automatikus telepítési megoldáshoz. Ez a megoldás egy olyan részt tartalmaz, amely összekapcsolja a virtuális gépet a Log Analytics munkaterülettel. A PowerShell-parancsfájl az előfizetésben található munkaterületet keresi a megadott névvel. Ebben az esetben a Log Analytics munkaterület egy másik előfizetésben található. A parancsfájl nem találja a munkaterületet, és megpróbál létrehozni egyet, de a név már használatban van. Így az üzembe helyezés sikertelen lesz.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához két lehetőség közül választhat:

* Módosítsa a PowerShell-parancsfájlt, hogy a Log Analytics munkaterületet egy másik előfizetésben keresse meg. Ez jó megoldás, ha a jövőben számos hibrid Runbook-feldolgozó gépet szeretne üzembe helyezni.

* Manuálisan konfigurálja a munkavégző gépet egy Orchestrator-homokozóban történő futtatásra. Ezután futtasson egy, a munkavégző Azure Automation fiókjában létrehozott runbook a funkció teszteléséhez.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Forgatókönyv: a Windows Azure virtuális gépek automatikusan el lettek dobva a hibrid feldolgozói csoportból

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozó vagy virtuális gépek nem láthatók, ha a munkavégző gép hosszú ideje ki van kapcsolva.

#### <a name="cause"></a>Ok

A hibrid Runbook Worker gép nem pingelése Azure Automation 30 napnál hosszabb ideig. Ennek eredményeképpen az Automation megtisztítta a hibrid Runbook Worker csoportot vagy a rendszer munkavégző csoportját. 

#### <a name="resolution"></a>Megoldás:

Indítsa el a munkavégző gépet, majd rereregister Azure Automation. Tekintse meg a runbook-környezet telepítéséhez és a Azure Automationhoz való csatlakozáshoz szükséges utasításokat a [Windows Hybrid Runbook Worker üzembe helyezése](../automation-windows-hrw-install.md)című témakörben.

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Forgatókönyv: a hibrid Runbook-feldolgozók tanúsítványtárolójában nem található tanúsítvány.

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozón futó runbook a következő hibaüzenettel meghiúsul.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a futtató [fiókot](../manage-runas-account.md) egy hibrid runbook-feldolgozón futó runbook kísérli meg használni, ahol a futtató fiók tanúsítványa nincs jelen. A hibrid Runbook-feldolgozók alapértelmezés szerint nem helyileg rendelkeznek a tanúsítvány eszközzel. A futtató fiók működéséhez az szükséges, hogy az eszköz megfelelően működjön.

#### <a name="resolution"></a>Megoldás:

Ha a hibrid Runbook-feldolgozó egy Azure-beli virtuális gép, akkor ehelyett [felügyelt identitásokat használhat az Azure-erőforrásokhoz](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Ez a forgatókönyv leegyszerűsíti a hitelesítést azáltal, hogy lehetővé teszi az Azure-erőforrások hitelesítését az Azure-beli virtuális gép felügyelt identitásával a futtató fiók helyett. Ha a hibrid Runbook Worker egy helyszíni gép, telepítenie kell a futtató fiók tanúsítványát a gépre. A tanúsítvány telepítésének megismeréséhez tekintse meg a következő témakört: a PowerShell runbook **export-RunAsCertificateToHybridWorker** futtatása a [Runbookok futó hibrid Runbook-feldolgozón](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Forgatókönyv: 403-es hiba a hibrid Runbook-feldolgozók regisztrálása során

#### <a name="issue"></a>Probléma

A Worker kezdeti regisztrációs fázisa meghiúsul, és a következő hibaüzenetet kapja (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Ok

A lehetséges okok a következők:

* Az ügynök beállításaiban nem szerepel a munkaterület-azonosító vagy a munkaterület kulcsa (elsődleges). 

* A hibrid Runbook-feldolgozó nem tudja letölteni a konfigurációt, ami egy fiók összekapcsolási hibáját okozza. Ha az Azure lehetővé teszi a megoldások használatát, csak bizonyos régiókat támogat a Log Analytics munkaterület és az Automation-fiók összekapcsolásához. Az is lehetséges, hogy helytelen dátum és/vagy idő van beállítva a számítógépen. Ha az idő +/-15 perc az aktuális időponttól, a bevezetés sikertelen lesz.

#### <a name="resolution"></a>Megoldás:

##### <a name="mistyped-workspace-idkey"></a>Nem típusos munkaterület azonosítója/kulcsa
Annak ellenőrzéséhez, hogy az ügynök munkaterület-azonosítója vagy a munkaterület kulcsa nem lett-e megadva, lásd: [munkaterület hozzáadása vagy eltávolítása](../../azure-monitor/platform/agent-manage.md#windows-agent) a Windows-ügynökhöz, vagy [munkaterület hozzáadása vagy eltávolítása](../../azure-monitor/platform/agent-manage.md#linux-agent) a Linux-ügynökhöz.  Győződjön meg arról, hogy a teljes karakterláncot kijelöli a Azure Portal, majd másolja és illessze be alaposan.

##### <a name="configuration-not-downloaded"></a>A konfiguráció nincs letöltve

A Log Analytics munkaterület és az Automation-fióknak egy összekapcsolt régióban kell lennie. A támogatott régiók listáját itt tekintheti meg: [Azure Automation és log Analytics munkaterület-hozzárendelések](../how-to/region-mappings.md).

Előfordulhat, hogy frissítenie kell a számítógép dátum-és/vagy időzónáját is. Ha egyéni időtartományt választ, győződjön meg arról, hogy a tartomány UTC-ben van, ami eltérhet a helyi időzónától.

## <a name="linux"></a>Linux

A Linux Hybrid Runbook Worker a [linuxos log Analytics-ügynöktől](../../azure-monitor/platform/log-analytics-agent.md) függ, hogy kommunikálni fog-e az Automation-fiókjával a feldolgozó, a Runbook-feladatok fogadása és a jelentés állapotának regisztrálásához. Ha a feldolgozó regisztrálása meghiúsul, a következő hiba lehetséges okai vannak:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Forgatókönyv: a Linux Log Analytics ügynöke nem fut

#### <a name="issue"></a>Probléma

A Linux Log Analytics ügynöke nem fut

#### <a name="cause"></a>Ok

Ha az ügynök nem fut, megakadályozza, hogy a Linux Hybrid Runbook Worker kommunikáljon Azure Automationokkal. Lehetséges, hogy az ügynök különböző okokból nem fut.

#### <a name="resolution"></a>Megoldás:

 Ellenőrizze, hogy fut-e az ügynök a `ps -ef | grep python`parancs beírásával. Az alábbihoz hasonló kimenetnek kell megjelennie, a Python-folyamatokat a **nxautomation** felhasználói fiókkal. Ha a Update Management vagy Azure Automation megoldás nincs engedélyezve, a következő folyamatok egyike sem fut.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A következő lista azokat a folyamatokat mutatja be, amelyek a Linux hibrid Runbook-feldolgozók számára lettek elindítva. Ezek mind a **/var/opt/Microsoft/omsagent/State/automationworker/** könyvtárban találhatók.

* **OMS. conf** – a Worker Manager folyamata. Közvetlenül a DSC-ből indult el.

* **Worker. conf** – az automatikusan regisztrált hibrid feldolgozói folyamat. A Worker Manager indította el. Ezt a folyamatot a Update Management használja, és átlátható a felhasználó számára. Ez a folyamat nem jelenik meg, ha a Update Management megoldás nincs engedélyezve a számítógépen.

* **DIY/Worker. conf** – a DIY hibrid feldolgozói folyamat. A DIY hibrid feldolgozói folyamat a hibrid Runbook-feldolgozón futtatott felhasználói runbookok végrehajtására szolgál. Ez csak az automatikusan regisztrált hibrid munkavégző folyamattól tér el a legfontosabb részletességgel, amelyet más konfigurációt használ. Ez a folyamat nem jelenik meg, ha a Azure Automation megoldás le van tiltva, és a DIY Linux Hybrid Worker nincs regisztrálva.

Ha az ügynök nem fut, futtassa a következő parancsot a szolgáltatás elindításához `sudo /opt/microsoft/omsagent/bin/service_control restart`:.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Forgatókönyv: a megadott osztály nem létezik

Ha a `The specified class does not exist..` **/var/opt/Microsoft/omsconfig/omsconfig.log**hibaüzenet jelenik meg, a linuxos log Analytics-ügynököt frissíteni kell. A következő parancs futtatásával telepítse újra az ügynököt:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

A Windows Hybrid Runbook Worker a [windows log Analytics ügynöktől](../../azure-monitor/platform/log-analytics-agent.md) függ, hogy kommunikálni tudjon az Automation-fiókjával a feldolgozó, a Runbook-feladatok fogadása és a jelentés állapotának regisztrálásához. Ha a feldolgozó regisztrálása meghiúsul, ez a szakasz néhány lehetséges okot is tartalmaz.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Forgatókönyv: a Windows Log Analytics ügynöke nem fut

#### <a name="issue"></a>Probléma

A `healthservice` nem fut a hibrid Runbook Worker gépen.

#### <a name="cause"></a>Ok

Ha a Windows-szolgáltatás Log Analytics nem fut, a hibrid Runbook-feldolgozó nem tud kommunikálni a Azure Automationval.

#### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy az ügynök fut, ehhez írja be a következő `Get-Service healthservice`parancsot a PowerShellben:. Ha a szolgáltatás leáll, írja be a következő parancsot a PowerShellben a szolgáltatás elindításához: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Forgatókönyv: a 4502-es esemény a Operations Manager naplóban

#### <a name="issue"></a>Probléma

Az **alkalmazás és szolgáltatások Logs\Operations-kezelő** eseménynaplójában a 4502-es és a EventMessage `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` -es esemény jelenik meg a következő leírással:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Ok

Ezt a problémát az okozhatja, hogy a proxy vagy a hálózati tűzfal blokkolja a Microsoft Azure felé irányuló kommunikációt. Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a ***. Azure-Automation.net** a 443-es porton. 

#### <a name="resolution"></a>Megoldás:

A naplókat a rendszer a **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**minden hibrid feldolgozóján helyileg tárolja. Ellenőrizheti, hogy van-e figyelmeztetési vagy hibaüzenet az **alkalmazás-és Logs\Microsoft-SMA\Operations** , valamint az **alkalmazás és a szolgáltatások Logs\Operations-kezelő** eseménynaplójában. Ezek a naplók olyan kapcsolatot vagy más típusú problémát jeleznek, amely hatással van a szerepkör bevezetésére a Azure Automation, vagy a normál működés során felmerülő hiba. Ha további segítségre van a Log Analytics ügynökkel kapcsolatos problémák elhárításához, olvassa el [a log Analytics Windows-ügynökkel kapcsolatos problémák elhárítása](../../azure-monitor/platform/agent-windows-troubleshoot.md)című témakört.

A hibrid feldolgozók a [Runbook kimenetét és üzeneteit](../automation-runbook-output-and-messages.md) ugyanúgy küldik el Azure Automationnak, mint a felhőben futó Runbook-feladatok kimenetének és üzeneteinek küldése. A részletes és a folyamat-adatfolyamokat ugyanúgy engedélyezheti, mint a runbookok.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Forgatókönyv: a Orchestrator. sandbox. exe nem tud kapcsolódni az Office 365-hez proxyn keresztül

#### <a name="issue"></a>Probléma

Egy Windows Hybrid Runbook Worker-példányon futó parancsfájl nem tud a várt módon csatlakozni az Office 365-Orchestrator a homokozóban. A szkript a [MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) kapcsolatot használja a kapcsolathoz. 

Ha a **Orchestrator. sandbox. exe. config fájlt** állítja be a proxy és a megkerülési lista beállításához, a sandbox továbbra sem csatlakozik megfelelően. Úgy tűnik, hogy a várt módon működik egy **Powershell_ise. exe. config** fájl ugyanazzal a proxy-és megkerülési listával. A Service Management Automation (SMA) naplók és a PowerShell-naplók nem biztosítanak semmilyen információt a proxyval kapcsolatban.

#### <a name="cause"></a>Ok

A-kiszolgálón lévő Active Directory összevonási szolgáltatások (AD FS) (ADFS) kapcsolata nem tudja megkerülni a proxyt. Ne feledje, hogy egy PowerShell-homokozó a naplózott felhasználóként fut. Egy Orchestrator-homokozó azonban nagy mértékben testre szabott, és figyelmen kívül hagyhatja a **Orchestrator. sandbox. exe. config** fájl beállításait. Speciális kóddal rendelkezik a gép vagy az MMA-proxybeállítások kezelésére, de más egyéni proxybeállítások kezelésére nem. 

#### <a name="resolution"></a>Megoldás:

A Orchestrator-alapú sandbox problémájának megoldásához áttelepítheti a parancsfájlt az Azure AD-modulok használatára a PowerShell-parancsmagok MSOnline modulja helyett. Lásd: [áttelepítés Orchestrator-ről Azure Automationra (bétaverzió)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Ha továbbra is a MSOnline modul-parancsmagokat szeretné használni, módosítsa a parancsfájlt a [hívási parancs](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7)használatára. Értékek megadása a `ComputerName` és `Credential` paraméterek számára. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

A kód módosítása egy teljesen új PowerShell-munkamenetet indít el a megadott hitelesítő adatok kontextusában. Engedélyeznie kell a forgalmat egy olyan proxykiszolgálón keresztül, amely az aktív felhasználót hitelesíti.

>[!NOTE]
>Ez a megoldás szükségtelenné teszi a sandbox konfigurációs fájljának kezelését. Még akkor is, ha a konfigurációs fájlnak a szkripttel való használata sikeres volt, a fájl minden alkalommal törlődik a hibrid Runbook Worker Agent frissítésekor.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Forgatókönyv: a hibrid Runbook Worker nem jelent jelentést

#### <a name="issue"></a>Probléma

A hibrid Runbook Worker-feldolgozó gépe fut, de a munkaterületen nem jelenik meg szívverési információ a gépen.

A következő példában szereplő lekérdezés a munkaterületen található gépeket és az utolsó szívverését jeleníti meg:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Ok

Ezt a problémát a hibrid Runbook-feldolgozó sérült gyorsítótára okozhatja.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához jelentkezzen be a hibrid Runbook-feldolgozóba, és futtassa az alábbi szkriptet. Ez a parancsfájl leállítja a Windows Log Analytics ügynökét, eltávolítja a gyorsítótárat, és újraindítja a szolgáltatást. Ez a művelet kényszeríti a hibrid Runbook-feldolgozót, hogy újra letöltse a konfigurációját Azure Automationról.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Forgatókönyv: nem adhat hozzá hibrid Runbook-feldolgozót

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor hibrid Runbook-feldolgozót próbál hozzáadni `Add-HybridRunbookWorker` a parancsmag használatával.

```error
Machine is already registered
```

#### <a name="cause"></a>Ok

Ezt a problémát akkor lehet okozni, ha a gép már regisztrálva van egy másik Automation-fiókkal, vagy ha megpróbálta beolvasni a hibrid Runbook-feldolgozót, miután eltávolította azt egy gépről.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához távolítsa el a következő beállításkulcsot, indítsa újra `HealthService`a parancsot, majd `Add-HybridRunbookWorker` próbálja megismételni a parancsmagot.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Az Azure [@AzureSupport](https://twitter.com/azuresupport)-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal való csatlakozással javíthatja az ügyfelek élményét: válaszokat, támogatást és szakértőket.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.