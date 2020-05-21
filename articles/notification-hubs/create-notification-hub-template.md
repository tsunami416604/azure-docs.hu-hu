---
title: Azure Notification hub létrehozása Azure Resource Manager sablon használatával
description: Megtudhatja, hogyan hozhat létre Azure Notification hub-t egy Azure Resource Manager sablon használatával.
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 05/15/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: d8dad7b05b87f36a0bab2044495de31085a192fc
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666233"
---
# <a name="quickstart-create-a-notification-hub-using-an-azure-resource-manager-template"></a>Gyors útmutató: értesítési központ létrehozása Azure Resource Manager sablon használatával

Az Azure Notification Hubs egy könnyen használható és méretezhető leküldéses motort biztosít, amely lehetővé teszi, hogy bármilyen háttérből (Felhőbeli vagy helyszíni) bármilyen platformra (iOS, Android, Windows, Kindle stb.) küldjön értesítéseket. A szolgáltatással kapcsolatos további információkért lásd: [Mi az az Azure Notification Hubs](notification-hubs-push-notification-overview.md).

Ez a rövid útmutató egy Azure Resource Manager sablonnal hozza létre az Azure Notification Hubs névteret, valamint egy "MyHub" nevű értesítési központot a névtérben.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="create-a-notification-hubs-namespace-and-hub"></a>Notification Hubs névtér és hub létrehozása

<!-- The second H2 must start with "Create a". For example,  'Create a Key Vault', 'Create a virtual machine', etc. -->

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-notification-hub/)származik.

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json" range="1-45" highlight="22-40":::

* [Microsoft. NotificationHubs/névterek](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces)
* [Microsoft. NotificationHubs/névterek/notificationHubs](/azure/templates/microsoft.notificationhubs/2017-04-01/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon egy Notification Hubs névtér nevét adja meg paraméterként. A sablon Ezután létrehoz egy névteret a névvel és egy **MyHub** nevű értesítési központtal az adott névtéren belül.

[![Üzembe helyezés az Azure-ban](./media/create-notification-hub-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Használhatja a Azure Portal az üzembe helyezett erőforrások vizsgálatára, vagy az Azure CLI vagy a Azure PowerShell parancsfájl használatával listázhatja az üzembe helyezett Notification Hubs névteret és hubot:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[parancssori felület](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Következő lépések

A sablonok létrehozásának folyamatát ismertető, lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](/azure/azure-resource-manager/templates/template-tutorial-create-first-template.md)
