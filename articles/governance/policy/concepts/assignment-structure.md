---
title: A szabályzat-hozzárendelési struktúra részletei
description: Az Azure Policy által használt szabályzat-hozzárendelési definíciót ismerteti az erőforrásokhoz a kiértékeléshez.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: c9f400aa72508822f8fff2fe41fb17ce98339737
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84205041"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-hozzárendelés struktúrája

A szabályzat-hozzárendeléseket a Azure Policy használja annak meghatározásához, hogy mely erőforrások legyenek hozzárendelve házirendekhez vagy kezdeményezésekhez. A szabályzat-hozzárendelés meghatározhatja az adott erőforráscsoport paramétereinek értékeit a hozzárendelés időpontjában, így lehetőség van arra, hogy újrahasznosítsa azokat a házirend-definíciókat, amelyek a különböző megfelelőségi igényeknek megfelelő erőforrás-tulajdonságokat kezelik.

A JSON használatával szabályzat-hozzárendelést hozhat létre. A szabályzat-hozzárendelés a következő elemeket tartalmazza:

- megjelenítendő név
- leírás
- metaadatok
- kényszerítési mód
- kizárt hatókörök
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
        "notScopes": [],
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

## <a name="excluded-scopes"></a>Kizárt hatókörök

A hozzárendelés **hatóköre** tartalmazza az összes gyermek erőforrás-tárolót és gyermek erőforrást. Ha a gyermek erőforrás-tároló vagy a gyermek erőforrás nem rendelkezik a definíció alkalmazásával, akkor a **notScopes**beállításával mindegyik kizárható a kiértékelésből. Ez a tulajdonság egy tömb, amely lehetővé teszi egy vagy több erőforrás-tároló vagy erőforrás kiszámítását a kiértékelésből. a **notScopes** a kezdeti hozzárendelés létrehozása után adhatók hozzá vagy frissíthetők.

## <a name="policy-definition-id"></a>Házirend-definíció azonosítója

Ebben a mezőben egy házirend-definíció vagy egy kezdeményezési definíció teljes elérési útjának kell szerepelnie.
`policyDefinitionId`karakterlánc, nem tömb. Javasoljuk, hogy ha több házirendet gyakran társítanak egymáshoz, inkább egy [kezdeményezést](./initiative-definition-structure.md) használjon.

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

Ebben a példában a házirend-definícióban korábban definiált paraméterek a `prefix` és a `suffix` . Ez az adott szabályzat `prefix` -hozzárendelés a **depta** és `suffix` a **-LC**értékre van szabva. Ugyanez a házirend-definíció a különböző részlegekhez tartozó paraméterek eltérő készletével használható, így csökkentve a házirend-definíciók ismétlődését és összetettségét, miközben rugalmasságot biztosít.

## <a name="next-steps"></a>További lépések

- A szabályzat- [definíciós struktúra](./definition-structure.md)megismerése.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).