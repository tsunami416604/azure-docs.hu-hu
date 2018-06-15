---
title: Az Azure-ral hálózati forgalom szűrésére hálózati és az alkalmazás biztonsági csoportok (előzetes verzió) |} Microsoft Docs
description: Útmutató a hálózati és az alkalmazás biztonsági csoportok (előzetes verzió) típusú hálózati forgalom korlátozása mindkét virtuális gépek létrehozásához.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2018
ms.locfileid: "30294281"
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Hálózati forgalom szűrésére, hálózati és az alkalmazás biztonsági csoportok (előzetes verzió)

Ebben az oktatóanyagban elsajátíthatja hálózati biztonsági csoportok szűrése hálózati forgalmat a virtuális gépek biztonsági csoportokkal csoportok létrehozása. Hálózati biztonsági csoportok és biztonsági csoportok kapcsolatos további információkért lásd: [biztonsági áttekintése](security-overview.md). 

A következő szakaszok tartalmazzák a lépéseket, amelyek hálózati biztonsági csoportok használata az Azure parancssori felület létrehozása ([Azure CLI](#azure-cli)) és [Azure PowerShell](#powershell). Az eredmény megegyezik, függetlenül attól, hogy a hálózati biztonsági csoportok létrehozására használhatja. Egy eszköz hivatkozásra kattintva az oktatóanyag szakaszt. 

Ez a cikk lépéseit hozza létre a hálózati biztonsági csoportokat a Resource Manager telepítési modell, amely a hálózati biztonsági csoportok létrehozásakor használjon telepítési modell használatával. Ha létrehoz egy hálózati biztonsági csoportot (klasszikus) van szüksége, tekintse meg [hozzon létre egy hálózati biztonsági csoportot (klasszikus)](virtual-networks-create-nsg-classic-ps.md). Ha nem ismeri az Azure üzembe helyezési modellel, lásd: [megértéséhez Azure üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> Ez az oktatóanyag használja a hálózati biztonsági csoport funkciókat, amelyek jelenleg előzetes kiadásban. Az előzetes funkciók nem rendelkeznek, az azonos rendelkezésre állásának és megbízhatóságának funkciókat, általában kiadási. Ha azt szeretné, valósítja meg a hálózati biztonsági csoportok csak olyan szolgáltatását használja, általában a kiadási kapcsolatos tudnivalókat lásd: [hálózati biztonsági csoport létrehozása](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>Azure CLI

Az Azure parancssori felület parancsait megegyeznek, hogy a parancsok a Windows, Linux vagy macOS hajtható végre. Vannak azonban parancsfájlok különbségei operációs rendszer ismertetése. A parancsfájlok a következő lépések a rendszerhéjakba hajtható végre. 

1. [Telepítse és konfigurálja az Azure parancssori felület](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Győződjön meg arról, 2.0.18 verzióját használja vagy újabb, írja be az Azure parancssori felület a `az --version` parancsot. Ha nem, telepítse a legújabb verziót.
3. Jelentkezzen be Azure-bA a `az login` parancsot.
4. Az előzetes rögzítése a következő parancsok beírásával:
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. Győződjön meg arról, hogy be vannak jegyezve a az előzetes a következő parancs beírásával:

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > Regisztrációs órát is igénybe vehet egy befejezéséhez. Ne folytassa a hátralévő lépéseket, amíg *regisztrált* jelenik meg **állapot** az előző parancs által visszaadott kimenet. Ha a folytatás előtt van regisztrálva a fennmaradó lépéseit sikertelen.

6. Futtassa a következő bash erőforráscsoport létrehozása:

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Hozzon létre három alkalmazás biztonsági csoportokat, egy, az egyes kiszolgáló:

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Hálózati biztonsági csoport létrehozása:

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Hozza létre a biztonsági szabályok az NSG-t a biztonsági csoportok beállítása célhelyként belül:
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Virtuális hálózat létrehozása: 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Társítsa a hálózati biztonsági csoportot a virtuális hálózat az alhálózatnak:

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Hozzon létre egy, az egyes kiszolgáló három hálózati adapterek: 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    A hálózati illesztő a hálózati illesztő tagja alkalmazás biztonsági csoport alapján csak a megfelelő biztonsági szabály 9. lépésében létrehozott vonatkozik. Ha például csak a *AppRule* szabály érvényben *myNic2*, mert a hálózati illesztő tagja a *AppServers* alkalmazás biztonsági csoport és a szabály Megadja a *AppServers* alkalmazás biztonsági csoport az helyeként. A *WebRule* és *DatabaseRule* szabályok nem érvényesek *myNic2*, mert a hálózati illesztő nem tagja a *webkiszolgálók*és *DatabaseServers* biztonsági csoportok. Mindkét a *WebRule* és *AppRule* szabályok érvényben *myNic1* , mert a *myNic1* hálózati illesztő tagja mind a *webkiszolgálók* és *AppServers* biztonsági csoportok és a szabályok adja meg a *webkiszolgálók* és *AppServers* biztonsági csoportok a célként. 

13. Hozzon létre egy virtuális gépet az egyes kiszolgáló, a megfelelő hálózati illesztő csatolása minden egyes virtuális géphez. Ebben a példában a Windows virtuális gépeket hoz létre, de bármikor módosíthatja *win2016datacenter* való *UbuntuLTS* helyette Linux virtuális gépek létrehozásához.

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Nem kötelező**: törli az Ön által létrehozott ebben az oktatóanyagban szereplő lépések végrehajtásával erőforrást [törli az erőforrást](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Telepítse és konfigurálja [PowerShell](/powershell/azure/install-azurerm-ps).
2. Győződjön meg arról, hogy 4.4.0-verziót, vagy magasabb AzureRm modul telepítve. A jelenleg telepített verzió beírásával ellenőrizheti a `Get-Module -ListAvailable AzureRM` parancsot. Ha telepíteni vagy frissíteni kell, telepítse a legújabb verziót a AzureRM modul a [PowerShell-galériában](https://www.powershellgallery.com/packages/AzureRM).
3. A PowerShell-munkamenetben jelentkezzen be Azure-bA a [Azure-fiók](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) használatával a `login-azurermaccount` parancsot.
4. Az előzetes rögzítése a következő parancsok beírásával:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Győződjön meg arról, hogy be vannak jegyezve a az előzetes a következő parancs beírásával:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > Regisztrációs órát is igénybe vehet egy befejezéséhez. Ne folytassa a hátralévő lépéseket, amíg *regisztrált* jelenik meg **RegistrationState** az előző parancs által visszaadott kimenet. Ha a folytatás előtt van regisztrálva a fennmaradó lépéseit sikertelen.
        
6. Hozzon létre egy erőforráscsoportot:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Hozzon létre három alkalmazás biztonsági csoportokat, egy, az egyes kiszolgáló:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Minden biztonsági szabályokat létrehozni.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. Hálózati biztonsági csoport létrehozása:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Alhálózat-konfiguráció létrehozása, és társítsa azt a hálózati biztonsági csoport:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Virtuális hálózat létrehozása: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Hozzon létre három hálózati felületek, egy, az egyes kiszolgáló. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    A hálózati illesztő a hálózati illesztő tagja alkalmazás biztonsági csoport alapján csak a megfelelő biztonsági szabály 8. lépésben létrehozott vonatkozik. Ha például csak a *AppRule* szabály érvényben *myNic2*, mert a hálózati illesztő tagja a *AppServers* alkalmazás biztonsági csoport és a szabály Megadja a *AppServers* alkalmazás biztonsági csoport az helyeként. A *WebRule* és *DatabaseRule* szabályok nem érvényesek *myNic2*, mert a hálózati illesztő nem tagja a *webkiszolgálók*és *DatabaseServers* biztonsági csoportok. Mindkét a *WebRule* és *AppRule* szabályok érvényben *myNic1* , mert a *myNic1* hálózati illesztő tagja mind a *webkiszolgálók* és *AppServers* biztonsági csoportok és a szabályok adja meg a *webkiszolgálók* és *AppServers* biztonsági csoportok a célként. 

13. Hozzon létre egy virtuális gépet az egyes kiszolgáló, a megfelelő hálózati illesztő csatolása minden egyes virtuális géphez. Ebben a példában a Windows virtuális gépeket hoz létre, de a parancsfájl végrehajtása előtt módosíthatja *-Windows* való *- Linux*, *MicrosoftWindowsServer* való *Kanonikus*, *WindowsServer* való *UbuntuServer* és *2016-Datacenter* való *14.04.2-LTS*helyette Linux virtuális gépek létrehozásához.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Nem kötelező**: törli az Ön által létrehozott ebben az oktatóanyagban szereplő lépések végrehajtásával erőforrást [törli az erőforrást](#delete-cli).

## <a name="remove-a-nic-from-an-asg"></a>Távolítsa el a hálózati adapter egy ASG
Ha eltávolít egy adott hálózati csatoló alkalmazás biztonsági csoportból, egyetlen adja meg az alkalmazáscsoport biztonsági szabály alkalmazása a hálózati adapter eltávolítása.

### <a name="azure-cli"></a>Azure CLI

Az eltávolítandó *myNic3* minden biztonsági csoportok, adja meg a következő parancsot:

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

Az eltávolítandó *myNic3* minden biztonsági csoportok, adja meg a következő parancsokat:

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>Erőforrások törlése

Ez az oktatóanyag befejezése után előfordulhat, hogy törölni kívánja az erőforrásokat, amelyek hozott létre, így használati költségek. Erőforráscsoport törlésével, az erőforráscsoport összes erőforrást is törli.

### <a name="delete-portal"></a>Azure-portálon

1. A portál keresési mezőbe, írja be a **myResourceGroup**. A keresési eredmények között kattintson **myResourceGroup**.
2. Az a **myResourceGroup** panelen kattintson a **törlése** ikonra.
3. A törlés megerősítéséhez a a **típus az ERŐFORRÁSCSOPORT neve** adja meg a **myResourceGroup**, és kattintson a **törlése**.

### <a name="delete-cli"></a>Azure CLI

Adja meg egy parancssori munkamenetet a következő parancsot:

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

A PowerShell-munkamenetben írja be a következő parancsot:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

