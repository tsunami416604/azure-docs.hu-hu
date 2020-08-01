---
title: Linuxos hibrid Runbook-feldolgozó üzembe helyezése Azure Automation
description: Ez a cikk azt ismerteti, hogyan telepíthet egy Azure Automation hibrid Runbook-feldolgozót a runbookok Linux-alapú gépeken való futtatásához a helyi adatközpontban vagy a felhőalapú környezetben.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 7f19aec65ed2616d757718116ac948473dd4b0ed
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448016"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker üzembe helyezése

A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető gépen és a környezet erőforrásain keresztül futtathatja a helyi erőforrások kezeléséhez. A Linux Hybrid Runbook Worker a runbookok-t olyan speciális felhasználóként hajtja végre, amely emelt szintű jogosultságszint-emelést igénylő parancsok futtatására használható. A Azure Automation a runbookok tárolja és felügyeli, majd egy vagy több kijelölt gépre továbbítja azokat. Ez a cikk bemutatja, hogyan telepítheti a hibrid Runbook-feldolgozót egy Linux rendszerű gépre, hogyan távolíthatja el a feldolgozót, és hogyan távolíthat el egy hibrid Runbook-feldolgozói csoportot.

A runbook-feldolgozó sikeres üzembe helyezése után tekintse át a [Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md) című témakört, amelyből megtudhatja, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel:

### <a name="a-log-analytics-workspace"></a>Log Analytics munkaterület

A hibrid Runbook-feldolgozói szerepkör a szerepkör telepítéséhez és konfigurálásához Azure Monitor Log Analytics munkaterülettől függ. [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), a [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)vagy a [Azure Portal](../azure-monitor/learn/quick-create-workspace.md)segítségével hozhatja létre.

Ha nem rendelkezik Azure Monitor Log Analytics munkaterülettel, tekintse át a [Azure monitor log tervezési útmutatót](../azure-monitor/platform/design-logs-deployment.md) a munkaterület létrehozása előtt.

Ha van munkaterülete, de nem kapcsolódik az Automation-fiókjához, az automatizálási funkció lehetővé teszi a Azure Automation funkcióinak hozzáadását, beleértve a hibrid Runbook-feldolgozó támogatását is. Ha engedélyezi a Log Analytics munkaterület Azure Automation funkciójának egyikét, különösen a [Update Management](update-management/update-mgmt-overview.md) vagy a [change Tracking és a leltárt](change-tracking.md), a rendszer automatikusan leküldi a munkavégző összetevőket az ügynök számítógépére.

A Update Management szolgáltatás munkaterülethez való hozzáadásához futtassa a következő PowerShell-parancsmagot:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

A Change Tracking és a leltár funkciónak a munkaterülethez való hozzáadásához futtassa a következő PowerShell-parancsmagot:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics-ügynök

A hibrid Runbook feldolgozói szerepkörhöz a [log Analytics ügynök](../azure-monitor/platform/log-analytics-agent.md) szükséges a támogatott Linux operációs rendszerhez.

### <a name="supported-linux-operating-systems"></a>Támogatott linuxos operációs rendszerek

A hibrid Runbook Worker szolgáltatás a következő disztribúciókat támogatja:

* Amazon Linux 2012,09 – 2015,09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)
* Oracle Linux 5, 6 és 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 és 7 (x86/x64)
* Debian GNU/Linux 6, 7 és 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS és 18,04 (x86/x64)
* 11 és 12 SUSE Linux Enterprise Server (x86/x64)

### <a name="minimum-requirements"></a>Minimális követelmények

A Linux hibrid Runbook-feldolgozók minimális követelményei a következők:

* Két mag
* 4 GB RAM
* 443-es port (kimenő)

