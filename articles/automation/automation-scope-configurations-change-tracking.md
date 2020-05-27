---
title: A Azure Automation Change Tracking és a leltár hatókör-konfigurációinak használata
description: Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a Change Tracking és a leltár használatakor.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4fac94cc2f8f378b7e9d8e9485baed6a0ffa838b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832163"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>A Change Tracking és a leltár hatókör-konfigurációinak használata

Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a [Update Management](automation-update-management.md) szolgáltatás virtuális gépeken való engedélyezésekor. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>A hatókör-konfiguráció keresése

A Update Management a Log Analytics munkaterület hatókör-konfigurációját használja, hogy megcélozza a Update Management számára engedélyezni kívánt számítógépeket. A hatókör-konfiguráció egy vagy több mentett keresés csoportja, amely a szolgáltatás hatókörének meghatározott számítógépekre való korlátozására szolgál. A hatókör-konfigurációk elérése:

1. Az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **munkaterület**lehetőséget. 

2. Válassza ki a munkaterületet a **munkaterület-adatforrások**területen, és válassza a **hatókör-konfigurációk**elemet.

3. Ha a kiválasztott munkaterülethez még nincs engedélyezve a Update Management funkció, a létrehozza a `MicrosoftDefaultScopeConfig-ChangeTracking` hatókör-konfigurációt. 

4. Ha a kijelölt munkaterületen már engedélyezve van a funkció, nincs újratelepítve, és a hatókör-konfiguráció nincs hozzáadva. 

5. Válassza ki a három pontot a hatókör bármelyik konfigurációján, majd kattintson a **Szerkesztés**gombra. 

6. A szerkesztési ablaktáblán válassza a **számítógépcsoportok kiválasztása**lehetőséget. A számítógépcsoportok ablaktáblán láthatók a hatókör-konfiguráció létrehozásához használt mentett keresések.

## <a name="view-a-saved-search"></a>Mentett keresés megtekintése

Ha a számítógép bekerül a Change Tracking és a leltárba, azt a rendszer a munkaterületen lévő mentett kereséshez is hozzáadja. A mentett keresés egy olyan lekérdezés, amely a célként megadott számítógépeket tartalmazza.

1. Navigáljon a Log Analytics munkaterületre, és válassza a **mentett keresések** **általános**lehetőséget. Az Update Management által használt mentett keresés:

    |Name     |Kategória  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Változáskövetési       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. A mentett keresés elem kiválasztásával megtekintheti a csoport feltöltéséhez használt lekérdezést. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

    ![Mentett keresések](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>További lépések

* A Change Tracking és a leltár kezelésével kapcsolatban lásd: a [change Tracking és a leltár kezelése](change-tracking-file-contents.md).
* Az általános szolgáltatással kapcsolatos problémák elhárításához lásd: [change Tracking és leltározási problémák elhárítása](troubleshoot/change-tracking.md).