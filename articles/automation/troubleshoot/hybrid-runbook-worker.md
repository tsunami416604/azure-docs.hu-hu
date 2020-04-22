---
title: Az Azure Automation hibrid runbook-feldolgozóinak hibaelhárítása
description: Ez a cikk az Azure Automation hibrid runbook-feldolgozóinak hibaelhárításával kapcsolatos információkat tartalmaz.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2b3bf6706e977bdb6915335dee59da3c250e7895
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679338"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hibrid Runbook-dolgozók – problémamegoldás

Ez a cikk a hibrid runbook-feldolgozókkal kapcsolatos problémák elhárításáról nyújt tájékoztatást.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="general"></a>Általános kérdések

A hibrid Runbook-feldolgozó egy ügynöktől függ, hogy kommunikáljon az Automation-fiókkal a dolgozó regisztrálásához, a runbook-feladatok fogadásához és a jelentés állapotának jelentéséhez. A Windows esetében ez az ügynök a Windows Log Analytics-ügynöke. Linux esetén ez a Log Analytics ügynök Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Eset: A Runbook végrehajtása sikertelen

#### <a name="issue"></a>Probléma

A Runbook végrehajtása sikertelen, és a következő hibaüzenet jelenik meg.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

A runbook röviddel azután, hogy háromszor kísérletet tett a végrehajtásra, fel van függesztve. Vannak olyan feltételek, amelyek megszakíthatják a runbook befejezését. Előfordulhat, hogy a kapcsolódó hibaüzenet nem tartalmaz további információkat.

#### <a name="cause"></a>Ok

A következő lehetséges okok lehetnek:

* A runbookok nem hitelesíthetők a helyi erőforrásokkal.
* A hibrid feldolgozó egy proxy vagy tűzfal mögött van.
* A hibrid Runbook-feldolgozó futtatására konfigurált számítógép nem felel meg a minimális hardverkövetelményeknek.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a 443-as porton a ***.azure-automation.net.azure-automation.net.-hez.**

A hibrid Runbook-feldolgozót futtató számítógépeknek meg kell felelniük a minimális hardverkövetelményeknek, mielőtt a dolgozó konfigurálva van a szolgáltatás üzemeltetésére. A Runbookok és az általuk használt háttérfolyamat a rendszer túlzott használatát okozhatja, és a runbook-feladatok késleltetését vagy időtúlterhelését okozhatja.

Győződjön meg arról, hogy a hibrid Runbook-feldolgozó szolgáltatás futtatásához szükséges számítógép megfelel a minimális hardverkövetelményeknek. Ha igen, figyelje a processzor- és memóriahasználatot a hibrid Runbook-feldolgozó folyamatok és a Windows teljesítménye közötti korreláció meghatározásához. Bármilyen memória- vagy PROCESSZORnyomás jelezheti az erőforrások frissítésének szükségességét. Kiválaszthat egy másik számítási erőforrást is, amely támogatja a minimális követelményeket, és méretezheti, ha a munkaterhelés-igények azt jelzik, hogy növekedésre van szükség.

Ellenőrizze a **Microsoft-SMA** eseménynaplójában a `Win32 Process Exited with code [4294967295]`megfelelő eseményt a leírással együtt. A hiba oka az, hogy nem konfigurálta a hitelesítést a runbookokban, vagy megadta a Futtatás hitelesítő adatok at a hibrid Runbook feldolgozó csoporthoz. Tekintse át a runbook-engedélyeket [a runbookok futtatása hibrid Runbook-feldolgozón](../automation-hrw-run-runbooks.md) című futtatása során, és ellenőrizze, hogy megfelelően konfigurálta-e a hitelesítést a runbookokhoz.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Eset: 15011-es esemény a hibrid Runbook-feldolgozóban

#### <a name="issue"></a>Probléma

