---
title: A szabályzat-hozzárendelési struktúra részletei
description: Az Azure Policy által használt szabályzat-hozzárendelési definíciót ismerteti az erőforrásokhoz a kiértékeléshez.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 5326e765701a42323ea62df8d35128c4117b2ed9
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981419"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-hozzárendelés struktúrája

A Azure Policy a szabályzat-hozzárendelések segítségével határozzák meg, hogy mely erőforrások legyenek hozzárendelve házirendekhez vagy kezdeményezésekhez. A szabályzat-hozzárendelés meghatározhatja az adott erőforráscsoport paramétereinek értékeit a hozzárendelés időpontjában, így lehetőség van arra, hogy újrahasznosítsa azokat a házirend-definíciókat, amelyek a különböző megfelelőségi igényeknek megfelelő erőforrás-tulajdonságokat kezelik.

A JSON használatával szabályzat-hozzárendelést hozhat létre. A szabályzat-hozzárendelés a következő elemeket tartalmazza:

- Megjelenített név
- description
- metaadatok
- kényszerítési mód
- házirend-definíció
- parameters

Például a következő JSON egy szabályzat-hozzárendelést mutat be _DoNotEnforce_ módban dinamikus paraméterekkel:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Az összes Azure Policy minta [Azure Policy minta](../samples/index.md).

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **DisplayName** és a **Leírás** használatával azonosíthatja a szabályzat-hozzárendelést, és megadhatja a környezetét az adott erőforrásokkal való használathoz. a **DisplayName** maximális hossza _128_ karakter, és a **Leírás** legfeljebb _512_ karakter hosszúságú lehet.

## <a name="enforcement-mode"></a>Kényszerítési mód

A **enforcementMode** tulajdonság lehetővé teszi a felhasználók számára, hogy a házirend érvénybe léptetése vagy az [Azure-műveletnapló](../../../azure-monitor/platform/activity-logs-overview.md)bejegyzéseinek elindítása nélkül teszteljék a meglévő erőforrásokra vonatkozó szabályzatok eredményét.
Ezt a forgatókönyvet általában "What If"-ként emlegetik, és a biztonságos üzembe helyezési gyakorlatokhoz igazodik.

Ennek a tulajdonságnak a következő értékei vannak:

|Mód |JSON-érték |Type |Szervizelés manuálisan |Tevékenység naplójának bejegyzése |Leírás |
|-|-|-|-|-|-|
|Enabled |Alapértelmezett |Karakterlánc |Igen |Igen |A házirend hatása az erőforrás létrehozásakor vagy frissítésekor lép érvénybe. |
|Letiltva |DoNotEnforce |Karakterlánc |Igen |Nem | A házirend-effektus nem kényszerített az erőforrás létrehozásakor vagy frissítésekor. |

Ha a **enforcementMode** nincs megadva házirend-vagy kezdeményezési definícióban, a rendszer az _alapértelmezett_ értéket használja. A [szervizelési feladatok](../how-to/remediate-resources.md) a [deployIfNotExists](./effects.md#deployifnotexists) házirendek esetében is elindíthatók, még akkor is, ha a **enforcementMode** beállítása _DoNotEnforce_.

## <a name="policy-definition-id"></a>Házirend-definíció azonosítója

Ebben a mezőben egy házirend-definíció vagy egy kezdeményezési definíció teljes elérési útjának kell szerepelnie.
@no__t – 0 karakterlánc, nem tömb. Javasoljuk, hogy ha több házirendet gyakran társítanak egymáshoz, inkább egy [kezdeményezést](./definition-structure.md#initiatives) használjon.

## <a name="parameters"></a>Paraméterek

A szabályzat-hozzárendelés ezen szegmense megadja a szabályzat- [definícióban vagy a kezdeményezési definícióban](./definition-structure.md#parameters)definiált paraméterek értékeit.
Ez a kialakítás lehetővé teszi egy házirend vagy kezdeményezési definíció más erőforrásokkal való felhasználását, de különböző üzleti értékeket vagy eredményeket kereshet.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

Ebben a példában a házirend-definícióban korábban definiált paraméterek a következők: `prefix` és `suffix`. Ez az adott szabályzat-hozzárendelés @no__t – 0 – **depta** és `suffix` – **LC**. Ugyanez a házirend-definíció a különböző részlegekhez tartozó paraméterek eltérő készletével használható, így csökkentve a házirend-definíciók ismétlődését és összetettségét, miközben rugalmasságot biztosít.

## <a name="next-steps"></a>További lépések

- A szabályzat- [definíciós struktúra](./definition-structure.md)megismerése.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).