---
title: A házirend-hozzárendelési struktúra részletei
description: Ismerteti az Azure Policy által használt szabályzat-hozzárendelés-definíciót, amely a szabályzatdefiníciókat és -paramétereket kiértékelésre szolgáló erőforrásokkal kapcsolja össze.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265297"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy-hozzárendelés struktúrája

A szabályzat-hozzárendeléseket az Azure Policy használja annak meghatározására, hogy mely erőforrások mely szabályzatokat vagy kezdeményezéseket rendelik hozzá. A házirend-hozzárendelés meghatározhatja az adott erőforráscsoport paramétereinek értékeit a hozzárendelés időpontjában, lehetővé téve a házirend-definíciók újbóli felhasználását, amelyek ugyanazokat az erőforrástulajdonságokat elégítik ki, és különböző megfelelőségi igényeket.

A JSON használatával házirend-hozzárendelést hozhat létre. A házirend-hozzárendelés a következő elemeket tartalmazza:

- megjelenítendő név
- leírás
- metaadatok
- kényszerítési mód
- házirend-definíció
- paraméterek

A következő JSON például egy házirend-hozzárendelést jelenít meg _DoNotEnforce_ módban dinamikus paraméterekkel:

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

Az Azure Policy-minták az [Azure Policy-mintákban](../samples/index.md)találhatók.

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **displayName** és a **description** segítségével azonosíthatja a házirend-hozzárendelést, és kontextust biztosíthatja annak adott erőforráskészlettel való használatához. **A displayName** maximális hossza _128_ karakter, **a leírása** pedig _legfeljebb 512_ karakter.

## <a name="enforcement-mode"></a>Kényszerítési mód

A **enforcementMode** tulajdonság lehetővé teszi az ügyfelek számára, hogy teszteljék a szabályzat eredményét a meglévő erőforrásokon anélkül, hogy kezdeményeznék a szabályzateffektust, vagy bejegyzéseket kezdeményezne az [Azure-tevékenységnaplóban.](../../../azure-monitor/platform/platform-logs-overview.md) Ezt a forgatókönyvet általában "Mi lenne, ha" néven nevezik, és igazodik a biztonságos telepítési eljárásokhoz. **a enforcementMode** különbözik a [Letiltott](./effects.md#disabled) hatástól, mivel ez a hatás megakadályozza az erőforrások kiértékelését.

Ez a tulajdonság a következő értékeket tartalmaz:

|Mód |JSON-érték |Típus |Kézi javítás |Tevékenységnapló-bejegyzés |Leírás |
|-|-|-|-|-|-|
|Engedélyezve |Alapértelmezett |sztring |Igen |Igen |A házirend-hatás az erőforrás létrehozása vagy frissítése során lép érvénybe. |
|Letiltva |DoNotEnforce |sztring |Igen |Nem | A házirend-hatás nem kényszeríti ki az erőforrás létrehozása vagy frissítése során. |

Ha **a rakateltmód** nincs megadva egy házirend- vagy kezdeményezésdefinícióban, a rendszer az _Alapértelmezett_ értéket használja. [A szervizelési feladatok](../how-to/remediate-resources.md) elindíthatók [az IfNotExists](./effects.md#deployifnotexists) házirendek esetében, még akkor is, ha a **enforcementMode** beállítás _DoNotEnforce_.

## <a name="policy-definition-id"></a>Házirend-definíció azonosítója

Ennek a mezőnek a házirend-definíció vagy a kezdeményezés definíciójának teljes elérési útjának kell lennie.
`policyDefinitionId`karakterlánc, nem tömb. Javasoljuk, hogy ha több szabályzat gyakran vannak összerendelve, használjon [helyette egy kezdeményezést.](./definition-structure.md#initiatives)

## <a name="parameters"></a>Paraméterek

A házirend-hozzárendelés ezen szegmense adja meg a [házirend-definícióban vagy kezdeményezésdefinícióban](./definition-structure.md#parameters)meghatározott paraméterek értékeit .
Ez a kialakítás lehetővé teszi a különböző erőforrásokkal rendelkező házirend- vagy kezdeményezésdefiníciók újbóli felhasználását, de a különböző üzleti értékek vagy eredmények ellenőrzését.

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

Ebben a példában a házirend-definícióban korábban meghatározott paraméterek a `prefix` és `suffix`a. Ez a konkrét `prefix` házirend-hozzárendelés `suffix` a **DeptA-ra** és a **-LC-re**van beállítva. Ugyanaz a házirend-definíció egy másik részleg paraméterkészletével újrafelhasználható, csökkentve a házirend-definíciók párhuzamosságát és összetettségét, miközben rugalmasságot biztosít.

## <a name="next-steps"></a>További lépések

- További információ a [házirend-definíciós struktúráról.](./definition-structure.md)
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](../how-to/programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](../how-to/get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](../how-to/remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)