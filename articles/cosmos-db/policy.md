---
title: A Azure Policy használata Azure Cosmos DB erőforrások irányításának és szabályozásának megvalósításához
description: Megtudhatja, hogyan használhatja a Azure Policyt Azure Cosmos DB erőforrások irányításának és szabályozásának megvalósítására.
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 2249dbdebecc52a8f5d6decccb83d3b1fc0777f7
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747382"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>A Azure Policy használata Azure Cosmos DB erőforrások irányításának és szabályozásának megvalósításához

[Azure Policy](../governance/policy/overview.md) segítséget nyújt a szervezeti irányítási szabványok betartatásához, az erőforrás-megfelelőség értékeléséhez és az automatikus szervizelés megvalósításához. Gyakori használati esetek közé tartozik a biztonság, a Cost-kezelés és a konfiguráció konzisztenciája.

A Azure Policy beépített szabályzat-definíciókat biztosít. Létrehozhat egyéni szabályzat-definíciókat olyan forgatókönyvekhez, amelyeket a beépített szabályzat-definíciók nem céloznak meg. További részletekért tekintse meg a [Azure Policy dokumentációját](../governance/policy/overview.md) .

## <a name="assign-a-built-in-policy-definition"></a>Beépített szabályzat-definíció kiosztása

A szabályzat-definíciók írják le az erőforrás-megfelelőségi feltételeket, valamint azt, hogy egy adott feltétel teljesül-e. A szabályzat- _hozzárendelések_ a szabályzat- _definíciók_alapján jönnek létre. A Azure Cosmos DB erőforrásaihoz beépített vagy egyéni szabályzat-definíciókat használhat. A szabályzat-hozzárendelések egy Azure felügyeleti csoportra, egy Azure-előfizetésre vagy egy erőforráscsoport hatókörére vonatkoznak, és a rendszer a kijelölt hatókörben lévő erőforrásokra alkalmazza őket. Igény szerint kizárhat bizonyos erőforrásokat a hatókörből.

Házirend-hozzárendeléseket a [Azure Portal](../governance/policy/assign-policy-portal.md), az [Azure PowerShell](../governance/policy/assign-policy-powershell.md), az [Azure CLI](../governance/policy/assign-policy-azurecli.md)vagy az [ARM sablon](../governance/policy/assign-policy-template.md)segítségével hozhat létre.

Ha a házirend-hozzárendelést a Azure Cosmos DB beépített szabályzat-definíciójában szeretné létrehozni, kövesse a [szabályzat-hozzárendelés létrehozása a Azure Portal](../governance/policy/assign-policy-portal.md) cikkben leírt lépéseket.

A házirend-definíció kiválasztásához írja be a `Cosmos DB` kifejezést a Keresés mezőbe az elérhető beépített szabályzat-definíciók listájának szűréséhez. Válassza ki a rendelkezésre álló beépített szabályzat-definíciók egyikét, majd válassza a **kijelölés** lehetőséget a szabályzat-hozzárendelés létrehozásához.

> [!TIP]
> A házirend-hozzárendelések létrehozásához használhatja a **rendelkezésre álló definíciók** ablaktáblán látható beépített szabályzat-definíciók nevét is Azure PowerShell, Azure CLI vagy ARM-sablonokkal.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Azure Cosmos DB beépített szabályzat-definíciók keresése":::

## <a name="create-a-custom-policy-definition"></a>Egyéni szabályzatdefiníció létrehozása

A beépített szabályzatok által nem tárgyalt konkrét forgatókönyvek esetén létrehozhat [egy egyéni szabályzat-definíciót](../governance/policy/tutorials/create-custom-policy-definition.md)is. Később létrehoz egy szabályzat- _hozzárendelést_ az egyéni szabályzat- _definícióból_.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Tulajdonságok típusai és tulajdonság-aliasok a házirend-szabályokban

Az [egyéni házirend-definíció lépéseivel](../governance/policy/tutorials/create-custom-policy-definition.md) azonosíthatja az erőforrás-tulajdonságokat és a tulajdonság-aliasokat, amelyek szükségesek a házirend-szabályok létrehozásához.

