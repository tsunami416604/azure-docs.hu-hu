---
title: Hibaelhárítás – Azure Automation hibrid Runbook-feldolgozók
description: Ez a cikk a hibrid Runbook-feldolgozók Azure Automationával kapcsolatos információkat tartalmaz
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 137623e4c52d24061aec8ec11fca0fc02ca54c7f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252687"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hibrid Runbook-feldolgozók hibáinak megoldása

Ez a cikk a hibrid Runbook-feldolgozókkal kapcsolatos hibák elhárításával kapcsolatos információkat tartalmaz.

## <a name="general"></a>Általános kérdések

A hibrid Runbook Worker egy ügynöktől függ, hogy hogyan regisztrálja az Automation-fiókját a feldolgozó, a Runbook-feladatok fogadása és a jelentés állapotának regisztrálásához. Windows esetén ez az ügynök a Windows Log Analytics ügynöke, más néven a Microsoft monitoring Agent (MMA). Linux esetén ez az Log Analytics-ügynök Linux rendszerhez.

### <a name="runbook-execution-fails"></a>Forgatókönyv: a Runbook végrehajtása sikertelen

#### <a name="issue"></a>Probléma

A Runbook végrehajtása sikertelen, és a következő hibaüzenet jelenik meg.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

A runbook röviddel azután fel van függesztve, hogy háromszor próbálkozik a végrehajtással. Vannak olyan feltételek, amelyek megszakítják a runbook befejezését. Előfordulhat, hogy a kapcsolódó hibaüzenet nem tartalmaz további információkat.

#### <a name="cause"></a>Ok

A lehetséges okok:

* A runbookok nem tud hitelesíteni a helyi erőforrásokkal.

* A hibrid feldolgozó proxy vagy tűzfal mögött van.

* A hibrid Runbook Worker szolgáltatás futtatására konfigurált számítógép nem felel meg a minimális hardverkövetelmények követelményeinek.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a *. azure-automation.net a 443-es porton.

A hibrid Runbook-feldolgozót futtató számítógépeknek meg kell felelniük a minimális hardverkövetelmények, mielőtt a feldolgozó konfigurálva lenne a szolgáltatás üzemeltetésére. A runbookok és az általuk használt háttérbeli folyamat okozhatja, hogy a rendszer felhasználható, és runbook a feladatok késését vagy időtúllépését okozhatja.

Győződjön meg arról, hogy a hibrid Runbook Worker szolgáltatás futtatására szolgáló számítógép megfelel a minimális hardverkövetelmények követelményeinek. Ha igen, figyelje a CPU-t és a memóriát a hibrid Runbook-munkavégző folyamatok és a Windows teljesítménye közötti korreláció megállapítása érdekében. A memória vagy a CPU nyomása az erőforrások frissítésének szükségességét jelezheti. Kiválaszthat egy másik számítási erőforrást is, amely támogatja a minimális követelményeket és a méretezést, ha a munkaterhelési igények azt jelzik, hogy növelésre van szükség.

Keresse meg a **Microsoft-SMA** eseménynaplót a megfelelő eseményhez, a Leírás *Win32 folyamata pedig a következő kóddal kilépett: [4294967295]* . Ennek a hibának az az oka, hogy nem konfigurálta a hitelesítést a runbookok, vagy megadta a hibrid Runbook Worker csoport futtató hitelesítő adatait. Tekintse át a runbook engedélyeit a [hibrid runbook-feldolgozón futó runbookok](../automation-hrw-run-runbooks.md) , és ellenőrizze, hogy helyesen konfigurálta-e a hitelesítést a runbookok.

### <a name="no-cert-found"></a>Forgatókönyv: a hibrid Runbook-feldolgozók tanúsítványtárolójában nem található tanúsítvány.

#### <a name="issue"></a>Probléma

A hibrid Runbook-feldolgozón futó runbook a következő hibaüzenettel meghiúsul.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a futtató [fiókot](../manage-runas-account.md) egy olyan runbook próbálja használni, amely olyan hibrid runbook-feldolgozón fut, amelyen a futtató fiók tanúsítványa nem található. A hibrid Runbook-feldolgozók alapértelmezés szerint nem rendelkeznek helyileg a tanúsítvánnyal, és a futtató fióknak megfelelően kell működnie.

#### <a name="resolution"></a>Megoldás:

Ha a hibrid Runbook-feldolgozó egy Azure-beli virtuális gép, akkor ehelyett [felügyelt identitásokat használhat az Azure-erőforrásokhoz](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) . Ez a forgatókönyv leegyszerűsíti a hitelesítést azáltal, hogy lehetővé teszi az Azure-erőforrások hitelesítését az Azure-beli virtuális gép felügyelt identitásával a futtató fiók helyett. Ha a hibrid Runbook Worker egy helyszíni gép, telepítenie kell a futtató fiók tanúsítványát a gépre. A tanúsítvány telepítésének megismeréséhez tekintse meg a következő témakört: a PowerShell runbook export-RunAsCertificateToHybridWorker futtatása a [Runbookok futó hibrid runbook-feldolgozón](../automation-hrw-run-runbooks.md).

