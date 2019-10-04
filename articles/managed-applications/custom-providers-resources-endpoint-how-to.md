---
title: Egyéni erőforrások felvétele az Azure REST API
description: Ismerje meg az egyéni erőforrások felvétele az Azure REST API-hoz. Ez a cikk részletesen ismerteti a követelmények és ajánlott eljárásait, egyéni erőforrások megvalósítani kívánt végpontok keresztül.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: a3cd1fe69a0d99f9faf3a451f76a3a420d713711
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795221"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Egyéni erőforrások hozzáadása az Azure REST API-val

Ez a cikk a követelmények és ajánlott eljárások az Azure egyéni erőforrás-szolgáltató olyan végpontok, amely megvalósítja az egyéni erőforrások létrehozása fog áthaladni. Ha ismeri az Azure egyéni erőforrás-szolgáltatók, [az Áttekintés az egyéni erőforrás-szolgáltatók](./custom-providers-overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Egy erőforrás végpontjának definiálása

Egy **végpont** URL-cím, amely egy szolgáltatás, amely megvalósítja az alapul szolgáló szerződés és az Azure között. A végpont az egyéni erőforrás-szolgáltató van definiálva, és bármilyen nyilvánosan elérhető URL-CÍMÉT. Az alábbi minta rendelkezik egy **resourceType** nevű `myCustomResource` által megvalósított `endpointURL`.

Minta **ResourceProvider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Egy erőforrás-végpont létrehozása

Egy **végpont** , amely megvalósítja az **resourceType** kell kezelnie a kérés- és az új API-hoz az Azure-ban. Ha egy egyéni erőforrás-szolgáltató egy **resourceType** van hozta létre, létrehoz egy új API-készletet az Azure-ban. Ebben az esetben a **resourceType** hoz létre egy új Azure-erőforrás API `PUT`, `GET`, és `DELETE` CRUD végrehajtásához egy erőforráson, valamint `GET` beolvasni az összes létező erőforráson:

Egyetlen erőforrás módosítására (`PUT`, `GET`, és `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Minden erőforrás lekérése (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Az egyéni erőforrások egyéni erőforrás-szolgáltatók kétféle **routingTypes**: "`Proxy`"és"`Proxy, Cache`".

### <a name="proxy-routing-type"></a>proxy útválasztási típus

A "`Proxy`" **routingType** proxyk összes kérelem módszerek a **végpont** az egyéni erőforrás-szolgáltató megadott. Mikor érdemes használni? "`Proxy`":

- A válasz teljes hozzáférésük van szükség.
- Rendszerek integrálása meglévő erőforrásokat.

További információ "`Proxy`" erőforrások, lásd: [az egyéni erőforrás-proxy – referencia](./custom-providers-proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>proxy gyorsítótár útválasztási típus

A "`Proxy, Cache`" **routingType** csak proxyk `PUT` és `DELETE` módszerek kérése a **végpont** az egyéni erőforrás-szolgáltató megadott. Az egyéni erőforrás-szolgáltató automatikusan visszatér `GET` kérések alapján mit tárolt gyorsítótárában. Ha egy egyéni erőforrás gyorsítótár meg van jelölve, az egyéni erőforrás-szolgáltató is hozzáadása / írja felül a választ, hogy az API-k Azure megfelelő mezőket. Mikor érdemes használni? "`Proxy, Cache`":

- Új rendszer létrehozása, amely nem rendelkezik sem meglévő erőforrásokkal.
- Meglévő Azure-ökoszisztéma működnek.

További információ "`Proxy, Cache`" erőforrások, lásd: [az egyéni erőforrás-gyorsítótár – referencia](./custom-providers-proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Egyéni erőforrás létrehozása

Minden egyéni erőforrás-szolgáltató egy egyéni erőforrás létrehozásának két fő módja van:

- Azure CLI
- Az Azure Resource Manager-sablonok

### <a name="azure-cli"></a>Azure CLI

Hozzon létre egy egyéni erőforrást:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Paraméter | Szükséges | Leírás
---|---|---
is-full-object | *igen* | Azt jelzi, hogy a Tulajdonságok objektumában további lehetőségeket, például hely, címkék, termékváltozat és/vagy a csomag tartalmaz-e.
id | *igen* | Az egyéni erőforrás erőforrás-Azonosítóját. Ez ki, léteznie kell a **ResourceProvider**
properties | *igen* | A kérés törzse, amely küld a **végpont**.

Egy egyéni Azure-erőforrás törlése:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Paraméter | Szükséges | Leírás
---|---|---
id | *igen* | Az egyéni erőforrás erőforrás-Azonosítóját. Ez ki, léteznie kell a **ResourceProvider**.

Az Azure egyéni erőforrás lekérése:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Paraméter | Szükséges | Leírás
---|---|---
id | *igen* | Az egyéni erőforrás erőforrás-Azonosítóját. Ez ki, léteznie kell a **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

> [!NOTE]
> Erőforrásokhoz szükséges, hogy a válasz tartalmazhat egy megfelelő `id`, `name`, és `type` származó a **végpont**.

Az Azure Resource Manager-sablonokat kell `id`, `name`, és `type` megfelelően az alsóbb rétegbeli végpontról visszaadott. Visszaadott erőforrások választ alakúnak kell lennie:

Minta **végpont** választ:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Példa az Azure Resource Manager-sablon:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Paraméter | Szükséges | Leírás
---|---|---
resourceTypeName | *igen* | A **neve** , a **resourceType** az egyéni szolgáltató meghatározott.
resourceProviderName | *igen* | Az egyéni erőforrás szolgáltató példány neve.
customResourceName | *igen* | Az egyéni erőforrás nevét.

## <a name="next-steps"></a>További lépések

- [Az Azure egyéni erőforrás-szolgáltatók áttekintése](./custom-providers-overview.md)
- [Rövid útmutató: Az Azure egyéni erőforrás-szolgáltató létrehozása és telepítése az egyéni erőforrások](./create-custom-provider.md)
- [Oktatóanyag: Egyéni műveletek és az erőforrások létrehozása az Azure-ban](./tutorial-custom-providers-101.md)
- [Útmutató: Az egyéni műveletek hozzáadása az Azure REST API-val](./custom-providers-action-endpoint-how-to.md)
- [Hivatkozás: Egyéni erőforrás-Proxy – referencia](./custom-providers-proxy-resource-endpoint-reference.md)
- [Hivatkozás: Egyéni erőforrás-gyorsítótár – referencia](./custom-providers-proxy-cache-resource-endpoint-reference.md)
