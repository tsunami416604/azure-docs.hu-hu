---
title: Az Azure Automation hibrid runbook-feldolgozója Linuxra
description: Ez a cikk tájékoztatást nyújt telepítése egy Azure Automation hibrid forgatókönyv-feldolgozó, így a Linux-alapú számítógépeken a helyi adatközpontban, illetve a felhőalapú környezetben futtatható runbookok is.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0c677b88228097efcaa30399160dfdafa1c01788
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096342"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>A Linux hibrid forgatókönyv-feldolgozók telepítése

A hibrid forgatókönyv-feldolgozó szolgáltatás az Azure Automation segítségével közvetlenül a számítógépen, amelyen a szerepkör és a helyi erőforrások kezelése környezetben erőforrásokon a runbookok futtatásához. A Linux hibrid forgatókönyv-feldolgozó végrehajtja a runbookok a jogosultságszint-emelés igénylő parancsok futtatásához bővíthetők különleges felhasználóként. Runbookok által tárolt és az Azure Automationben kezelt és ezután a felhasználóikhoz kerülnek egy vagy több kijelölt számítógépekre.

Ez a cikk ismerteti a hibrid forgatókönyv-feldolgozót a Linux rendszerű számítógépre telepítéséhez.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

A hibrid forgatókönyv-feldolgozó szolgáltatás a következő terjesztési támogatja:

* Amazon Linux 2012.09 való 2015.09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)
* Oracle Linux 5, 6 és 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 és 7 (x86/x64)
* Debian GNU/Linux 8 (x86/x64), 6, 7
* Ubuntu 12.04 LTS, 14.04 LTS és 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 és 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>A Linux hibrid forgatókönyv-feldolgozók telepítése

Telepítéséhez, és a hibrid forgatókönyv-feldolgozók konfigurálása a Linux rendszerű számítógépen, akkor egy egyszerű eljárást követve próbál kézi telepítését és konfigurálását a szerepkör. Engedélyezését igényli a **Automation Hibridfeldolgozó** megoldás Azure Naplóelemzési munkaterületet, és futtassa a parancsokat a számítógép regisztrálásához workerként, és hozzáadja egy csoporthoz.

A Linux hibrid forgatókönyv-feldolgozók minimális követelményei a következők:

* Két mag
* 4 GB RAM
* Port 443-as (kimenő)

### <a name="package-requirements"></a>Csomag követelmények

| **Szükséges csomag** | **Leírás** | **Minimális verzió**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-függvénytár| 2.5-12 |
|Openssl| OpenSSL-függvénytárak | 1.0 (a TLS 1.1 és TLS 1.2 támogatottak|
|curl | cURL-webügyfél | 7.15.5|
|Python-ctypes | |
|A PAM | Cserélhető hitelesítési modulok|
| **Nem kötelező csomag** | **Leírás** | **Minimális verzió**|
| PowerShell Core | PowerShell kell telepíteni, tekintse meg futtatni a PowerShell-forgatókönyvek, [PowerShell központi telepítése Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) megtudhatja, hogyan telepítheti azt.  | 6.0.0 |

### <a name="installation"></a>Telepítés

Mielőtt továbblépne, vegye figyelembe a Naplóelemzési munkaterület, amely az Automation-fiók hozzá van csatolva. Azt is vegye figyelembe az Automation-fiók elsődleges kulcsát. Az Azure-portálon az Automation kiválasztásával fiók, kiválasztásával található **munkaterület** a munkaterület azonosítója, majd válassza **kulcsok** elsődleges kulcs. Portok és a hibrid forgatókönyv-feldolgozót a szüksége címek kapcsolatos tudnivalókat lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

1. Engedélyezze a **Automation Hibridfeldolgozó** megoldás az Azure-ban, az alábbi módszerek egyikének használatával:

   * Hozzáadás a **Automation Hibridfeldolgozó** megoldás az előfizetéséhez, a következő eljárás használatával [hozzáadni a Naplóelemzési megoldások a munkaterületre](../log-analytics/log-analytics-add-solutions.md).
   * Futtassa a következő parancsmagot:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Az OMS-ügynök telepítése Linux a következő parancs futtatásával. Cserélje le \<WorkspaceID\> és \<WorkspaceKey\> a munkaterület megfelelő értékeivel.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. A következő parancsot, a paraméterek értékeinek módosítása *-w*, *-k*, *-g*, és *-e*. Az a *-g* paraméter, cserélje le az értéket, amely az új Linux hibrid forgatókönyv-feldolgozó csatlakoznia kell a hibrid forgatókönyv-feldolgozó csoport nevét. Ha a név nem létezik az Automation-fiókban, egy új hibrid forgatókönyv-feldolgozó csoport ezzel a névvel történik.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. A parancs befejezése után a **hibrid dolgozó csoportok** az Azure portálon lapon látható az új csoport és a tagok száma. Ha egy meglévő csoportot, a tagok száma értéke akkor növekszik. A csoportot a listából kiválaszthatja a a **hibrid dolgozó csoportok** lapon, és válassza a **hibrid feldolgozók** csempére. Az a **hibrid feldolgozók** lapon megjelenik a felsorolt csoport minden tagja.

## <a name="turning-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása

Alapértelmezés szerint a Linux hibrid forgatókönyv-feldolgozók aláírás érvényesítés szükséges. Ha egy aláíratlan runbookot futtatni egy munkavégző, amely szerint a "aláírás érvényesítése sikertelen." hibaüzenet jelenik meg Az aláírás érvényesítése kikapcsolásához futtassa a következő parancsot. A második paraméter cserélje le a Naplóelemzési munkaterület-azonosítót.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Támogatott runbooktípusokkal

Linux hibrid forgatókönyv-feldolgozók runbooktípusokkal teljes készlete nem támogatják az Azure Automationben.

A következő forgatókönyv esetében egy Linux hibrid feldolgozón működik:

* Python 2
* PowerShell

  > [!NOTE]
  > A PowerShell-forgatókönyvek PowerShell Core telepíthető a Linux rendszerű számítógépen a szükséges. Lásd: [PowerShell központi telepítése Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) megtudhatja, hogyan telepítheti azt.

A következő runbook-típusok nem működnek a Linux hibrid feldolgozók:

* PowerShell-munkafolyamat
* Grafikus
* Grafikus PowerShell-munkafolyamat

## <a name="troubleshoot"></a>Hibaelhárítás

A hibrid forgatókönyv-feldolgozók hibaelhárítása kapcsolatban [hibaelhárítási Linux hibrid forgatókönyv-feldolgozók](troubleshoot/hybrid-runbook-worker.md#linux)

## <a name="next-steps"></a>További lépések

* A runbookok automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezet konfigurálása, lásd: [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md).
* Távolítsa el a hibrid forgatókönyv-feldolgozók konfigurálásáról lásd: [eltávolítása Azure Automation hibrid forgatókönyv-feldolgozók](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
