---
title: "Erőforrások üzembe helyezése az Azure-ban | Microsoft Docs"
description: "Az Azure PowerShell vagy Azure CLI használatával helyezhet üzembe erőforrásokat az Azure-ban. Az erőforrások egy Resource Manager-sablonban vannak meghatározva."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 41d9476d4ce6b7afc2ba9c757e96db7e8e72d5ae
ms.openlocfilehash: a77354d0719240e5916b5ec945dad75e534802d3


---
# <a name="deploy-resources-to-azure"></a>Erőforrások üzembe helyezése az Azure-ban

Ez a témakör bemutatja, hogyan helyezhet üzembe erőforrásokat az Azure-előfizetésében. Az Azure PowerShell vagy az Azure CLI használatával helyezhet üzembe a megoldása infrastruktúráját meghatározó Resource Manager-sablont.

A Resource Manager alapelveinek áttekintése: [Az Azure Resource Manager áttekintése](resource-group-overview.md).

## <a name="steps-for-deployment"></a>Az üzembe helyezés lépései

Ez a témakör feltételezi, hogy a témakör [példa tárolósablonját](#example-storage-template) helyezi üzembe. Használhat más sablont, de az átadott paraméterek különböznek a témakörben láthatóktól.

A sablon létrehozása után a sablon üzembe helyezésének általános lépései a következők:

1. Bejelentkezés a fiókjába
2. A használni kívánt előfizetés kiválasztása (csak akkor szükséges, ha több előfizetése van, és nem az alapértelmezett előfizetést szeretné használni)
3. Hozzon létre egy erőforráscsoportot
4. A sablon üzembe helyezése
5. Az üzemelő példány állapotának ellenőrzése

A következő szakaszok bemutatják, hogyan végezheti el ezeket a lépéseket a [PowerShell](#powershell) vagy az [Azure CLI](#azure-cli) használatával.

## <a name="powershell"></a>PowerShell

1. Az Azure PowerShell telepítése: [Az Azure PowerShell-parancsmagok használatának első lépései](/powershell/azureps-cmdlets-docs).

2. Az üzemelő példány használatának gyors megkezdése érdekében használja a következő parancsmagokat:

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  A `Set-AzureRmContext` parancsmagra csak akkor van szükség, ha nem az alapértelmezett előfizetést szeretné használni. Az összes előfizetés és a hozzájuk tartozó azonosítóik megtekintéséhez használja a következőt:

  ```powershell
  Get-AzureRmSubscription
  ```

3. Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, a következőhöz hasonló üzenet jelenik meg:

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. Ha ellenőrizni szeretné, hogy az erőforráscsoportja és a tárfiókja üzembe van-e helyezve az előfizetésben, használja a következőt:

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. Sablon üzembe helyezésekor a sablon paramétereit megadhatja PowerShell-paraméterekként. A korábbi példában nem szerepeltek sablonparaméterek, így a rendszer a sablon alapértelmezett értékeit használta. Másik tárfiók üzembe helyezéséhez, valamint a tárolónév előtagjához és a tárfiók SKU-jához tartozó paraméterértékek megadásához használja a következőt:

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  Most két tárfiókja van az erőforráscsoportban. 

## <a name="azure-cli"></a>Azure CLI

1. Az Azure CLI telepítése: [Az Azure CLI 2.0 telepítése](/cli/azure/install-az-cli2).

2. Az üzembe helyezés gyors megkezdéséhez használja a következő parancsokat:

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  A `az account set` parancsra csak akkor van szükség, ha nem az alapértelmezett előfizetést szeretné használni. Az összes előfizetés és a hozzájuk tartozó azonosítóik megtekintéséhez használja a következőt:

  ```azurecli
  az account list
  ```

3. Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, a következőhöz hasonló üzenet jelenik meg:

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. Ha ellenőrizni szeretné, hogy az erőforráscsoportja és a tárfiókja üzembe van-e helyezve az előfizetésben, használja a következőt:

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. Sablon üzembe helyezésekor a sablon paramétereit megadhatja PowerShell-paraméterekként. A korábbi példában nem szerepeltek sablonparaméterek, így a rendszer a sablon alapértelmezett értékeit használta. Másik tárfiók üzembe helyezéséhez, valamint a tárolónév előtagjához és a tárfiók SKU-jához tartozó paraméterértékek megadásához használja a következőt:

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  Most két tárfiókja van az erőforráscsoportban. 

## <a name="example-storage-template"></a>Példa tárolósablon

A következő példasablonnal üzembe helyezhet egy tárfiókot az előfizetésben:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Következő lépések

* Részletes információ a sablonok PowerShell használatával végzett üzembe helyezéséről: [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](/azure/azure-resource-manager/resource-group-template-deploy).
* Részletes információ a sablonok Azure CLI-vel végzett üzembe helyezéséről: [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](/azure/azure-resource-manager/resource-group-template-deploy-cli).






<!--HONumber=Feb17_HO3-->