A hibrid Runbook Worker megkapja az 15011-es eseményt, jelezve, hogy a lekérdezés eredménye érvénytelen. A következő hiba jelenik meg, amikor a dolgozó megpróbál kapcsolatot nyitni a [SignalR-kiszolgálóval](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó nincs megfelelően konfigurálva az automatikus központi telepítési megoldáshoz. Ez a megoldás tartalmaz egy alkatrészt, amely összeköti a virtuális gép a Log Analytics munkaterülettel. A PowerShell-parancsfájl a megadott névvel rendelkező előfizetés munkaterületét keresi. Ebben az esetben a Log Analytics munkaterület egy másik előfizetésben van. A parancsfájl nem találja a munkaterületet, és megpróbál létrehozni egyet, de a név már foglalt. Így az üzembe helyezés sikertelen lesz.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához két lehetőség közül választhat:

* Módosítsa a PowerShell-parancsfájlt a Log Analytics-munkaterület egy másik előfizetésben való megkereséséhez. Ez egy jó megoldás, ha azt tervezi, hogy a jövőben számos hibrid Runbook-feldolgozó gépet telepít.

* Manuálisan konfigurálja a feldolgozógépet, hogy egy Orchestrator sandbox ban fusson. Ezután futtassa az Azure Automation-fiókban létrehozott runbookot a dolgozón a funkció teszteléséhez.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Eset: A Windows Azure virtuális gépek automatikusan kikerülnek a hibrid feldolgozócsoportból

#### <a name="issue"></a>Probléma

Nem láthatja a hibrid Runbook-feldolgozó t vagy a virtuális gépeket, ha a feldolgozógép már régóta ki van kapcsolva.

#### <a name="cause"></a>Ok

A hibrid Runbook-feldolgozó gép több mint 30 napja nem pingelte az Azure Automationt. Ennek eredményeképpen az Automation törölte a hibrid Runbook-feldolgozó csoportot vagy a Rendszerfeldolgozó csoportot. 

#### <a name="resolution"></a>Megoldás:

Indítsa el a feldolgozógépet, majd regisztrálja újra az Azure Automation segítségével. Tekintse meg a runbook-környezet telepítésére és az Azure Automationhez való csatlakozásra vonatkozó utasításokat [a Windows hibrid runbook-feldolgozó telepítése kor.](../automation-windows-hrw-install.md)

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Eset: Nem található tanúsítvány a hibrid runbook-feldolgozó tanúsítványtárolójában

#### <a name="issue"></a>Probléma

A hibrid runbook-feldolgozón futó runbook a következő hibaüzenettel sikertelen.

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha egy [Run As fiókot](../manage-runas-account.md) próbál használni egy olyan runbookban, amely egy hibrid Runbook-feldolgozón fut, ahol a Futtatás másként fióktanúsítvány nincs jelen. Hibrid Runbook-feldolgozók alapértelmezés szerint nem rendelkeznek a tanúsítvány-eszközzel. A Futtatás másként fiók megköveteli, hogy ez az eszköz megfelelően működjön.

#### <a name="resolution"></a>Megoldás:

Ha a hibrid Runbook-feldolgozó egy Azure-beli virtuális gép, [használhatja a felügyelt identitások az Azure-erőforrások](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) helyett. Ez a forgatókönyv leegyszerűsíti a hitelesítést azáltal, hogy lehetővé teszi az Azure-erőforrások hitelesítését az Azure virtuális gép felügyelt identitásával a Futtatás másként fiók helyett. Ha a hibrid Runbook-feldolgozó egy helyszíni gép, telepítenie kell a Futtatás mint fiók tanúsítványt a számítógépen. A tanúsítvány telepítéséről a **PowerShell-runbook Export-RunAsCertificateToHybridWorker** című runbook futtatásához szükséges lépésekből megtudhatja, hogy miként futtathatja a [hibrid runbook-feldolgozót.](../automation-hrw-run-runbooks.md)

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Eset: 403-as hiba a hibrid Runbook-feldolgozó regisztrálása során

#### <a name="issue"></a>Probléma

A dolgozó kezdeti regisztrációs fázisa sikertelen, és a következő hibaüzenet (403) jelenik meg.

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Ok

A következő lehetséges okok lehetnek:

* Az ügynök beállításaiban van egy elgépelt munkaterület-azonosító vagy munkaterületkulcs (elsődleges). 

* A hibrid Runbook-feldolgozó nem tudja letölteni a konfigurációt, ami fiókcsatolási hibát okoz. Ha az Azure engedélyezi a megoldásokat, csak bizonyos régiókat támogat a Log Analytics-munkaterület és az Automation-fiók összekapcsolásához. Az is lehetséges, hogy helytelen dátum és/vagy idő van beállítva a számítógépen. Ha az idő +/-15 percre van az aktuális időtől, a bevezetés sikertelen lesz.

#### <a name="resolution"></a>Megoldás:

##### <a name="mistyped-workspace-idkey"></a>Elgépelt munkaterület-azonosító/kulcs
Annak ellenőrzéséhez, hogy az ügynök munkaterület-azonosítója vagy munkaterületi kulcsa ellett-e gépelve, olvassa el a [Munkaterület hozzáadása vagy eltávolítása – Windows-ügynök](../../azure-monitor/platform/agent-manage.md#windows-agent) a Windows-ügynökhöz vagy munkaterület hozzáadása vagy eltávolítása – [Linux-ügynök](../../azure-monitor/platform/agent-manage.md#linux-agent) a Linux-ügynökhöz című témakört.  Győződjön meg arról, hogy válassza ki a teljes karakterláncot az Azure Portalon, és másolja be óvatosan.

##### <a name="configuration-not-downloaded"></a>A konfiguráció nincs letöltve

A Log Analytics-munkaterületi és Automation-fióknak egy összekapcsolt régióban kell lennie. A támogatott régiók listáját az [Azure Automation és a Log Analytics munkaterület-leképezései című témakörben található.](../how-to/region-mappings.md)

Előfordulhat, hogy frissítenie kell a számítógép dátumát és/vagy időzónáját is. Ha egyéni időtartományt választ, győződjön meg arról, hogy a tartomány UTC-ben van, ami eltérhet a helyi időzónától.

## <a name="linux"></a>Linux

A Linux-hibrid Runbook-feldolgozó a Ttól függ, hogy a [Log Analytics-ügynök linuxos](../../azure-monitor/platform/log-analytics-agent.md) kommunikálni az Automation-fiókkal a dolgozó regisztrálásához, a Runbook-feladatok fogadásához és a jelentés állapotának jelentéséhez. Ha a dolgozó regisztrációja sikertelen, a következő oka lehet a hibának:

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Eset: A Log Analytics-ügynök Linuxhoz nem fut

#### <a name="issue"></a>Probléma

A Linuxos Log Analytics-ügynök nem fut

#### <a name="cause"></a>Ok

Ha az ügynök nem fut, megakadályozza, hogy a Linux hibrid Runbook-feldolgozó kommunikáljon az Azure Automation. Előfordulhat, hogy az ügynök különböző okok miatt nem fut.

#### <a name="resolution"></a>Megoldás:

 Ellenőrizze, hogy fut-e `ps -ef | grep python`az ügynök a parancs beírásával. Az nxautomation felhasználói fiókkal rendelkező Python-folyamatok a következőhöz hasonló kimenetet kell **látnia.** Ha az Update Management vagy az Azure Automation-megoldás nincs engedélyezve, az alábbi folyamatok egyike sem fut.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Az alábbi lista a Linux hibrid Runbook-feldolgozó elindított folyamatokat mutatja be. Ezek mind a **/var/opt/microsoft/omsagent/state/automationworker/** könyvtárban találhatók.

* **oms.conf** - A dolgozói menedzser folyamat. Közvetlenül a DSC-től indult.

* **worker.conf** - Az automatikusan regisztrált hibrid munkavégző folyamat. A dolgozó igazgató indította el. Ezt a folyamatot az Update Management használja, és a felhasználó számára átlátható. Ez a folyamat nem jelenik meg, ha az Update Management megoldás nincs engedélyezve a számítógépen.

* **diy/worker.conf** - A DIY hibrid munkafolyamat. A diy hibrid munkavégző folyamat a hibrid runbookok futtatásához használható a hibrid Runbook worker. Ez csak abban különbözik az automatikusan regisztrált hibrid munkavégző folyamat a legfontosabb részleteket, hogy más konfigurációt használ. Ez a folyamat nincs jelen, ha az Azure Automation-megoldás le van tiltva, és a DIY Linux hibrid feldolgozó nincs regisztrálva.

Ha az ügynök nem fut, futtassa a `sudo /opt/microsoft/omsagent/bin/service_control restart`következő parancsot a szolgáltatás elindításához: .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Eset: A megadott osztály nem létezik

Ha a hibát `The specified class does not exist..` a **/var/opt/microsoft/omsconfig/omsconfig.log**fájlban látja, a Linuxalapú Log Analytics-ügynököt frissíteni kell. Az ügynök újratelepítéséhez futtassa a következő parancsot:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

A Windows hibrid Runbook-feldolgozó a [Windows Log Analytics-ügynöktől](../../azure-monitor/platform/log-analytics-agent.md) függ, hogy kommunikáljon az Automation-fiókkal a dolgozó regisztrálásához, a runbook-feladatok fogadásához és a jelentés állapotának jelentéséhez. Ha a dolgozó regisztrációja sikertelen, ez a szakasz néhány lehetséges okot tartalmaz.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Eset: A Windows Log Analytics-ügynöke nem fut

#### <a name="issue"></a>Probléma

A `healthservice` nem fut a hibrid Runbook feldolgozó gépen.

#### <a name="cause"></a>Ok

Ha a Windows-napló szolgáltatás nem fut, a hibrid Runbook-feldolgozó nem tud kommunikálni az Azure Automation használatával.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy az ügynök fut-e, ha `Get-Service healthservice`beírja a következő parancsot a PowerShellben: . Ha a szolgáltatás le áll, írja be a következő `Start-Service healthservice`parancsot a PowerShell beindításához: .

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Eset: 4502-es esemény az Operations Manager naplójában

#### <a name="issue"></a>Probléma

Az **Alkalmazás- és szolgáltatásnaplók\Operations Manager** eseménynaplóban a 4502-es esemény és az EventMessage a következő leírással jelenik meg: `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Ok

Ezt a problémát okozhatja a proxy vagy a hálózati tűzfal blokkolja a Microsoft Azure-ral folytatott kommunikációt. Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a 443-as porton a ***.azure-automation.net.azure-automation.net.-hez.** 

#### <a name="resolution"></a>Megoldás:

A naplókat a **c:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes mappában**tárolja a rendszer. Ellenőrizheti, hogy vannak-e figyelmeztető vagy hibaesemények az **Alkalmazás- és szolgáltatásnaplókban\Microsoft-SMA\Operations** and **Application and Services Logs\Operations Manager** eseménynaplók. Ezek a naplók olyan kapcsolatot vagy más típusú problémát jeleznek, amely hatással van a szerepkör Azure Automation-be történő bevezetésre, vagy egy normál működés során észlelt problémát. A Log Analytics-ügynökkel kapcsolatos problémák elhárításához a [Log Analytics Windows-ügynökkel kapcsolatos problémák elhárítása című témakörben talál további](../../azure-monitor/platform/agent-windows-troubleshoot.md)segítséget.

A hibrid dolgozók [runbook-kimenetet és üzeneteket](../automation-runbook-output-and-messages.md) küldenek az Azure Automationbe ugyanúgy, ahogy a felhőben futó runbook-feladatok kimenetet és üzeneteket küldenek. Engedélyezheti a részletes és a folyamatfolyamok, mint te a runbookok.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Eset: Az Orchestrator.Sandbox.exe nem tud proxyn keresztül csatlakozni az Office 365-höz

#### <a name="issue"></a>Probléma

A Windows hibrid runbook-feldolgozón futó parancsfájlok nem tudnak a várt módon csatlakozni az Office 365-höz egy Orchestrator sandboxon. A parancsfájl [connect-MsolService kapcsolatot](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) használ. 

Ha az **Orchestrator.Sandbox.exe.config** fájl beállításával állítja be a proxyt és a megkerülő listát, a sandbox továbbra sem csatlakozik megfelelően. Úgy tűnik, hogy a **Powershell_ise.exe.config** fájl ugyanazzal a proxy- és megkerülési listabeállítással működik, mint ahogy azt elvárja. A Szolgáltatásfelügyeleti automatizálás (SMA) naplói és a PowerShell-naplók nem nyújtanak semmilyen információt a proxyval kapcsolatban.

#### <a name="cause"></a>Ok

A kiszolgálón lévő Active Directory összevonási szolgáltatásokkal (ADFS) létesítő kapcsolat nem tudja megkerülni a proxyt. Ne feledje, hogy a PowerShell-sandbox a bejelentkezett felhasználóként fut. Az Orchestrator sandbox a testre szabott, és figyelmen kívül hagyhatja az **Orchestrator.Sandbox.exe.config** fájl beállításait. Ez birtokol speciális kód részére kezelés gép vagy MMA helyettes elintézés, de nem részére kezelés más szokás helyettes elintézés. 

#### <a name="resolution"></a>Megoldás:

Az Orchestrator sandbox probléma megoldásához a parancsfájl áttelepítésével az Azure AD-modulok használata helyett az MSOnline modul PowerShell-parancsmagok. Lásd: [Áttelepítés az Orchestratorról az Azure Automationbe (Béta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Ha továbbra is használni szeretné az MSOnline modul parancsmagjait, módosítsa a parancsfájlt [az Invoke-Command parancs használatára.](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) Adja meg `ComputerName` a `Credential` és a paraméterek értékeit. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Ez a kódmódosítás egy teljesen új PowerShell-munkamenetet indít el a megadott hitelesítő adatok környezetében. Lehetővé kell tennie, hogy a forgalom az aktív felhasználót hitelesítő proxykiszolgálón keresztül folyjon.

>[!NOTE]
>Ez a megoldás szükségtelenné teszi a sandbox konfigurációs fájl ának manipulálását. Még akkor is, ha sikerül a konfigurációs fájl működését a parancsfájlt, a fájl törlődik minden alkalommal, amikor a hibrid Runbook worker ügynök frissül.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Eset: Hibrid Runbook Feldolgozó nem jelenti

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozó gép fut, de nem látja a szívverés adatait a gép a munkaterületen.

A következő példalekérdezés a gépeket mutatja egy munkaterületen és az utolsó szívverésüket:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Ok

Ezt a problémát okozhatja a hibrid Runbook-feldolgozó sérült gyorsítótára.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához jelentkezzen be a hibrid Runbook-feldolgozóba, és futtassa a következő parancsfájlt. Ez a parancsfájl leállítja a Windows Log Analytics-ügynökét, eltávolítja a gyorsítótárat, és újraindítja a szolgáltatást. Ez a művelet arra kényszeríti a hibrid Runbook-feldolgozót, hogy töltse le újra a konfigurációját az Azure Automationből.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Eset: Hibrid Runbook-feldolgozó nem adhat hozzá

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor hibrid `Add-HybridRunbookWorker` Runbook-feldolgozót próbál hozzáadni a parancsmag használatával.

```error
Machine is already registered
```

#### <a name="cause"></a>Ok

Ez a probléma akkor jelentkezhet, ha a gép már regisztrálva van egy másik Automation-fiókkal, vagy ha megpróbálja olvasni a hibrid Runbook-feldolgozót, miután eltávolította azt egy gépről.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához távolítsa el a `HealthService`következő beállításkulcsot, indítsa újra a t, majd próbálkozzon újra a `Add-HybridRunbookWorker` parancsmaggal.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>További lépések

Ha a fenti probléma nem jelenik meg, vagy nem tudja megoldani a problémát, próbálkozzon az alábbi csatornák egyikével további támogatásért:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**