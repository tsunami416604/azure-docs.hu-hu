---
title: Windows virtuális gép létrehozása az Azure-sablon alapján |} Microsoft Docs
description: A Resource Manager-sablon és a PowerShell segítségével egyszerűen hozzon létre egy új Windows virtuális Gépet.
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
ms.openlocfilehash: 1c911d7500b61218323dd736aa51f50980d702cc
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "31601838"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Windows rendszerű virtuális gép létrehozása egy Resource Manager sablon

Ez a cikk bemutatja, hogyan telepítheti az Azure Resource Manager-sablon PowerShell használatával. A sablon az Ön által létrehozott telepít egy új virtuális hálózatot egyetlen alhálózattal Windows Server rendszerű egyetlen virtuális gépet.

A virtuálisgép-erőforrás részletes ismertetését lásd: [Azure Resource Manager-sablonokban a virtuális gépek](template-description.md). A sablon összes erőforrásokra vonatkozó további információkért lásd: [Azure Resource Manager sablonokhoz](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Öt perc kell vennie a cikkben leírt lépéseket tennie.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.3-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Minden erőforrás kell telepíteni egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md).

1. Szerezzen be egy listát az összes elérhető helyről, ahol erőforrásokat lehet létrehozni.
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. Az erőforráscsoport létrehozása a kiválasztott helyen. Ez a példa bemutatja nevű erőforráscsoport **myResourceGroup** a a **USA nyugati régiója** helye:

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
    ```

## <a name="create-the-files"></a>A fájlok létrehozása

Ebben a lépésben létrehoz egy sablonfájlban, amely telepíti az erőforrások és paraméterfájl, amely megadja az paraméterértékeket a sablonhoz. Is létrehozhat egy engedélyezési fájlt, amely az Azure Resource Manager műveletek végrehajtásához használatos.

1. Hozzon létre egy fájlt *CreateVMTemplate.json* , és ez a JSON-kód felvétele:

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

2. Hozzon létre egy fájlt *Parameters.json* , és ez a JSON-kód felvétele:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

3. Hozzon létre egy új tárfiók és tároló:

    ```powershell
    $storageName = "st" + (Get-Random)
    New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" -AccountName $storageName -Location "West US" -SkuName "Standard_LRS" -Kind Storage
    $accountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name $storageName).Value[0]
    $context = New-AzureStorageContext -StorageAccountName $storageName -StorageAccountKey $accountKey 
    New-AzureStorageContainer -Name "templates" -Context $context -Permission Container
    ```

4. A fájlok feltöltése a tárfiókba:

    ```powershell
    Set-AzureStorageBlobContent -File "C:\templates\CreateVMTemplate.json" -Context $context -Container "templates"
    Set-AzureStorageBlobContent -File "C:\templates\Parameters.json" -Context $context -Container templates
    ```

    Változás - fájl elérési útját a fájlok tárolási helyét.

## <a name="create-the-resources"></a>Az erőforrások létrehozása

A sablon a paraméterek használatával telepítéséhez:

```powershell
$templatePath = "https://" + $storageName + ".blob.core.windows.net/templates/CreateVMTemplate.json"
$parametersPath = "https://" + $storageName + ".blob.core.windows.net/templates/Parameters.json"
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -Name "myDeployment" -TemplateUri $templatePath -TemplateParameterUri $parametersPath 
```

> [!NOTE]
> Sablonok és a paraméterek a helyi fájlokból is telepítheti. További tudnivalókért lásd: [Azure PowerShell használata az Azure Storage](../../storage/common/storage-powershell-guide-full.md).

## <a name="next-steps"></a>További lépések

- Ha problémák merültek fel a központi telepítést, előfordulhat, hogy vessen egy pillantást [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
- Megtudhatja, hogyan hozhatja létre és kezelheti a virtuális gép [létrehozása és kezelése Windows virtuális gépek az Azure PowerShell modulra](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

