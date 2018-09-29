---
title: Windows virtuális gép létrehozása az Azure-ban sablon alapján |} A Microsoft Docs
description: A Resource Manager-sablon és a PowerShell használatával egyszerűen létrehozhat egy új Windows virtuális Gépet.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: df1cada5e730189546f65d9799926807390db083
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432256"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Windows virtuális gép létrehozása Resource Manager-sablonból

Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure Resource Manager-sablon PowerShell használatával. A sablon által létrehozott telepít egy új virtuális hálózatot egyetlen alhálózattal rendelkező Windows Server rendszerű egyetlen virtuális gépet.

A virtuális gép típusú erőforrást részletes ismertetését lásd: [egy Azure Resource Manager-sablonban virtuális gépek](template-description.md). A sablon összes erőforrásokra vonatkozó további információkért lásd: [Azure Resource Manager sablonokhoz](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Az ebben a cikkben található lépések elvégzése nagyjából öt perc alatt kell vennie.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.3-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az összes erőforrás telepíteni kell egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md).

1. Szerezzen be egy listát az összes elérhető helyről, ahol erőforrásokat lehet létrehozni.
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. Az erőforráscsoport létrehozása a kiválasztott helyen. Ez a példa bemutatja egy nevű erőforráscsoportot **myResourceGroup** a a **USA nyugati RÉGIÓJA** helye:

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
    ```

## <a name="create-the-files"></a>A fájlok létrehozása

Ebben a lépésben hozzon létre egy sablon fájlt, amely üzembe helyezi az erőforrásokat és a egy paramétereket tartalmazó fájlt, amely a sablonhoz paraméter értékeket. Emellett létrehozhat egy Azure Resource Manager-műveletek végrehajtásához használt engedélyezési fájl.

1. Hozzon létre egy fájlt *CreateVMTemplate.json* és a JSON-kód hozzáadása:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]" 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

2. Hozzon létre egy fájlt *Parameters.json* és a JSON-kód hozzáadása:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
      "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

3. Hozzon létre egy új storage-fiókot és egy tárolót:

    ```powershell
    $storageName = "st" + (Get-Random)
    New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" -AccountName $storageName -Location "West US" -SkuName "Standard_LRS" -Kind Storage
    $accountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name $storageName).Value[0]
    $context = New-AzureStorageContext -StorageAccountName $storageName -StorageAccountKey $accountKey 
    New-AzureStorageContainer -Name "templates" -Context $context -Permission Container
    ```

4. A fájlok feltöltése a storage-fiók:

    ```powershell
    Set-AzureStorageBlobContent -File "C:\templates\CreateVMTemplate.json" -Context $context -Container "templates"
    Set-AzureStorageBlobContent -File "C:\templates\Parameters.json" -Context $context -Container templates
    ```

    Változás - fájl elérési útjára, a fájlok tárolásának helyét.

## <a name="create-the-resources"></a>Az erőforrások létrehozása

Helyezze üzembe a sablont a paraméterek használatával:

```powershell
$templatePath = "https://" + $storageName + ".blob.core.windows.net/templates/CreateVMTemplate.json"
$parametersPath = "https://" + $storageName + ".blob.core.windows.net/templates/Parameters.json"
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -Name "myDeployment" -TemplateUri $templatePath -TemplateParameterUri $parametersPath 
```

> [!NOTE]
> Sablonok és a paraméterek a helyi fájlokból is telepítheti. További tudnivalókért lásd: [az Azure PowerShell az Azure Storage](../../storage/common/storage-powershell-guide-full.md).

## <a name="next-steps"></a>További lépések

- Ha a központi telepítési problémái vannak, előfordulhat, hogy vessen egy pillantást [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Ismerje meg, hogyan hozhat létre és kezelhet egy virtuális géphez a [létrehozása és a Windows virtuális gépek kezelése az Azure PowerShell modullal](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

