---
title: Hozzon létre, és felhasználja az Azure egyéni szolgáltató
description: Ez az oktatóanyag ismerteti, hogyan hozzon létre és a egy egyéni szolgáltató kerül.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800000"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Jelentésszerkesztő egy RESTful-végpont egyéni szolgáltatók számára

Egyéni szolgáltatók lehetővé teszik az Azure-ban a munkafolyamatok testreszabása. Egy egyéni szolgáltató az Azure között létrejött szerződés és a egy `endpoint`. Ebben az oktatóanyagban a folyamaton, létrehozhat egy egyéni szolgáltató fog áthaladni. Ha ismeri az Azure egyéni szolgáltatókat, [az Áttekintés az egyéni erőforrás-szolgáltatók](./custom-providers-overview.md).

Ebben az oktatóanyagban meg van osztva az alábbi lépéseket:

- Mi az egyéni szolgáltató
- Az egyéni műveletek és-erőforrások
- Az egyéni szolgáltató telepítése

Ebben az oktatóanyagban az alábbi oktatóanyagok hoz létre:

- [Jelentésszerkesztő egy RESTful-végpont egyéni szolgáltatók számára](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>Egy egyéni szolgáltató létrehozása

> [!NOTE]
> Ebben az oktatóanyagban nem megy keresztül szerzői egy végpontot. Kérjük, kövesse a [útmutató a szerzői RESTful végpontokon](./tutorial-custom-providers-function-authoring.md) Ha nem rendelkezik egy RESTful végpontot.

Egyszer a `endpoint` van létrehozva, hozzon létre egy egyéni szolgáltató létrehozásához a között, hozhat létre, és a `endpoint`. Egy egyéni szolgáltató végpont-definíciók listájának megadását teszi lehetővé.

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Tulajdonság | Szükséges | Leírás
---|---|---
name | *igen* | A végpont-definíció neve. Azure fogja elérhetővé tenni ezt a nevet, a saját API-n keresztül "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Meghatározza, hogy a Szerződéstípus a `endpoint`. Ha nincs megadva, az alapértelmezés lesz az "Proxyszolgáltató".
endpoint | *igen* | A végpontot irányíthatja a kérelmeket. Ez a válasz, valamint a kérés ügyféloldali eredő esetleges kezeli.

Ebben az esetben a `endpoint` az Azure-függvény, eseményindító URL-címe. A `<yourapp>`, `<funcname>`, és `<functionkey>` le kell cserélni a létrehozott függvény értékek.

## <a name="defining-custom-actions-and-resources"></a>Az egyéni műveletek és-erőforrások

Az egyéni szolgáltató modellezve a szolgáltatásvégpont-definíció listáját tartalmazza `actions` és `resourceTypes`. `actions` az egyéni műveletek készítsen leképezést az egyéni szolgáltató által közzétett, miközben `resourceTypes` az egyéni erőforrásokat. Ebben az oktatóanyagban egy egyéni szolgáltató meghatározunk egy `action` nevű `myCustomAction` és a egy `resourceType` nevű `myCustomResources`.

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

Cserélje le `endpoint` a trigger URL-címet a korábban az előző oktatóanyagban létrehozott függvény.

## <a name="deploying-the-custom-provider"></a>Az egyéni szolgáltató telepítése

> [!NOTE]
> A `endpoint` le kell cserélni a függvény URL-CÍMÉT.

A fenti egyéni szolgáltató Azure Resource Manager-sablon használatával is telepíthető.

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

## <a name="using-custom-actions-and-resources"></a>Az egyéni műveletek és az erőforrások használata

Után hoztunk létre egy egyéni szolgáltató, hogy az új Azure API-jainkkal. A következő szakaszban ismertetik, hogyan meghívása és a egy egyéni szolgáltató.

### <a name="custom-actions"></a>Az egyéni műveletek

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> A `{subscriptionId}` és `{resourceGroupName}` le kell cserélni az előfizetést és az erőforráscsoport, amelyben az egyéni szolgáltató telepítve lett.

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
a művelet | *igen* | A művelet a létrehozott egyéni szolgáltató definiált neve.
azonosítók | *igen* | A létrehozott egyéni szolgáltató erőforrás-Azonosítóját.
kérelem – törzs | *no* | A kérés törzse, amely küld a `endpoint`.

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Nincs.

---

### <a name="custom-resources"></a>Egyéni erőforrások

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> A `{subscriptionId}` és `{resourceGroupName}` le kell cserélni az előfizetést és az erőforráscsoport, amelyben az egyéni szolgáltató telepítve lett.

Hozzon létre egy egyéni erőforrást:

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
is-full-object | *igen* | Azt jelzi, hogy a Tulajdonságok objektumában további lehetőségeket, például hely, címkék, termékváltozat és/vagy a csomag tartalmaz-e.
id | *igen* | Az egyéni erőforrás erőforrás-Azonosítóját. Ez a létrehozott egyéni szolgáltató engedményt léteznie kell.
properties | *igen* | A kérés törzse, amely küld a `endpoint`.

Egy egyéni Azure-erőforrás törlése:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paraméter | Szükséges | Leírás
---|---|---
id | *igen* | Az egyéni erőforrás erőforrás-Azonosítóját. Ez a létrehozott egyéni szolgáltató engedményt léteznie kell.

Az Azure egyéni erőforrás lekérése:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Paraméter | Szükséges | Leírás
---|---|---
id | *igen* | Az egyéni erőforrás erőforrás-Azonosítóját. Ez a létrehozott egyéni szolgáltató engedményt léteznie kell.

# <a name="templatetabtemplate"></a>[Sablon](#tab/template)

Példa az Azure Resource Manager-sablon:

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
resourceTypeName | *igen* | A `name` , a *resourceType* az egyéni szolgáltató meghatározott.
resourceProviderName | *igen* | Az egyéni szolgáltató-példány neve.
customResourceName | *igen* | Az egyéni erőforrás nevét.

---

> [!NOTE]
> Ha végzett, telepítését és használatát az egyéni szolgáltató, ne felejtse el többek között az Azure-függvény minden létrehozott erőforrások törlése.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megismerhette egyéni szolgáltatókat. Nyissa meg a következő cikk az egyéni szolgáltató létrehozásához.

- [Útmutató: Az egyéni műveletek hozzáadása az Azure REST API-val](./custom-providers-action-endpoint-how-to.md)
- [Útmutató: Egyéni erőforrások hozzáadása az Azure REST API-val](./custom-providers-resources-endpoint-how-to.md)
