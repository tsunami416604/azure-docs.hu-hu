---
title: Egyéni szolgáltató létrehozása és használata
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és használhat azure-beli egyéni szolgáltatót. Egyéni szolgáltatók használatával módosíthatja a munkafolyamatokat az Azure-ban.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 09df78955de6423244c2d8ec94e1e1c06ecab257
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650029"
---
# <a name="create-and-use-a-custom-provider"></a>Egyéni szolgáltató létrehozása és használata

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatókkal módosíthatja a munkafolyamatokat az Azure-ban. Ez az oktatóanyag az egyéni szolgáltató létrehozásának folyamatát mutatja be. Ha nem ismeri az Azure egyéni szolgáltatókat, tekintse [meg az Azure egyéni erőforrás-szolgáltatók áttekintését.](overview.md)

## <a name="create-a-custom-provider"></a>Egyéni szolgáltató létrehozása

> [!NOTE]
> Ez az oktatóanyag nem mutatja meg, hogyan kell végpontot szerző. Ha nem rendelkezik RESTFUL-végpont, kövesse az [oktatóanyag-ból authoring RESTful végpontok](./tutorial-custom-providers-function-authoring.md), amely az alapja az aktuális oktatóanyag.

Miután létrehozott egy végpontot, létrehozhat egy egyéni szolgáltatót, amely létrehoz egy szerződést a szolgáltató és a végpont között. Egyéni szolgáltatóval megadhatja a végpontdefiníciók listáját:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Tulajdonság | Kötelező | Leírás
---|---|---
**név** | Igen | A végpontdefiníció neve. Az Azure ezt a nevet az API-n keresztül teszi elérhetővé a /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders alatt.<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType típus** | Nem | A végpontszerződés típusa. Ha az érték nincs megadva, akkor alapértelmezés szerint "Proxy".
**endpoint** | Igen | A végpont, amelyhez a kérelmeket továbbítani szeretné. Ez a végpont kezeli a választ, és a kérelem esetleges mellékhatásai.

A **végpont** értéke az Azure függvényalkalmazás eseményindító URL-címe. A `<yourapp>` `<funcname>`, `<functionkey>` és helyőrzők kell cserélni értékeket a létrehozott függvényalkalmazás.

## <a name="define-custom-actions-and-resources"></a>Egyéni műveletek és erőforrások definiálása

Az egyéni szolgáltató a **műveletek** és a **resourceTypes** tulajdonságok alatt modellezett végpontdefiníciók listáját tartalmazza. A **actions** tulajdonság leképezi az egyéni szolgáltatók által elérhetővé tett egyéni műveleteket, és a **resourceTypes** tulajdonság az egyéni erőforrások. Ebben az oktatóanyagban az egyéni `myCustomAction` szolgáltató rendelkezik egy `myCustomResources` **nevű művelettulajdonsággal** és egy **resourceTypes** tulajdonsággal.

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

## <a name="deploy-the-custom-provider"></a>Az egyéni szolgáltató telepítése

> [!NOTE]
> A **végpontértékeket** le kell cserélnie az előző oktatóanyagban létrehozott függvényalkalmazás eseményindító URL-címére.

Az előző egyéni szolgáltatót egy Azure Resource Manager-sablon használatával telepítheti:

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

Miután létrehozott egy egyéni szolgáltatót, használhatja az új Azure API-kat. A következő lapok bemutatják, hogyan hívhat fel és használhat egyéni szolgáltatót.

### <a name="custom-actions"></a>Egyéni műveletek

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Le kell `{subscriptionId}` cserélnie a és `{resourceGroupName}` a helyőrzőket annak az előfizetésnek és erőforráscsoportnak az előfizetésére és erőforráscsoportjára, ahol az egyéni szolgáltatót telepítette.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Paraméter | Kötelező | Leírás
---|---|---
*Akció* | Igen | Az egyéni szolgáltatóban definiált művelet neve
*Azonosítók* | Igen | Az egyéni szolgáltató erőforrásazonosítója
*megkereső szerv* | Nem | A végpontra küldendő kérelemtörzs

# <a name="template"></a>[Sablon](#tab/template)

Nincs.

---

### <a name="custom-resources"></a>Egyéni erőforrások

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Le kell `{subscriptionId}` cserélnie a és `{resourceGroupName}` a helyőrzőket annak az előfizetésnek és erőforráscsoportnak az előfizetésére és erőforráscsoportjára, ahol az egyéni szolgáltatót telepítette.

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

Paraméter | Kötelező | Leírás
---|---|---
*teljes objektum* | Igen | Azt jelzi, hogy a tulajdonságobjektum tartalmaz-e más beállításokat, például a helyet, a címkéket, a termékváltozatokat vagy a tervet.
*id* | Igen | Az egyéni erőforrás erőforrásazonosítója. Ez az azonosító az egyéni szolgáltatói erőforrás-azonosító kiterjesztése.
*Tulajdonságok* | Igen | A végpontra küldendő kérelemtörzs.

#### <a name="delete-a-custom-resource"></a>Egyéni erőforrás törlése

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paraméter | Kötelező | Leírás
---|---|---
*id* | Igen | Az egyéni erőforrás erőforrásazonosítója. Ez az azonosító az egyéni szolgáltatói erőforrás-azonosító kiterjesztése.

#### <a name="retrieve-a-custom-resource"></a>Egyéni erőforrás beolvasása

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paraméter | Kötelező | Leírás
---|---|---
*id* | Igen | Az egyéni erőforrás erőforrásazonosítója. Ez az azonosító az egyéni szolgáltatói erőforrás-azonosító kiterjesztése.

# <a name="template"></a>[Sablon](#tab/template)

Minta erőforrás-kezelő sablon:

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

Paraméter | Kötelező | Leírás
---|---|---
*resourceTypeName* | Igen | Az `name` egyéni szolgáltatóban definiált **resourceTypes** tulajdonság értéke.
*resourceProviderName* | Igen | Az egyéni szolgáltatópéldány neve.
*customResourceName* | Igen | Az egyéni erőforrás neve.

---

> [!NOTE]
> Miután befejezte az egyéni szolgáltató üzembe helyezését és használatát, ne felejtse el megtisztítani az összes létrehozott erőforrást, beleértve az Azure függvényalkalmazást is.

## <a name="next-steps"></a>További lépések

Ebben a cikkben az egyéni szolgáltatókról szerzett tudomást. További információkért lásd:

- [Útmutató: Egyéni műveletek hozzáadása az Azure REST API-hoz](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST API-hoz](./custom-providers-resources-endpoint-how-to.md)
