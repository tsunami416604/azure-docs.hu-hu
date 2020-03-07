---
title: A szabályzat-hozzárendelési struktúra részletei
description: Az Azure Policy által használt szabályzat-hozzárendelési definíciót ismerteti az erőforrásokhoz a kiértékeléshez.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355054"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-hozzárendelés struktúrája

A szabályzat-hozzárendeléseket a Azure Policy használja annak meghatározásához, hogy mely erőforrások legyenek hozzárendelve házirendekhez vagy kezdeményezésekhez. A szabályzat-hozzárendelés meghatározhatja az adott erőforráscsoport paramétereinek értékeit a hozzárendelés időpontjában, így lehetőség van arra, hogy újrahasznosítsa azokat a házirend-definíciókat, amelyek a különböző megfelelőségi igényeknek megfelelő erőforrás-tulajdonságokat kezelik.

A JSON használatával szabályzat-hozzárendelést hozhat létre. A szabályzat-hozzárendelés a következő elemeket tartalmazza:

- Megjelenített név
- leírás
- metaadatok
- kényszerítési mód
- házirend-definíció
- paraméterek

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

A **enforcementMode** tulajdonság lehetővé teszi a felhasználók számára, hogy a házirend érvénybe léptetése vagy az [Azure-műveletnapló](../../../azure-monitor/platform/platform-logs-overview.md)bejegyzéseinek elindítása nélkül teszteljék a meglévő erőforrásokra vonatkozó szabályzatok eredményét. Ezt a forgatókönyvet általában "What If"-ként emlegetik, és a biztonságos üzembe helyezési gyakorlatokhoz igazodik. a **enforcementMode** eltér a [letiltott](./effects.md#disabled) effektustól, mivel ennek hatására az erőforrás-kiértékelés egyáltalán nem történik meg.

Ennek a tulajdonságnak a következő értékei vannak:

|Mód |JSON-érték |Típus |Szervizelés manuálisan |Tevékenység naplójának bejegyzése |Leírás |
|-|-|-|-|-|-|
|Engedélyezve |Alapértelmezett |sztring |Igen |Igen |A házirend hatása az erőforrás létrehozásakor vagy frissítésekor lép érvénybe. |
|Letiltva |DoNotEnforce |sztring |Igen |Nem | A házirend-effektus nem kényszerített az erőforrás létrehozásakor vagy frissítésekor. |

Ha a **enforcementMode** nincs megadva házirend-vagy kezdeményezési definícióban, a rendszer az _alapértelmezett_ értéket használja. A [szervizelési feladatok](../how-to/remediate-resources.md) a [deployIfNotExists](./effects.md#deployifnotexists) házirendek esetében is elindíthatók, még akkor is, ha a **enforcementMode** beállítása _DoNotEnforce_.

## <a name="policy-definition-id"></a>Házirend-definíció azonosítója

Ebben a mezőben egy házirend-definíció vagy egy kezdeményezési definíció teljes elérési útjának kell szerepelnie.
`policyDefinitionId` karakterlánc, nem tömb. Javasoljuk, hogy ha több házirendet gyakran társítanak egymáshoz, inkább egy [kezdeményezést](./definition-structure.md#initiatives) használjon.

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

Ebben a példában a házirend-definícióban korábban definiált paraméterek `prefix` és `suffix`. Ez az adott házirend-hozzárendelési csoport a **depta** és a `suffix` – **LC**értékre `prefix`. Ugyanez a házirend-definíció a különböző részlegekhez tartozó paraméterek eltérő készletével használható, így csökkentve a házirend-definíciók ismétlődését és összetettségét, miközben rugalmasságot biztosít.

## <a name="next-steps"></a>Következő lépések

- A szabályzat- [definíciós struktúra](./definition-structure.md)megismerése.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).