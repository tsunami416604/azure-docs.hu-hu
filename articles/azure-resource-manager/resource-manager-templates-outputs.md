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
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 92e5dd5190a76bd09e33ea4c40a5b5cc2d66bc7b
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301129"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonokban kimeneti szakasz

A kimeneti szakaszban adjon meg értékeket, amelyek a központi telepítés rendszer adja vissza. Visszaadhatja például az URI-t üzembe helyezett erőforrások eléréséhez. A választható `condition` tulajdonságot adja meg, hogy a kimeneti értéket adja vissza.

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

A következő példa bemutatja, hogyan feltételesen a nyilvános IP-cím erőforrás-azonosító alapján egy új egyik telepítve lett-e vissza:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Az üzembe helyezés után az érték-parancsfájllal kérheti le. PowerShell esetén használja az alábbi parancsot:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
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
        "condition": "<boolean-value-whether-to-output-value>",
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| outputName |Igen |A kimeneti érték neve. Érvényes JavaScript-azonosítónak kell lennie. |
| feltétel |Nem | Logikai érték, amely azt jelzi, hogy ez a kimeneti értéket adja vissza. Amikor `true`, az érték szerepel a kimenet a központi telepítéshez. Amikor `false`, a rendszer kihagyta a kimeneti értéket ehhez a központi telepítéshez. Ha nincs megadva, az alapértelmezett értéke `true`. |
| type |Igen |A kimeneti érték típusát. Sablon bemeneti paraméterként azonos kimeneti értékeket támogatásához. |
| érték |Igen |Sablonnyelv-kifejezés, amely értékeli ki és adja vissza a kimeneti értéket. |

Megjegyzések adásával kapcsolatos információkért lásd: [sablonok megjegyzéseket](resource-group-authoring-templates.md#comments).

## <a name="example-templates"></a>Példa sablonok

|Sablon  |Leírás  |
|---------|---------|
|[Másolja a változók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Komplex változók hoz létre, és kiírja ezeket az értékeket. Nem telepíti az erőforrásokat. |
|[Nyilvános IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Létrehoz egy nyilvános IP-címet, és kiírja az erőforrás-azonosítója. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Az előző sablon mutató hivatkozásokat tartalmaz. A terheléselosztó létrehozásakor használja a kimenetben az erőforrás-azonosítója. |


## <a name="next-steps"></a>További lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja függvényeivel kapcsolatos részletekért lásd: [Azure Resource Manager-Sablonfüggvények](resource-group-template-functions.md).
* Sablonok létrehozásával kapcsolatos ajánlások, lásd: [gyakorlati tanácsok az Azure Resource Manager-sablon](template-best-practices.md).
