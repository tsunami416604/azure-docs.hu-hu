---
title: 'Minta: erőforrások üzembe helyezése házirend-definícióval'
description: Ez a Azure Policy minta azt szemlélteti, hogyan helyezhet üzembe erőforrásokat házirend-definícióval.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172673"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure Policy minta: erőforrások üzembe helyezése

A [deployIfNotExists](../concepts/effects.md#deployifnotexists) hatására a nem megfelelő erőforrások létrehozásakor vagy frissítésekor lehetővé válik [Azure Resource Manager-sablon](../../../azure-resource-manager/templates/overview.md) üzembe helyezése. Ezt a megközelítést érdemes használni a [Megtagadás](../concepts/effects.md#deny) hatásaként, mivel az erőforrások továbbra is létrejönnek, de gondoskodik arról, hogy a módosítások megfelelőek legyenek.

## <a name="sample-policy-definition"></a>Példa a házirend-definícióra

Ez a házirend-definíció a **mező** operátort használja `type` a létrehozott vagy frissített erőforrás kiértékeléséhez. Ha ez az erőforrás _Microsoft. Network/virtualNetworks_, a házirend az új vagy frissített erőforrás helyén keresi a Network watchert. Ha nem található egyező hálózati figyelő, a rendszer telepíti a Resource Manager-sablont a hiányzó erőforrás létrehozásához.

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>Magyarázat

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

A **Properties. ' policyrule osztály. retails** blokk közli Azure Policy, hogy mit kell keresni a létrehozott vagy frissített erőforráshoz a **Properties. ' policyrule osztály. if** blokkban. Ebben a példában az erőforráscsoport **networkWatcherRG** lévő hálózati figyelőnek léteznie kell az új vagy frissített erőforrás helyével megegyező **mezővel** `location` . A `field()` függvény használata lehetővé teszi, hogy a **existenceCondition** az új vagy frissített erőforrás tulajdonságait, pontosabban `location` a tulajdonságot hozzáférjenek.

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

Az **roleDefinitionIds** _Array_ tulajdonság a **Properties. ' policyrule osztály. retails** blokkban megadja azt a házirend-definíciót, amely szerint a felügyelt identitásnak telepítenie kell a tartalmazott Resource Manager-sablont. Ezt a tulajdonságot úgy kell beállítani, hogy olyan szerepköröket tartalmazzon, amelyek rendelkeznek a sablon üzembe helyezéséhez szükséges engedélyekkel, de a "legalacsonyabb jogosultság elve" fogalmát kell használniuk, és csak a szükséges műveletek és semmi más.

#### <a name="deployment-template"></a>Üzembehelyezési sablon

A házirend-definíció **központi telepítési** része egy olyan **tulajdonság** -blokkot tartalmaz, amely a három alapvető összetevőt definiálja:

- **Mode (mód** ) – Ez a tulajdonság a sablon [központi telepítési módját](../../../azure-resource-manager/templates/deployment-modes.md) állítja be.

- **sablon** – ez a tulajdonság magába foglalja a sablont. Ebben a példában a **Location** template paraméter beállítja az új Network Watcher-erőforrás helyét.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **Paraméterek** – ez a tulajdonság határozza meg a **sablonhoz**megadott paramétereket. A paraméterek nevének egyezniük kell a **sablonban**definiált értékekkel. Ebben a példában a paraméter neve **hely** az egyeztetéshez. A **hely** értéke a `field()` függvény ismételt használatával beolvassa a kiértékelt erőforrás értékét, amely a ' policyrule osztály virtuális hálózata **. Ha** a blokk meg van határozva.

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>További lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).