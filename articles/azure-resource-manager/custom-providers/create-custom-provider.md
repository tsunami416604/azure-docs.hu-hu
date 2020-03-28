---
title: Erőforrás-szolgáltató létrehozása
description: Ez a témakör azt ismerteti, hogy miként hozhat létre erőforrás-szolgáltatót, és hogyan telepítheti egyéni erőforrástípusait.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649868"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Rövid útmutató: Egyéni szolgáltató létrehozása és egyéni erőforrások üzembe helyezése

Ebben a rövid útmutatóban saját erőforrás-szolgáltatót hoz létre, és egyéni erőforrástípusokat telepít az adott erőforrás-szolgáltatóhoz. Az egyéni szolgáltatókról az [Azure egyéni szolgáltatók előzetes verzió – áttekintés című témakörben olvashat bővebben.](overview.md)

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató lépései végrehajtásához meg kell hívnia a REST-műveleteket. A [REST-kérelmek küldésének különböző módjai](/rest/api/azure/)vannak. Ha még nem rendelkezik a REST-műveletekhez szükséges eszközzel, telepítse az [ARMClient programot.](https://github.com/projectkudu/ARMClient) Ez egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívását.

## <a name="deploy-custom-provider"></a>Egyéni szolgáltató telepítése

Az egyéni szolgáltató beállítása, üzembe helyezegy [példasablont](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) az Azure-előfizetésben.

A sablon üzembe helyezése után az előfizetés a következő erőforrásokkal rendelkezik:

* Függvényalkalmazás az erőforrások és műveletek műveleteit.
* Tárfiók az egyéni szolgáltatón keresztül létrehozott felhasználók tárolására.
* Egyéni szolgáltató, amely meghatározza az egyéni erőforrástípusokat és műveleteket. A függvényalkalmazás-végpontot használja a kérelmek küldéséhez.
* Egyéni erőforrás az egyéni szolgáltatótól.

Az egyéni szolgáltató PowerShell használatával történő üzembe helyezéséhez használja a következőket:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Vagy a következő gombbal telepítheti a megoldást:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Egyéni szolgáltató és erőforrás megtekintése

A portálon az egyéni szolgáltató egy rejtett erőforrástípus. Annak ellenőrzéséhez, hogy az erőforrás-szolgáltató telepítve van-e, keresse meg az erőforráscsoportot. Válassza a **rejtett típusok megjelenítésének**lehetőségét.

![Rejtett erőforrástípusok megjelenítése](./media/create-custom-provider/show-hidden.png)

A telepített egyéni erőforrástípus megtekintéséhez használja a GET műveletet az erőforrástípuson.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Az ARMClient segítségével használja:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

A következő választ kapja:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>Hívásművelet

Az egyéni szolgáltató nak ping **nevű**művelete is van. A kérelmet felértékelő kód a függvényalkalmazásban van megvalósítva. A pingművelet üdvözléssel válaszol.

Pingkérés küldéséhez használja a POST műveletet az egyéni szolgáltatón.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Az ARMClient segítségével használja:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

A következő választ kapja:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Erőforrástípus létrehozása

Az egyéni erőforrástípus létrehozásához telepítheti az erőforrást egy sablonban. Ez a megközelítés az ebben a rövid útmutatóban üzembe helyezett sablonban jelenik meg. Az erőforrástípushoz PUT-kérelmet is küldhet.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Az ARMClient segítségével használja:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

A következő választ kapja:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>További lépések

Az egyéni szolgáltatók bemutatása az [Azure Egyéni szolgáltatók előzetes verzió – áttekintés című témakörben található.](overview.md)
