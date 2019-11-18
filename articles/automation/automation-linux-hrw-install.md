---
title: Az Azure Automation hibrid runbook-feldolgozója Linuxra
description: Ez a cikk a Azure Automation hibrid Runbook-feldolgozók telepítésével kapcsolatos információkat tartalmaz, így a runbookok Linux-alapú számítógépeken futtatható a helyi adatközpontban vagy a felhőalapú környezetben.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8e497d18e39a199f34ff76b11b0e6c2c213f35fb
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129849"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker üzembe helyezése

A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető számítógépen és a környezetben található erőforrásokon futtathatja a helyi erőforrások kezeléséhez. A Linux Hybrid Runbook Worker a runbookok-t olyan speciális felhasználóként hajtja végre, amely emelt szintű jogosultságszint-emelést igénylő parancsok futtatására használható. A runbookok tárolása és kezelése Azure Automation, majd egy vagy több kijelölt számítógépre történik.

Ez a cikk bemutatja, hogyan telepítheti a hibrid Runbook-feldolgozót egy Linux rendszerű gépre.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

A hibrid Runbook Worker szolgáltatás a következő disztribúciókat támogatja:

* Amazon Linux 2012,09 – 2015,09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)
* Oracle Linux 5, 6 és 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 és 7 (x86/x64)
* Debian GNU/Linux 6, 7 és 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS és 16,04 LTS (x86/x64)
* 11 és 12 SUSE Linux Enterprise Server (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker telepítése

A hibrid Runbook-feldolgozók Linux rendszerű számítógépen történő telepítéséhez és konfigurálásához egy egyszerű folyamatot kell végrehajtani a szerepkör manuális telepítéséhez és konfigurálásához. Ehhez engedélyezni kell a **Automation Hybrid Worker** megoldást az Azure log Analytics-munkaterületen, majd parancsokat kell futtatnia a számítógép feldolgozóként való regisztrálásához és egy csoportba való felvételéhez.

A Linux hibrid Runbook-feldolgozók minimális követelményei a következők:

* Két mag
* 4 GB RAM
* 443-es port (kimenő)

### <a name="package-requirements"></a>Csomagra vonatkozó követelmények

| **Szükséges csomag** | **Leírás** | **Minimális verzió**|
|--------------------- | --------------------- | -------------------|
|glibc |GNU C könyvtár| 2.5-12 |
|Openssl| OpenSSL-kódtárak | 1,0 (a TLS 1,1 és a TLS 1,2 támogatott|
|Curl | cURL webes ügyfél | 7.15.5|
|Python – ctypes | A Python 2. x megadása kötelező |
|PAM | Csatlakoztatható hitelesítési modulok|
| **Választható csomag** | **Leírás** | **Minimális verzió**|
| PowerShell Core | A PowerShell-runbookok futtatásához a PowerShellt telepíteni kell, lásd: [a PowerShell Core telepítése Linuxon](/powershell/scripting/install/installing-powershell-core-on-linux) , hogy megtudja, hogyan telepítheti.  | 6.0.0 |

### <a name="installation"></a>Telepítés

A folytatás előtt jegyezze fel az Automation-fiókhoz kapcsolódó Log Analytics munkaterületet. Jegyezze fel az Automation-fiók elsődleges kulcsát is. A Azure Portal az Automation-fiók kiválasztásával, a munkaterület-azonosító **munkaterületének** kiválasztásával, valamint az elsődleges kulcs **kulcsainak** kiválasztásával is megtalálhatja. További információ a hibrid Runbook-feldolgozóhoz szükséges portokról és címekről: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

1. Engedélyezze a **Automation Hybrid Worker** megoldást az Azure-ban az alábbi módszerek egyikének használatával:

   * Adja hozzá a **Automation Hybrid Worker** megoldást az előfizetéséhez az alábbi eljárással: [Azure monitor naplók hozzáadása a munkaterülethez](../log-analytics/log-analytics-add-solutions.md).
   * Futtassa a következő parancsmagot:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. A következő parancs futtatásával telepítse a Linux rendszerhez készült Log Analytics-ügynököt. Cserélje le \<munkaterület azonosítója\> és \<WorkspaceKey\> a munkaterület megfelelő értékeire.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Futtassa a következő parancsot, és módosítsa a paraméterek értékeit *– w*, *-k*, *-g*és *-e*. A *-g* paraméternél cserélje le az értéket annak a hibrid Runbook-feldolgozó csoportnak a nevére, amelynek az új Linux Hybrid Runbook Worker-nek csatlakoznia kell. Ha a név nem létezik az Automation-fiókjában, akkor létrejön egy új hibrid Runbook Worker-csoport ezzel a névvel.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. A parancs befejezése után a Azure Portal **hibrid feldolgozói csoportok** lapja az új csoportot és a tagok számát jeleníti meg. Ha ez egy meglévő csoport, a tagok száma nő. Válassza ki a csoportot a **hibrid munkavégző csoportok** lapon a listából, és válassza a **hibrid feldolgozók** csempét. A **hibrid dolgozók** oldalon láthatja a csoport egyes tagjait.

> [!NOTE]
> Ha a Linux rendszerhez készült Azure Monitor virtuálisgép-bővítményt használja egy Azure-beli virtuális GÉPHEZ, javasoljuk, hogy a `autoUpgradeMinorVersion` hamis értékre állítása esetén a verzió automatikus verziófrissítése problémát okozhat a hibrid Runbook-feldolgozók számára. A bővítmény manuális frissítéséről az [Azure CLI üzembe helyezésével ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)foglalkozó témakörben olvashat bővebben.

## <a name="turning-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása

A Linux Hybrid Runbook-feldolgozók alapértelmezés szerint aláírás-ellenőrzést igényelnek. Ha aláíratlan runbook futtat egy feldolgozón, akkor egy hibaüzenet jelenik meg, amely szerint az "aláírás ellenőrzése nem sikerült." Az aláírás-ellenőrzés kikapcsolásához futtassa a következő parancsot. Cserélje le a második paramétert a log Analytics-munkaterület azonosítójával.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Támogatott runbook-típusok

A linuxos hibrid Runbook-feldolgozók nem támogatják a Runbook-típusok teljes készletét Azure Automation.

A következő runbook-típusok egy linuxos hibrid feldolgozón működnek:

* Python 2
* PowerShell

  > [!NOTE]
  > A PowerShell-runbookok a PowerShell Core-t kell telepíteni a Linux rendszerű gépen. A telepítésének megismeréséhez lásd: [a PowerShell Core telepítése Linux rendszeren](/powershell/scripting/install/installing-powershell-core-on-linux) .

A következő runbook-típusok nem működnek linuxos hibrid feldolgozón:

* PowerShell-munkafolyamat
* Grafikus
* Grafikus PowerShell-munkafolyamat

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid Runbook-feldolgozók eltávolításával kapcsolatos útmutatásért lásd: [Azure Automation hibrid Runbook-feldolgozók eltávolítása](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [linuxos hibrid Runbook-feldolgozók hibaelhárítása](troubleshoot/hybrid-runbook-worker.md#linux)
