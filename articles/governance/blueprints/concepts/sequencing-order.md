---
title: Az Azure-tervek telepítési sorrendjét ismertetése
description: Ismerje meg az életciklus-tervrajz halad át, és minden egyes szakaszhoz részleteit.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: fcfffe6094361c8b47b1cc7ce42cb79561261b15
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51620595"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Az Azure-tervek telepítési sorrendjét ismertetése

Azure tervek az egy **alkalmazás-előkészítés rendelés** erőforrás-létrehozás sorrendjének meghatározásához tervrajz hozzárendelésének feldolgozása során. Ez a cikk a következő fogalmakat ismerteti:

- Az alapértelmezett műveleti sorrend sorrend használt
- A rendelés testreszabása
- A testre szabott rendelés feldolgozásának módja

A JSON-példák, amelyek a saját értékeire cserélni kell változók vannak:

- `{YourMG}` – Cserélje le a felügyeleti csoport nevére

## <a name="default-sequencing-order"></a>Alapértelmezett alkalmazás-előkészítés sorrend

Ha a tervezet nem ahhoz, hogy az összetevők üzembe helyezése a direktívákat tartalmaz, vagy az irányelv null értékű, a következő sorrendben szolgál:

- Előfizetési szinten **szerepkör-hozzárendelés** összetevők szerelvényösszetevő-név szerint rendezve
- Előfizetési szinten **szabályzat-hozzárendelés** összetevők szerelvényösszetevő-név szerint rendezve
- Előfizetési szinten **Azure Resource Manager-sablon** összetevők szerelvényösszetevő-név szerint rendezve
- **Erőforráscsoport** összetevőket (beleértve az alárendelt összetevők) helyőrző név szerint rendezve

Belül **erőforráscsoport** összetevő, a következő feladatütemezési sorrendben szolgál az összetevők adott erőforráscsoporton belül kell létrehozni:

- Erőforrás-csoport gyermek **szerepkör-hozzárendelés** összetevők szerelvényösszetevő-név szerint rendezve
- Erőforrás-csoport gyermek **szabályzat-hozzárendelés** összetevők szerelvényösszetevő-név szerint rendezve
- Erőforrás-csoport gyermek **Azure Resource Manager-sablon** összetevők szerelvényösszetevő-név szerint rendezve

## <a name="customizing-the-sequencing-order"></a>Az alkalmazás-előkészítés sorrendjének testreszabása

Nagy tervezetek létrehozása, amikor a meghatározott sorrendben létrehozandó erőforrás szükség lehet. Ebben a forgatókönyvben a leggyakoribb használati mintáját akkor, ha a tervrajz több Azure Resource Manager-sablont is tartalmaz. Tervezetek kezeli ezt a mintát azáltal, hogy az alkalmazás-előkészítés sorrendben kell definiálni.

Az eredménykészlet definiálásával történik egy `dependsOn` a JSON-tulajdonságot. Csak a tervezet (az erőforráscsoportok) és az összetevő objektumok támogatja ezt a tulajdonságot. `dependsOn` az összetevő neve, amely az adott összetevő kell létrehozni, mielőtt a létrehozás egy karakterlánc-tömbben.

> [!NOTE]
> **Erőforráscsoport** összetevők támogatja a `dependsOn` tulajdonság, azonban nem lehet célja egy `dependsOn` bármely összetevő típusa szerint.

### <a name="example---blueprint-with-ordered-resource-group"></a>Példa – tervezet rendezett erőforráscsoport

Ebben a példában tervezet egy erőforráscsoportot, amely egy egyéni alkalmazás-előkészítés sorrendben definiált értéket deklarálásával `dependsOn`, és a egy standard erőforráscsoportot. Ebben az esetben a lehívandó összetevő nevű **assignPolicyTags** előtt a program feldolgozza a **rendezett-rg** erőforráscsoportot. **Standard-rg** az alapértelmezett műveleti sorrend sorrend szerint lesz feldolgozva.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Példa – egyéni sorrendjében összetevő

Ebben a példában egy szabályzat-összetevő, amely egy Azure Resource Manager-sablon függ. Alapértelmezett rendezése, amelyet egy házirend összetevő létrehozott előtt az Azure Resource Manager-sablon. A rendezés lehetővé teszi, hogy a szabályzat lehívandó összetevő várja meg, az Azure Resource Manager-sablonok létrehozását.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

## <a name="processing-the-customized-sequence"></a>A testre szabott feladatütemezési feldolgozása

A létrehozási folyamat során a topológiai rendezést a függőségi grafikon a tervek összetevők létrehozására szolgál. Az ellenőrzés biztosítja, hogy az egyes fenyegetési erőforráscsoportok és összetevők közötti függőség támogatott.

Ha az összetevő függ, amely nem módosítható az alapértelmezett sorrend deklarálva van, majd nem történik változás. Ilyen például, egy erőforráscsoportot, amely egy előfizetés-szintű szabályzat függ. Egy másik példa, erőforrás csoport "standard-rg" gyermek szabályzat-hozzárendelés, amely a "standard-rg erőforráscsoportban" gyermek szerepkör-hozzárendelés függ. Mindkét esetben a `dependsOn` nem módosította az alapértelmezett műveleti sorrend sorrendjét és a módosítások nem kerül sor.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](lifecycle.md)
- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása
- A [tervek erőforrás-zárolásának](resource-locking.md) alkalmazásával kapcsolatos részletek
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md)