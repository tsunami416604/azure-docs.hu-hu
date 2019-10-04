---
title: Az egyéni műveletek hozzáadása az Azure REST API
description: Ismerje meg az egyéni műveletek hozzáadása az Azure REST API-hoz. Ez a cikk részletesen ismerteti a követelmények és ajánlott eljárások a végpontok, amelyek az egyéni műveletek végrehajtásához.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795301"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Az egyéni műveletek hozzáadása az Azure REST API-val

Ez a cikk a követelmények és az egyéni műveletek megvalósító egyéni erőforrás-szolgáltató Azure-végpontok létrehozásának ajánlott eljárásai fog áthaladni. Ha ismeri az Azure egyéni erőforrás-szolgáltatók, [az Áttekintés az egyéni erőforrás-szolgáltatók](./custom-providers-overview.md).

## <a name="how-to-define-an-action-endpoint"></a>A művelet a végpont definiálása

Egy **végpont** URL-cím, amely egy szolgáltatás, amely megvalósítja az alapul szolgáló szerződés és az Azure között. A végpont az egyéni erőforrás-szolgáltató van definiálva, és bármilyen nyilvánosan elérhető URL-CÍMÉT. Az alábbi minta rendelkezik egy **művelet** nevű `myCustomAction` által megvalósított `endpointURL`.

Minta **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>A művelet a végpont létrehozása

Egy **végpont** , amely megvalósítja az **művelet** kell kezelnie a kérés- és az új API-hoz az Azure-ban. Ha egy egyéni erőforrás-szolgáltató egy **művelet** van hozta létre, létrehoz egy új API-készletet az Azure-ban. Ebben az esetben a művelet hoz létre egy új Azure műveletet API `POST` hívások:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Az Azure API bejövő kérelem:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Ezt a kérelmet a rendszer ezután továbbítja a **végpont** formájában:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Hasonlóképpen, a válasz a **végpont** ezután kerül vissza az ügyfélnek. A végpont kapott választ kell visszaadnia:

- Egy érvényes JSON-objektum dokumentumot. Tömbök és a karakterláncok egy felső objektum alatt ágyazhatók egymásba.
- A `Content-Type` fejlécében kell megadni az "application/json; charset = utf-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Az Azure egyéni erőforrás-szolgáltató válasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Egyéni művelet hívásakor.

Az egyéni erőforrás-szolgáltató minden egyéni művelet hívásakor két fő módja van:

- Azure CLI
- Az Azure Resource Manager-sablonok

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Paraméter | Szükséges | Leírás
---|---|---
a művelet | *igen* | A művelet a megadott nevét, a **ResourceProvider**.
azonosítók | *igen* | Az erőforrás-Azonosítóját a **ResourceProvider**.
kérelem – törzs | *no* | A kérés törzse, amely küld a **végpont**.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

> [!NOTE]
> Műveletek csak korlátozott támogatást az Azure Resource Manager-sablonok. Ahhoz, hogy a műveletet úgy, hogy legyen meghívva, egy sablon, akkor tartalmaznia kell a [ `list` ](../azure-resource-manager/resource-group-template-functions-resource.md#list) előtag, amelynek neve.

Minta **ResourceProvider** lista művelettel:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Példa az Azure Resource Manager-sablon:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Paraméter | Szükséges | Leírás
---|---|---
resourceIdentifier | *igen* | Az erőforrás-Azonosítóját a **ResourceProvider**.
apiVersion | *igen* | Az erőforrás-futtatókörnyezet API verziója. Ez mindig legyen "2018-09-01-preview".
functionValues | *no* | A kérés törzse, amely küld a **végpont**.

## <a name="next-steps"></a>További lépések

- [Az Azure egyéni erőforrás-szolgáltatók áttekintése](./custom-providers-overview.md)
- [Rövid útmutató: Az Azure egyéni erőforrás-szolgáltató létrehozása és telepítése az egyéni erőforrások](./create-custom-provider.md)
- [Oktatóanyag: Egyéni műveletek és az erőforrások létrehozása az Azure-ban](./tutorial-custom-providers-101.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST API-val](./custom-providers-resources-endpoint-how-to.md)
