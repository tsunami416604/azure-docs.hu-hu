---
title: Egyéni erőforrásproxy – referencia
description: Egyéni erőforrás-proxy hivatkozás az Azure egyéni erőforrás-szolgáltatók. Ez a cikk a proxy egyéni erőforrásokat megvalósító végpontok követelményeit.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650460"
---
# <a name="custom-resource-proxy-reference"></a>Egyéni erőforrásproxy–hivatkozás

Ez a cikk a proxy egyéni erőforrásokat megvalósító végpontok követelményeit. Ha nem ismeri az Azure egyéni erőforrás-szolgáltatókat, olvassa [el az egyéni erőforrás-szolgáltatók áttekintését.](overview.md)

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Proxyerőforrás-végpont definiálása

Proxyerőforrás a **routingType** (Proxy) értéket a "Proxy" mezőre való megadásával hozható létre.

Példa egyéni erőforrás-szolgáltató:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>Épületproxy-erőforrás végpontja

Egy "Proxy" **erőforrás-végpontot** megvalósító **végpontnak** kezelnie kell az azure-beli új API-ra vonatkozó kérést és választ. Ebben az esetben a **resourceType** létrehoz egy `PUT`új `GET`Azure-erőforrás API-t, és `DELETE` egyetlen `GET` erőforráson hajtsa végre a CRUD-t, valamint az összes meglévő erőforrás lekéréséhez.

> [!NOTE]
> A `id` `name`, `type` és a mezők nem szükségesek, de szükségesek az egyéni erőforrás meglévő Azure-ökoszisztémával való integrálásához.

Minta erőforrás:

``` JSON
{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Paraméter hivatkozása:

Tulajdonság | Sample | Leírás
---|---|---
név | '{myCustomResourceName}' | Az egyéni erőforrás neve.
type | "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}" | Az erőforrástípus névtere.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}" | Az erőforrás-azonosító.

### <a name="create-a-custom-resource"></a>Egyéni erőforrás létrehozása

Azure API bejövő kérése:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Ezt a kérést a következő formában továbbítja a **végpontra:**

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Hasonlóképpen a **végpontválasz** ezután visszakerül az ügyfélhez. A végpontválaszának a következőt kell visszaadnia:

- Érvényes JSON-objektumdokumentum. Minden tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet "application/json; charset=utf-8".

**Végpont** Válasz:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Az Azure egyéni erőforrás-szolgáltatóválasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Egyéni erőforrás eltávolítása

Azure API bejövő kérése:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Ezt a kérést a következő formában továbbítja a **végpontra:**

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Hasonlóképpen a **végpontválasz** a végpontról visszakerül az ügyfélhez. A végpontválaszának a következőt kell visszaadnia:

- Érvényes JSON-objektumdokumentum. Minden tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet "application/json; charset=utf-8".

**Végpont** Válasz:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Az Azure egyéni erőforrás-szolgáltatóválasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Egyéni erőforrás beolvasása

Azure API bejövő kérése:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Ezt a kérést a következő formában továbbítja a **végpontra:**

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Hasonlóképpen a **végpontválasz** ezután visszakerül az ügyfélhez. A végpontválaszának a következőt kell visszaadnia:

- Érvényes JSON-objektumdokumentum. Minden tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet "application/json; charset=utf-8".

**Végpont** Válasz:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Az Azure egyéni erőforrás-szolgáltatóválasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Az összes egyéni erőforrás felsorolása

Azure API bejövő kérése:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Ezt a kérést a következő formában továbbítja a **végpontra:**

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Hasonlóképpen a **végpontválasz** ezután visszakerül az ügyfélhez. A végpontválaszának a következőt kell visszaadnia:

- Érvényes JSON-objektumdokumentum. Minden tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet "application/json; charset=utf-8".
- Az erőforrások listáját a legfelső szintű `value` tulajdonság alá kell helyezni.

**Végpont** Válasz:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

Az Azure egyéni erőforrás-szolgáltatóválasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

- [Az Azure egyéni erőforrás-szolgáltatóinak áttekintése](overview.md)
- [Rövid útmutató: Hozzon létre egyéni erőforrás-szolgáltatót, és telepítsen egyéni erőforrásokat](./create-custom-provider.md)
- [Oktatóanyag: Egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Útmutató: Egyéni műveletek hozzáadása az Azure REST API-hoz](./custom-providers-action-endpoint-how-to.md)
- [Hivatkozás: Egyéni erőforrás-gyorsítótár hivatkozása](proxy-cache-resource-endpoint-reference.md)
