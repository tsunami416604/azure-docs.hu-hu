---
title: Hatókör-konfigurációk használata Azure Automation Update Management
description: Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a Update Management használatakor.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95563e816fa4f0931e547b6bd163dce23c338a44
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749122"
---
# <a name="work-with-scope-configurations-for-update-management"></a>A Update Management hatókör-konfigurációinak használata

Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a [Update Management](automation-update-management.md) szolgáltatás virtuális gépeken való használatakor. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>A hatókör-konfiguráció keresése

A Update Management a Log Analytics munkaterület hatókör-konfigurációját használja a szolgáltatás engedélyezéséhez szükséges számítógépek célzására. A hatókör-konfiguráció egy vagy több mentett keresés csoportja, amely a szolgáltatás hatókörének meghatározott számítógépekre való korlátozására szolgál. A hatókör-konfigurációk elérése:

1. Az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **munkaterület**lehetőséget. 

2. Válassza ki a munkaterületet a **munkaterület-adatforrások**területen, és válassza a **hatókör-konfigurációk**elemet.

3. Ha a kiválasztott munkaterülethez még nincs engedélyezve a Update Management funkció, a létrehozza a `MicrosoftDefaultScopeConfig-Updates` hatókör-konfigurációt. 

4. Ha a kijelölt munkaterületen már engedélyezve van a funkció, nincs újratelepítve, és a hatókör-konfiguráció nincs hozzáadva. 

5. Válassza ki a három pontot a hatókör bármelyik konfigurációján, majd kattintson a **Szerkesztés**gombra. 

6. A szerkesztési ablaktáblán válassza a **számítógépcsoportok kiválasztása**lehetőséget. A számítógépcsoportok ablaktáblán láthatók a hatókör-konfiguráció létrehozásához használt mentett keresések.

## <a name="view-a-saved-search"></a>Mentett keresés megtekintése

Amikor hozzáad egy számítógépet a Update Managementhoz, azt a rendszer a munkaterületen lévő mentett kereséshez is hozzáadja. A mentett keresés egy olyan lekérdezés, amely a célként megadott számítógépeket tartalmazza.

1. Navigáljon a Log Analytics munkaterületre, és válassza a **mentett keresések** **általános**lehetőséget. Az Update Management által használt mentett keresés:

|Name     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

2. A mentett keresés elem kiválasztásával megtekintheti a csoport feltöltéséhez használt lekérdezést. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

    ![Mentett keresések](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>További lépések

* A szolgáltatással kapcsolatos általános információkért lásd: [Update Management áttekintése](automation-update-management.md).
* A szolgáltatással való együttműködéshez tekintse meg az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című témakört.
* Ha Automation-fiókot szeretne használni a szolgáltatás engedélyezéséhez, olvassa el a [Update Management automatizálási fiókból való engedélyezését](automation-onboard-solutions-from-automation-account.md)ismertető témakört.
* A funkció engedélyezéséhez Azure Portal tallózással tekintse meg a [Azure Portal Update Management engedélyezése](automation-onboard-solutions-from-browse.md)című témakört.
* Ha runbook szeretne használni a szolgáltatás engedélyezéséhez, olvassa el a [Update Management engedélyezése a runbook](automation-onboard-solutions.md)című témakört.
* Ha Azure-beli virtuális gépet kíván használni a szolgáltatás engedélyezéséhez, tekintse meg a [Update Management engedélyezése Azure-beli virtuális](automation-onboard-solutions-from-vm.md)gépről című témakört.
* A szolgáltatással kapcsolatos hibák elhárításához tekintse meg [Update Management problémák elhárítása](troubleshoot/update-management.md)című témakört.
* A Windows Update Agent hibáinak elhárításához tekintse meg a [Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md)című témakört.
* A Linux frissítési ügynök hibáinak elhárításához tekintse meg a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md)című témakört.