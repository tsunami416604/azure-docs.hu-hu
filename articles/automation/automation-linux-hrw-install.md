---
title: Az Azure Automation hibrid runbook-feldolgozója Linuxra
description: Ez a cikk tájékoztatást nyújt telepítése egy Azure Automation hibrid forgatókönyv-feldolgozó, amely lehetővé teszi runbookok futtatását a Linux-alapú számítógépeken a helyi adatközpontban, illetve a felhőalapú környezetben.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 08d86e467a51ab786e190c7b834f57e365c8130c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>A Linux hibrid forgatókönyv-feldolgozók központi telepítése

A hibrid forgatókönyv-feldolgozó szolgáltatás az Azure Automation lehetővé teszi a runbookok futtatásához, közvetlenül a szerepkört futtató számítógépen és a helyi erőforrások kezelése környezetben erőforrásokon. A Linux hibrid forgatókönyv-feldolgozó végrehajtja a runbookok a jogosultságszint-emelés igénylő parancsok futtatásához bővíthetők, amelyek különleges felhasználóként. Runbookok által tárolt és az Azure Automationben kezelt és ezután a felhasználóikhoz kerülnek egy vagy több kijelölt számítógépekre. Ez a következő cikket: decribes a hibrid forgatókönyv-feldolgozó telepítése Linux rendszerű gépen.

## <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek

A következő egy támogatott Linux terjesztésekről listáját:

* Amazon Linux 2012.09 2015.09 (x86/x64)-->
* CentOS Linux 5, 6 és 7 (x86/x64)
* Oracle Linux 5, 6 és 7 (x86/x64)
* Red Hat Enterprise Linux Server 5,6 és 7 (x86/x64)
* Debian GNU/Linux 8 (x86/x64), 6, 7
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux a vállalati kiszolgáló 11 és 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Linux hibrid forgatókönyv-feldolgozó telepítése

Telepítéséhez, és a hibrid forgatókönyv-feldolgozók konfigurálása a Linux rendszerű számítógépen, hajtsa végre egy előre egyenes folyamat kézi telepítését és konfigurálását a szerepkör. Engedélyezését igényli a **Automation Hibridfeldolgozó** megoldás Naplóelemzési munkaterületet, és futtassa a parancsokat a számítógép regisztrálása workerként, adja hozzá egy új vagy meglévő csoporthoz.

Mielőtt továbblépne, kell figyelembe venni a Naplóelemzési munkaterület az Automation-fiók hozzá van csatolva, és az Automation-fiók elsődleges kulcsát. Található mind a portálról az Automation-fiók kiválasztása, majd válassza **munkaterület** a munkaterület azonosítója és kiválasztása **kulcsok** elsődleges kulcs. Portok és címek, amelyek szükségesek a hibrid forgatókönyv-feldolgozó kapcsolatos tudnivalókat lásd: [a hálózat konfigurálása](automation-hybrid-runbook-worker.md#network-planning).

1. Engedélyezze a "Automation Hibridfeldolgozó" megoldás az Azure-ban. Ezt úgy teheti meg:

   1. Hozzáadás a **Automation Hibridfeldolgozó** megoldás az előfizetéséhez, a következő eljárás segítségével [hozzáadni a Naplóelemzési megoldások a munkaterületre](../log-analytics/log-analytics-add-solutions.md).
   1. Futtassa a következő parancsmagot:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. A Linux OMS-ügynök telepítése a következő parancs futtatásával cseréje \<WorkspaceID\> és \<WorkspaceKey\> a munkaterület megfelelő értékeivel.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. A következő parancsot, a paraméterek értékeinek módosítása *-w*, *-k*, *-g*, és *-e*. Az a *-g* paraméter, cserélje le az értéket, amely az új Linux hibrid forgatókönyv-feldolgozó csatlakoznia kell a hibrid forgatókönyv-feldolgozó csoport nevét. Ha a név már szerepel az Automation-fiók, egy új hibrid forgatókönyv-feldolgozó csoport ezzel a névvel történik.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. A parancs befejezése után a hibrid dolgozó csoportok lapon, az Azure portálon jelennek meg az új csoport és a tagok száma, vagy ha egy meglévő csoportot, a tagok száma növeli. A csoportot a listából kiválaszthatja a a **hibrid dolgozó csoportok** lapon, és válassza a **hibrid feldolgozók** csempére. Az a **hibrid feldolgozók** lapon megjelenik a felsorolt csoport minden tagja.

## <a name="turning-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása

Alapértelmezés szerint a Linux hibrid forgatókönyv-feldolgozók aláírás érvényesítés szükséges. Ha egy aláíratlan runbookot futtatni egy munkavégző, tartalmazó "Aláírás érvényesítése nem sikerült" hibaüzenet jelenik meg. Az aláírás érvényesítése kikapcsolásához futtassa a következő parancsot, a második paraméter cseréje a Naplóelemzési munkaterület azonosítója:

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Támogatott runbooktípusokkal

Linux hibrid forgatókönyv-feldolgozók nem támogatják az Azure Automation szolgáltatásbeli belül található runbooktípusokkal teljes készletét.

A következő forgatókönyv esetében egy Linux hibrid feldolgozón működik:

* Python 2
* PowerShell

A következő runbook-típusok nem használhatók a Linux hibrid feldolgozók:

* PowerShell-munkafolyamat
* Grafikus
* Grafikus PowerShell-munkafolyamat

## <a name="next-steps"></a>További lépések

* Felülvizsgálati [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.
* Távolítsa el a hibrid forgatókönyv-feldolgozók konfigurálásáról lásd: [eltávolítása Azure Automation hibrid forgatókönyv-feldolgozók](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)
