---
title: Hatókör-konfigurációk használata Azure Automation Update Management
description: Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a Update Management használatakor.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832027"
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

* A szolgáltatással való együttműködéshez tekintse meg az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című témakört.
* A szolgáltatással kapcsolatos hibák elhárításához tekintse meg [Update Management problémák elhárítása](troubleshoot/update-management.md)című témakört.
* A Windows Update Agent hibáinak elhárításához tekintse meg a [Windows Update Agent problémáinak elhárítása](troubleshoot/update-agent-issues.md)című témakört.
* A Linux frissítési ügynök hibáinak elhárításához tekintse meg a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](troubleshoot/update-agent-issues-linux.md)című témakört.