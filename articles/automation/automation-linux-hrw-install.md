---
title: Linuxos hibrid Runbook-feldolgozó üzembe helyezése Azure Automation
description: Ez a cikk azt ismerteti, hogyan telepíthet egy Azure Automation hibrid Runbook-feldolgozót a runbookok Linux-alapú számítógépeken való futtatásához a helyi adatközpontban vagy a felhőalapú környezetben.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: a6cf348142d694a03da24f32793fc72325701931
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835223"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker üzembe helyezése

A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető számítógépen és a környezetben található erőforrásokon futtathatja a helyi erőforrások kezeléséhez. A Linux Hybrid Runbook Worker a runbookok-t olyan speciális felhasználóként hajtja végre, amely emelt szintű jogosultságszint-emelést igénylő parancsok futtatására használható. A runbookok tárolása és kezelése Azure Automation, majd egy vagy több kijelölt számítógépre történik.

Ez a cikk bemutatja, hogyan telepítheti a hibrid Runbook-feldolgozót egy Linux rendszerű gépre, hogyan távolíthatja el a feldolgozót, és hogyan távolíthat el egy hibrid Runbook-feldolgozói csoportot.

## <a name="supported-linux-operating-systems"></a>Támogatott linuxos operációs rendszerek

A hibrid Runbook Worker szolgáltatás a következő disztribúciókat támogatja:

* Amazon Linux 2012,09 – 2015,09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)
* Oracle Linux 5, 6 és 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 és 7 (x86/x64)
* Debian GNU/Linux 6, 7 és 8 (x86/x64)
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS és 18,04 (x86/x64)
* 11 és 12 SUSE Linux Enterprise Server (x86/x64)

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

## <a name="deployment-requirements"></a>Üzembe helyezésre vonatkozó követelmények

A Linux hibrid Runbook-feldolgozók minimális követelményei a következők:

* Két mag
* 4 GB RAM
* 443-es port (kimenő)

### <a name="package-requirements"></a>Csomagra vonatkozó követelmények

| **Szükséges csomag** | **Leírás** | **Minimális verzió**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C könyvtár| 2.5-12 |
|Openssl| OpenSSL-kódtárak | 1,0 (a TLS 1,1 és a TLS 1,2 támogatott)|
|Curl | cURL webes ügyfél | 7.15.5|
|Python – ctypes | A Python 2. x megadása kötelező |
|PAM | Cserélhető hitelesítési modulok|
| **Választható csomag** | **Leírás** | **Minimális verzió**|
| PowerShell Core | A PowerShell-runbookok futtatásához a PowerShellt telepíteni kell, lásd: [a PowerShell Core telepítése Linuxon](/powershell/scripting/install/installing-powershell-core-on-linux) , hogy megtudja, hogyan telepítheti.  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker telepítése

A hibrid Runbook-feldolgozók Linux rendszerű számítógépen történő telepítéséhez és konfigurálásához kövesse az egyszerű manuális folyamatot. Ehhez engedélyezni kell a hibrid Runbook-feldolgozót az Azure Log Analytics-munkaterületen, majd parancsokat kell futtatnia a számítógép feldolgozóként való regisztrálásához és egy csoportba való felvételéhez.

A folytatás előtt jegyezze fel az Automation-fiókhoz kapcsolódó Log Analytics munkaterületet. Jegyezze fel az Automation-fiók elsődleges kulcsát is. A Azure Portal az Automation-fiók kiválasztásával, a munkaterület-azonosító **munkaterületének** kiválasztásával, valamint az elsődleges kulcs **kulcsainak** kiválasztásával is megtalálhatja. További információ a hibrid Runbook-feldolgozóhoz szükséges portokról és címekről: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

>[!NOTE]
> A Linux Hybrid Worker telepítése során a megfelelő sudo engedélyekkel rendelkező [nxautomation-fióknak](automation-runbook-execution.md#log-analytics-agent-for-linux) jelen kell lennie. Ha megpróbálja telepíteni a munkavégzőt, és a fiók nem létezik, vagy nem rendelkezik a megfelelő engedélyekkel, a telepítés sikertelen lesz.

1. Engedélyezze a hibrid Runbook-feldolgozót az Azure-ban az alábbi módszerek egyikének használatával:

   * Adja hozzá a hibrid Runbook-feldolgozót az előfizetéséhez a következő eljárás használatával: [Azure monitor naplók hozzáadása a munkaterülethez](../log-analytics/log-analytics-add-solutions.md).
   * Futtassa a következő parancsmagot:

        ```azurepowershell-interactive
         Set-AzOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. A következő parancs futtatásával telepítse a Linux rendszerhez készült Log Analytics-ügynököt. Cserélje le a \< munkaterület azonosítója \> és \< \> a WorkspaceKey értéket a munkaterület megfelelő értékeire.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Futtassa a következő parancsot, és módosítsa a paraméterek értékeit *– w*, *-k*, *-g*és *-e*. A *-g* paraméternél cserélje le az értéket annak a hibrid Runbook-feldolgozó csoportnak a nevére, amelynek az új Linux Hybrid Runbook Worker-nek csatlakoznia kell. Ha a név nem létezik az Automation-fiókjában, akkor létrejön egy új hibrid Runbook Worker-csoport ezzel a névvel.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. A parancs befejezése után a Azure Portal hibrid feldolgozói csoportok lapja az új csoportot és a tagok számát jeleníti meg. Ha ez egy meglévő csoport, a tagok száma nő. Válassza ki a csoportot a hibrid munkavégző csoportok lapon a listából, és válassza a **hibrid feldolgozók** csempét. A hibrid dolgozók oldalon láthatja a csoport egyes tagjait.

> [!NOTE]
> Ha a Linux rendszerhez készült Azure Monitor virtuálisgép-bővítményt használja egy Azure-beli virtuális GÉPHEZ, azt javasoljuk, hogy a FALSE értéket adja meg `autoUpgradeMinorVersion` , mert az automatikus verziófrissítés a hibrid Runbook-feldolgozó számára is problémát okozhat. A bővítmény manuális frissítéséről az [Azure CLI üzembe helyezésével](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)foglalkozó témakörben olvashat bővebben.

## <a name="turn-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása

A Linux Hybrid Runbook-feldolgozók alapértelmezés szerint aláírás-ellenőrzést igényelnek. Ha aláíratlan runbook futtat egy feldolgozón, hibaüzenet jelenik meg `Signature validation failed` . Az aláírás-ellenőrzés kikapcsolásához futtassa a következő parancsot. Cserélje le a második paramétert a Log Analytics munkaterület-azonosítójával.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>Hibrid Runbook-feldolgozó eltávolítása helyszíni linuxos számítógépről

A `ls /var/opt/microsoft/omsagent` hibrid Runbook-feldolgozón a parancs használatával kérheti le a munkaterület azonosítóját. A rendszer létrehoz egy nevű mappát a munkaterület-AZONOSÍTÓval.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ez a kód nem távolítja el a Linux rendszerhez készült Log Analytics-ügynököt a számítógépről. Csak a hibrid Runbook-feldolgozói szerepkör funkcióit és konfigurációját távolítja el.

## <a name="remove-a-hybrid-worker-group"></a>Hibridfeldolgozó-csoport eltávolítása

A Linux rendszerű számítógépek hibrid Runbook Worker csoportjának eltávolításához ugyanazokat a lépéseket kell használnia, mint a Windows Hybrid Worker-csoport esetében. Lásd: [hibrid feldolgozói csoport eltávolítása](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása – Linux](troubleshoot/hybrid-runbook-worker.md#linux).
