---
title: Egyéni erőforrások hozzáadása az Azure REST API-hoz
description: Ismerje meg, hogyan adhat hozzá egyéni erőforrásokat az Azure REST API-hoz. Ez a cikk bemutatja az egyéni erőforrásokat megvalósító végpontok követelményeit és ajánlott eljárásokat.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650525"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Egyéni erőforrások hozzáadása az Azure REST API-hoz

Ez a cikk az egyéni erőforrásokat megvalósító Azure egyéni erőforrás-szolgáltatóvégpontok létrehozásához szükséges követelményeken és gyakorlati tanácsokon megy keresztül. Ha nem ismeri az Azure egyéni erőforrás-szolgáltatókat, olvassa [el az egyéni erőforrás-szolgáltatók áttekintését.](overview.md)

## <a name="how-to-define-a-resource-endpoint"></a>Erőforrás-végpont definiálása

A **végpont** egy URL-címet, amely egy szolgáltatásra mutat, amely megvalósítja az alapul szolgáló szerződést közte és az Azure között. A végpont az egyéni erőforrás-szolgáltatóban van definiálva, és bármely nyilvánosan elérhető URL-cím lehet. Az alábbi minta rendelkezik `endpointURL`egy **resourceType** nevű `myCustomResource` implementált .

Minta **Erőforrás-szolgáltató**:

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

## <a name="building-a-resource-endpoint"></a>Erőforrás-végpont létrehozása

Egy **erőforrástípust** megvalósító **végpontnak** kezelnie kell az azure-beli új API-ra vonatkozó kérést és választ. Amikor egy **erőforrás-típussal** rendelkező egyéni erőforrás-szolgáltató jön létre, új API-készletet hoz létre az Azure-ban. Ebben az esetben a **resourceType** létrehoz egy `PUT`új `GET`Azure-erőforrás API-t a számára, és `DELETE` egyetlen erőforráson hajtja végre a CRUD-t, valamint `GET` az összes meglévő erőforrás lekéréséhez:

Egyetlen erőforrás (`PUT` `GET`, `DELETE`és ): kezelése

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Az összes`GET`erőforrás beolvasása ( ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Egyéni erőforrások esetén az egyéni erőforrás-szolgáltatók kétféle`Proxy` **útválasztási típust**kínálnak: " és "`Proxy, Cache`".

### <a name="proxy-routing-type"></a>proxyút-műveletterv típusa

A`Proxy`" " **routingType** proxyk az egyéni erőforrás-szolgáltatóban megadott **végpontösszes** kérelemmetódusát proxyk. Mikor kell`Proxy`használni " ":

- A válasz teljes ellenőrzése szükséges.
- Rendszerek integrálása a meglévő erőforrásokkal.

Ha többet szeretne`Proxy`megtudni a " " erőforrásokról, olvassa el [az egyéni erőforrásproxy-referencia](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>proxygyorsítótár-útválasztás típusa

A`Proxy, Cache`" " " **routingType** proxyk csak `PUT` és `DELETE` kérés metódusok az egyéni erőforrás-szolgáltatóban megadott **végpontra.** Az egyéni erőforrás-szolgáltató `GET` automatikusan visszaküldi a kérelmeket a gyorsítótárban tárolt adatok alapján. Ha egy egyéni erőforrás gyorsítótárral van megjelölve, az egyéni erőforrás-szolgáltató is hozzáad / felülír a mezőket a válaszban, hogy az API-k azure-kompatibilis legyenek. Mikor kell`Proxy, Cache`használni " ":

- Olyan új rendszer létrehozása, amely nem rendelkezik meglévő erőforrásokkal.
- A meglévő Azure-ökoszisztémával dolgozhat.

Ha többet szeretne`Proxy, Cache`megtudni a " " erőforrásokról, olvassa el [az egyéni erőforrás-gyorsítótár hivatkozását](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Egyéni erőforrás létrehozása

Egyéni erőforrás-szolgáltatón kívül kétféleképpen hozhat létre egyéni erőforrást:

- Azure CLI
- Azure Resource Manager-sablonok

### <a name="azure-cli"></a>Azure CLI

Egyéni erőforrás létrehozása:

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

Paraméter | Kötelező | Leírás
---|---|---
teljes objektum | *igen* | Azt jelzi, hogy a tulajdonságobjektum más beállításokat is tartalmaz, például helyet, címkéket, termékváltozatokat és/vagy tervet.
id | *igen* | Az egyéni erőforrás erőforrásazonosítója. Ennek a **ResourceProvider szolgáltatón** kívül kell léteznie.
properties | *igen* | A **végpontra**küldendő kérelemtörzs.

Egyéni Azure-erőforrás törlése:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Paraméter | Kötelező | Leírás
---|---|---
id | *igen* | Az egyéni erőforrás erőforrásazonosítója. Ennek a **ResourceProvider szolgáltatón**kívül kell léteznie.

Azure-beli egyéni erőforrás lekérése:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Paraméter | Kötelező | Leírás
---|---|---
id | *igen* | Az egyéni erőforrás erőforrásazonosítója. Ennek a **ResourceProvider szolgáltatón** kívül kell léteznie.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

> [!NOTE]
> Az erőforrások megkövetelik , `id` `name`hogy `type` a válasz tartalmazzon egy megfelelő , és a **végpontról**.

Az Azure Resource Manager-sablonok megkövetelik, hogy `id`, , `name`és `type` megfelelően adja vissza az alsóbb rétegbeli végpontról. A visszaadott erőforrás-válasznak a következő formában kell lennie:

**Mintavégpont válasza:**

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

Minta Azure Resource Manager sablon:

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

Paraméter | Kötelező | Leírás
---|---|---
resourceTypeName | *igen* | Az egyéni szolgáltatóban definiált **resourceType** **neve.**
resourceProviderName | *igen* | Az egyéni erőforrás-szolgáltató példányának neve.
customResourceName | *igen* | Az egyéni erőforrás neve.

## <a name="next-steps"></a>További lépések

- [Az Azure egyéni erőforrás-szolgáltatóinak áttekintése](overview.md)
- [Rövid útmutató: Hozzon létre egyéni erőforrás-szolgáltatót, és telepítsen egyéni erőforrásokat](./create-custom-provider.md)
- [Oktatóanyag: Egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Útmutató: Egyéni műveletek hozzáadása az Azure REST API-hoz](./custom-providers-action-endpoint-how-to.md)
- [Hivatkozás: Egyéni erőforrásproxy-hivatkozás](proxy-resource-endpoint-reference.md)
- [Hivatkozás: Egyéni erőforrás-gyorsítótár hivatkozása](proxy-cache-resource-endpoint-reference.md)
