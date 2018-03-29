---
title: Az Azure Automation hibrid runbook-feldolgozója Linuxra
description: Ez a cikk tájékoztatást nyújt telepítése egy Azure Automation hibrid forgatókönyv-feldolgozó, amely lehetővé teszi runbookok futtatását a Linux-alapú számítógépeken a helyi adatközpontban, illetve a felhőalapú környezetben.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b4559afa9294111eaa1f20fdf295d1fb26dcc994
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>A Linux hibrid forgatókönyv-feldolgozók központi telepítése

Az Azure Automation Runbookjai nem férnek hozzá erőforrásokhoz, a többi felhőből vagy a helyszíni környezetben, mivel azok Azure felhőben lefutott. A hibrid forgatókönyv-feldolgozó szolgáltatás az Azure Automation lehetővé teszi a runbookok futtatásához, közvetlenül a szerepkört futtató számítógépen és a helyi erőforrások kezelése környezetben erőforrásokon. Runbookok által tárolt és az Azure Automationben kezelt és ezután a felhasználóikhoz kerülnek egy vagy több kijelölt számítógépekre.

Ez a funkció az alábbi ábrán látható:<br>  

![Hibrid forgatókönyv-feldolgozó – áttekintés](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

A hibrid forgatókönyv-feldolgozói szerepkör a műszaki áttekintés: [Automation architektúrájának áttekintése](automation-offering-get-started.md#automation-architecture-overview). Tekintse át a következő információkat vonatkozó a [hardver- és szoftverkövetelmények](automation-offering-get-started.md#hybrid-runbook-worker) és [adatait a hálózatot](automation-offering-get-started.md#network-planning) mielőtt elkezdené a hibrid forgatókönyv-feldolgozók telepítését. Miután sikeresen telepítette a forgatókönyv-feldolgozók, tekintse át a [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.     

## <a name="hybrid-runbook-worker-groups"></a>Hibrid forgatókönyv-feldolgozó csoportok
Minden egyes hibrid forgatókönyv-feldolgozó az ügynök telepítése során a hibrid forgatókönyv-feldolgozó csoport tagja. Egy csoport ügynököt lehetnek, de több ügynök is telepíthető egy magas rendelkezésre állási csoportot.

A hibrid forgatókönyv-feldolgozók a runbook indításakor, a csoport az fut, adja meg. A csoportnak a tagjai határozza meg, melyik worker kiszolgálja a kérést. Egy adott munkavégző nem adható meg.

## <a name="installing-linux-hybrid-runbook-worker"></a>Linux hibrid forgatókönyv-feldolgozó telepítése
Telepítéséhez, és a hibrid forgatókönyv-feldolgozók konfigurálása a Linux rendszerű számítógépen, hajtsa végre egy előre egyenes folyamat kézi telepítését és konfigurálását a szerepkör. Engedélyezését igényli a **Automation Hibridfeldolgozó** megoldás Naplóelemzési munkaterületet, és futtassa a parancsokat a számítógép regisztrálása workerként, adja hozzá egy új vagy meglévő csoporthoz. 

Mielőtt továbblépne, kell figyelembe venni a Naplóelemzési munkaterület az Automation-fiók hozzá van csatolva, és az Automation-fiók elsődleges kulcsát. Található mind a portálról az Automation-fiók kiválasztása, majd válassza **munkaterület** a munkaterület azonosítója és kiválasztása **kulcsok** elsődleges kulcs.  

1.  Engedélyezze a "Automation Hibridfeldolgozó" megoldás az Azure-ban. Ezt úgy teheti meg:

   1. Hozzáadás a **Automation Hibridfeldolgozó** megoldás az előfizetéséhez, a következő eljárás segítségével [hozzáadni a Naplóelemzési megoldások a munkaterületre](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-add-solutions).
   2. Futtassa a következő parancsmagot:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  A következő parancsot, a paraméterek értékeinek módosítása *-w*, *-k*, *-g*, és *-e*. Az a *-g* paraméter értékét cserélje le az új Linux hibrid forgatókönyv-feldolgozó csatlakoznia kell a hibrid forgatókönyv-feldolgozó csoport nevét. Ha a név már szerepel az Automation-fiók, egy új hibrid forgatókönyv-feldolgozó csoport ezzel a névvel történik.
    
    ```python
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. A parancs befejezése után a hibrid dolgozó csoportok panel az Azure portálon jelennek meg az új csoport és a tagok száma, vagy ha egy meglévő csoportot, a tagok száma növeli. A csoportot a listából kiválaszthatja a a **hibrid dolgozó csoportok** panelhez, és válassza a **hibrid feldolgozók** csempére. Az a **hibrid feldolgozók** panelen megjelenik a felsorolt csoport minden tagja.  


## <a name="turning-off-signature-validation"></a>Aláírás-ellenőrzés kikapcsolása 
Alapértelmezés szerint a Linux hibrid forgatókönyv-feldolgozók aláírás érvényesítés szükséges. Ha egy aláíratlan runbookot futtatni egy munkavégző, tartalmazó "Aláírás érvényesítése nem sikerült" hibaüzenet jelenik meg. Az aláírás érvényesítése kikapcsolásához futtassa a következő parancsot, a második paraméter cseréje a Naplóelemzési munkaterület azonosítója:

 ```python
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
* Távolítsa el a hibrid forgatókönyv-feldolgozók konfigurálásáról lásd: [eltávolítása Azure Automation hibrid forgatókönyv-feldolgozók](automation-remove-hrw.md)
