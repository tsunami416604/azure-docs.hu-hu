---
title: Erőforrás-szolgáltató létrehozása
description: Útmutatás erőforrás-szolgáltató létrehozásához és az egyéni erőforrástípusok üzembe helyezéséhez.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649868"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Gyors útmutató: egyéni szolgáltató létrehozása és egyéni erőforrások üzembe helyezése

Ebben a rövid útmutatóban létrehoz egy saját erőforrás-szolgáltatót, és üzembe helyezi az adott erőforrás-szolgáltató egyéni erőforrás-típusait. További információ az egyéni szolgáltatókról: az [Azure Custom Providers előzetes](overview.md)verziójának áttekintése.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató lépéseinek elvégzéséhez meg kell hívnia a REST-műveleteket. [A REST-kérelmek küldésének különböző módjai](/rest/api/azure/)vannak. Ha még nincs eszköz a REST-műveletekhez, telepítse a [ARMClient](https://github.com/projectkudu/ARMClient)-t. Ez egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti a Azure Resource Manager API meghívását.

## <a name="deploy-custom-provider"></a>Egyéni szolgáltató üzembe helyezése

Az egyéni szolgáltató beállításához telepítsen egy [példa sablont](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) az Azure-előfizetésre.

A sablon telepítése után az előfizetése a következő erőforrásokkal rendelkezik:

* Függvényalkalmazás az erőforrásokkal és műveletekkel kapcsolatos műveletekkel.
* Az egyéni szolgáltatón keresztül létrehozott felhasználók tárolására szolgáló Storage-fiók.
* Egyéni erőforrás-típusokat és műveleteket definiáló egyéni szolgáltató. A függvény alkalmazás-végpontot használja a kérelmek küldéséhez.
* Egyéni erőforrás az egyéni szolgáltatótól.

Az egyéni szolgáltató PowerShell használatával történő üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

A megoldás a következő gombbal is üzembe helyezhető:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Egyéni szolgáltató és erőforrás megtekintése

A portálon az egyéni szolgáltató egy rejtett erőforrástípus. Az erőforrás-szolgáltató üzembe helyezésének megerősítéséhez navigáljon az erőforráscsoporthoz. Válassza a **rejtett típusok megjelenítésének**lehetőségét.

![Rejtett erőforrástípusok megjelenítése](./media/create-custom-provider/show-hidden.png)

A telepített egyéni erőforrástípus megtekintéséhez használja a GET műveletet az erőforrás-típuson.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

A ARMClient használata esetén használja a következőket:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

A válasz a következőket kapja:

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

## <a name="call-action"></a>Hívási művelet

Az egyéni szolgáltató is rendelkezik egy **ping**nevű művelettel. A kérést feldolgozó kód implementálva van a Function alkalmazásban. A pingelési művelet egy üdvözléssel válaszol.

Pingelési kérelem küldéséhez használja a POST műveletet az egyéni szolgáltatónál.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

A ARMClient használata esetén használja a következőket:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

A válasz a következőket kapja:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Erőforrástípus létrehozása

Az egyéni erőforrástípus létrehozásához üzembe helyezheti az erőforrást egy sablonban. Ez a megközelítés az ebben a rövid útmutatóban üzembe helyezett sablonban látható. Egy PUT-kérést is küldhet az erőforrás típusára.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

A ARMClient használata esetén használja a következőket:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

A válasz a következőket kapja:

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

## <a name="next-steps"></a>Következő lépések

Az egyéni szolgáltatók bevezetését az [Azure Custom Providers előzetes](overview.md)verziójának áttekintése című témakörben tekintheti meg.
