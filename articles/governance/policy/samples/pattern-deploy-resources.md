---
title: 'Minta: Erőforrások üzembe helyezése házirend-definícióval'
description: Ez az Azure Policy minta egy példa arra, hogyan üzembe helyezheti az erőforrásokat egy szabályzatdefinícióval.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172673"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure-szabályzat minta: erőforrások üzembe helyezése

A [deployIfNotExists](../concepts/effects.md#deployifnotexists) hatás lehetővé teszi egy [Azure Resource Manager-sablon](../../../azure-resource-manager/templates/overview.md) üzembe helyezését egy nem megfelelő erőforrás létrehozásakor vagy frissítésekor. Ez a megközelítés előnyben részesíthető a [megtagadási](../concepts/effects.md#deny) hatás használatával, mivel lehetővé teszi az erőforrások létrehozását, de biztosítja a módosításokat, hogy azok megfelelőek legyenek.

## <a name="sample-policy-definition"></a>Mintaházirend-definíciója

Ez a házirend-definíció a `type` **mezőoperátort** használja a létrehozott vagy frissített erőforrás kiértékelésére. Ha az erőforrás _Microsoft.Network/virtualNetworks,_ a házirend az új vagy frissített erőforrás helyén keresi a hálózati figyelőt. Ha egy megfelelő hálózati figyelő nem található, az Erőforrás-kezelő sablon telepítve van a hiányzó erőforrás létrehozásához.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Magyarázat

#### <a name="existencecondition"></a>existenceCondition (létezésfeltétel)

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

A **properties.policyRule.then.details** blokk megmondja az Azure Policy-nek, hogy mit keressen a **properties.policyRule.if** blokkban létrehozott vagy frissített erőforráshoz kapcsolódóan. Ebben a példában a **NetworkerRG** erőforráscsoport egyik hálózati figyelőjének léteznie kell az új vagy frissített erőforrás helyével megegyező **mezővel.** `location` A `field()` függvény használatával lehetővé teszi, hogy a **existenceCondition** az `location` új vagy frissített erőforrás, különösen a tulajdonság tulajdonságait.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

A **properties.policyRule.then.details** blokk **roleDefinitionIds** _tömbtulajdonsága_ közli a házirend-definícióval, hogy a felügyelt identitásnak milyen jogokra van szüksége a mellékelt Erőforrás-kezelő sablon telepítéséhez. Ezt a tulajdonságot úgy kell beállítani, hogy tartalmazza a sablon központi telepítéséhez szükséges engedélyekkel rendelkező szerepköröket, de a "legkisebb jogosultság elve" fogalmát kell használnia, és csak a szükséges műveleteket kell tartalmaznia, semmi több.

#### <a name="deployment-template"></a>Üzembehelyezési sablon

A házirend-definíció **központi telepítési** része rendelkezik egy **tulajdonságblokkkal,** amely meghatározza a három alapvető összetevőt:

- **mode** - Ez a tulajdonság beállítja a sablon [telepítési módját.](../../../azure-resource-manager/templates/deployment-modes.md)

- **sablon** - Ez a tulajdonság magában a sablonban található. Ebben a példában a **helysablon** paraméter beállítja az új hálózati figyelő erőforrás helyét.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **paraméterek** - Ez a tulajdonság a **sablonhoz**megadott paramétereket határozza meg. A paraméterneveknek meg kell egyezniük a **sablonban megadottakkal.** Ebben a példában a paraméter neve sejesa meg a **megfelelő helyet.** A **hely** értéke `field()` ismét a függvényt használja a kiértékelt erőforrás értékének leértékeléséhez, amely a **policyRule.if** blokk virtuális hálózata.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>További lépések

- Tekintse át az egyéb [mintákat és a beépített definíciókat.](./index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).