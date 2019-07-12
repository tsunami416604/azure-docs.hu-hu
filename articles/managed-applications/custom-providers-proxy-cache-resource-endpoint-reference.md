---
title: Egyéni erőforrás-gyorsítótár – referencia
description: Egyéni erőforrás cache-referencia az Azure egyéni erőforrás-szolgáltatók. Ez a cikk a végpontok végrehajtási gyorsítótár egyéni erőforrások követelményei fog áthaladni.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 2970e69e825dacb6f548b3e66a830f221ece0b1c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795229"
---
# <a name="custom-resource-cache-reference"></a>Egyéni erőforrás-gyorsítótár – referencia

Ez a cikk a végpontok végrehajtási gyorsítótár egyéni erőforrások követelményei fog áthaladni. Ha ismeri az Azure egyéni erőforrás-szolgáltatók, [az Áttekintés az egyéni erőforrás-szolgáltatók](./custom-providers-overview.md).

## <a name="how-to-define-a-cache-resource-endpoint"></a>A gyorsítótár resource végpont definiálása

Megadásával egy proxykiszolgáló-erőforrás hozható létre a **routingType** "Proxynak, gyorsítótár".

Mintául szolgáló egyéni erőforrás-szolgáltató:

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

## <a name="building-proxy-resource-endpoint"></a>Épület proxy erőforrás végpontjának

Egy **végpont** , amely megvalósítja az "A Proxy, a gyorsítótár" erőforrás **végpont** kell kezelnie a kérés- és az új API-hoz az Azure-ban. Ebben az esetben a **resourceType** hoz létre egy új Azure-erőforrás API `PUT`, `GET`, és `DELETE` CRUD végrehajtásához egy erőforráson, valamint `GET` összes meglévő erőforrást beolvasni:

> [!NOTE]
> Az Azure API-t hoz létre a kérési metódusainak `PUT`, `GET`, és `DELETE`, de a gyorsítótár **végpont** csak kezelnie kell `PUT` és `DELETE`.
> Azt javasoljuk, hogy a **végpont** is megvalósul `GET`.

### <a name="create-a-custom-resource"></a>Hozzon létre egy egyéni erőforrás

Az Azure API bejövő kérelem:

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

Ezt a kérelmet a rendszer ezután továbbítja a **végpont** formájában:

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

Hasonlóképpen, a válasz a **végpont** ezután kerül vissza az ügyfélnek. A végpont kapott választ kell visszaadnia:

- Egy érvényes JSON-objektum dokumentumot. Tömbök és a karakterláncok egy felső objektum alatt ágyazhatók egymásba.
- A `Content-Type` fejlécében kell megadni az "application/json; charset = utf-8 ".
- Az egyéni erőforrás-szolgáltató, azzal felülírja a `name`, `type`, és `id` mezők a kérelem.
- Az egyéni erőforrás-szolgáltató csak alatti mezők adja vissza a `properties` objektum-gyorsítótár végpontjához.

**Végpont** választ:

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

A `name`, `id`, és `type` mezők automatikusan létrehozza az egyéni erőforrás az egyéni erőforrás-szolgáltató.

Az Azure egyéni erőforrás-szolgáltató válasza:

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

Az Azure API bejövő kérelem:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Ezt a kérelmet a rendszer ezután továbbítja a **végpont** formájában:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Hasonlóképpen, a válasz a **végpont** ezután kerül vissza az ügyfélnek. A végpont kapott választ kell visszaadnia:

- Egy érvényes JSON-objektum dokumentumot. Tömbök és a karakterláncok egy felső objektum alatt ágyazhatók egymásba.
- A `Content-Type` fejlécében kell megadni az "application/json; charset = utf-8 ".
- Az Azure egyéni erőforrás-szolgáltató csak eltávolítja az elemet a gyorsítótárból, ha a 200-as szintű válasz. Akkor is, ha az erőforrás nem létezik, a **végpont** 204 adja vissza.

**Végpont** választ:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Az Azure egyéni erőforrás-szolgáltató válasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Egy egyéni erőforrás lekérése

Az Azure API bejövő kérelem:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

A kérelem fogja **nem** továbbítja a **végpont**.

Az Azure egyéni erőforrás-szolgáltató válasza:

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

### <a name="enumerate-all-custom-resources"></a>Minden egyéni erőforrás számbavétele

Az Azure API bejövő kérelem:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Erre a kérelemre **nem** továbbítja a **végpont**.

Az Azure egyéni erőforrás-szolgáltató válasza:

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

- [Az Azure egyéni erőforrás-szolgáltatók áttekintése](./custom-providers-overview.md)
- [Rövid útmutató: Az Azure egyéni erőforrás-szolgáltató létrehozása és telepítése az egyéni erőforrások](./create-custom-provider.md)
- [Oktatóanyag: Egyéni műveletek és az erőforrások létrehozása az Azure-ban](./tutorial-custom-providers-101.md)
- [Útmutató: Az egyéni műveletek hozzáadása az Azure REST API-val](./custom-providers-action-endpoint-how-to.md)
- [Hivatkozás: Egyéni erőforrás-Proxy – referencia](./custom-providers-proxy-resource-endpoint-reference.md)
