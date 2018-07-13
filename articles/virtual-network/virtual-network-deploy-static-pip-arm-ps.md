---
title: Virtuális gép létrehozása egy statikus nyilvános IP-cím – Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy virtuális Gépet PowerShell-lel statikus nyilvános IP-cím.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68656db0b76a29e7ab36fd6fa9ad4647712233ee
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696583"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>Virtuális gép létrehozása PowerShell használatával statikus nyilvános IP-cím

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk ismerteti a Microsoft azt javasolja, a klasszikus üzemi modell helyett új telepítések esetén a Resource Manager üzemi modell használatával.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>A parancsfájl futtatásához
Letöltheti a teljes PowerShell-parancsfájlt használja [Itt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Módosítsa a parancsfájl futtatásához az alábbi lépésekkel.

A központi telepítéshez használni kívánt értékek alapján az alábbi változók értékeinek módosítása A következő értékek leképezése a forgatókönyvet, a cikk ezt használja:

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="create-the-necessary-resources-for-your-vm"></a>Hozzon létre a szükséges erőforrásokat a virtuális gép
Virtuális gép létrehozása előtt szüksége egy erőforráscsoportot, virtuális hálózat, nyilvános IP-cím és hálózati Adaptert a virtuális gép által használandó.

1. Hozzon létre egy új erőforráscsoportot.

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. A virtuális hálózat és alhálózat létrehozása.

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. A nyilvános IP-erőforrás létrehozásához. 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. Hozzon létre a hálózati adapter (NIC) a nyilvános IP-címmel a fent létrehozott alhálózat a virtuális géphez. Figyelje meg, hogy a virtuális hálózat beolvasása az Azure-ból az első parancsmag, ez az szükséges, mivel egy `Set-AzureRmVirtualNetwork` végre lett hajtva a meglévő virtuális hálózat módosítása.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. Hozzon létre egy tárfiókot, a virtuális gép operációsrendszer-meghajtó üzemeltetéséhez.

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>Virtuális gép létrehozása
Most, hogy az összes szükséges erőforrást érvényesítve van, létrehozhat egy új virtuális Gépet.

1. A konfigurációs objektum létrehozása a virtuális Géphez.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. A virtuális gép helyi rendszergazdai fiók hitelesítő adatainak lekérése.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. Hozzon létre egy virtuális gép konfigurációs objektumot.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Állítsa be az operációs rendszer lemezképét a virtuális gép számára.

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. Az operációsrendszer-lemez konfigurálása.

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. Adja hozzá a hálózati Adaptert a virtuális Gépet.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. A virtuális gép létrehozásához.

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. Mentse a parancsfájlt.

## <a name="run-the-script"></a>A parancsfájl futtatása

Miután kiválasztotta a szükséges módosításokat, futtassa az előző parancsfájlt. A virtuális gép létrehozása néhány perc múlva.

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím. Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-powershell.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait.

## <a name="next-steps"></a>További lépések

Hálózati forgalmat, és a virtuális gépről a jelen cikkben létrehozott áramolhasson. Bejövő és kimenő biztonsági szabályok egy hálózati biztonsági csoporton belül, amely korlátozza a forgalmat, amely a hálózati adapter vagy az alhálózaton, illetve határozhatja meg. Hálózati biztonsági csoportokkal kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok áttekintése](security-overview.md).