| **Szükséges csomag** | **Leírás** | **Minimális verzió**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C könyvtár| 2.5-12 |
|Openssl| OpenSSL-kódtárak | 1,0 (a TLS 1,1 és a TLS 1,2 támogatott)|
|Curl | cURL webes ügyfél | 7.15.5|
|Python – ctypes | A Python 2. x megadása kötelező |
|PAM | Cserélhető hitelesítési modulok|
| **Választható csomag** | **Leírás** | **Minimális verzió**|
| PowerShell Core | A PowerShell-runbookok futtatásához telepíteni kell a PowerShell Core-t. A telepítésének megismeréséhez lásd: [a PowerShell Core telepítése Linux rendszeren](/powershell/scripting/install/installing-powershell-core-on-linux) . | 6.0.0 |

## <a name="supported-runbook-types"></a>Támogatott runbook-típusok

A linuxos hibrid Runbook-feldolgozók korlátozott számú Runbook-típust támogatnak Azure Automationban, és a következő táblázatban vannak leírva.

|Runbook típusa | Támogatott |
|-------------|-----------|
|Python 2 |Igen |
|PowerShell |Igen<sup>1</sup> |
|PowerShell-munkafolyamat |Nem |
|Grafikus |Nem |
|Grafikus PowerShell-munkafolyamat |Nem |

<sup>1</sup> A PowerShell-runbookok a PowerShell Core-t kell telepíteni a Linux rendszerű gépen. A telepítésének megismeréséhez lásd: [a PowerShell Core telepítése Linux rendszeren](/powershell/scripting/install/installing-powershell-core-on-linux) .

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker telepítése

A Linux Hybrid Runbook Worker telepítéséhez és konfigurálásához hajtsa végre az alábbi lépéseket.

