---
title: Az Azure Resource Manager-sablon kimenete |} Microsoft Docs
description: "Az Azure Resource Manager-sablonok deklaratív JSON-szintaxis használatával kimeneti definiálását mutatja."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: 64d7a0ea72b2f629160f31e4bc1fb4a90f10653d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Az Azure Resource Manager sablonokban kimenetek szakasz
A kimenetek szakaszban adja meg központi telepítéséből a visszaadott érték. Visszaadhatja például az URI telepített erőforrások eléréséhez.

## <a name="define-and-use-output-values"></a>Adja meg, és kimeneti értékek használata

A következő példa bemutatja, hogyan vissza egy nyilvános IP-cím erőforrás-azonosító:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

A telepítést követően az érték-parancsfájllal kérheti le. PowerShell esetén használja az alábbi parancsot:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Kérheti le a kimeneti értéket egy csatolt sablon használatával a [hivatkozás](resource-group-template-functions-resource.md#reference) függvény. Ahhoz, hogy egy kimeneti értéket egy csatolt sablonból, lekérdezni a tulajdonság szintaxissal: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Például beállíthatja az IP-címet a terheléselosztóhoz érték beolvasásával csatolt sablonból.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nem használhatja a `reference` kimenetek szakaszában függvény egy [beágyazott sablon](resource-group-linked-templates.md#link-or-nest-a-template). Egy beágyazott sablon üzembe helyezett erőforrás értékek visszaállításához a beágyazott sablon átalakítása csatolt sablont.

## <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

A következő példa egy kimeneti definíciója szerkezetét mutatja:

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
| outputName |Igen |A kimeneti érték nevét. Egy érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A kimeneti érték típusa. Kimeneti értékek támogatásához sablon bemeneti paraméterként. |
| érték |Igen |Amely értékeli ki, és kimeneti értéket adja vissza a sablonnyelvi kifejezés. |

## <a name="recommendations"></a>Javaslatok

Egy sablon használatával nyilvános IP-címek létrehozása, ha tartalmaznak egy kimenetek szakaszt, amely az IP-cím és a teljesen minősített tartománynevét (FQDN) adatait adja vissza. Egyszerűen beolvashatók a telepítést követően nyilvános IP-címek és teljes tartománynevek kimeneti értékeket is használhat.

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
|[Másolja a változók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Összetett változók hoz létre, és kiírja ezeket az értékeket. Nem kell minden olyan erőforrásnál telepítenie. |
|[Nyilvános IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Létrehoz egy nyilvános IP-címet, és kiírja az erőforrás-azonosító. |
|[Terheléselosztó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | A fenti sablon mutató hivatkozásokat. Használja a kimenet az erőforrás-azonosító, a terheléselosztó létrehozásakor. |


## <a name="next-steps"></a>További lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja a functions szolgáltatással kapcsolatos részletekért lásd: [Azure Resource Manager Sablonfüggvényei](resource-group-template-functions.md).
* Egyesítenie több sablon üzembe helyezése során, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Szükség lehet egy másik erőforráscsoportban található erőforrások használatával. Ez a forgatókönyv nem közös, ha a storage-fiókok vagy több erőforrás csoporttal megosztott virtuális hálózatok. További információkért lásd: a [resourceId függvény](resource-group-template-functions-resource.md#resourceid).