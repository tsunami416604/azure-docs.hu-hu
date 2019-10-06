---
title: Az üzembe helyezési sorrend sorrendjének megismerése
description: Ismerje meg a terv meghatározásának életciklusát, valamint az egyes szakaszok részleteit.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: bda7a6caea931a993a6ddd6731688792bf0b3948
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981018"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Az üzembe helyezési folyamat ismertetése az Azure-tervezetekben

Az Azure-tervrajzok az **előkészítési sorrend** alapján határozzák meg az erőforrás-létrehozási sorrendet, amikor feldolgozzák a terv definíciójának hozzárendelését. Ez a cikk a következő fogalmakat ismerteti:

- A használt alapértelmezett sorrendi sorrend
- A sorrend testreszabása
- A testre szabott sorrend feldolgozása

A JSON-példákban változók szerepelnek, amelyeket a saját értékeivel kell helyettesítenie:

- `{YourMG}` – Cserélje le a felügyeleti csoport nevére

## <a name="default-sequencing-order"></a>Alapértelmezett sorrendi sorrend

Ha a terv meghatározása nem tartalmaz irányelvet az összetevők üzembe helyezéséhez, vagy ha a direktíva null értékű, a rendszer a következő sorrendet használja:

- Előfizetés szintű **szerepkör-hozzárendelési** összetevők az összetevő neve szerint rendezve
- Az előfizetési szint **házirendjének hozzárendelési** összetevői az összetevő neve szerint rendezve
- Az előfizetési szint **Azure Resource Manager a sablon** az összetevő neve alapján rendezve
- **Erőforráscsoport** -összetevők (beleértve a gyermekeket is) helyőrző név szerint rendezve

Minden **erőforráscsoport** -összetevőn belül a következő sorrendet használja a rendszer az adott erőforráscsoport-beli összetevők létrehozásához:

- Erőforráscsoport alárendelt **szerepkör-hozzárendelési** összetevői az összetevő neve szerint rendezve
- Erőforráscsoport-alárendelt **házirend-hozzárendelési** összetevők az összetevő neve alapján rendezve
- Erőforráscsoport-gyermek **Azure Resource Manager sablon** -összetevők az összetevő neve alapján rendezve

> [!NOTE]
> Az összetevők használata [()](../reference/blueprint-functions.md#artifacts) egy implicit függőséget hoz létre a hivatkozott összetevőtől.

## <a name="customizing-the-sequencing-order"></a>Az előkészítési sorrend testreszabása

Nagyméretű tervrajzok meghatározásakor szükség lehet arra, hogy az erőforrások egy adott sorrendben jöjjenek létre. Ennek a forgatókönyvnek a leggyakoribb felhasználási módja az, amikor a terv definíciója több Azure Resource Manager sablont tartalmaz. A tervrajzok úgy kezelik ezt a mintát, hogy lehetővé teszik az előkészítési sorrend meghatározását.

A rendelés a JSON-ban található `dependsOn` tulajdonság definiálásával valósítható meg. A terv definíciója, az erőforráscsoportok és az összetevő-objektumok támogatják ezt a tulajdonságot. a `dependsOn` egy olyan karakterlánc-tömb, amelyben az adott összetevő létrehozása előtt létre kell hozni az adott összetevőt.

> [!NOTE]
> A tervrajzi objektumok létrehozásakor minden összetevő-erőforrás megkapja a nevét a fájlnévből, ha a [PowerShellt](/powershell/module/az.blueprint/new-azblueprintartifact)használja, vagy az URL-végpontot, ha [REST API](/rest/api/blueprints/artifacts/createorupdate)használ.
> az összetevőkben található _resourceGroup_ -hivatkozásoknak meg kell egyezniük a terv definíciójában definiált értékekkel.

### <a name="example---ordered-resource-group"></a>Példa szerint rendezett erőforráscsoport

Ebben a példában a terv definíciója olyan erőforráscsoportot tartalmaz, amely egyéni sorrendi sorrendet definiált a `dependsOn` értéknek a szabványos erőforráscsoporthoz való deklarálása mellett. Ebben az esetben a **assignPolicyTags** nevű összetevő feldolgozása a **rendezett-RG** erőforráscsoport előtt történik.
a **standard-RG** az alapértelmezett sorrend szerint lesz feldolgozva.

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

### <a name="example---artifact-with-custom-order"></a>Példa – egyéni sorrendű összetevő

Ez a példa olyan házirend-összetevő, amely egy Azure Resource Manager sablontól függ. Alapértelmezés szerint a rendszer egy házirend-összetevőt hoz létre a Azure Resource Manager sablon előtt. Ez a rendezés lehetővé teszi, hogy a házirend-összetevő megvárja a Azure Resource Manager sablon létrehozását.

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

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Példa – az előfizetés szintű sablon összetevő az erőforráscsoport függvényében

Ez a példa egy olyan Resource Manager-sablonra vonatkozik, amely az előfizetés szintjén van üzembe helyezve, hogy az erőforráscsoport függjön. Az alapértelmezett rendezés során a rendszer az előfizetési szint összetevőit minden erőforráscsoport és alárendelt összetevő előtt hozza létre. Az erőforráscsoport a terv definíciójában van definiálva, például a következőhöz:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Az előfizetési szint sablonja a **WAIT-on-Me** erőforráscsoporthoz függően a következőképpen van definiálva:

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

## <a name="processing-the-customized-sequence"></a>A testreszabott folyamat feldolgozása

A létrehozási folyamat során a rendszer egy topológiai rendezést használ a tervrajzok függőségi gráfjának létrehozásához. Az ellenőrzés során az erőforráscsoportok és az összetevők közötti függőségek is támogatottak.

Ha olyan összetevő-függőség van deklarálva, amely nem változtatja meg az alapértelmezett sorrendet, akkor nem történt módosítás. Ilyen például egy olyan erőforráscsoport, amely egy előfizetési szint házirendjétől függ. Egy másik példa egy "standard-RG" alárendelt házirend-hozzárendelés, amely az erőforráscsoport "standard-RG" alárendelt szerepkör-hozzárendeléstől függ. Mindkét esetben a `dependsOn` nem változtatta meg az alapértelmezett sorrendi sorrendet, és nem történt módosítás.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](lifecycle.md).
- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása.
- A [tervek erőforrás-zárolásának](resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).