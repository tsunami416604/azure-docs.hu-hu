---
title: Hozzon létre egy virtuális Gépet egy statikus nyilvános IP-cím - Azure PowerShell |} Microsoft Docs
description: 'Útmutató: virtuális gép létrehozása a PowerShell használatával statikus nyilvános IP-cím.'
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
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525132"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>Virtuális gép létrehozása a PowerShell használatával statikus nyilvános IP-cím

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a Microsoft azt javasolja, hogy a klasszikus üzembe helyezési modellel helyett az új telepítések esetén a Resource Manager telepítési modell használatát bemutatja.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="start-your-script"></a>A parancsfájl futtatásához
Letöltheti használt teljes PowerShell-parancsfájl [Itt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Módosíthatja a parancsfájlnak a környezetben az alábbi lépésekkel.

A telepítéshez használni kívánt értékek alapján az alábbi változók értékeinek módosítása. A következő értékek leképezése a forgatókönyvet, a cikk ezt használja:

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

## <a name="create-the-necessary-resources-for-your-vm"></a>A szükséges erőforrások a virtuális gép létrehozása
Virtuális gép létrehozása előtt meg kell egy erőforráscsoport, hálózatok, nyilvános IP-cím és a hálózati adapter a virtuális gép által használandó.

1. Hozzon létre egy új erőforráscsoportot.

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. A VNet és alhálózat létrehozása.

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. A nyilvános IP-erőforrás létrehozása. 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. Hozzon létre a hálózati kártya (NIC) a virtuális géphez létre felett, a nyilvános IP-cím az alhálózat. Figyelje meg, az első parancsmag beolvasása a virtuális hálózat az Azure-ból, ez pedig szükséges, mivel egy `Set-AzureRmVirtualNetwork` módosíthatja a meglévő VNet végre lett hajtva.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. Hozzon létre egy tárfiókot, a virtuális gép operációs rendszere meghajtó üzemeltetéséhez.

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="create-the-vm"></a>Virtuális gép létrehozása
Most, hogy minden szükséges erőforrás van érvényben, létrehozhat egy új virtuális Gépet.

1. A konfigurációs objektumot létrehozni a virtuális gép számára.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. A virtuális gép helyi rendszergazdai fiók hitelesítő adatainak lekéréséhez.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. Hozzon létre egy virtuális gép konfigurációs objektuma.

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Állítsa be az operációs rendszer lemezképét a virtuális gép számára.

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. Konfigurálhatja az operációsrendszer-lemezképet.

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. A hálózati adapter hozzáadása a virtuális Gépet.

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. A virtuális gép létrehozása.

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. Mentse a parancsfájlt.

## <a name="run-the-script"></a>A parancsfájl futtatása

Miután kiválasztotta a szükséges módosításokat, futtassa az előző parancsfájlt. A virtuális gép létrehozása néhány perc múlva.

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Manuálisan soha ne rendelje a nyilvános IP-cím, egy Azure virtuális gépen belül a virtuális gép operációs rendszerének rendelt. Javasoljuk, hogy nem statikusan rendelje a magánhálózati IP-címe az operációs rendszerben a virtuális gépek az Azure virtuális géphez rendelt kivéve, ha szükséges, például amikor [több IP-címek hozzárendelése egy Windows virtuális gépre](virtual-network-multiple-ip-addresses-powershell.md). Ha manuálisan állítsa be a magánhálózati IP-cím, az operációs rendszerből, győződjön meg arról, hogy az a magánhálózati IP-cím, az Azure rendelt megegyező címre [hálózati illesztő](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. További információ [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait.

## <a name="next-steps"></a>További lépések

A hálózati forgalommal áramolhasson felé és felől a virtuális gép létrehozása az ebben a cikkben. Megadhatja a bejövő és kimenő biztonsági szabályok, korlátozó áramolhasson az a hálózati adapter vagy az alhálózat érkező vagy oda irányuló forgalmat hálózati biztonsági csoporton belül. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoport – áttekintés](security-overview.md).