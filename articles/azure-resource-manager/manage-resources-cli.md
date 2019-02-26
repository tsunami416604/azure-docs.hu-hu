---
title: Azure-erőforrások kezelése az Azure CLI-vel |} A Microsoft Docs
description: Azure CLI és az Azure Resource Manager használatával kezelheti az erőforrásokat.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c7a863c017bbd7440704f024ee362c3a0d252891
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825114"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Azure-erőforrások kezelése az Azure CLI-vel

Ismerje meg, hogyan használható az Azure CLI-t [Azure Resource Manager](resource-group-overview.md) az Azure-erőforrások kezeléséhez. Erőforráscsoportok kezeléséhhez lásd: [kezelése Azure-erőforráscsoportok az Azure CLI-vel](./manage-resource-groups-cli.md).

Egyéb erőforrások kezelésével kapcsolatos cikkek:

- [Azure-erőforrások kezelése az Azure portal használatával](./manage-resources-portal.md)
- [Azure-erőforrások kezelése az Azure CLI-vel](./manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások üzembe helyezése egy meglévő erőforráscsoportot

Azure-erőforrások üzembe közvetlenül az Azure PowerShell használatával, vagy az Azure-erőforrások létrehozása Resource Manager-sablon üzembe helyezése.

### <a name="deploy-a-resource"></a>Erőforrások üzembe helyezése

A következő szkript létrehoz egy tárfiókot.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Sablon üzembe helyezése

A következő szkriptet hoz létre egy storage-fiók létrehozása gyorsindítási sablon üzembe helyezése. További információkért lásd: [a rövid útmutató: Az Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Egy erőforráscsoport és erőforrások üzembe helyezése

Hozzon létre egy erőforráscsoportot, és helyezheti üzembe az erőforrásokat a csoporthoz. További információkért lásd: [hozzon létre és helyezhet üzembe erőforrásokat](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Több előfizetések vagy erőforráscsoportok erőforrások üzembe helyezése

Általában végzi az üzembe helyezést összes erőforrást a sablonhoz, amelyekkel egyetlen erőforráscsoportra. Vannak azonban forgatókönyvek, ahol szeretné erőforráscsoport telepítsen együtt, de különböző erőforráscsoport vagy előfizetés helyezze el őket. További információkért lásd: [több előfizetések vagy erőforráscsoportok üzembe helyezése az Azure-erőforrások](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Erőforrások törlése

Az alábbi parancsfájl bemutatja, hogyan törölheti a tárfiókokat.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Milyen az Azure Resource Manager orders erőforrások törlését kapcsolatos további információkért lásd: [törölni az Azure Resource Manager-erőforrást csoport](./resource-group-delete.md).

## <a name="move-resources"></a>Erőforrások áthelyezése

Az alábbi parancsfájl bemutatja, hogyan távolítsa el a storage-fiók egyik erőforráscsoportból egy másik erőforráscsoportba.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

## <a name="lock-resources"></a>Erőforrások zárolása

Zárolás megakadályozza a véletlen törlését vagy módosítását a kritikus fontosságú erőforrások, például az Azure-előfizetésre, erőforráscsoportra vagy erőforrásra a szervezet más felhasználói. 

A következő parancsfájl egy storage-fiók zárolja, így a fiók nem törölhető.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

A következő parancsfájl egy tárfiókhoz tartozó összes zárolásainak beolvasása:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

A következő parancsfájl egy storage-fiók zárolása törli:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Erőforrások címkézése

Az erőforráscsoport és erőforrások rendszerezéséhez logikailag címkézési segít. További információ: [az Azure-erőforrások rendszerezése címkék használatával](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Az erőforrásokhoz való hozzáférés kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. További információkért lásd: [RBAC és az Azure CLI-hozzáférés kezelése](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>További lépések

- Azure Resource Manager kapcsolatban lásd: [Azure Resource Manager áttekintése](./resource-group-overview.md).
- A Resource Manager-sablon szintaxisáról kapcsolatban lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](./resource-group-authoring-templates.md).
- Ismerje meg, hogyan fejleszthet sablonokat, tekintse meg a [lépésről lépésre haladó oktatóanyagok](/azure/azure-resource-manager/).
- Az Azure Resource Manager-sablon sémák megtekintése: [sablonreferenciája](/azure/templates/).