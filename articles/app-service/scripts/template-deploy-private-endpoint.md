---
title: Ezzel a Azure Resource Manager sablonnal saját végpontot telepíthet a webalkalmazáshoz.
description: Ismerje meg, hogyan helyezhet üzembe privát végpontot a webalkalmazáshoz a ARM sablon használatával
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535711"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>App Service alkalmazás létrehozása és privát végpont üzembe helyezése Azure Resource Manager sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablonnal hozhat létre webalkalmazást, és közzéteheti azt egy privát végponttal.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Előfeltétel

Aktív előfizetéssel rendelkező Azure-fiókra van szüksége. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ez a sablon létrehoz egy privát végpontot egy Azure-webalkalmazáshoz.

### <a name="review-the-template"></a>A sablon áttekintése

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Azure Resource Manager-sablon üzembe helyezése az Azure-ban:

1. Az Azure-ba való bejelentkezéshez és a sablon megnyitásához válassza az **üzembe helyezés az Azure**-ban lehetőséget. A sablon létrehozza a VNet, a webalkalmazást, a privát végpontot és a magánhálózati DNS-zónát.

   [Üzembe helyezés az Azure-ban](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Válassza ki vagy hozza létre az erőforráscsoportot.
3. Írja be a webalkalmazás nevét, App Service tervet, privát végpontot.
5. Olvassa el a feltételek és kikötések utasítást. Ha elfogadja, válassza az Elfogadom a fenti feltételeket és kikötéseket > a vásárlást. Az üzembe helyezés több percet is igénybe vehet.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége a privát végponttal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a magánhálózati végpontot és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

- A Azure App Service Web Apps további Azure Resource Manager sablonjai az [ARM-sablon mintái](../samples-resource-manager-templates.md)között találhatók.
