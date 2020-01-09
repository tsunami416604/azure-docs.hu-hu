---
title: Egyéni erőforrások hozzáadása az Azure REST APIhoz
description: Ismerje meg, hogyan adhat hozzá egyéni erőforrásokat az Azure REST APIhoz. Ez a cikk végigvezeti azon végpontok követelményein és ajánlott eljárásain, amelyek egyéni erőforrásokat kívánnak megvalósítani.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650525"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Egyéni erőforrások hozzáadása az Azure REST APIhoz

Ez a cikk az egyéni erőforrásokat megvalósító Azure-beli egyéni erőforrás-szolgáltatói végpontok létrehozásának követelményeit és ajánlott eljárásait ismerteti. Ha nem ismeri az Azure egyéni erőforrás-szolgáltatóit, tekintse [meg az egyéni erőforrás-szolgáltatók áttekintése](overview.md)című témakört.

## <a name="how-to-define-a-resource-endpoint"></a>Erőforrás-végpont definiálása

A **végpont** olyan URL-cím, amely egy szolgáltatásra mutat, amely megvalósítja az alapul szolgáló szerződést az IT és az Azure között. A végpont az egyéni erőforrás-szolgáltatóban van definiálva, és bármely nyilvánosan elérhető URL-cím lehet. Az alábbi minta egy `endpointURL`által megvalósított `myCustomResource` nevű **resourceType** rendelkezik.

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

## <a name="building-a-resource-endpoint"></a>Erőforrás-végpont létrehozása

Egy **resourceType** megvalósító **végpontnak** az új API-ra vonatkozó kérést és választ kell kezelnie az Azure-ban. Egy **resourceType** rendelkező egyéni erőforrás-szolgáltató létrehozásakor új API-készletet fog létrehozni az Azure-ban. Ebben az esetben a **resourceType** új Azure Resource API-t hoz majd `PUT`hoz, `GET`hoz és `DELETE`, hogy a szifiliszt egyetlen erőforráson végezze el, valamint `GET` az összes meglévő erőforrás lekéréséhez:

Egyetlen erőforrás (`PUT`, `GET`és `DELETE`) kezelése:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Az összes erőforrás beolvasása (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Egyéni erőforrások esetében az egyéni erőforrás-szolgáltatók kétféle **routingTypes**kínálnak: "`Proxy`" és "`Proxy, Cache`".

### <a name="proxy-routing-type"></a>proxy útválasztási típusa

A "`Proxy`" **routingType** a minden kérési metódust az egyéni erőforrás-szolgáltatóban megadott **végpontra** . Mikor kell használni a "`Proxy`":

- A válasz teljes körű ellenőrzése szükséges.
- Rendszerek integrálása meglévő erőforrásokkal.

Ha többet szeretne megtudni a "`Proxy`" erőforrásokról, tekintse meg [az egyéni erőforrás-proxy referenciáját](proxy-resource-endpoint-reference.md) .

### <a name="proxy-cache-routing-type"></a>proxy gyorsítótárának útválasztási típusa

A "`Proxy, Cache`" **routingType** proxyk csak az egyéni erőforrás-szolgáltatóban megadott **végpontra** `PUT` és `DELETE` kérelmeket. Az egyéni erőforrás-szolgáltató automatikusan visszaküldi `GET` kérelmeket a gyorsítótárában tárolt elemek alapján. Ha egy egyéni erőforrás a gyorsítótárral van megjelölve, akkor az egyéni erőforrás-szolgáltató is hozzáadja/felülírja a válaszban a mezőket az API-k Azure-nak megfelelővé tételéhez. Mikor kell használni a "`Proxy, Cache`":

- Olyan új rendszer létrehozása, amely nem rendelkezik meglévő erőforrásokkal.
- A meglévő Azure-ökoszisztéma használata.

Ha többet szeretne megtudni a "`Proxy, Cache`" erőforrásokról, tekintse meg [az egyéni erőforrás-gyorsítótár referenciáját](proxy-cache-resource-endpoint-reference.md) .

## <a name="creating-a-custom-resource"></a>Egyéni erőforrás létrehozása

Egyéni erőforrás-szolgáltatón kívül két fő módszert hozhat létre:

- Azure parancssori felület (CLI)
- Azure Resource Manager sablonok

### <a name="azure-cli"></a>Azure parancssori felület (CLI)

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
teljes objektum | *igen* | Azt jelzi, hogy a tulajdonságok objektum olyan egyéb beállításokat tartalmaz, mint például a hely, a címkék, az SKU és/vagy a csomag.
id | *igen* | Az egyéni erőforrás erőforrás-azonosítója. Ez a **ResourceProvider** kívül van
properties | *igen* | A **végpontnak**küldendő kérelem törzse.

Egyéni Azure-erőforrás törlése:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Paraméter | Szükséges | Leírás
---|---|---
id | *igen* | Az egyéni erőforrás erőforrás-azonosítója. Ez a **ResourceProvider**kívülről is fennáll.

Egyéni Azure-erőforrás beolvasása:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Paraméter | Szükséges | Leírás
---|---|---
id | *igen* | Az egyéni erőforrás erőforrás-azonosítója. Ez a **ResourceProvider** kívül van

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

> [!NOTE]
> Az erőforrásokhoz szükséges, hogy a válasz megfelelő `id`, `name`és `type` a **végpontból**.

Azure Resource Manager-sablonokhoz a `id`, a `name`és a `type` megfelelő értéket kell visszaadni az alsóbb rétegbeli végpontból. A visszaadott erőforrás-válasznak a következő formában kell szerepelnie:

Példa a **végpont** válaszára:

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

Paraméter | Szükséges | Leírás
---|---|---
resourceTypeName | *igen* | Az egyéni szolgáltatóban definiált **resourceType** **neve** .
resourceProviderName | *igen* | Az egyéni erőforrás-szolgáltató példányának neve.
customResourceName | *igen* | Az egyéni erőforrás neve.

## <a name="next-steps"></a>Következő lépések

- [Az Azure egyéni erőforrás-szolgáltatóinak áttekintése](overview.md)
- [Rövid útmutató: Azure egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Oktatóanyag: egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Útmutató: egyéni műveletek hozzáadása az Azure REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Hivatkozás: egyéni erőforrás-proxy referenciája](proxy-resource-endpoint-reference.md)
- [Hivatkozás: az erőforrás-gyorsítótár egyéni leírása](proxy-cache-resource-endpoint-reference.md)
