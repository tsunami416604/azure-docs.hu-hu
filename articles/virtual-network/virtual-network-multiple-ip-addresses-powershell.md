---
title: Több IP-cím az Azure virtuális gépekhez - PowerShell | Microsoft dokumentumok
description: Megtudhatja, hogyan rendelhet több IP-címet egy virtuális géphez a PowerShell használatával. | Erőforrás-kezelő
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: a8bd4e4779d94cfc22ac7726c9746fe755764033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279571"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Több IP-cím hozzárendelése virtuális gépekhez a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy virtuális gépet (VM) az Azure Resource Manager üzembe helyezési modelljén keresztül a PowerShell használatával. A klasszikus központi telepítési modellen keresztül létrehozott erőforrásokhoz nem rendelhető hozzá több IP-cím. Ha többet szeretne megtudni az Azure üzembe helyezési modellekről, olvassa el a [Telepítési modellek ismertetése](../resource-manager-deployment-model.md) című cikket.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

Az alábbi lépések ismertetik, hogyan hozhat létre egy példa virtuális gép több IP-cím, a forgatókönyvben leírtak szerint. Módosítsa a változóértékeket a megvalósításhoz szükséges szerint.

1. Nyisson meg egy PowerShell-parancssort, és hajtsa végre a további lépéseket ebben a szakaszban egyetlen PowerShell-munkameneten belül. Ha még nincs telepítve és konfigurálva a PowerShell, hajtsa végre az [Azure PowerShell telepítéséről és konfigurálásáról](/powershell/azure/overview) szóló cikk lépéseit.
2. Jelentkezzen be a fiókjába a `Connect-AzAccount` paranccsal.
3. Cserélje le *a myResourceGroup-ot* és a *westus-t* az Ön által választott névre és helyre. Hozzon létre egy erőforráscsoportot. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Hozzon létre egy virtuális hálózatot (VNet) és alhálózatot ugyanazon a helyen, mint az erőforráscsoport:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Hozzon létre egy hálózati biztonsági csoportot (NSG) és egy szabályt. Az NSG biztonságossá teszi a virtuális gép bejövő és kimenő szabályok használatával. Ebben az esetben létrejön egy bejövő szabály a 3389-es porthoz, amely lehetővé teszi a bejövő távoli asztali kapcsolatokat.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Adja meg a hálózati adapter elsődleges IP-konfigurációját. Módosítsa a 10.0.0.4 értéket érvényes címre a létrehozott alhálózatban, ha nem a korábban megadott értéket használta. Statikus IP-cím hozzárendelése előtt ajánlott először megerősíteni, hogy még nincs használatban. Írja be `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`a parancsot . Ha a cím elérhető, a kimenet *igaz*értéket ad vissza. Ha nem érhető el, a kimenet hamis értéket ad *vissza,* és az elérhető címek listáját. 

    A következő parancsokban **cserélje le \<a lecserélendő egyedi névre> a használandó egyedi DNS-névre.** A névnek egyedinek kell lennie az Azure-régión belüli összes nyilvános IP-cím között. Ez egy nem kötelező paraméter. Eltávolítható, ha csak a nyilvános IP-cím használatával szeretne csatlakozni a virtuális géphez.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Ha több IP-konfigurációt rendel egy hálózati adapterhez, egy konfigurációt kell hozzárendelni az *-Primary*.

    > [!NOTE]
    > A nyilvános IP-címek névleges díjkötelesek. Ha többet szeretne megtudni az IP-cím díjszabásáról, olvassa el az [IP-cím díjszabási](https://azure.microsoft.com/pricing/details/ip-addresses) oldalát. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.

7. Adja meg a hálózati adapter másodlagos IP-konfigurációit. Szükség esetén hozzáadhat vagy eltávolíthat konfigurációkat. Minden IP-konfigurációhoz hozzá kell rendelni egy privát IP-címet. Minden konfigurációhoz szükség van egy nyilvános IP-címhez.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Hozza létre a hálózati adaptert, és társítsa hozzá a három IP-konfigurációt:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Bár az összes konfiguráció kontinusza van rendelve egy hálózati adapter ebben a cikkben, több IP-konfigurációk a virtuális géphez csatlakoztatott minden hálózati adapterhez rendelhető. Ha meg szeretné tudni, hogyan hozhat létre virtuális gép több hálózati adapterek, olvassa el a [virtuális gép létrehozása több hálózati adapterek](../virtual-machines/windows/multiple-nics.md) cikket.

9. Hozza létre a virtuális gép beírásával a következő parancsokat:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Adja hozzá a privát IP-címeket a virtuális gép operációs rendszeréhez az operációs rendszer lépéseivégrehajtásával a [cikk IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszában. Ne adja hozzá a nyilvános IP-címeket az operációs rendszerhez.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-címek hozzáadása virtuális géphez

A következő lépések végrehajtásával hozzáadhat magán- és nyilvános IP-címeket az Azure hálózati felülethez. A következő szakaszokban szereplő példák feltételezik, hogy már rendelkezik egy virtuális gép a [forgatókönyvben](#scenario) a cikkben leírt három IP-konfigurációk, de nem szükséges, hogy nem.

1. Nyisson meg egy PowerShell-parancssort, és hajtsa végre a további lépéseket ebben a szakaszban egyetlen PowerShell-munkameneten belül. Ha még nincs telepítve és konfigurálva a PowerShell, hajtsa végre az [Azure PowerShell telepítéséről és konfigurálásáról](/powershell/azure/overview) szóló cikk lépéseit.
2. Módosítsa a következő $Variables "értékeit" annak a hálózati adapternek a nevére, amelyhez IP-címet szeretne adni, valamint azt az erőforráscsoportot és helyet, amelyben a hálózati adapter megtalálható:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Ha nem tudja a módosítani kívánt hálózati adapter nevét, írja be a következő parancsokat, majd módosítsa az előző változók értékeit:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Hozzon létre egy változót, és állítsa be a meglévő hálózati adapterre a következő parancs beírásával:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. A következő parancsokban módosítsa a *MyVNet* és a *MySubnet* nevet a virtuális hálózat és alhálózat nevére, amelyhez a hálózati adapter csatlakozik. Adja meg azokat a parancsokat, amelyek lekérik azokat a virtuális hálózatokat és alhálózati objektumokat, amelyekhez a hálózati adapter csatlakozik:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Ha nem tudja azt a virtuális hálózatot vagy alhálózati nevet, amelyhez a hálózati adapter kapcsolódik, írja be a következő parancsot:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   A kimenetben keresse meg a következő példakimenethez hasonló szöveget:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    Ebben a kimenetben *a MyVnet* a virtuális hálózat, *a MySubnet* pedig az az alhálózat, amelyhez a hálózati adapter csatlakozik.

5. Hajtsa végre az alábbi szakaszok valamelyikének lépéseit az Ön igényei nek megfelelően:

   **Privát IP-cím hozzáadása**

   Ha privát IP-címet szeretne hozzáadni egy hálózati adapterhez, létre kell hoznia egy IP-konfigurációt. A következő parancs létrehoz egy konfigurációt, amelynek statikus IP-címe 10.0.0.7. Statikus IP-cím megadásakor az alhálózat nem használt címének kell lennie. Javasoljuk, hogy először tesztelje a címet, hogy megbizonyosodjon arról, hogy elérhető a `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` parancs beírásával. Ha az IP-cím elérhető, a kimenet *igaz*értéket ad vissza. Ha nem érhető el, a kimenet *hamis*értéket ad vissza, és az elérhető címek listáját.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Hozzon létre annyi konfigurációt, amennyire szüksége van, egyedi konfigurációnevek és privát IP-címek használatával (statikus IP-címekkel rendelkező konfigurációk esetén).

   Adja hozzá a privát IP-címet a virtuális gép operációs rendszeréhez az operációs rendszer lépéseinek végrehajtásával a [cikk IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszában.

   **Nyilvános IP-cím hozzáadása**

   A nyilvános IP-cím hozzáadódik azáltal, hogy egy nyilvános IP-cím erőforrást társít egy új IP-konfigurációhoz vagy egy meglévő IP-konfigurációhoz. Végezze el a következő szakaszok egyikének lépéseit, ahogy azt szeretné.

   > [!NOTE]
   > A nyilvános IP-címek névleges díjkötelesek. Ha többet szeretne megtudni az IP-cím díjszabásáról, olvassa el az [IP-cím díjszabási](https://azure.microsoft.com/pricing/details/ip-addresses) oldalát. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.
   >

   **A nyilvános IP-cím erőforrás társítása új IP-konfigurációhoz**

   Amikor nyilvános IP-címet ad hozzá egy új IP-konfigurációban, saját IP-címet is hozzá kell adnia, mert minden IP-konfigurációnak privát IP-címmel kell rendelkeznie. Hozzáadhat egy meglévő nyilvános IP-címerőforrást, vagy létrehozhat egy újat. Új létrehozásához írja be a következő parancsot:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Ha statikus privát IP-címmel és a kapcsolódó *myPublicIp3* nyilvános IP-cím erőforrással rendelkező új IP-konfigurációt szeretne létrehozni, írja be a következő parancsot:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **A nyilvános IP-cím erőforrás társítása meglévő IP-konfigurációhoz**

   Nyilvános IP-cím erőforrás csak olyan IP-konfigurációhoz társítható, amelyhez még nem tartozik. A következő parancs megadásával meghatározhatja, hogy egy IP-konfigurációrendelkezik-e nyilvános IP-címmel:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Az alábbihoz hasonló kimenetet fog látni:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Mivel az *IpConfig-3* **PublicIpAddress** oszlopa üres, jelenleg nincs hozzá nyilvános IP-címerőforrás társítva. Az IpConfig-3 protokollhoz hozzáadhat egy meglévő nyilvános IP-címerőforrást, vagy a következő parancsot adhatja meg:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Írja be a következő parancsot a nyilvános IP-cím erőforrás társításához az *IpConfig-3*nevű meglévő IP-konfigurációhoz:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Állítsa be a hálózati adaptert az új IP-konfigurációval a következő parancs megadásával:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. A hálózati adapterhez rendelt privát IP-címek és nyilvános IP-cím erőforrások megtekintése a következő parancs megadásával:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Adja hozzá a privát IP-címet a virtuális gép operációs rendszeréhez az operációs rendszer lépéseinek végrehajtásával a [cikk IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszában. Ne adja hozzá a nyilvános IP-címet az operációs rendszerhez.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]