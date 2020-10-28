---
title: Hatókör a bővítmény erőforrástípusok esetében
description: Ismerteti, hogyan használható a hatókör tulajdonság a bővítmény típusú erőforrástípusok telepítésekor.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: bd468d31454c38bd314269243702d7df4f279a5e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681581"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Hatókör beállítása a bővítmény erőforrásaihoz az ARM-sablonokban

A kiterjesztési erőforrások olyan erőforrások, amelyek egy másik erőforrást módosítanak. Például hozzárendelhet egy szerepkört egy erőforráshoz a hozzáférés korlátozásához. A szerepkör-hozzárendelés egy bővítmény típusú erőforrástípus.

A bővítmények erőforrásainak teljes listáját lásd: [más erőforrások képességeit kiterjesztő erőforrástípusok](../management/extension-resource-types.md).

Ez a cikk bemutatja, hogyan állíthatja be egy bővítmény erőforrástípus hatókörét Azure Resource Manager sablon (ARM-sablon) telepítésekor. Ismerteti a hatókör tulajdonságot, amely a bővítmény erőforrásaihoz érhető el az erőforrásokra való alkalmazáskor.

## <a name="apply-at-deployment-scope"></a>Alkalmazás a központi telepítés hatókörében

A bővítmény erőforrástípus a cél központi telepítési hatókörön való alkalmazásához adja hozzá az erőforrást a sablonhoz, ahogy az erőforrástípus lenne. Az elérhető hatókörök az [erőforráscsoport](deploy-to-resource-group.md), az [előfizetés](deploy-to-subscription.md), a [felügyeleti csoport](deploy-to-management-group.md)és a [bérlő](deploy-to-tenant.md). A központi telepítési hatókörnek támogatnia kell az erőforrás típusát.

A következő sablon egy zárolást helyez üzembe.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Egy erőforráscsoport telepítésekor a rendszer zárolja az erőforráscsoportot.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

A következő példa egy szerepkört rendel hozzá.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Egy előfizetéshez való üzembe helyezéskor a szerepkört hozzárendeli az előfizetéshez.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Alkalmazás erőforrásra

Ha erőforrásra szeretné alkalmazni a bővítmény erőforrását, használja a (z `scope` ) tulajdonságot. Állítsa a hatókör tulajdonságot annak az erőforrásnak a nevére, amelyhez a bővítményt hozzáadja. A hatókör tulajdonság a bővítmény erőforrástípus legfelső szintű tulajdonsága.

A következő példa létrehoz egy Storage-fiókot, és egy szerepkört alkalmaz rá.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).
* A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).
