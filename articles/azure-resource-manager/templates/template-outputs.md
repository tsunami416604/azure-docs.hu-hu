---
title: Kimenetek a sablonokban
description: Ismerteti, hogyan lehet kimeneti értékeket definiálni egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: ec96b45cdc5ccf488d46c2d8da03caf16d002dfa
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622842"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Kimenetek Azure Resource Manager sablonban

Ez a cikk bemutatja, hogyan határozhatja meg a kimeneti értékeket a Azure Resource Manager-sablonban. A kimenetek akkor használhatók, ha értékeket kell visszaadnia az üzembe helyezett erőforrásokból.

## <a name="define-output-values"></a>Kimeneti értékek definiálása

Az alábbi példa bemutatja, hogyan állítható vissza a nyilvános IP-cím erőforrás-azonosító:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Feltételes kimenet

A kimenetek szakaszban feltételesen adhat vissza értéket. A kimenetekben jellemzően feltételt kell használni, amikor [feltételesen telepített](conditional-resource-deployment.md) egy erőforrást. Az alábbi példa azt mutatja be, hogyan lehet feltételesen visszaadni egy nyilvános IP-cím erőforrás-AZONOSÍTÓját attól függően, hogy egy újat telepített-e:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

A feltételes kimenet egyszerű példáját lásd: [feltételes kimeneti sablon](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Kimenetek dinamikus száma

Bizonyos helyzetekben nem tudja, hogy hány példányban kell visszaadni a sablon létrehozásakor. A **copy** elem használatával változó számú értéket adhat vissza.

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

További információ: [outputs iteráció Azure Resource Manager-sablonokban](copy-outputs.md).

## <a name="linked-templates"></a>Hivatkozott sablonok

Egy csatolt sablon kimeneti értékének lekéréséhez használja a fölérendelt sablon [hivatkozási](template-functions-resource.md#reference) függvényét. A fölérendelt sablon szintaxisa a következőket eredményezi:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Amikor egy kimeneti tulajdonság lekérése egy hivatkozott sablonnak, a tulajdonság neve nem tartalmazhatja az kötőjellel.

Az alábbi példa bemutatja, hogyan állíthatja be az IP-címet egy terheléselosztó számára egy érték egy csatolt sablonból való beolvasásával.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

[Beágyazott sablon](linked-templates.md#nested-template)kimenetek szakaszában nem használhatja a `reference` függvényt. Az értékeket egy üzembe helyezett erőforrás visszaadása egy beágyazott sablont, váltson egy hivatkozott sablonnak a beágyazott sablont.

## <a name="get-output-values"></a>Kimeneti értékek beolvasása

Az üzembe helyezés sikeressége után a rendszer automatikusan visszaadja a kimeneti értékeket a központi telepítés eredményeiben.

A telepítési előzményekből származó kimeneti értékek lekéréséhez használhatja a parancsfájlt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák a kimenetek használatának forgatókönyveit mutatják be.

|Sablon  |Leírás  |
|---------|---------|
|[Változók másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Komplex változók hoz létre, és kiírja ezeket az értékeket. Nem telepíti az erőforrásokat. |
|[Nyilvános IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Létrehoz egy nyilvános IP-címet, és kiírja az erőforrás-azonosítója. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Az előző sablon mutató hivatkozásokat tartalmaz. A terheléselosztó létrehozásakor használja a kimenetben az erőforrás-azonosítója. |

## <a name="next-steps"></a>Következő lépések

* További információ a kimenetek elérhető tulajdonságairól: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md).
