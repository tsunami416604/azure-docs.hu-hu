---
title: A nemmegfelelőség okainak meghatározása
description: Amikor az erőforrás nem megfelelő, nincsenek számos oka lehet. Ismerje meg, mi okozta a meg nem felelés megtanulhatja, hogyan.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1121bdb379265ef592a26a8a31a90d402e529cc8
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802745"
---
# <a name="determine-causes-of-non-compliance"></a>A nemmegfelelőség okainak meghatározása

Ha egy Azure-erőforrás nem kompatibilis a szabály a házirend, hasznos lehet tudni, mely az erőforrás nem felelnek meg a szabály részét. Emellett hasznos megérteni, milyen módosítása módosítani egy korábban megfelelő erőforrással, hogy nem megfelelő. Ezen adatok két módja van:

> [!div class="checklist"]
> - [Megfelelőségi részletei](#compliance-details)
> - [Változások nyomon követése (előzetes verzió)](#change-history-preview)

## <a name="compliance-details"></a>Megfelelőségi részletek

Amikor az erőforrás nem megfelelő, az adott erőforráshoz a megfelelőségi információk érhetők el a **szabályzatoknak való megfelelés** lapot. A megfelelőségi részletek panelen a következő információkat tartalmazza:

- Erőforrás részleteit, például a név, típus, hely és erőforrás-azonosító
- Megfelelőségi állapotok és az utolsó értékelés a jelenlegi szabályzat-hozzárendeléshez tartozó időbélyeg
- Listáját _okokból_ az erőforrás meg nem felelés esetén

> [!IMPORTANT]
> A megfelelőségi adatai számára, egy _nem megfelelő_ erőforrás látható tulajdonságok aktuális értékét az erőforráson, a felhasználónak rendelkeznie kell **olvasási** művelet a **típus** , az erőforrás. Például ha a _nem megfelelő_ erőforrás **Microsoft.Compute/virtualMachines** akkor a felhasználónak rendelkeznie kell a **Microsoft.Compute/virtualMachines/read** a művelet. Ha a felhasználó nem rendelkezik a szükséges művelet, hozzáférési hiba jelenik meg.

A megfelelőségi részletek megjelenítéséhez kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

1. Az a **áttekintése** vagy **megfelelőségi** lapra, jelölje be a házirend egy **megfelelőségi állapot** , amely _nem megfelelő_.

1. Alatt a **erőforrás megfelelőségi** lapján a **szabályzatoknak való megfelelés** lapon kattintson a jobb gombbal, vagy kattintson a három pontra az erőforrások egy **megfelelőségi állapot** , amely  _Nem megfelelő_. Válassza ki **megfelelőségi részletek megtekintéséhez**.

   ![Nézet megfelelőségi részletek lehetőség](../media/determine-non-compliance/view-compliance-details.png)

1. A **megfelelőségi részletek** ablaktáblán jelennek meg a jelenlegi szabályzat-hozzárendelés a legújabb kiértékelés az erőforrás adatait. Ebben a példában a mező **Microsoft.Sql/servers/version** bizonyul _12.0-s_ során a várt szabályzatdefiníció _14.0_. Ha több okból az erőforrás nem megfelelő, mindegyik megjelenik ezen a panelen.

   ![Megfelelőségi részletek ablaktábla és a meg nem felelés oka](../media/determine-non-compliance/compliance-details-pane.png)

   Az egy **auditIfNotExists** vagy **deployIfNotExists** szabályzatdefiníciót, szerepel a **details.type** tulajdonság és a választható tulajdonságok. Egy listát lásd: [auditIfNotExists tulajdonságok](../concepts/effects.md#auditifnotexists-properties) és [deployIfNotExists tulajdonságok](../concepts/effects.md#deployifnotexists-properties). **Legutóbbi értékelés erőforrás** a kapcsolódó erőforrások a **részletek** a definíció szakasza.

   Példa részleges **deployIfNotExists** definíciója:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Megfelelőségi részletek ablaktábla - * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Az adatok védelme, amikor egy tulajdonság értéke egy _titkos_ a jelenlegi érték csillagokat.

Ezeket a részleteket ismertetik, hogy miért erőforrás jelenleg nem megfelelő, de ne jelenjen meg, amikor az erőforrás, amely miatt nem nem megfelelővé válik a módosítást hajtottak végre. Ez az információ, lásd: [módosítási előzmények (előzetes verzió)](#change-history-preview) alatt.

### <a name="compliance-reasons"></a>Megfelelőségi okokból

A következő mátrix térképek minden lehetséges _OK_ a a felelős [feltétel](../concepts/definition-structure.md#conditions) szabályzatdefinícióban:

|Ok | Állapot |
|-|-|
|Az aktuális értéknek tartalmaznia kell a célértéket kulcsként. |containsKey vagy **nem** notContainsKey |
|Az aktuális értéknek tartalmaznia kell a célértéket. |tartalmaz vagy **nem** notContains |
|Az aktuális értéknek egyenlőnek kell lennie a célértékkel. |egyenlő vagy **nem** notEquals |
|Az aktuális értéknek léteznie kell. |Létezik |
|Az aktuális értéknek a célértéken belülinek kell lennie. |a vagy **nem** notIn |
|Az aktuális értéknek a célértékhez hasonlónak kell lennie. |például vagy **nem** notLike |
|Az aktuális értéknek a ki- és nagybetűk közötti különbség figyelembevételével meg kell felelnie a célértéknek. |megfelelő vagy **nem** notMatch |
|Az aktuális értéknek a kis- és nagybetűk közötti különbség figyelmen kívül hagyásával meg kell felelnie a célértéknek. |matchInsensitively vagy **nem** notMatchInsensitively |
|Az aktuális értéknek nem tartalmazhatja a célértéket kulcsként. |notContainsKey vagy **nem** containsKey|
|Az aktuális értéknek nem tartalmazhatja a célértéket. |notContains vagy **nem** tartalmaz |
|Az aktuális érték nem lehet egyenlő a célértékkel. |notEquals vagy **nem** egyenlő |
|Az aktuális érték nem létezhet. |**nem** létezik  |
|Az aktuális érték nem eshet a célértékbe. |notIn vagy **nem** a |
|Az aktuális érték nem lehet a célértékhez hasonló. |notLike vagy **nem** például |
|Az aktuális érték a kis- és nagybetűk közötti különbség figyelembevételével nem felelhet meg a célértéknek. |notMatch vagy **nem** felel meg |
|Az aktuális érték a kis- és nagybetűk közötti különbség figyelmen kívül hagyásával nem felelhet meg a célértéknek. |notMatchInsensitively vagy **nem** matchInsensitively |
|A szabályzat definíciójában nem felel meg kapcsolódó erőforrás a hatás részleteinek. |A megadott típusú erőforrás **then.details.type** és a meghatározott erőforráshoz kapcsolódó a **Ha** része a szabály nem létezik. |

## <a name="change-history-preview"></a>Változások nyomon követése (előzetes verzió)

Egy új részeként **nyilvános előzetes verzióban**, az elmúlt 14 napban változtatás előzmények érhető el az összes Azure-erőforrások, amelyek támogatják a [mód törlésének befejezéséhez](../../../azure-resource-manager/complete-mode-deletion.md). Változások nyomon követése kapcsolatos tudnivalókat tartalmaz, amikor a rendszer észlelte a változást, és a egy _diff vizuális_ minden módosításakor. Egy címváltozásának felderítését akkor aktiválódik, ha a Resource Manager-tulajdonságok hozzáadott, eltávolított vagy módosítani.

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

1. Az a **áttekintése** vagy **megfelelőségi** lapon, válassza ki a szabályzat minden **megfelelőségi állapot**.

1. Alatt a **erőforrás megfelelőségi** lapján a **szabályzatoknak való megfelelés** lap, válasszon ki egy erőforrást.

1. Válassza ki a **változások nyomon követése (előzetes verzió)** lapján a **erőforrás megfelelőségi** lapot. Listáját módosítások észlelése, ha a bármely exist jelennek meg.

   ![Erőforrás megfelelősége oldalon házirend-változások nyomon követése lap](../media/determine-non-compliance/change-history-tab.png)

1. Válassza ki az egyik észlelt változás. A _diff vizuális_ esetében az erőforrás egyike jelenik meg a **módosítási előzmények** lapot.

   ![Szabályzat módosítása előzmények vizuális Diff módosítási előzmények lapon](../media/determine-non-compliance/change-history-visual-diff.png)

A _diff vizuális_ segédeszközökkel erőforrásai azonosításában. A módosítások észlelhetők nem kapcsolódik az erőforrás aktuális megfelelőségi állapotát.

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy-minták](../samples/index.md)
- Tekintse át a [szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)
- Felülvizsgálat [házirend hatások ismertetése](../concepts/effects.md)
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](programmatically-create.md)
- Ismerje meg, hogyan [megfelelőségi adatok lekérése](getting-compliance-data.md)
- Ismerje meg, hogyan [javítani a nem megfelelő erőforrások](remediate-resources.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../../management-groups/overview.md) ismertető részt.