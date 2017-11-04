---
title: "Hozzon létre egy virtuális Gépet egy statikus nyilvános IP-cím - Azure Resource Manager-sablon |} Microsoft Docs"
description: "Útmutató: virtuális gép létrehozása az Azure Resource Manager-sablonnal statikus nyilvános IP-cím."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d551085a-c7ed-4ec6-b4c3-e9e1cebb774c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f503aa60fdd9b7cf66ef482a1041e34c88e5c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-an-azure-resource-manager-template"></a>Virtuális gép létrehozása az Azure Resource Manager-sablonnal statikus nyilvános IP-cím

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [Sablon](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a Microsoft azt javasolja, hogy a klasszikus üzembe helyezési modellel helyett az új telepítések esetén a Resource Manager telepítési modell használatát bemutatja.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-address-resources-in-a-template-file"></a>Nyilvános IP-cím erőforrás egy sablonfájlban
Tekintheti meg és töltse le a [mintasablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

A következő szakasz meghatározása a nyilvános IP-erőforrás, a fenti forgatókönyv alapján jeleníti meg:

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('webVMSetting').pipName]",
  "location": "[variables('location')]",
  "properties": {
    "publicIPAllocationMethod": "Static"
  },
  "tags": {
    "displayName": "PublicIPAddress - Web"
  }
},
```

Figyelje meg a **publicIPAllocationMethod** tulajdonság, melynek értéke *statikus*. Ez a tulajdonság lehet *dinamikus* (alapértelmezett érték) vagy *statikus*. Értékre állítaná statikus biztosítja, hogy az a nyilvános IP-cím lesz soha ne módosuljanak.

A következő szakasz jeleníti meg egy hálózati adapter társítását az nyilvános IP-cím:

```json
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[variables('webVMSetting').nicName]",
    "location": "[variables('location')]",
    "tags": {
    "displayName": "NetworkInterface - Web"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
      "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
    ],
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
          "privateIPAllocationMethod": "Static",
          "privateIPAddress": "[variables('webVMSetting').ipAddress]",
          "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
          },
          "subnet": {
            "id": "[variables('frontEndSubnetRef')]"
          }
        }
      }
    ]
  }
},
```

Figyelje meg a **publicIPAddress** mutató tulajdonság a **azonosító** nevű erőforrás **variables('webVMSetting').pipName**. Ez a fenti nyilvános IP-erőforrás nevét.

Végezetül, a fenti hálózati adapter szerepel-e a **networkProfile** tulajdonság a virtuális gép létrehozása folyamatban.

```json
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }
```

## <a name="deploy-the-template-by-using-click-to-deploy"></a>A sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel

A nyilvános tárházban elérhető mintasablon a fent leírt forgatókönyv létrehozásához használt alapértelmezett értékeket tartalmazó paraméterfájlt használja. Ez a sablon üzembe helyezése kattintással végrehajtható telepítése telepítéséhez kattintson **az Azure telepítéséhez** Readme.md fájljában a [statikus PIP rendelkező virtuális gépet](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) sablont. Cserélje le az alapértelmezett paraméterértékek, ha szükséges, és adja meg az üres paraméterek értékeit.  Kövesse az utasításokat a portálon hozzon létre egy virtuális gépet egy statikus nyilvános IP-cím.

## <a name="deploy-the-template-by-using-powershell"></a>A sablon üzembe helyezése a PowerShell használatával

A letöltött sablon PowerShell használatával történő üzembe helyezéséhez kövesse az alábbi lépéseket.

1. Ha még sosem használta az Azure PowerShell, hajtsa végre a a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) cikk.
2. A PowerShell-konzolban, futtassa a `New-AzureRmResourceGroup` parancsmag segítségével hozzon létre egy új erőforráscsoportot, ha szükséges. Ha már létrehozott egy erőforráscsoport, folytassa a 3.

    ```powershell
    New-AzureRmResourceGroup -Name PIPTEST -Location westus
    ```

    Várt kimenet:
   
        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. A PowerShell-konzolban, futtassa a `New-AzureRmResourceGroupDeployment` parancsmagot, hogy a sablon telepítéséhez.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
        -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
        -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json
    ```

    Várt kimenet:
   
        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0
   
        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         
   
        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>A sablon üzembe helyezése az Azure parancssori felület használatával
A sablon telepítéséhez az Azure parancssori felület használatával, kövesse az alábbi lépéseket:

1. Ha még sosem használta az Azure parancssori felület, kövesse a lépéseket a [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md) cikk telepítéséhez és konfigurálásához.
2. Futtassa a `azure config mode` parancs futtatásával váltson Resource Manager módra a lent látható módon.

    ```azurecli
    azure config mode arm
    ```

    A fenti parancs várható kimenete:

        info:    New mode is arm

3. Nyissa meg a [paraméterfájl](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), válassza ki annak tartalmát, és mentse a fájlt a számítógépen. Az ebben a példában a paraméterek nevű fájlba menti *parameters.json*. Módosítsa a paraméterértékeket a fájlban, ha szükséges, de legalább javasoljuk, hogy egy egyedi, összetett jelszót módosítja a adminPassword paraméter értéke.
4. Futtassa a `azure group deployment create` cmd telepíteni az új VNet sablonnal és paraméterfájlokkal meg a fent letöltött és módosított fájlok. Az alábbi parancsban cserélje le <path> az elérési úttal rendelkező a fájlt mentette. 

    ```azurecli
    azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json
    ```

    Várt kimenet (használt paraméterértékeket ismerteti):

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/[Subscription ID]/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

