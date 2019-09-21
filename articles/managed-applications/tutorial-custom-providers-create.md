---
title: Egyéni szolgáltató létrehozása és használata
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és használhat egyéni szolgáltatót.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 053cf9fca03bf58cf10c313ae2569ce1918a46b9
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172903"
---
# <a name="create-and-use-a-custom-provider"></a>Egyéni szolgáltató létrehozása és használata

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatók használatával megváltoztathatja az Azure-beli munkafolyamatokat. Ez az oktatóanyag az egyéni szolgáltató létrehozásának folyamatát mutatja be. Ha nem ismeri az egyéni Azure-szolgáltatókat, tekintse meg [Az Azure-beli egyéni erőforrás-szolgáltatók áttekintését](./custom-providers-overview.md).

## <a name="create-a-custom-provider"></a>Egyéni szolgáltató létrehozása

> [!NOTE]
> Ez az oktatóanyag nem mutatja be a végpontok létrehozási módját. Ha nem rendelkezik REST-végponttal, kövesse az [oktatóanyagot a REST-végpontok létrehozásához](./tutorial-custom-providers-function-authoring.md), amely az aktuális oktatóanyag alapja.

A végpont létrehozása után létrehozhat egy egyéni szolgáltatót a szolgáltató és a végpont közötti szerződések létrehozásához. Egyéni szolgáltató esetén megadhatja a végpont-definíciók listáját:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Tulajdonság | Szükséges | Leírás
---|---|---
**name** | Igen | A végpont definíciójának neve. Az Azure a/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders alatti API-n keresztül teszi elérhetővé a nevet<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Nem | A végponti szerződés típusa Ha az érték nincs megadva, a rendszer alapértelmezés szerint a "proxy" értéket adja meg.
**végpont** | Igen | A végpont, amelybe a kérelmeket át kell irányítani. Ez a végpont kezeli a kérés válaszát, illetve a kérelem mellékhatásait.

A **végpont** értéke az Azure Function alkalmazás triggerének URL-címe. A `<yourapp>`, `<funcname>`, és`<functionkey>` helyőrzőket a létrehozott Function alkalmazás értékeivel kell helyettesíteni.

## <a name="define-custom-actions-and-resources"></a>Egyéni műveletek és erőforrások definiálása

Az egyéni szolgáltató a **műveletek** és a **resourceTypes** tulajdonságai között modellezett végpont-definíciók listáját tartalmazza. A **műveletek** tulajdonság az egyéni szolgáltató által elérhetővé tett egyéni műveletekre mutat, a **resourceTypes** tulajdonság pedig az egyéni erőforrások. Ebben az oktatóanyagban az egyéni szolgáltatónak van egy nevű `myCustomAction` művelet tulajdonsága és egy nevű `myCustomResources` **resourceTypes** tulajdonsága.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>Az egyéni szolgáltató üzembe helyezése

> [!NOTE]
> A **végpontok** értékeit az előző oktatóanyagban létrehozott Function alkalmazás trigger URL-címével kell helyettesíteni.

Az előző egyéni szolgáltatót Azure Resource Manager sablonnal is telepítheti:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Egyéni műveletek és erőforrások használata

Miután létrehozta az egyéni szolgáltatót, használhatja az új Azure API-kat. A következő lapok bemutatják, hogyan hívhat meg és használhat egyéni szolgáltatót.

### <a name="custom-actions"></a>Egyéni műveletek

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> A `{subscriptionId}` és`{resourceGroupName}` a helyőrzőket le kell cserélnie az előfizetés és az erőforráscsoport között, ahol az egyéni szolgáltatót telepítette.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Paraméter | Szükséges | Leírás
---|---|---
*művelet* | Igen | Az egyéni szolgáltatóban definiált művelet neve
*azonosítók* | Igen | Az egyéni szolgáltató erőforrás-azonosítója
*kérelem – törzs* | Nem | A végpontnak küldendő kérelem törzse

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Nincs.

---

### <a name="custom-resources"></a>Egyéni erőforrások

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> A `{subscriptionId}` és`{resourceGroupName}` a helyőrzőket le kell cserélnie az előfizetés és az erőforráscsoport között, ahol az egyéni szolgáltatót telepítette.

#### <a name="create-a-custom-resource"></a>Egyéni erőforrás létrehozása

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Paraméter | Szükséges | Leírás
---|---|---
*teljes objektum* | Igen | Azt jelzi, hogy a tulajdonságok objektum tartalmaz-e más beállításokat, például a helyet, a címkéket, az SKU-t vagy a csomagot.
*id* | Igen | Az egyéni erőforrás erőforrás-azonosítója. Ez az azonosító az egyéni szolgáltató erőforrás-AZONOSÍTÓjának kiterjesztése.
*Tulajdonságok* | Igen | A végpontnak küldendő kérelem törzse.

#### <a name="delete-a-custom-resource"></a>Egyéni erőforrás törlése

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paraméter | Szükséges | Leírás
---|---|---
*id* | Igen | Az egyéni erőforrás erőforrás-azonosítója. Ez az azonosító az egyéni szolgáltató erőforrás-AZONOSÍTÓjának kiterjesztése.

#### <a name="retrieve-a-custom-resource"></a>Egyéni erőforrás lekérése

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paraméter | Szükséges | Leírás
---|---|---
*id* | Igen | Az egyéni erőforrás erőforrás-azonosítója. Ez az azonosító az egyéni szolgáltató erőforrás-AZONOSÍTÓjának kiterjesztése.

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Példa Resource Manager-sablonra:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Paraméter | Szükséges | Leírás
---|---|---
*resourceTypeName* | Igen | Az egyéni szolgáltatóban definiált **resourceTypes** tulajdonság értéke.`name`
*resourceProviderName* | Igen | Az egyéni szolgáltató példányának neve.
*customResourceName* | Igen | Az egyéni erőforrás neve.

---

> [!NOTE]
> Az egyéni szolgáltató üzembe helyezésének és használatának befejezése után ne felejtse el törölni az összes létrehozott erőforrást, beleértve az Azure Function alkalmazást is.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta az egyéni szolgáltatókat. További információkért lásd:

- [Útmutató: Egyéni műveletek hozzáadása az Azure REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST APIhoz](./custom-providers-resources-endpoint-how-to.md)
