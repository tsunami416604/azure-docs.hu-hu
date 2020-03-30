---
title: Egyéni műveletek hozzáadása az Azure REST API-hoz
description: Ismerje meg, hogyan adhat hozzá egyéni műveleteket az Azure REST API-hoz. Ez a cikk bemutatja az egyéni műveleteket végrehajtani kívánt végpontok követelményeit és ajánlott eljárásokat.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650395"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Egyéni műveletek hozzáadása az Azure REST API-hoz

Ez a cikk az egyéni műveleteket megvalósító Azure egyéni erőforrás-szolgáltatóvégpontok létrehozásához szükséges követelményeken és gyakorlati tanácsokon megy keresztül. Ha nem ismeri az Azure egyéni erőforrás-szolgáltatókat, olvassa [el az egyéni erőforrás-szolgáltatók áttekintését.](overview.md)

## <a name="how-to-define-an-action-endpoint"></a>Műveletvégpont definiálása

A **végpont** egy URL-címet, amely egy szolgáltatásra mutat, amely megvalósítja az alapul szolgáló szerződést közte és az Azure között. A végpont az egyéni erőforrás-szolgáltatóban van definiálva, és bármely nyilvánosan elérhető URL-cím lehet. Az alábbi minta `myCustomAction` a `endpointURL`által végrehajtott **műveletet** tartalmazza.

Minta **Erőforrás-szolgáltató**:

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

## <a name="building-an-action-endpoint"></a>Műveletvégpont létrehozása

Egy **műveletet** megvalósító **végpontnak** kezelnie kell az azure-beli új API-ra vonatkozó kérést és választ. Amikor egy egyéni erőforrás-szolgáltató egy **műveletet** hoz létre, akkor létrehoz egy új API-k az Azure-ban. Ebben az esetben a művelet egy új `POST` Azure-művelet API-t hoz létre a hívásokhoz:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API bejövő kérése:

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

Ezt a kérést a következő formában továbbítja a **végpontra:**

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

Hasonlóképpen a **végpontválasz** ezután visszakerül az ügyfélhez. A végpontválaszának a következőt kell visszaadnia:

- Érvényes JSON-objektumdokumentum. Minden tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet "application/json; charset=utf-8".

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

Az Azure egyéni erőforrás-szolgáltatóválasza:

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

## <a name="calling-a-custom-action"></a>Egyéni művelet hívása

Az egyéni műveletek egyéni erőforrás-szolgáltatótól való lehívásának két fő módja van:

- Azure CLI
- Azure Resource Manager-sablonok

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

Paraméter | Kötelező | Leírás
---|---|---
action | *igen* | A **ResourceProvider-ben**definiált művelet neve.
Azonosítók | *igen* | A **ResourceProvider erőforrásazonosítója**.
megkereső szerv | *nem* | A **végpontra**küldendő kérelemtörzs.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

> [!NOTE]
> A műveletek támogatása korlátozott az Azure Resource Manager-sablonokban. Ahhoz, hogy a műveletet sablonon belül [`list`](../templates/template-functions-resource.md#list) lehessen meghívni, az előtagot a nevében kell tartalmaznia.

Minta **erőforrás-szolgáltató** listaművelettel:

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

Minta Azure Resource Manager sablon:

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

Paraméter | Kötelező | Leírás
---|---|---
erőforrás-azonosító | *igen* | A **ResourceProvider erőforrásazonosítója**.
apiVersion | *igen* | Az erőforrás-futásidejű API-verziója. Ennek mindig "2018-09-01-preview" legyen.
függvényértékek | *nem* | A **végpontra**küldendő kérelemtörzs.

## <a name="next-steps"></a>További lépések

- [Az Azure egyéni erőforrás-szolgáltatóinak áttekintése](overview.md)
- [Rövid útmutató: Hozzon létre egyéni erőforrás-szolgáltatót, és telepítsen egyéni erőforrásokat](./create-custom-provider.md)
- [Oktatóanyag: Egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST API-hoz](./custom-providers-resources-endpoint-how-to.md)
