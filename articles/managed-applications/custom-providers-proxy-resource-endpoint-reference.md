---
title: Egyéni erőforrásproxy – referencia
description: Egyéni erőforrás-proxy – referencia az Azure egyéni erőforrás-szolgáltatók. Ez a cikk az egyéni proxy erőforrásokat végrehajtási végpontok követelményei fog áthaladni.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: bbb907a7d73036352d4f5b8f36ccefacd89681ae
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478861"
---
# <a name="custom-resource-proxy-reference"></a>Egyéni erőforrás-Proxy – referencia

Ez a cikk az egyéni proxy erőforrásokat végrehajtási végpontok követelményei fog áthaladni. Ha ismeri az Azure egyéni erőforrás-szolgáltatók, [az Áttekintés az egyéni erőforrás-szolgáltatók](./custom-providers-overview.md).

## <a name="how-to-define-a-proxy-resource-endpoint"></a>A proxy erőforrás végpont definiálása

Megadásával egy proxykiszolgáló-erőforrás hozható létre a **routingType** "Proxyszolgáltató".

Mintául szolgáló egyéni erőforrás-szolgáltató:

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

## <a name="building-proxy-resource-endpoint"></a>Épület proxy erőforrás végpontjának

Egy **végpont** , amely megvalósítja a "Proxyszolgáltató" erőforrás **végpont** kell kezelnie a kérés- és az új API-hoz az Azure-ban. Ebben az esetben a **resourceType** hoz létre egy új Azure-erőforrás API `PUT`, `GET`, és `DELETE` CRUD végrehajtásához egy erőforráson, valamint `GET` összes meglévő erőforrást beolvasni.

> [!NOTE]
> A `id`, `name`, és `type` mezők nem szükségesek, de az egyéni erőforrás integrálhatja meglévő Azure-ökoszisztéma van szükség.

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

Paraméter leírása:

Tulajdonság | Sample | Leírás
---|---|---
name | '{myCustomResourceName}' | Az egyéni erőforrás neve.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | Az erőforrás-típus névtere.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | Az erőforrás-azonosítója.

### <a name="create-a-custom-resource"></a>Hozzon létre egy egyéni erőforrás

Az Azure API bejövő kérelem:

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

**Végpont** választ:

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

Hasonlóképpen válasza a **végpont** ezután kerül vissza az ügyfélnek. A végpont kapott választ kell visszaadnia:

- Érvényes JSON-objektum dokumentum. Tömbök és a karakterláncok egy felső objektum alatt ágyazhatók egymásba.
- A `Content-Type` fejlécében kell megadni az "application/json; charset = utf-8 ".

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

Ezt a kérelmet a rendszer ezután továbbítja a **végpont** formájában:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Hasonlóképpen, a válasz a **végpont** ezután kerül vissza az ügyfélnek. A végpont kapott választ kell visszaadnia:

- Egy érvényes JSON-objektum dokumentumot. Tömbök és a karakterláncok egy felső objektum alatt ágyazhatók egymásba.
- A `Content-Type` fejlécében kell megadni az "application/json; charset = utf-8 ".

**Végpont** választ:

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

Ezt a kérelmet a rendszer ezután továbbítja a **végpont** formájában:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Hasonlóképpen, a válasz a **végpont** ezután kerül vissza az ügyfélnek. A végpont kapott választ kell visszaadnia:

- Egy érvényes JSON-objektum dokumentumot. Tömbök és a karakterláncok egy felső objektum alatt ágyazhatók egymásba.
- A `Content-Type` fejlécében kell megadni az "application/json; charset = utf-8 ".
- Az erőforrások listájához a legfelső szintű alatt kell elhelyezni `value` tulajdonság.

**Végpont** választ:

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
- [Oktatóanyag: Az Azure egyéni erőforrás-szolgáltató létrehozása és telepítése az egyéni erőforrások](./create-custom-provider.md)
- [Útmutató: Az egyéni műveletek hozzáadása az Azure REST API-val](./custom-providers-action-endpoint-how-to.md)
- [Hivatkozás: Egyéni erőforrás-gyorsítótár – referencia](./custom-providers-proxy-cache-resource-endpoint-reference.md)
