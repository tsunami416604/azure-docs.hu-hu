---
title: Azure Automation Update Management központi telepítési hatókör korlátozása
description: Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a Update Management üzemelő példány hatókörének korlátozására.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 72065b388f348da1d268f875a10d5b13d2f8cf3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117407"
---
# <a name="limit-update-management-deployment-scope"></a>Update Management központi telepítési hatókör korlátozása

Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk, ha a [Update Management](automation-update-management.md) funkció használatával telepítheti a frissítéseket és a javításokat a virtuális gépekre. További információ: [figyelési megoldások célzása Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting). 

## <a name="about-scope-configurations"></a>A hatókör-konfigurációk ismertetése

A hatókör-konfiguráció egy vagy több mentett keresés (lekérdezés) csoportja, amely a Update Management hatókörének adott számítógépekre való korlátozására szolgál. A hatókör-konfiguráció a Log Analytics munkaterületen található, hogy megcélozza az engedélyezni kívánt számítógépeket. Ha hozzáad egy számítógépet a Update Management frissítéseinek fogadásához, a rendszer a munkaterületen lévő mentett kereséshez is hozzáadja a számítógépet.

## <a name="set-the-scope-limit"></a>Hatókör korlátjának beállítása

A Update Management-telepítés hatókörének korlátozása:

1. Az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület** lehetőséget.

2. Kattintson **a munkaterület ugrása**elemre.

3. Válassza ki a **hatókör-konfigurációk (előzetes verzió)** elemet a **munkaterület-adatforrások**területen.

4. Válassza ki a hatókör-konfigurációtól jobbra található három pontot `MicrosoftDefaultScopeConfig-Updates` , majd kattintson a **Szerkesztés**gombra. 

5. A szerkesztési ablaktáblán bontsa ki a **számítógépcsoportok kijelölése elemet**. A számítógépcsoportok ablaktáblán láthatók a hatókör-konfiguráció létrehozásához használt mentett keresések. Az Update Management által használt mentett keresés:

    |Name     |Kategória  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

6. A mentett keresés elem kiválasztásával megtekintheti és szerkesztheti a csoport feltöltéséhez használt lekérdezést. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

    ![Mentett keresések](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>További lépések

* A szolgáltatással való együttműködéshez tekintse meg az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című témakört.
* A szolgáltatással kapcsolatos hibák elhárításához tekintse meg [Update Management problémák elhárítása](troubleshoot/update-management.md)című témakört.
* A Windows Update Agent hibáinak elhárításához tekintse meg a [Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md)című témakört.
* A Linux frissítési ügynök hibáinak elhárításához tekintse meg a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md)című témakört.