### <a name="error-403-on-registration"></a>Forgatókönyv: 403-es hiba a hibrid Runbook-feldolgozók regisztrálása során

#### <a name="issue"></a>Probléma

A Worker kezdeti regisztrációs fázisa meghiúsul, és a következő hibaüzenetet kapja (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Ok

A lehetséges okok:
* Az ügynök beállításaiban nem szerepel a munkaterület-azonosító vagy a munkaterület kulcsa (elsődleges). 
* A hibrid Runbook-feldolgozó nem tudja letölteni a konfigurációt, ami egy fiók összekapcsolási hibáját okozza. Ha az Azure lehetővé teszi a megoldások használatát, csak bizonyos régiókat támogat a Log Analytics munkaterület és az Automation-fiók összekapcsolásához. Az is lehetséges, hogy helytelen dátum és/vagy idő van beállítva a számítógépen. Ha az idő +/-15 perc az aktuális időponttól, a bevezetés sikertelen lesz.

#### <a name="resolution"></a>Megoldás:

##### <a name="mistyped-workspace-idkey"></a>Nem típusos munkaterület azonosítója/kulcsa
Annak ellenőrzéséhez, hogy az ügynök munkaterület-azonosítója vagy a munkaterület kulcsa nem lett-e megadva, lásd: [munkaterület hozzáadása vagy eltávolítása](../../azure-monitor/platform/agent-manage.md#windows-agent) a Windows-ügynökhöz, vagy [munkaterület hozzáadása vagy eltávolítása](../../azure-monitor/platform/agent-manage.md#linux-agent) a Linux-ügynökhöz.  Győződjön meg arról, hogy a teljes karakterláncot kijelöli a Azure Portal, majd másolja és illessze be alaposan.

##### <a name="configuration-not-downloaded"></a>A konfiguráció nincs letöltve

A Log Analytics munkaterület és az Automation-fióknak egy összekapcsolt régióban kell lennie. A támogatott régiók listáját itt tekintheti meg: [Azure Automation és log Analytics munkaterület-hozzárendelések](../how-to/region-mappings.md).

Előfordulhat, hogy frissítenie kell a számítógép dátum-és időzónáját is. Ha egyéni időtartományt választ, győződjön meg arról, hogy a tartomány UTC-ben van, ami eltérhet a helyi időzónától.

## <a name="linux"></a>Linux

A Linux Hybrid Runbook Worker a [linuxos log Analytics-ügynöktől](../../azure-monitor/platform/log-analytics-agent.md) függ, hogy kommunikálni fog-e az Automation-fiókjával a feldolgozó, a Runbook-feladatok fogadása és a jelentés állapotának regisztrálásához. Ha a feldolgozó regisztrálása meghiúsul, a következő hiba lehetséges okai vannak:

### <a name="oms-agent-not-running"></a>Forgatókönyv: a Linux Log Analytics ügynöke nem fut

#### <a name="issue"></a>Probléma

A Linux Log Analytics ügynöke nem fut

#### <a name="cause"></a>Ok

Ha az ügynök nem fut, megakadályozza, hogy a Linux Hybrid Runbook Worker kommunikáljon Azure Automationokkal. Lehetséges, hogy az ügynök különböző okokból nem fut.

#### <a name="resolution"></a>Megoldás:

 A következő parancs beírásával ellenőrizze, hogy fut-e az ügynök: `ps -ef | grep python`. Az alábbihoz hasonló kimenetnek kell megjelennie, a Python-folyamatokat a **nxautomation** felhasználói fiókkal. Ha a Update Management vagy Azure Automation megoldások nincsenek engedélyezve, a következő folyamatok egyike sem fut.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A következő lista azokat a folyamatokat mutatja be, amelyek a Linux hibrid Runbook-feldolgozók számára lettek elindítva. Ezek mind a `/var/opt/microsoft/omsagent/state/automationworker/` könyvtárban találhatók.

* **OMS. conf** – a Worker Manager folyamata. Közvetlenül a DSC-ből indult el.

* **Worker. conf** – a hibrid munkavégző folyamat automatikus regisztrálása. A Worker Manager indította el. Ezt a folyamatot a Update Management használja, és átlátható a felhasználó számára. Ez a folyamat nem jelenik meg, ha a Update Management megoldás nincs engedélyezve a számítógépen.

* **DIY/Worker. conf** – a DIY hibrid feldolgozói folyamat. A DIY hibrid feldolgozói folyamat a hibrid Runbook-feldolgozón futtatott felhasználói runbookok végrehajtására szolgál. Ez csak az automatikus regisztrált hibrid munkavégző folyamattól tér el a legfontosabb részletességgel, amelyet más konfigurációt használ. Ez a folyamat nem jelenik meg, ha a Azure Automation megoldás le van tiltva, és a DIY Linux Hybrid Worker nincs regisztrálva.

Ha az ügynök nem fut, futtassa a következő parancsot a szolgáltatás elindításához: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Forgatókönyv: a megadott osztály nem létezik

Ha úgy látja a hibát, hogy **a megadott osztály nem létezik.** a `/var/opt/microsoft/omsconfig/omsconfig.log`a linuxos Log Analytics-ügynököt frissíteni kell. A következő parancs futtatásával telepítse újra az ügynököt:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

A Windows Hybrid Runbook Worker a [windows log Analytics ügynöktől](../../azure-monitor/platform/log-analytics-agent.md) függ, hogy kommunikálni tudjon az Automation-fiókjával a feldolgozó, a Runbook-feladatok fogadása és a jelentés állapotának regisztrálásához. Ha a feldolgozó regisztrálása meghiúsul, a következő hiba lehetséges okai vannak:

### <a name="mma-not-running"></a>Forgatókönyv: a Microsoft monitoring Agent nem fut.

#### <a name="issue"></a>Probléma

A `healthservice` szolgáltatás nem fut a hibrid Runbook Worker gépen.

#### <a name="cause"></a>Ok

Ha a Microsoft monitoring Agent Microsoft szolgáltatás nem fut, ez az állapot megakadályozza, hogy a hibrid Runbook-feldolgozó kommunikáljon a Azure Automationkal.

#### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy fut-e az ügynök a következő parancs megadásával a PowerShellben: `Get-Service healthservice`. Ha a szolgáltatás leáll, írja be a következő parancsot a PowerShellben a szolgáltatás elindításához: `Start-Service healthservice`.

### <a name="event-4502"></a>Forgatókönyv: a 4502-es esemény a Operations Manager naplóban

#### <a name="issue"></a>Probléma

Az **Application and Services Logs\Operations-kezelő** eseménynaplójában a **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. HybridAgent** a következő leírással rendelkező 4502-es és EventMessage-os esemény jelenik meg: *a szolgáltatás által bemutatott \<wsid\>. OMS.opinsights.Azure.com nem a Microsoft-szolgáltatásokhoz használt hitelesítésszolgáltató állította ki. Forduljon a hálózati rendszergazdához, és ellenőrizze, hogy fut-e olyan proxy, amely elfogja a TLS/SSL-kommunikációt.*

#### <a name="cause"></a>Ok

Ezt a problémát az okozhatja, hogy a proxy vagy a hálózati tűzfal blokkolja a Microsoft Azure felé irányuló kommunikációt. Ellenőrizze, hogy a számítógép rendelkezik-e kimenő hozzáféréssel a *. azure-automation.net a 443-es portokon. 

#### <a name="resolution"></a>Megoldás:

A naplók tárolása helyileg történik minden hibrid feldolgozón a következő helyen: C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Ellenőrizze, hogy vannak-e olyan figyelmeztetési vagy hibaüzenetek az **alkalmazás és szolgáltatások Logs\Microsoft-SMA\Operations** , valamint az **alkalmazás és szolgáltatások Logs\Operations-kezelő** eseménynaplójában, amely egy kapcsolatot vagy más olyan problémát jelez, amely hatással van a szerepkör bevezetésére Azure Automation vagy probléma esetén a normál műveletek során. Ha további segítségre van a Log Analytics ügynökkel kapcsolatos problémák elhárításához, olvassa el [a log Analytics Windows-ügynökkel kapcsolatos problémák elhárítása](../../azure-monitor/platform/agent-windows-troubleshoot.md)című témakört.

A [Runbook kimenetét és üzeneteit](../automation-runbook-output-and-messages.md) a hibrid feldolgozók Azure Automation kapják, ugyanúgy, mint a felhőben futó Runbook-feladatok. A részletes és a folyamatjelző adatfolyamokat ugyanúgy is engedélyezheti, mint a többi runbookok.

### <a name="corrupt-cache"></a>Forgatókönyv: a hibrid Runbook Worker nem jelent jelentést

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

A probléma megoldásához jelentkezzen be a hibrid Runbook-feldolgozóba, és futtassa az alábbi szkriptet. Ez a szkript leállítja a Microsoft monitoring agentet, eltávolítja a gyorsítótárat, és újraindítja a szolgáltatást. Ez a művelet kényszeríti a hibrid Runbook-feldolgozót, hogy újra letöltse a konfigurációját Azure Automationról.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Forgatókönyv: nem adhat hozzá hibrid Runbook-feldolgozót

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor hibrid Runbook-feldolgozót próbál hozzáadni a `Add-HybridRunbookWorker` parancsmag használatával.

```error
Machine is already registered
```

#### <a name="cause"></a>Ok

Ezt a problémát akkor lehet okozni, ha a gép már regisztrálva van egy másik Automation-fiókkal, vagy ha megpróbálta beolvasni a hibrid Runbook-feldolgozót, miután eltávolította azt egy gépről.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához távolítsa el a következő beállításkulcsot, és indítsa újra a `HealthService`, majd próbálja megismételni a `Add-HybridRunbookWorker` parancsmagot:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.