1. Telepítse a Log Analytics ügynököt a célszámítógépen.

    * Az Azure-beli virtuális gépek esetében telepítse a Linux rendszerhez készült Log Analytics-ügynököt a [linuxos virtuálisgép-bővítmény](../virtual-machines/extensions/oms-linux.md)használatával. A bővítmény telepíti a Log Analytics ügynököt az Azure Virtual Machines szolgáltatásban, és egy Azure Resource Manager sablon vagy az Azure CLI használatával regisztrálja a virtuális gépeket egy meglévő Log Analytics-munkaterületen. Miután telepítette az ügynököt, a virtuális gép hozzáadhatók az Automation-fiókjában lévő hibrid Runbook Worker csoportjához.

    * A nem Azure-beli virtuális gépek esetében telepítse a Linux rendszerhez készült Log Analytics-ügynököt a [Linux rendszerű számítógépek összekötése a Azure monitor](../azure-monitor/platform/agent-linux.md) cikkhez című cikkben ismertetett telepítési beállításokkal. Ezt a folyamatot több gép esetében is megismételheti, ha több feldolgozót szeretne hozzáadni a környezethez. Az ügynök telepítése után a virtuális gépek hozzáadhatók az Automation-fiókjában lévő hibrid Runbook Worker csoportjához.

    > [!NOTE]
    > A hibrid Runbook-feldolgozói szerepkört a kívánt állapot-konfigurációval (DSC) támogató gépek konfigurációjának kezeléséhez a gépeket DSC-csomópontként kell hozzáadnia.

    > [!NOTE]
    > A Linux Hybrid Worker telepítése során a megfelelő sudo engedélyekkel rendelkező [nxautomation-fióknak](automation-runbook-execution.md#log-analytics-agent-for-linux) jelen kell lennie. Ha megpróbálja telepíteni a munkavégzőt, és a fiók nem létezik, vagy nem rendelkezik a megfelelő engedélyekkel, a telepítés sikertelen lesz.

2. Ellenőrizze, hogy az ügynök jelentést tesz-e a munkaterületre

    A Linux Log Analytics-ügynöke csatlakoztatja a gépeket egy Azure Monitor Log Analytics-munkaterülethez. Amikor telepíti az ügynököt a gépre, és összekapcsolja a munkaterülettel, automatikusan letölti a hibrid Runbook-feldolgozóhoz szükséges összetevőket.

    Ha az ügynök néhány perc elteltével sikeresen csatlakozott a Log Analytics munkaterülethez, a következő lekérdezés futtatásával ellenőrizheti, hogy a rendszer a szívverési adatokat küldi a munkaterületre.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    A keresési eredmények között meg kell jelennie a gép szívverési rekordjainak, ami azt jelzi, hogy csatlakoztatva van, és a szolgáltatáshoz jelent jelentést. Alapértelmezés szerint minden ügynök egy szívverési rekordot továbbít a hozzárendelt munkaterülethez.

3. A következő parancs futtatásával vegye fel a gépet egy hibrid Runbook Worker-csoportba, adja meg a paraméterek, a, és a értékeit `-w` `-k` `-g` `-e` .

    A paraméterekhez `-k` és az `-e` Automation-fiók **kulcsok** lapjáról kérheti le a szükséges információkat. A lap bal oldalán található **Fiókbeállítások** szakaszban válassza a **kulcsok** lehetőséget.

    ![Kulcsok kezelése lap](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * A `-e` paraméter esetében másolja az **URL-cím**értékét.

    * A `-k` paraméter esetében másolja az **elsődleges elérési kulcs**értékét.

    * A `-g` paraméternél adja meg annak a hibrid Runbook-feldolgozó csoportnak a nevét, amelyhez az új Linux Hybrid Runbook Worker csatlakoznia kell. Ha ez a csoport már létezik az Automation-fiókban, az aktuális gép hozzá lesz adva. Ha ez a csoport nem létezik, akkor a rendszer ezt a nevet hozza létre.

    * A `-w` paraméternél adja meg a log Analytics-munkaterület azonosítóját.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. A parancs befejezése után az Automation-fiókjában lévő hibrid feldolgozói csoportok lap az új csoportot és a tagok számát jeleníti meg. Ha ez egy meglévő csoport, a tagok száma nő. Válassza ki a csoportot a hibrid munkavégző csoportok lapon a listából, és válassza a **hibrid feldolgozók** csempét. A hibrid dolgozók oldalon láthatja a csoport egyes tagjait.

    > [!NOTE]
    > Ha a Linux rendszerhez készült Log Analytics virtuálisgép-bővítményt használja az Azure-beli virtuális gépekhez, javasoljuk, `autoUpgradeMinorVersion` hogy a `false` verzió automatikus verziófrissítésének beállítása a hibrid Runbook-feldolgozóval kapcsolatos problémákat okozzon. A bővítmény manuális frissítéséről az [Azure CLI üzembe helyezésével](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)foglalkozó témakörben olvashat bővebben.

## <a name="turn-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása

A Linux Hybrid Runbook-feldolgozók alapértelmezés szerint aláírás-ellenőrzést igényelnek. Ha aláíratlan runbook futtat egy feldolgozón, hibaüzenet jelenik meg `Signature validation failed` . Az aláírás-ellenőrzés kikapcsolásához futtassa a következő parancsot. Cserélje le a második paramétert a Log Analytics munkaterület-azonosítójával.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>Hibrid Runbook-feldolgozó eltávolítása egy helyszíni linuxos gépről

A `ls /var/opt/microsoft/omsagent` hibrid Runbook-feldolgozón a parancs használatával kérheti le a munkaterület azonosítóját. A rendszer létrehoz egy nevű mappát a munkaterület-AZONOSÍTÓval.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ez a szkript nem távolítja el a Linux rendszerhez készült Log Analytics-ügynököt a gépről. Csak a hibrid Runbook-feldolgozói szerepkör funkcióit és konfigurációját távolítja el.

## <a name="remove-a-hybrid-worker-group"></a>Hibridfeldolgozó-csoport eltávolítása

Linux rendszerű gépek hibrid Runbook Worker csoportjának eltávolításához ugyanazokat a lépéseket kell használnia, mint a Windows Hybrid Worker-csoport esetében. Lásd: [hibrid feldolgozói csoport eltávolítása](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.

* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása – Linux](troubleshoot/hybrid-runbook-worker.md#linux).
