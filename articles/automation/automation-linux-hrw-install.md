---
title: Az Azure Automation hibrid runbook-feldolgozója Linuxra
description: Ez a cikk az Azure Automation hibrid runbook-feldolgozó telepítésével kapcsolatban tartalmaz információkat, így futtassa a runbookokat Linux-alapú számítógépeken a helyi adatközpontban vagy a felhőkörnyezetben.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: f2584a8d4e68b7c16b3acdc29f64f0a19d83d735
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457671"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linuxos hibrid runbook-feldolgozó üzembe helyezése

Az Azure Automation hibrid Runbook-feldolgozó szolgáltatásával runbookokat futtathat közvetlenül a szerepkört üzemeltető számítógépen és a környezetben lévő erőforrásokkal a helyi erőforrások kezeléséhez. A Linux hibrid Runbook Worker végrehajtja runbookok, mint egy speciális felhasználó, amely emelt szintű a magasságot igénylő parancsok futtatásához. A Runbookok tárolása és kezelése az Azure Automationben van tárolva, majd egy vagy több kijelölt számítógépre leszállítható.

Ez a cikk ismerteti, hogyan telepítheti a hibrid Runbook-feldolgozó egy Linux-gépen.

## <a name="supported-linux-operating-systems"></a>Támogatott linuxos operációs rendszerek

A hibrid Runbook-feldolgozó szolgáltatás a következő disztribúciókat támogatja:

* Amazon Linux 2012.09 - 2015.09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)
* Oracle Linux 5, 6 és 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 és 7 (x86/x64)
* Debian GNU/Linux 6, 7 és 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS és 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 és 12 (x86/x64)

## <a name="supported-runbook-types"></a>Támogatott runbook-típusok

A Linux-hibrid Runbook-feldolgozók nem támogatják az Azure Automation teljes runbook-típuskészletét.

A következő runbook-típusok működnek egy Linux-hibrid feldolgozón:

* Piton 2
* PowerShell

  > [!NOTE]
  > A PowerShell-runbookok megkövetelik a PowerShell Core linuxos gépen való telepítését. A [PowerShell Core linuxos telepítése](/powershell/scripting/install/installing-powershell-core-on-linux) című témakörből megtudhatja, hogyan telepítheti azt.

A következő runbook-típusok nem működnek linuxos hibrid feldolgozókon:

* PowerShell-munkafolyamat
* Grafikus
* Grafikus PowerShell-munkafolyamat

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Linuxos hibrid Runbook-feldolgozó telepítése

Hibrid Runbook-feldolgozó linuxos számítógépen való telepítéséhez és konfigurálásához kövesse na, kövesse az egyszerű manuális eljárást. Engedélyeznie kell az Automation Hybrid Worker megoldást az Azure Log Analytics-munkaterületen, majd parancsokat kell futtatnia a számítógép feldolgozóként való regisztrálásához és egy csoporthoz való hozzáadásához.

A Linux hibrid Runbook-feldolgozó minimális követelményei a következők:

* Két mag
* 4 GB RAM
* 443-as port (kimenő)

### <a name="package-requirements"></a>A csomagra vonatkozó követelmények

| **Kötelező csomag** | **Leírás** | **Minimális verzió**|
|--------------------- | --------------------- | -------------------|
|Glibc között |GNU C Könyvtár| 2.5-12 |
|Openssl| OpenSSL-könyvtárak | 1.0 (A TLS 1.1 és a TLS 1.2|
|Curl | cURL webes ügyfél | 7.15.5|
|Python-ctypes | Python 2.x szükséges |
|PAM | Cserélhető hitelesítési modulok|
| **Opcionális csomag** | **Leírás** | **Minimális verzió**|
| PowerShell Core | A PowerShell-runbookok futtatásához a PowerShellt telepíteni kell, olvassa el [a PowerShell-mag linuxos telepítése](/powershell/scripting/install/installing-powershell-core-on-linux) című témakört a telepítésről.  | 6.0.0 |

### <a name="installation"></a>Telepítés

Mielőtt továbblépne, vegye figyelembe a Log Analytics munkaterületet, amelyhez az Automation-fiók kapcsolódik. Is vegye figyelembe az automation-fiók elsődleges kulcsa. Az Azure Portalon is megtalálhatja, ha kiválasztja az Automation-fiókot, kiválasztja a **munkaterületet** a munkaterület-azonosítóhoz, és kiválasztja az elsődleges kulcs **kulcsait.** A hibrid runbook-feldolgozóhoz szükséges portokról és címekről [a Hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning)című témakörben talál további információt.

1. Engedélyezze az Automation Hybrid Worker megoldást az Azure-ban az alábbi módszerek egyikével:

   * Adja hozzá az Automation Hybrid Worker megoldást az előfizetéséhez az [Azure Monitor naplómegoldásainak hozzáadása a munkaterülethez](../log-analytics/log-analytics-add-solutions.md)című eljárással.
   * Futtassa a következő parancsmagot:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Telepítse a Log Analytics ügynök Linux hoz a következő parancs futtatásával. Cserélje \<le a\> \<WorkspaceID és a WorkspaceKey\> értékét a munkaterület megfelelő értékeire.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Futtassa a következő parancsot a *-w*, *-k*, *-g*és -e paraméterek *értékeinek módosításával.* A *-g* paraméter, cserélje le az értéket a hibrid Runbook feldolgozó csoport nevére, amelyhez az új Linux hibrid Runbook worker csatlakoznia kell. Ha a név nem létezik az Automation-fiókban, egy új hibrid Runbook feldolgozó csoport készül ezzel a névvel.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. A parancs befejezése után a hibrid feldolgozócsoportok lap az Azure Portalon az új csoport és a tagok számát jeleníti meg. Ha ez egy meglévő csoport, a tagok száma növekszik. Kiválaszthatja a csoportot a hibrid feldolgozócsoportok lapon lévő listából, és kiválaszthatja a **hibrid dolgozók** csempét. A hibrid dolgozók lapon láthatja a csoport minden egyes tagja szerepel.

> [!NOTE]
> Ha az Azure Monitor virtuálisgép-bővítményt használja linuxos Azure-beli virtuális géphez, javasoljuk, hogy hamis beállítást tegyen, `autoUpgradeMinorVersion` mivel az automatikus frissítési verziók problémákat okozhatnak a hibrid Runbook-feldolgozószámára. A bővítmény manuális frissítéséről az [Azure CLI központi telepítéséről](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)olvashat.

## <a name="turning-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása

Alapértelmezés szerint a Linux hibrid Runbook-feldolgozók aláírás-érvényesítést igényelnek. Ha egy aláíratlan runbookot futtat egy `Signature validation failed` dolgozón, hibaüzenet jelenik meg. Az aláírás-ellenőrzés kikapcsolásához futtassa a következő parancsot. Cserélje le a második paramétert a Log Analytics munkaterület-azonosítójára.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="next-steps"></a>További lépések

* Ha tudni szeretné, hogyan konfigurálhatja a runbookokat a folyamatok automatizálására a helyszíni adatközpontban vagy más felhőbeli környezetben, olvassa [el a Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid runbook-feldolgozók eltávolításáról az [Azure Automation hibrid runbook-feldolgozóinak eltávolítása című](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)témakörben talál.
* A hibrid runbook-feldolgozók hibaelhárításáról a [Linux-hibrid runbook-feldolgozók hibaelhárítása című](troubleshoot/hybrid-runbook-worker.md#linux) témakörben olvashat.
