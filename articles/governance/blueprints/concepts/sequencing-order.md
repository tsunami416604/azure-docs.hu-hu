---
title: A telepítési sorrend ismertetése
description: Ismerje meg az alapértelmezett sorrendet, amelyben a tervezet összetevők üzembe helyezése során egy tervezet-hozzárendelés, és hogyan szabhatja testre a telepítési sorrendben.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 41b1b1ada5b7c6c919f227927001570332eeccbf
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677563"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Ismerje meg a központi telepítési sorrendet az Azure Blueprints-ben

Az Azure Blueprints egy **szekvenálási sorrendet** használ az erőforrás-létrehozás sorrendjének meghatározásához egy tervezetdefiníció hozzárendelésének feldolgozásakor. Ez a cikk a következő fogalmakat ismerteti:

- A használt alapértelmezett szekvenálási sorrend
- A sorrend testreszabása
- A testreszabott rendelés feldolgozása

Vannak változók a JSON példákat, hogy meg kell cserélni a saját értékeket:

- `{YourMG}` – Cserélje le a felügyeleti csoport nevére

## <a name="default-sequencing-order"></a>Alapértelmezett sorrend

Ha a tervezet definíciója nem tartalmaz irányelvet az összetevők telepítésének végzésére vonatkozóan, vagy az irányelv null értékű, akkor a következő sorrendet használja a következő:

- Előfizetési szintű **szerepkör-hozzárendelési** összetevők műtermék neve szerint rendezve
- Előfizetési szintű **házirend-hozzárendelési** összetevők műtermék neve szerint rendezve
- Előfizetési szintű **Azure Resource Manager-sablonösszetevők** műtermék név szerint rendezve
- **Erőforráscsoport-összetevők** (beleértve a gyermekösszetevőket is) helyőrző név szerint rendezve

Az egyes **erőforráscsoportok** összetevőin belül a következő szekvenciasorrend et használja a következő összetevők az adott erőforráscsoporton belül létrehozandó összetevőkhöz:

- Erőforráscsoport **gyermekszerepkör-hozzárendelési** összetevői, műtermék neve szerint rendezve
- Erőforráscsoport **gyermekházirend-hozzárendelési** összetevői, műtermék név szerint rendezve
- Erőforráscsoport **gyermek Azure Resource Manager sablon** összetevők műtermék neve szerint rendezve

> [!NOTE]
> A [műtermékek()](../reference/blueprint-functions.md#artifacts) használata implicit függőséget hoz létre a hivatkozott műterméktől.

## <a name="customizing-the-sequencing-order"></a>A sorrend testreszabása

Nagy tervezetdefiníciók írásakor szükség lehet az erőforrások adott sorrendben történő létrehozásához. A forgatókönyv leggyakoribb használati mintája az, amikor egy tervezetdefiníció több Azure Resource Manager-sablont tartalmaz. Az Azure Blueprints kezeli ezt a mintát azáltal, hogy lehetővé teszi a szekvenálási sorrend ben kell meghatározni.

A rendezés a JSON-ban egy `dependsOn` tulajdonság meghatározásával érhető el. A tervezet definíciója erőforráscsoportokhoz és műtermék-objektumokhoz támogatja ezt a tulajdonságot. `dependsOn`A műterméknevek karakterlánctömbje, amelyet az adott összetevőnek létre kell hoznia a létrehozása előtt.

> [!NOTE]
> Tervrajz-objektumok létrehozásakor minden műtermék-erőforrás a fájlnévről kapja a nevét, ha [A PowerShellt](/powershell/module/az.blueprint/new-azblueprintartifact)vagy az URL-végpontot használja, ha [REST API-t](/rest/api/blueprints/artifacts/createorupdate)használ.
> _az_ összetevőkben szereplő erőforráscsoport-hivatkozásoknak meg kell egyezniük a tervezetdefinícióban meghatározotthivatkozásokkal.

### <a name="example---ordered-resource-group"></a>Példa - rendezett erőforráscsoport

Ez a példa tervezet definíciója rendelkezik egy erőforráscsoport, amely meghatározott egyéni `dependsOn`sorrendben deklarálásával értéket, valamint egy szabványos erőforráscsoport. Ebben az esetben a **assignPolicyTags** nevű összetevő feldolgozása a **rendezett-rg** erőforráscsoport előtt kerül feldolgozásra.
**a standard-rg** feldolgozása az alapértelmezett sorrend szerint történik.

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
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Példa - műtermék egyéni sorrenddel

Ez a példa egy olyan házirend-összetevő, amely egy Azure Resource Manager-sablontól függ. Alapértelmezés szerint a rendezési összetevő jön létre az Azure Resource Manager sablon előtt. Ez a rendelés lehetővé teszi, hogy a házirend-összetevő megvárja az Azure Resource Manager sablon létrehozását.

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
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Példa – előfizetési szintű sablonösszetevő egy erőforráscsoporttól függően

Ez a példa egy erőforrás-kezelő sablon üzembe helyezett az előfizetés szintjén függ egy erőforráscsoport. Az alapértelmezett rendezési, az előfizetési szintű összetevők jönnek létre, mielőtt az erőforráscsoportok és a gyermek összetevők az adott erőforráscsoportokban. Az erőforráscsoport a tervezetdefinícióban a következőképpen van definiálva:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Az előfizetési szintű sablon összetevő a **várakozási idő** erőforráscsoporttól függően a következőképpen van definiálva:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>A testreszabott sorrend feldolgozása

A létrehozási folyamat során egy topológiai rendezési létrehozásához a tervrajzok összetevők függőségi grafikonja. Az ellenőrzés biztosítja, hogy az erőforráscsoportok és az összetevők közötti függőség minden szintje támogatott.

Ha egy műtermék-függőség deklarálva van, amely nem változtatja meg az alapértelmezett sorrendet, akkor nem történik változás. Egy példa egy erőforráscsoport, amely egy előfizetési szintű szabályzattól függ. Egy másik példa egy erőforráscsoport "standard-rg" gyermekházirend-hozzárendelés, amely a "standard-rg" gyermekszerepkör-hozzárendeléstől függ. Mindkét esetben a `dependsOn` nem módosította volna az alapértelmezett sorrendet, és nem történt módosítás.

## <a name="next-steps"></a>További lépések

- További információ a [tervterv életciklusáról.](lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](parameters.md)használni.
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../how-to/update-existing-assignments.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.