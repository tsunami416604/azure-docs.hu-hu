---
title: 'Oktatóanyag: Címkecégirányítás kezelése'
description: Ebben az oktatóanyagban az Azure-szabályzat modify hatása használatával hozzon létre és kényszerítsen egy címkecégirányítási modellt az új és a meglévő erőforrásokon.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: 5e9cb9a4acb930c117374281a3debaeecce47110
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75965983"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Oktatóanyag: Címkecégirányítás kezelése az Azure Policy használatával

[A címkék](../../../azure-resource-manager/management/tag-resources.md) fontos részét képezik az Azure-erőforrások rendszerezésének egy taxonómiai rendszerbe rendezésében. Ha a [címkekezeléssel kapcsolatos gyakorlati tanácsokat](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)követi, a címkék alapul adhatnak az üzleti szabályzatok Azure Policy használatával történő alkalmazásához, illetve a [költségkezelés sel kapcsolatos költségek nyomon követéséhez.](../../../cost-management-billing/costs/cost-mgt-best-practices.md#organize-and-tag-your-resources)
Nem számít, hogyan és miért használja a címkéket, fontos, hogy gyorsan hozzáadhatja, módosíthatja és eltávolíthatja ezeket a címkéket az Azure-erőforrásokból.

Az Azure Policy [modify](../concepts/effects.md#modify) hatása célja, hogy segítse a címkék irányítását, függetlenül attól, hogy milyen szakaszában erőforrás-kormányzás ön-ban. **A módosítás** akkor segít, ha:

- Új a felhőben, és nincs címkeszabályozása
- Már több ezer erőforrással rendelkezik címkeirányítás nélkül
- Már rendelkezik egy meglévő taxonómiával, amelyet módosítani kell

Ebben az oktatóanyagban a következő feladatokat hajthatja végre:

> [!div class="checklist"]
> - Azonosítsa üzleti igényeit
> - Az egyes követelmények hozzárendelése házirend-definícióhoz
> - A címkeházirendek csoportosítása kezdeményezésbe

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="identify-requirements"></a>Követelmények azonosítása

Mint minden jó végrehajtása irányítási ellenőrzések, a követelményeknek meg kell származnia az üzleti igényeket, és jól érthető létrehozása előtt technikai ellenőrzéseket. Ebben a forgatókönyvben oktatóanyag esetén a következő elemek tartoznak az üzleti követelményeinkhez:

- Két szükséges címke az összes erőforráson: _CostCenter_ és _Env_
- _A CostCenternak_ minden tárolón és erőforráson léteznie kell
  - Az erőforrások örökölnek attól a tárolótól, amelyben vannak, de egyénileg felülbírálhatók
- _Env_ kell léteznie minden tárolók és az egyes erőforrások
  - Az erőforrások tárolóelnevezési séma szerint határozzák meg a környezetet, és nem bírálhatók felül
  - A tárolóban lévő összes erőforrás ugyanannak a környezetnek a része

## <a name="configure-the-costcenter-tag"></a>A CostCenter címke konfigurálása

Az Azure Policy által kezelt Azure-környezetre jellemző, a _CostCenter-címke_ követelmények a következőket kérik:

- A CostCenter-címkét nem jelző erőforráscsoportok _megtagadása_
- Erőforrások módosítása a _CostCenter_ címke hozzáadásához a szülő erőforráscsoportból, ha hiányzik

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>A CostCenter-címkét nem jelző erőforráscsoportok megtagadása

Mivel egy erőforráscsoport _CostCenter-e_ nem határozható meg az erőforráscsoport neve alapján, az erőforráscsoport létrehozásához a kérelemben meg kell határoznia a címkét. A [Megtagadás](../concepts/effects.md#deny) hatással rendelkező következő házirendszabály megakadályozza a _CostCenter_ címkével nem rendelkező erőforráscsoportok létrehozását vagy frissítését:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Mivel ez a házirendszabály egy erőforráscsoportot céloz meg, a házirend-definíció ban szereplő _módnak_ "Minden" típusúnak kell lennie az "Indexelt" helyett.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Erőforrások módosítása a CostCenter címke örökléséhez, ha hiányzik

A _costcenter_ második igénye az, hogy minden erőforrás örökölje a címkét a szülő erőforráscsoportból, ha hiányzik. Ha a címke már definiálva van az erőforráson, még akkor is, ha eltér a szülő erőforráscsoporttól, akkor azt békén kell hagyni. A következő házirendszabály [a Módosítás :](../concepts/effects.md#modify)

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Ez a házirendszabály az **add** operation helyett **addOrReplace,** mivel nem szeretnénk módosítani a címke értékét, ha jelen van a meglévő erőforrások [kiivantén.](../how-to/remediate-resources.md) A `[resourcegroup()]` sablon függvényt is használja a címke értékének leéséhez a szülő erőforráscsoportból.

> [!NOTE]
> Mivel ez a házirendszabály a címkéket támogató erőforrásokat célozza meg, a házirend-definíció ban szereplő _módnak_ "Indexelt" kell lennie. Ez a konfiguráció azt is biztosítja, hogy ez a házirend kihagyja az erőforráscsoportokat.

## <a name="configure-the-env-tag"></a>Az Env címke konfigurálása

Az Azure-szabályzat által kezelt Azure-környezetre jellemző _env-címke_ követelmények a következőket kérik:

- Az _Env_ erőforráscsoport Env-címkéjének módosítása az erőforráscsoport elnevezési séma alapján
- Az Erőforráscsoport összes erőforrásának _Env_ címkéjének módosítása a szülő erőforráscsoporttal azonos

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Erőforráscsoportok módosítása Env címke név alapján

Az Azure-környezetben létező minden környezethez [módosítási](../concepts/effects.md#modify) szabályzat szükséges. Az egyes házirend módosítása a következő házirend-definícióhoz hasonlóan néz ki:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Mivel ez a házirendszabály egy erőforráscsoportot céloz meg, a házirend-definíció ban szereplő _módnak_ "Minden" típusúnak kell lennie az "Indexelt" helyett.

Ez a házirend csak a termelési erőforrásokhoz használt `prd-`mintaelnevezési sémával egyezteti az erőforráscsoportokat. Bonyolultabb elnevezési sémák érhető el több **egyezési** feltételek helyett az **egyetlen, mint** ebben a példában.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Erőforrások módosítása az Env címke örökléséhez

Az üzleti követelmény minden erőforrásszámára megköveteli, hogy a szülő erőforráscsoport által megadott _Env_ címke legyen. Ez a címke nem bírálható felül, ezért az **addOrReplace** műveletet a [Modify effektussal](../concepts/effects.md#modify) fogjuk használni. A módosítási minta a következő szabálynak tűnik:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Mivel ez a házirendszabály a címkéket támogató erőforrásokat célozza meg, a házirend-definíció ban szereplő _módnak_ "Indexelt" kell lennie. Ez a konfiguráció azt is biztosítja, hogy ez a házirend kihagyja az erőforráscsoportokat.

Ez a házirendszabály minden olyan erőforrást keres, amely nem rendelkezik szülő erőforráscsoport-értékkel az _Env_ címkéhez, vagy hiányzik az _Env_ címke. Az egyező erőforrások _Env_ címkéje a szülő erőforráscsoportok értékére van állítva, még akkor is, ha a címke már létezett az erőforráson, de más értékkel.

## <a name="assign-the-initiative-and-remediate-resources"></a>A kezdeményezés hozzárendelése és erőforrások kiújítása

A fenti címkeszabályzatok létrehozása után csatlakozzon hozzájuk egyetlen kezdeményezésbe a címkeszabályozáshoz, és rendelje hozzá őket egy felügyeleti csoporthoz vagy előfizetéshez. A kezdeményezés és a benne foglalt szabályzatok majd értékeli a meglévő erőforrások megfelelőségét, és módosítja az új vagy frissített erőforrások, amelyek megfelelnek az **if** tulajdonság a házirend-szabályban. A szabályzat azonban nem frissíti automatikusan a meglévő nem megfelelő erőforrásokat a megadott címkemódosításokkal.

A [deployIfNotExists](../concepts/effects.md#deployifnotexists) házirendekhez hasonlóan a **Modify** házirend is szervizelési feladatokat használ a meglévő nem megfelelő erőforrások módosításához. Kövesse az útmutató [útmutató utasításait](../how-to/remediate-resources.md) a nem megfelelő **erőforrások módosításához** és a címkék nek a megadott rendszeradatokhoz való javításához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az oktatóanyagból származó erőforrásokkal, az alábbi lépésekkel törölheti a fent létrehozott hozzárendelések vagy definíciók bármelyikét:

1. Válassza **a Definíciók** (vagy **hozzárendelések,** ha egy hozzárendelést próbál törölni) lehetőséget az Azure Policy lap bal oldalán található **Szerzői** műveletek területen.

1. Keresse meg az eltávolítani kívánt új kezdeményezést vagy szabályzatdefiníciót (vagy hozzárendelést).

1. Kattintson a jobb gombbal a sorra, vagy a bal gombbal a definíció (vagy a hozzárendelés) mellett található három pontra, majd a **Definíció törlése** (vagy a **Hozzárendelés törlése**) parancsra.

## <a name="review"></a>Áttekintés

Ebben az oktatóanyagban a következő feladatokról szerzett tudomást:

> [!div class="checklist"]
> - Az üzleti követelmények azonosítása
> - Minden követelmény tanod egy házirend-definícióhoz
> - A címkeházirendek csoportosítása egy kezdeményezésbe

## <a name="next-steps"></a>További lépések

A szabályzatdefiníciók szerkezetéről szóló további információkért lásd az alábbi cikket:

> [!div class="nextstepaction"]
> [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)