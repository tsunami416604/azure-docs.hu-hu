---
title: Az Azure-tervek telepítési sorrendjét ismertetése
description: Ismerje meg az életciklus-tervrajz halad át, és minden egyes szakaszhoz részleteit.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955452"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Az Azure-tervek telepítési sorrendjét ismertetése

Azure tervek az egy **alkalmazás-előkészítés rendelés** erőforrás-létrehozás sorrendjének meghatározásához tervrajz hozzárendelésének feldolgozása során. Ez a cikk ismerteti az alapértelmezett műveleti sorrend sorrend használt, a rendelés testreszabása és a testreszabott rendelés feldolgozásának módja.

A JSON-példák, amelyek a saját értékeire cserélni kell változók vannak:

- `{YourMG}` – Cserélje le a felügyeleti csoport neve

## <a name="default-sequencing-order"></a>Alapértelmezett alkalmazás-előkészítés sorrend

Ha a tervezet nem ahhoz, hogy az összetevők üzembe helyezése a direktívákat tartalmaz, vagy az irányelv null értékű, a következő sorrendben szolgál:

- Előfizetési szinten **szerepkör-hozzárendelés** összetevők szerelvényösszetevő-név szerint rendezve
- Előfizetési szinten **szabályzat-hozzárendelés** összetevők szerelvényösszetevő-név szerint rendezve
- Előfizetési szinten **Azure Resource Manager-sablon** összetevők szerelvényösszetevő-név szerint rendezve
- **Erőforráscsoport** összetevőket (beleértve az alárendelt összetevők) helyőrző név szerint rendezve

Belül **erőforráscsoport** összetevő feldolgozott, a következő feladatütemezési sorrendben szolgál az összetevők adott erőforráscsoporton belül kell létrehozni:

- Erőforrás-csoport gyermek **szerepkör-hozzárendelés** összetevők szerelvényösszetevő-név szerint rendezve
- Erőforrás-csoport gyermek **szabályzat-hozzárendelés** összetevők szerelvényösszetevő-név szerint rendezve
- Erőforrás-csoport gyermek **Azure Resource Manager-sablon** összetevők szerelvényösszetevő-név szerint rendezve

## <a name="customizing-the-sequencing-order"></a>Az alkalmazás-előkészítés sorrendjének testreszabása

Nagy tervezetek létrehozása, ha egy másik erőforráshoz való kapcsolat a meghatározott sorrendben létrehozandó erőforrás szükség lehet. Ez leggyakrabban használt használata minta akkor, ha a tervrajz magában foglalja a több Azure Resource Manager-sablonok. Tervezetek kezeli ezt azáltal, hogy az alkalmazás-előkészítés sorrendben kell definiálni.

Mindez definiálásával egy `dependsOn` a JSON-tulajdonságot. Csak a tervezet (az erőforráscsoportok) és az összetevő objektumok támogatja ezt a tulajdonságot. `dependsOn` az összetevő neve, amely az adott összetevő kell létrehozni, mielőtt a létrehozás egy karakterlánc-tömbben.

### <a name="example---blueprint-with-ordered-resource-group"></a>Példa – tervezet rendezett erőforráscsoport

Ez az egy erőforráscsoport, amely egy egyéni alkalmazás-előkészítés sorrendben definiált értéket deklarálásával egy példa tervezet `dependsOn`, és a egy standard erőforráscsoportot. Ebben az esetben a lehívandó összetevő nevű **assignPolicyTags** előtt a program feldolgozza a **rendezett-rg** erőforráscsoportot.
**Standard-rg** az alapértelmezett műveleti sorrend sorrend szerint lesz feldolgozva.

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

Ez a példa házirend összetevőt, amely egy Azure Resource Manager-sablon függ. Alapértelmezett rendezése, amelyet egy házirend összetevő létrehozott előtt az Azure Resource Manager-sablon. Ez lehetővé teszi, hogy a szabályzat lehívandó összetevő várja meg, az Azure Resource Manager-sablonok létrehozását.

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

A létrehozási folyamat során a topológiai rendezést a függőségi grafikon a tervezet és az összetevői létrehozására szolgál. Ez biztosítja, hogy erőforráscsoportok és összetevők közötti függőség több szintjét támogatja.

Függőség van deklarálva a tervezet vagy egy összetevő, amely az alapértelmezett sorrend nem módosítható, ha semmilyen változás nem az alkalmazás-előkészítés rendelés van végrehajtva. A példák egy erőforráscsoportot, amely előfizetési szintű házirend vagy az erőforrás csoport "standard-rg" gyermek szabályzat-hozzárendelés, amely a "standard-rg erőforráscsoportban" gyermek szerepkör-hozzárendelés függ attól függ. Mindkét esetben a `dependsOn` nem módosította az alapértelmezett műveleti sorrend sorrendjét és a módosítások nem kerül sor.

## <a name="next-steps"></a>További lépések

- További információ a [tervezetet életciklus](lifecycle.md)
- Megtudhatja, hogyan használja [statikus és dinamikus paraméterek](parameters.md)
- Ismerje meg, győződjön meg arról, hogyan használhatja az [tervezetet erőforrás zárolása](resource-locking.md)
- Ismerje meg, hogyan [meglévő hozzárendelések frissítése](../how-to/update-existing-assignments.md)
- A tervrajz hozzárendelésének során felmerülő problémák megoldása [általános hibaelhárítási](../troubleshoot/general.md)