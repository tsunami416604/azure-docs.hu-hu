---
title: Az Azure Automation hibrid runbook-feldolgozója Linuxra
description: Ez a cikk egy Azure Automation hibrid Runbook-feldolgozó telepítése Linux-alapú számítógépeken a helyi adatközpontban vagy a felhőalapú környezetben a runbookok futtatásához információkat biztosít.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e0aaddb841687718295e09e64b23d9cefa9246fd
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436110"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Üzembe helyezése egy hibrid Runbook-feldolgozója Linuxra

Az Azure Automation hibrid Runbook-feldolgozó szolgáltatása segítségével a runbookok futtatása, közvetlenül a számítógépen, amelyen a szerepkör és a helyi erőforrások kezelése a környezetben az erőforrásokon. A hibrid Runbook-feldolgozója Linuxra runbookok végrehajtja a jogosultságszint-emelési igénylő parancsok futtatásához bővíthetők speciális felhasználóként. Runbookok tárolt és kezelt az Azure Automation és egy vagy több kijelölt számítógépekre kézbesítését.

Ez a cikk ismerteti a hibrid Runbook Worker telepítése Linux rendszerű gépen.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

A hibrid Runbook-feldolgozó szolgáltatás támogatja a következő disztribúciók:

* Amazon Linux 2012.09 való 2015.09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)
* Oracle Linux 5, 6 és 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 és 7 (x86/x64)
* Debian GNU/Linux 6, 7, és 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS és 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 – 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>A hibrid Runbook-feldolgozója Linuxra telepítése

Telepítése, és a egy hibrid Runbook-feldolgozó konfigurálása a Linux rendszerű számítógépen, hajtsa végre kézzel telepítse és konfigurálja a szerepkört egy egyszerű folyamat. Engedélyezni kell a **Automation hibrid feldolgozó** megoldás az Azure Log Analytics-munkaterület, és futtassa a számítógép regisztrálása, mint a egy feldolgozó, és hozzáadja egy csoporthoz parancsokat.

A hibrid Runbook-feldolgozója Linuxra vonatkozó minimális követelmények a következők:

* Két magot
* 4 GB RAM
* Port 443-as (kimenő)

### <a name="package-requirements"></a>Csomag követelmények

| **Szükséges csomag** | **Leírás** | **Minimális verziója**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-kódtár| 2.5-12 |
|openssl| OpenSSL-függvénytárak | 1.0-t (a TLS 1.1 és TLS 1.2 támogatott|
|A curl | a cURL webes ügyféllel | 7.15.5|
|Python-ctypes | |
|PAM | Cserélhető hitelesítési modulok|
| **Nem kötelező csomag** | **Leírás** | **Minimális verziója**|
| PowerShell Core | PowerShell-runbookok futtatásához a PowerShell kell telepíteni, tekintse meg [a PowerShell Core telepítése Linux rendszeren](/powershell/scripting/setup/installing-powershell-core-on-linux) megtudhatja, hogyan telepítheti azt.  | 6.0.0 |

### <a name="installation"></a>Telepítés

Mielőtt folytatja a műveletet, vegye figyelembe a Log Analytics-munkaterületet, amely az Automation-fiókhoz van csatolva. Azt is vegye figyelembe az Automation-fiók elsődleges kulcsát. Mindkettő az Azure Portalon válassza az Automation-fiók, választ talál **munkaterület** a munkaterület Azonosítóját, a, és válassza **kulcsok** az elsődleges kulcs. Portok és a hibrid Runbook-feldolgozó szükséges címek kapcsolatos tudnivalókat lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

1. Engedélyezze a **Automation hibrid feldolgozó** megoldás az Azure-ban az alábbi módszerek egyikének használatával:

   * Hozzáadás a **Automation hibrid feldolgozó** megoldás az előfizetéséhez, a következő eljárás használatával [adja hozzá a Log Analytics felügyeleti megoldások a munkaterülethez](../log-analytics/log-analytics-add-solutions.md).
   * Futtassa a következő parancsmagot:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. A Linuxhoz készült Log Analytics-ügynök telepítése a következő parancs futtatásával. Cserélje le \<munkaterület azonosítója\> és \<WorkspaceKey\> a munkaterület a megfelelő értékekkel.

  [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Futtassa a következő parancsot, és a paraméterek értékeinek módosításával *-w*, *-k*, *-g*, és *-e*. Az a *-g* paraméter értékét cserélje le a nevet a hibrid Runbook-feldolgozó csoport, amelyikhez az új hibrid Runbook-feldolgozója Linuxra csatlakoznia kell. A név nem létezik az Automation-fiókban, ha egy új hibrid Runbook-feldolgozó csoport ezen a néven jön létre.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. A parancs befejeződése után a **hibrid Feldolgozócsoportok** oldal az Azure Portalon az új csoport és a tagok száma látható. Ha egy meglévő csoportot, a tagok száma értéke akkor növekszik. A csoportot a listából kiválaszthatja a a **hibrid Feldolgozócsoportok** lapon, és válassza a **hibrid feldolgozók** csempére. Az a **hibrid feldolgozók** lapon láthatja a felsorolt csoport minden tagja.

## <a name="turning-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása

Alapértelmezés szerint a Linux hibrid Runbook-feldolgozók szükséges aláírás-ellenőrzése. Ha egy feldolgozó egy előjel nélküli runbook futtatja, arról, hogy a "aláírás-ellenőrzése nem sikerült." hibaüzenet jelenik meg Aláírás-ellenőrzése ki szeretné kapcsolni, futtassa a következő parancsot. A második paraméter cserélje le a Log Analytics-munkaterület azonosítóját.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Támogatott runbook-típusok

Linux hibrid Runbook-feldolgozók runbook-típusok teljes készlete nem támogatják az Azure Automationben.

A következő runbook-típusok egy Linux hibrid feldolgozón működik:

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell-forgatókönyvek a PowerShell Core a Linux rendszerű gépre telepíteni kell. Lásd: [a PowerShell Core telepítése Linux rendszeren](/powershell/scripting/setup/installing-powershell-core-on-linux) megtudhatja, hogyan telepítheti azt.

A következő runbook-típusok nem használhatók egy Linux-hibrid feldolgozó:

* PowerShell-munkafolyamat
* Grafikus
* Grafikus PowerShell-munkafolyamat

## <a name="troubleshoot"></a>Hibaelhárítás

Hogyan háríthatók el a hibrid Runbook-feldolgozók kapcsolatban lásd: [hibáinak elhárítása Linux hibrid Runbook-feldolgozók](troubleshoot/hybrid-runbook-worker.md#linux)

## <a name="next-steps"></a>További lépések

* A runbookok automatizálhatja a folyamatokat a helyszíni adatközpont vagy egyéb felhőalapú környezetbe történő konfigurálásával kapcsolatban lásd: [runbookok futtatása hibrid Runbook-feldolgozók](automation-hrw-run-runbooks.md).
* A hibrid Runbook-feldolgozóinak eltávolítása útmutatásért lásd: [távolítsa el az Azure Automation hibrid Runbook-feldolgozók](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).

