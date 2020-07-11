---
title: Azure Automation Change Tracking és a leltár központi telepítési hatókörének korlátozása
description: Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a Change Tracking és a leltári telepítés hatókörének korlátozására.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 353f29d9b4c6599226619d40e4378e21618bcad4
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185908"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>A Change Tracking és a leltár központi telepítési hatókörének korlátozása

Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a [change Tracking és leltár](change-tracking.md) funkció használatakor a virtuális gépek változásainak üzembe helyezéséhez. További információ: [figyelési megoldások célzása Azure monitor (előzetes verzió)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>A hatókör-konfigurációk ismertetése

A hatókör-konfiguráció egy vagy több mentett keresés (lekérdezés) csoportja, amely a Change Tracking és a leltár hatókörének adott számítógépekre való korlátozására szolgál. A hatókör-konfiguráció a Log Analytics munkaterületen található, hogy megcélozza az engedélyezni kívánt számítógépeket. Ha hozzáad egy számítógépet a szolgáltatás változásaihoz, a rendszer a munkaterületen lévő mentett kereséshez is hozzáadja a számítógépet.

## <a name="set-the-scope-limit"></a>Hatókör korlátjának beállítása

A Change Tracking hatókörének és a leltár üzembe helyezésének korlátozása:

1. Az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület** lehetőséget.

2. Kattintson **a munkaterület ugrása**elemre.

3. Válassza ki a **hatókör-konfigurációk (előzetes verzió)** elemet a **munkaterület-adatforrások**területen.

4. Válassza ki a hatókör-konfigurációtól jobbra található három pontot `MicrosoftDefaultScopeConfig-ChangeTracking` , majd kattintson a **Szerkesztés**gombra. 

5. A szerkesztési ablaktáblán válassza a **számítógépcsoportok kiválasztása**lehetőséget. A számítógépcsoportok ablaktáblán láthatók a hatókör-konfiguráció létrehozásához használt mentett keresések. A Change Tracking és a leltár által használt mentett keresés:

    |Név     |Kategória  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Változáskövetési       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. A mentett keresés elem kiválasztásával megtekintheti és szerkesztheti a csoport feltöltéséhez használt lekérdezést. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

    ![Mentett keresések](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>További lépések

* A Change Tracking és a leltár kezelésével kapcsolatban lásd: a [change Tracking és a leltár kezelése](change-tracking-file-contents.md).
* Az általános szolgáltatással kapcsolatos problémák elhárításához lásd: [change Tracking és leltározási problémák elhárítása](troubleshoot/change-tracking.md).
