---
title: Hálózati biztonsági csoport – Azure Resource Manager-sablon létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és telepíthet a hálózati biztonsági csoportok Azure Resource Manager-sablonnal.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: f3e7385d-717c-44ff-be20-f9aa450aa99b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fc210ac5e28efe10be27f8b5a752788d3d5a5a3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-network-security-groups-using-an-azure-resource-manager-template"></a>Hálózati biztonsági csoportok használata az Azure Resource Manager-sablon létrehozása

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [NSG-k létrehozása a klasszikus üzembe helyezési modellel](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## <a name="nsg-resources-in-a-template-file"></a>Egy sablonfájlban NSG-erőforrások
Tekintheti meg és töltse le a [mintasablon](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/NSGs.json).

A következő szakasz az előtér-NSG-t, a forgatókönyv alapján definíciója jeleníti meg.

```json
"apiVersion": "2017-03-01",
"type": "Microsoft.Network/networkSecurityGroups",
"name": "[parameters('frontEndNSGName')]",
"location": "[resourceGroup().location]",
"tags": {
  "displayName": "NSG - Front End"
},
"properties": {
  "securityRules": [
    {
      "name": "rdp-rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 100,
        "direction": "Inbound"
      }
    },
    {
      "name": "web-rule",
      "properties": {
        "description": "Allow WEB",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 101,
        "direction": "Inbound"
      }
    }
  ]
}
```
Rendelje hozzá a az NSG az előtér-alhálózathoz, akkor módosítsa az alhálózati definíció a sablonban, majd az NSG-t a referencia-azonosító használata.

```json
"subnets": [
  {
    "name": "[parameters('frontEndSubnetName')]",
    "properties": {
      "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
      "networkSecurityGroup": {
      "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
      }
    }
  }, 
```

Figyelje meg, ugyanazt a háttér-NSG-t és a sablon a háttér-alhálózat számára történik.

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Az ARM-sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel
A nyilvános tárházban elérhető mintasablon a fent leírt forgatókönyv létrehozásához használt alapértelmezett értékeket tartalmazó paraméterfájlt használja. Ha a sablon üzembe helyezését kattintással végrehajtható üzembe helyezéssel szeretné elvégezni, kövesse [ezt a hivatkozást](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), kattintson az **Üzembe helyezés az Azure-on** lehetőségre, cserélje ki az alapértelmezett paraméterértékeket, ha szükséges, majd kövesse a portálon megjelenő utasításokat.

## <a name="deploy-the-arm-template-by-using-powershell"></a>Az ARM-sablon üzembe helyezése a PowerShell használatával
A letöltött ARM-sablon PowerShell használatával történő üzembe helyezéséhez kövesse az alábbi lépéseket.

1. Ha még sosem használta az Azure PowerShell, kövesse az utasításokat a [hogyan telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) való telepítéséhez és konfigurálásához.
2. Futtassa a **`New-AzureRmResourceGroup`** parancsmag segítségével hozzon létre egy erőforráscsoportot a sablon használatával.

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location uswest `
    -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' `
    -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'
    ```

    Várt kimenet:

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  
   
        Resources         :
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            sqlAvSet            Microsoft.Compute/availabilitySets       westus  
                            webAvSet            Microsoft.Compute/availabilitySets       westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            Web1                Microsoft.Compute/virtualMachines        westus  
                            Web2                Microsoft.Compute/virtualMachines        westus  
                            TestNICSQL1         Microsoft.Network/networkInterfaces      westus  
                            TestNICSQL2         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb1         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb2         Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            TestPIPSQL1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPSQL2         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb2         Microsoft.Network/publicIPAddresses      westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
   
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Az ARM-sablon üzembe helyezése az Azure CLI-vel
Az ARM-sablon az Azure parancssori felület használatával történő telepítéséhez kövesse az alábbi lépéseket.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **`azure config mode`** parancs futtatásával váltson Resource Manager módra a lent látható módon.

    ```azurecli
    azure config mode arm
    ```

    A következő a parancs várható kimenete:

        info:    New mode is arm

3. Futtassa az **`azure group deployment create`** parancsmagot, hogy a fent letöltött és módosított sablonnal és paraméterfájlokkal üzembe helyezhesse az új VNetet. A kimenet után látható lista ismerteti a használt paramétereket.

    ```azurecli
    azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' -e 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'
    ```

    Várt kimenet:
   
        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Creating resource group TestRG
        info:    Created resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK
   
   * **-n (vagy --name)**. Létrejön az erőforráscsoport neve.
   * **-l (vagy --location)**. Azure-régió, ahol az erőforráscsoport létrejön.
   * **-f (vagy --template-file)**. Az ARM-sablonfájl elérési útja.
   * **-e (vagy --parameters-file)**. Az ARM-paraméterfájl elérési útja.

