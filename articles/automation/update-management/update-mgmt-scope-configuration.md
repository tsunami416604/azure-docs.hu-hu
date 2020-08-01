---
title: Azure Automation Update Management központi telepítési hatókör korlátozása
description: Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk a Update Management üzemelő példány hatókörének korlátozására.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: aafd284122ca61ba2b668186095b88003be2775c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450103"
---
# <a name="limit-update-management-deployment-scope"></a>Update Management központi telepítési hatókör korlátozása

Ez a cikk azt ismerteti, hogyan használhatók a hatókör-konfigurációk, ha a [Update Management](update-mgmt-overview.md) funkció használatával telepítheti a frissítéseket és a javításokat a virtuális gépekre. További információ: [figyelési megoldások célzása Azure monitor (előzetes verzió)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>A hatókör-konfigurációk ismertetése

A hatókör-konfiguráció egy vagy több mentett keresés (lekérdezés) csoportja, amely a Update Management hatókörének adott számítógépekre való korlátozására szolgál. A hatókör-konfiguráció a Log Analytics munkaterületen található, hogy megcélozza az engedélyezni kívánt számítógépeket. Ha hozzáad egy számítógépet a Update Management frissítéseinek fogadásához, a rendszer a munkaterületen lévő mentett kereséshez is hozzáadja a számítógépet.

## <a name="set-the-scope-limit"></a>Hatókör korlátjának beállítása

A Update Management-telepítés hatókörének korlátozása:

1. Az Automation-fiókban a **kapcsolódó erőforrások**területen válassza a **csatolt munkaterület** lehetőséget.

2. Válassza **a ugrás munkaterületre**lehetőséget.

3. Válassza ki a **hatókör-konfigurációk (előzetes verzió)** elemet a **munkaterület-adatforrások**területen.

4. Válassza ki a hatókör-konfiguráció jobb oldalán található három pontot `MicrosoftDefaultScopeConfig-Updates` , majd válassza a **Szerkesztés**lehetőséget.

5. A szerkesztési ablaktáblán bontsa ki a **számítógépcsoportok kijelölése elemet**. A számítógépcsoportok ablaktáblán láthatók a hatókör-konfiguráció létrehozásához használt mentett keresések. Az Update Management által használt mentett keresés:

    |Name     |Kategória  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

6. A mentett keresés elem kiválasztásával megtekintheti és szerkesztheti a csoport feltöltéséhez használt lekérdezést. Az alábbi ábrán a lekérdezés és annak eredményei láthatók:

    [![Mentett keresések](./media/update-mgmt-scope-configuration/logsearch.png)](./media/update-mgmt-scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>További lépések

[Azure monitor naplók lekérdezésével](update-mgmt-query-logs.md) elemezheti a frissítési felméréseket, a központi telepítéseket és az egyéb kapcsolódó felügyeleti feladatokat. Előre definiált lekérdezéseket tartalmaz, amelyek segítenek az első lépések megtételében.
