---
title: Kimenetek a sablonokban
description: Bemutatja, hogyan definiálható kimeneti értékek egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460024"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Kimenetek az Azure Resource Manager-sablonban

Ez a cikk ismerteti, hogyan definiálhatja a kimeneti értékeket az Azure Resource Manager-sablonban. A kimeneteket akkor használja, amikor értékeket kell visszaadnia az üzembe helyezett erőforrásokból.

## <a name="define-output-values"></a>Kimeneti értékek meghatározása

A következő példa bemutatja, hogyan adja vissza az erőforrás-azonosítót egy nyilvános IP-címhez:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Feltételes kimenet

A kimenetek szakaszban feltételesen visszaadhat egy értéket. Általában feltételt használ a kimenetekben, ha feltételesen telepített egy [erőforrást.](conditional-resource-deployment.md) A következő példa bemutatja, hogyan lehet feltételesen visszaadni egy nyilvános IP-cím erőforrás-azonosítóját annak alapján, hogy újat telepítettek-e:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

A feltételes kimenet egyszerű példáját a [feltételes kimeneti sablon című témakörben olvashatja.](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)

## <a name="dynamic-number-of-outputs"></a>A kimenetek dinamikus száma

Bizonyos esetekben nem tudja, hogy a sablon létrehozásakor hány értéket kell visszaadnia. A **másolási** elem segítségével változó számú értéket adhat vissza.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

További információ: [Outputs iteráció az Azure Resource Manager-sablonokban.](copy-outputs.md)

## <a name="linked-templates"></a>Hivatkozott sablonok

A kimeneti érték csatolt sablonból történő beolvasásához használja a [szülősablon hivatkozási](template-functions-resource.md#reference) függvényét. A szülősablon szintaxisa a következő:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Amikor egy kimeneti tulajdonságot egy csatolt sablonból kap, a tulajdonság neve nem tartalmazhat kötőjelet.

A következő példa bemutatja, hogyan állíthatja be az IP-címet egy terheléselosztón egy csatolt sablonból származó érték beolvasásával.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

A függvény nem `reference` használható a beágyazott sablon kimenetek [szakaszában.](linked-templates.md#nested-template) Ha egy beágyazott sablonban lévő üzembe helyezett erőforrás értékeit szeretné visszaadni, konvertálja a beágyazott sablont csatolt sablonná.

## <a name="get-output-values"></a>Kimeneti értékek bekése

Ha a központi telepítés sikeres, a kimeneti értékek automatikusan visszaadják a központi telepítés eredményeként.

A központi telepítési előzmények kimeneti értékek beírásához parancsfájlt használhat.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák bemutatják a kimenetek használatának forgatókönyveit.

|Sablon  |Leírás  |
|---------|---------|
|[Változók másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Összetett változókat hoz létre, és ezeket az értékeket adja ki. Nem telepít erőforrásokat. |
|[Nyilvános IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Nyilvános IP-címet hoz létre, és kiadja az erőforrás-azonosítót. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Az előző sablonra mutató hivatkozások. A terheléselosztó létrehozásakor a kimenetben lévő erőforrás-azonosítót használja. |

## <a name="next-steps"></a>További lépések

* A kimenetek elérhető tulajdonságairól az [Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](template-syntax.md)
