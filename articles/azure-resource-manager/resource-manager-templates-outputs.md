---
title: Az Azure Resource Manager-sablon kimenete |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy Azure Resource Manager-sablonok deklaratív JSON-szintaxist használva kimeneteit meghatározásához.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: tomfitz
ms.openlocfilehash: 85aab429fd59afd36cd026e6d8aef2b7e6f6e122
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140455"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonokban kimeneti szakasz
A kimeneti szakaszban adjon meg értékeket, amelyek a központi telepítés rendszer adja vissza. Visszaadhatja például az URI-t üzembe helyezett erőforrások eléréséhez.

## <a name="define-and-use-output-values"></a>Definiálja és kimeneti értékeket

Az alábbi példa bemutatja, hogyan állítható vissza a nyilvános IP-cím erőforrás-azonosító:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Az üzembe helyezés után az érték-parancsfájllal kérheti le. PowerShell esetén használja az alábbi parancsot:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Kérheti le a kimeneti értéket egy hivatkozott sablonnak a használatával a [referencia](resource-group-template-functions-resource.md#reference) függvény. Egy kimeneti értéket egy hivatkozott sablonnak a lekéréséhez a szintaxissal tulajdonság értékét a vizualizációhoz: `"[reference('deploymentName').outputs.propertyName.value]"`.

Amikor egy kimeneti tulajdonság lekérése egy hivatkozott sablonnak, a tulajdonság neve nem tartalmazhatja az kötőjellel.

Például beállíthatja az IP-cím az egy terheléselosztóhoz társított sablonból értéket lekérésével.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nem használhatja a `reference` függvény kimenetek szakaszában egy [beágyazott sablont](resource-group-linked-templates.md#link-or-nest-a-template). Az értékeket egy üzembe helyezett erőforrás visszaadása egy beágyazott sablont, váltson egy hivatkozott sablonnak a beágyazott sablont.

## <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

A következő példa egy kimeneti definíciót szerkezetét mutatja:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| outputName |Igen |A kimeneti érték neve. Érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A kimeneti érték típusát. Sablon bemeneti paraméterként azonos kimeneti értékeket támogatásához. |
| érték |Igen |Sablonnyelv-kifejezés, amely értékeli ki és adja vissza a kimeneti értéket. |

## <a name="recommendations"></a>Javaslatok

Ha egy sablon használatával hozzon létre nyilvános IP-címek, közé tartozik egy kimeneti szakasz, amely az IP-cím és a teljesen minősített tartománynevét (FQDN) adja vissza. Nyilvános IP-címek és teljes tartománynevek könnyen hozzáférhet az üzembe helyezést követően a kimeneti értékeket is használhat.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Példa sablonok


|Sablon  |Leírás  |
|---------|---------|
|[Másolja a változók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Komplex változók hoz létre, és kiírja ezeket az értékeket. Nem telepíti az erőforrásokat. |
|[Nyilvános IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Létrehoz egy nyilvános IP-címet, és kiírja az erőforrás-azonosítója. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Az előző sablon mutató hivatkozásokat tartalmaz. A terheléselosztó létrehozásakor használja a kimenetben az erőforrás-azonosítója. |


## <a name="next-steps"></a>További lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja függvényeivel kapcsolatos részletekért lásd: [Azure Resource Manager-Sablonfüggvények](resource-group-template-functions.md).
* Úgy, hogy több sablon üzembe helyezése során, tekintse meg a [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Szükség lehet a belül egy másik erőforráscsoportban található erőforrások. Ebben a forgatókönyvben nem gyakori, ha a storage-fiókok vagy a virtuális hálózatokat, amelyek több erőforráscsoporthoz vannak megosztva. További információkért lásd: a [resourceId függvény](resource-group-template-functions-resource.md#resourceid).