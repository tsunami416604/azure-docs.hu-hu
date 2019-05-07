---
title: Erőforrás-szolgáltató létrehozása az egyéni szolgáltatók Azure-on Előzetesben
description: Ismerteti, hogyan lehet egy erőforrás-szolgáltató létrehozása és telepítése az egyéni erőforrástípusokat.
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 34dd5efda2c9f6cc9a7b5ddcde06e8f7d27de901
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158426"
---
# <a name="tutorial-create-custom-provider-and-deploy-custom-resources"></a>Oktatóanyag: Hozzon létre egyéni szolgáltatót és az egyéni erőforrások üzembe helyezése

Ebben az oktatóanyagban a saját erőforrás-szolgáltató létrehozása, és egyéni erőforrás-típus adott erőforrás-szolgáltató telepítése. Egyéni szolgáltatók kapcsolatos további információkért lásd: [Azure egyéni szolgáltatók előzetes áttekintése](custom-providers-overview.md).

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban szereplő lépések végrehajtásához szüksége REST-műveleteinek meghívására. Nincsenek [REST-kérelmeket küldő különböző módokon](/rest/api/azure/). Ha egy eszköz a REST-műveleteket még nem rendelkezik, telepítse a [ARMClient](https://github.com/projectkudu/ARMClient). Egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása.

## <a name="deploy-custom-provider"></a>Egyéni szolgáltató üzembe helyezése

Állítsa be az egyéni szolgáltató, helyezzen üzembe egy [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) az Azure-előfizetéshez.

A sablon üzembe helyezése után az előfizetés számára az alábbi forrásanyagokat:

* A műveletek az erőforrások és a műveletek a Függvényalkalmazást.
* Az egyéni szolgáltatón keresztül létrehozott felhasználók tárolására szolgáló Tárfiókot.
* Egyéni szolgáltató, amely meghatározza az egyéni erőforrástípusok és műveleteket. A függvény alkalmazás végpont kérelmek küldéséhez használ.
* Az egyéni szolgáltató egyéni erőforrás.

Az egyéni szolgáltató a PowerShell-lel üzembe helyezéséhez használja:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Vagy telepítheti a megoldás az alábbi gombra:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Nézet egyéni szolgáltatói és erőforrás

A portálon az egyéni szolgáltató olyan rejtett erőforrás. Győződjön meg arról, hogy az erőforrás-szolgáltató van telepítve, nyissa meg az erőforráscsoportot. Válassza a **rejtett típusok megjelenítése**.

![Rejtett típusok megjelenítése](./media/create-custom-providers/show-hidden.png)

Az üzembe helyezett egyéni erőforrástípus megtekintéséhez használja a GET műveletet az erőforrás típusát.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

A ARMClient használhatók:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

A válasz jelenik meg:

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

## <a name="call-action"></a>Művelet meghívása

Az egyéni szolgáltató is rendelkezik nevű művelet **ping**. A függvényalkalmazás implementálva van a kódot, amely feldolgozza a kérést. A művelet az üdvözlőszöveget válaszol.

Egy pingelés kérelmet küld, használja az egyéni szolgáltató a POST műveletet.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

A ARMClient használhatók:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

A válasz jelenik meg:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Erőforrás-típus létrehozása

Szeretne létrehozni az egyéni erőforrás típusa, az erőforrás-sablonban is telepítheti. Ez a megközelítés akkor jelenik meg a sablon ebben az oktatóanyagban telepítette. Egy PUT kérelmet az erőforrástípusok is küldhet.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

A ARMClient használhatók:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

A válasz jelenik meg:

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

Egyéni szolgáltatók bemutatása, lásd: [Azure egyéni szolgáltatók előzetes áttekintése](custom-providers-overview.md).
