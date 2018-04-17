---
title: Hozzon létre egy virtuális gép több hálózati adapter - Azure Resource Manager sablon |} Microsoft Docs
description: Egy Azure Resource Manager-sablon használatával több hálózati adapterrel rendelkező virtuális gép létrehozása.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>Egy sablon használatával több hálózati adapterrel rendelkező virtuális gép létrehozása
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md).  Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti, amelyet a Microsoft a legtöbb új telepítéshez a [klasszikus üzemi modell](virtual-network-deploy-multinic-classic-ps.md) helyett javasol.
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Az alábbi lépéseket használja nevű erőforráscsoport *IaaSStory* a webkiszolgálók és az erőforráscsoport neve *IaaSStory-háttérrendszer* adatbázis-kiszolgálók.

## <a name="prerequisites"></a>Előfeltételek
Az adatbázis-kiszolgálók létrehozása előtt kell létrehoznia a *IaaSStory* erőforráscsoport ehhez a forgatókönyvhöz szükséges minden erőforráshoz. Ezek az erőforrások létrehozásához kövesse az alábbi lépéseket:

1. Navigáljon a [sablonlap](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. A sablon lap jobb oldalán lévő **szülő erőforráscsoport**, kattintson a **az Azure telepítéséhez**.
3. Ha szükséges, paraméterértékek módosításához, majd kövesse a telepítéséhez az erőforráscsoportot az Azure betekintő portálon.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tárfiókok neve egyedi. Ismétlődő tárfiókok neve nem lehet az Azure-ban.
> 

## <a name="understand-the-deployment-template"></a>A központi telepítési sablont ismertetése
Ez a dokumentáció a sablon telepítéséhez tudja, hogy hogyan kezeli. Az alábbi lépések nyújtanak a sablon megfelelő áttekintése:

1. Navigáljon a [sablonlap](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Kattintson a **azuredeploy.json** sablon fájl megnyitásához.
3. Figyelje meg a *osType* alábbi paraméter. Ez a paraméter használatával való használatra az adatbázis-kiszolgáló milyen Virtuálisgép-lemezkép kiválasztása, több operációs rendszer és a kapcsolódó beállításokat.

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. Görgessen le a változók listáját, és ellenőrizze a definícióját a **dbVMSetting** alább felsorolt változók. Kap található tömbelemek közül a **dbVMSettings** változó. Ha jártas software development terminológia, megtekintheti a **dbVMSettings** egy kivonattáblát vagy dictionary változó.

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. Tegyük fel, hogy úgy dönt, hogy a háttérben SQL futó Windows virtuális gépek telepítése. Értéke majd **osType** lenne *Windows*, és a **dbVMSetting** változó tartalmazza egyrészt az alábbi, az elem, amelynek első értékét jelöli az **dbVMSettings** változó.

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. Figyelje meg a **vmSize** értéke *Standard_DS3*. Csak egyes Virtuálisgép-méretek több hálózati adapter használatát teszik lehetővé. Ellenőrizheti, hogy mely Virtuálisgép-méretek támogatja több hálózati adapter ehhez beolvassa a [Windows Virtuálisgép-méretek](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) és [Linux Virtuálisgép-méretek](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) cikkeket.

7. Görgessen le a **erőforrások** , és figyelje meg az első elem. A tárfiók ismerteti. Ezt a tárfiókot az egyes adatbázisok virtuális gép által használt adatlemezek karbantartásához használható. Ebben az esetben az egyes adatbázisok VM van egy rendszeres storage-ban tárolt operációsrendszer-lemez, és két adatlemezek SSD-re (prémium) tárolja.

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. Az alább felsorolt, görgessen le a következő erőforrás. Ehhez az erőforráshoz jelöli az egyes adatbázis VM adatbázis eléréséhez használt hálózati adapter. Figyelje meg a a **másolási** függvényt ehhez az erőforráshoz. A sablon lehetővé teszi, hogy a telepíteni kívánt, a alapján sok virtuális gép a **dbCount** paraméter. Ezért létrehozásához szükséges az adatbázis eléréséhez, az egyes virtuális gépek egyik hálózati adapter olyan mértékű.

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. Az alább felsorolt, görgessen le a következő erőforrás. Ehhez az erőforráshoz a hálózati adapter egyes adatbázis VM-kezelésre szolgáló jelöli. Ebben az esetben kell ezek a hálózati adapterek közül az egyes adatbázisok virtuális gép. Figyelje meg a **hálózati biztonsági csoporthoz tartozik** elem csatolása, amely lehetővé teszi a hozzáférést az RDP/SSH ehhez a hálózati adapterhez csak egy NSG.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. Az alább felsorolt, görgessen le a következő erőforrás. Ez az erőforrás rendelkezésre állási készlet összes adatbázis virtuális gépek közös jelöli. Ily módon azt garantálja, hogy mindig lesz egy virtuális gép a karbantartás során a beállított.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. Görgessen le a következő erőforrás. Ehhez az erőforráshoz jelöli a virtuális gépek, adatbázis, az első néhány sor alább látható módon. Figyelje meg a a **másolási** újra működik, győződjön meg arról, hogy több virtuális gép alapján hozzák létre a **dbCount** paraméter. Is láthatja a **dependsOn** gyűjtemény. Felsorolja szükséges létrehozni a virtuális gép központi telepítése előtt, valamint a rendelkezésre állási csoport és a storage-fiók alatt két hálózati adaptert.

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. Görgessen le a virtuális gép erőforrást a **networkProfile** elem, az alább felsorolt. Figyelje meg, hogy vannak-e két hálózati adaptert, az egyes virtuális gépek hivatkozás alatt. Több hálózati adaptert a virtuális gépek létrehozásakor meg kell adni a **elsődleges** tulajdonság számára a hálózati adapterek egyikének *igaz*, és a többi pedig *hamis*.

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Az ARM-sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel

> [!IMPORTANT]
> Győződjön meg arról, hogy kövesse a [szükséges előfeltételek](#Pre-requisites) előtt az alábbi utasításokat a következő lépéseket.
> 

A nyilvános tárházban elérhető mintasablon a fent leírt forgatókönyv létrehozásához használt alapértelmezett értékeket tartalmazó paraméterfájlt használja. Ez a sablon üzembe helyezése kattintással végrehajtható telepítése telepítéséhez kövesse [Ez a hivatkozás](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), jobb oldalán **erőforráscsoport háttér (lásd a dokumentációt)** kattintson **az Azure telepítéséhez**, cserélje le az alapértelmezett paraméterértékek, ha szükséges, és kövesse az utasításokat a portálon.

Az alábbi ábra a tartalmát az új erőforráscsoportot, a telepítés utáni.

![Háttér-erőforráscsoport](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>A sablon üzembe helyezése a PowerShell használatával
A sablon PowerShell használatával történő üzembe helyezéséhez PowerShell telepítése és konfigurálása a lépések végrehajtásával a [PowerShell telepítése és konfigurálása](/powershell/azure/overview) a következő cikket, és fejezze be az alábbi lépéseket:

Futtassa a **`New-AzureRmResourceGroup`** parancsmag segítségével hozzon létre egy erőforráscsoportot a sablon használatával.

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

Várt kimenet:

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>A sablon üzembe helyezése az Azure parancssori felület használatával
Az alábbi lépéseket követve hozhatja létre a sablont az Azure parancssori felület használatával.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **`azure config mode`** parancs futtatásával váltson Resource Manager módra a lent látható módon.

    ```azurecli
    azure config mode arm
    ```

    A várható kimenete a következőképpen:

        info:    New mode is arm

3. Nyissa meg a [paraméterfájl](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), válassza ki annak tartalmát, és mentse a fájlt a számítógépen. Ennél a példánál a paramétereket tartalmazó fájlt a *parameters.json* fájlba mentettük.
4. Futtassa az **`azure group deployment create`** parancsmagot, hogy a fent letöltött és módosított sablonnal és paraméterfájlokkal üzembe helyezhesse az új VNetet. A kimenet után látható lista ismerteti a használt paramétereket.

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    Várt kimenet:
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