Azure Cosmos DB adott tulajdonság-aliasok azonosításához használja a névteret az `Microsoft.DocumentDB` egyéni házirend-definíció lépései cikkben látható módszerek egyikével.

#### <a name="use-the-azure-cli"></a>Az Azure CLI használata:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Azure PowerShell használata:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Ezek a parancsok a Azure Cosmos DB tulajdonsághoz tartozó tulajdonságok aliasának listáját jelenítik meg. A következő egy részlet a kimenetből:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

Az [egyéni házirend-definíciós szabályokban](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule)bármelyik tulajdonság-alias nevet használhatja.

Az alábbi példa olyan házirend-definíciót mutat be, amely ellenőrzi, hogy egy Azure Cosmos DB SQL-adatbázis kiépített átviteli sebessége nagyobb-e, mint a maximálisan megengedett 400 RU/s. Az egyéni házirend-definíció két szabályt tartalmaz: az egyiket, hogy megkeresse az adott típusú tulajdonság-aliast, a második pedig a típus adott tulajdonságát. Mindkét szabály az alias nevét használja.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
      "field": "type",
      "equals": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings"
      },
      {
      "field": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/default.resource.throughput",
      "greater": 400
      }
    ]
  }
}
```

Az egyéni szabályzat-definíciók használatával házirend-hozzárendeléseket hozhat létre, ugyanúgy, mint a beépített szabályzat-definíciók.

## <a name="policy-compliance"></a>Szabályzatmegfelelőség

A szabályzat-hozzárendelések létrehozása után Azure Policy kiértékeli a hozzárendelés hatókörében lévő erőforrásokat. A rendszer minden erőforrás _megfelelőségét_ kivizsgálja a szabályzattal. A házirendben megadott _effektus_ a nem megfelelő erőforrásokra lesz alkalmazva.

A megfelelőségi eredményeket és a szervizelés részleteit a [Azure Portalban](../governance/policy/how-to/get-compliance-data.md#portal) , illetve az [Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) -n vagy a [Azure monitor-naplókon](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs)tekintheti át.

Az alábbi képernyőfelvételen két példa van a házirend-hozzárendelésekre. Az egyik hozzárendelés egy beépített szabályzat-definíción alapul, amely ellenőrzi, hogy a Azure Cosmos DB-erőforrások csak az engedélyezett Azure-régiókra vannak-e telepítve. A másik hozzárendelés egy egyéni szabályzat-definíción alapul. Ez a hozzárendelés ellenőrzi, hogy a kiépített átviteli sebesség Azure Cosmos DB erőforráson nem haladja-e meg a megadott maximális korlátot.

A szabályzat-hozzárendelések telepítése után a megfelelőségi irányítópulton megjelennek a kiértékelés eredményei. Vegye figyelembe, hogy ez a szabályzat-hozzárendelés telepítése után akár 30 percet is igénybe vehet.

A képernyőképen a következő megfelelőségi kiértékelési eredmények láthatók:

- A megadott hatókörben lévő Azure Cosmos DB fiókok közül nulla nem felel meg a szabályzat-hozzárendelésnek, hogy a rendszer az erőforrásokat az engedélyezett régiókra telepítse.
- A megadott hatókörben lévő két Azure Cosmos DB adatbázis vagy gyűjtemény erőforrásai megfelelnek a megadott maximális korlátot meghaladó kiépített átviteli sebesség ellenőrzéséhez szükséges szabályzat-hozzárendelésnek.

:::image type="content" source="./media/policy/compliance.png" alt-text="Azure Cosmos DB beépített szabályzat-definíciók keresése":::

A nem megfelelő erőforrások javításához tekintse meg a [szervizelt Azure Policytel](../governance/policy/how-to/remediate-resources.md) című cikket.

## <a name="next-steps"></a>Következő lépések

- [Tekintse át az egyéni házirend-definíciókat a Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)
- [Szabályzat-hozzárendelés létrehozása a Azure Portalban](../governance/policy/assign-policy-portal.md)
- [Tekintse át Azure Policy beépített szabályzat-definíciókat Azure Cosmos DB](./policy-samples.md)
