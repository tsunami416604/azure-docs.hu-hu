---
title: Egyéni erőforrás-gyorsítótár – referencia
description: Egyéni erőforrás-gyorsítótár hivatkozás az Azure egyéni erőforrás-szolgáltatók. Ez a cikk a gyorsítótár egyéni erőforrásait megvalósító végpontok követelményeit ismerteti.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650382"
---
# <a name="custom-resource-cache-reference"></a>Egyéni erőforrás-gyorsítótár – hivatkozás

Ez a cikk a gyorsítótár egyéni erőforrásait megvalósító végpontok követelményeit ismerteti. Ha nem ismeri az Azure egyéni erőforrás-szolgáltatókat, olvassa [el az egyéni erőforrás-szolgáltatók áttekintését.](overview.md)

## <a name="how-to-define-a-cache-resource-endpoint"></a>Gyorsítótár-erőforrás-végpont definiálása

Proxyerőforrás a **"Proxy,** Gyorsítótár" útválasztó típusának megadásával hozható létre.

Példa egyéni erőforrás-szolgáltató:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
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

Egy **végpont,** amely megvalósítja a "Proxy, gyorsítótár" **erőforrás-végpont** kell kezelnie a kérelmet és választ az új API-t az Azure-ban. Ebben az esetben a **resourceType** létrehoz egy `PUT`új `GET`Azure-erőforrás API-t a számára, `DELETE` `GET` és egyetlen erőforráson hajtja végre a CRUD-t, valamint az összes meglévő erőforrás t:

> [!NOTE]
> Az Azure API létrehozza `PUT`a `GET`kérési módszereket , `DELETE`és , `DELETE`de a gyorsítótár **végpontjának** csak kezelnie `PUT` kell a és a.
> Azt javasoljuk, **endpoint** hogy a `GET`végpont is megvalósítsa.

### <a name="create-a-custom-resource"></a>Egyéni erőforrás létrehozása

Azure API bejövő kérése:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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
- Az egyéni erőforrás-szolgáltató `name`felülírja a , `type`és `id` a kérés mezőit.
- Az egyéni erőforrás-szolgáltató csak `properties` a gyorsítótár végpontjának objektumalatti mezőket adja vissza.

**Végpont** Válasz:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

A `name` `id`, `type` és mezőket az egyéni erőforrás-szolgáltató automatikusan létrehozza az egyéni erőforráshoz.

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

Hasonlóképpen a **végpontválasz** ezután visszakerül az ügyfélhez. A végpontválaszának a következőt kell visszaadnia:

- Érvényes JSON-objektumdokumentum. Minden tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet "application/json; charset=utf-8".
- Az Azure egyéni erőforrás-szolgáltató csak akkor távolítja el az elemet a gyorsítótárból, ha egy 200-szintű választ ad vissza. Még ha az erőforrás nem is létezik, a **végpontnak** 204-et kell visszaadnia.

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

A kérés **nem** lesz továbbítva a **végpontra**.

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

Ez a kérés **nem** lesz továbbítva a **végpontra**.

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
- [Hivatkozás: Egyéni erőforrásproxy-hivatkozás](proxy-resource-endpoint-reference.md)
