---
title: Több IP-címek az Azure virtuális gépek - PowerShell |} Microsoft Docs
description: Megtudhatja, hogyan több IP-címek hozzárendelése a virtuális gép PowerShell-lel |} Erőforrás-kezelő.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: jdial;annahar
ms.openlocfilehash: 4c74833933642ec67bdd2a77d073b083d54a3038
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602840"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Több IP-címek hozzárendelése a virtuális gépek PowerShell használatával

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk azt ismerteti, hogyan létrehozni egy virtuális gépet (VM) a PowerShell használata Azure Resource Manager telepítési modell használatával. Több IP-cím nem lehet hozzárendelni a klasszikus üzembe helyezési modell használatával létrejött erőforrásokat. Az Azure üzembe helyezési modellel kapcsolatos további tudnivalókért olvassa el a [üzembe helyezési modellel megértéséhez](../resource-manager-deployment-model.md) cikk.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Több IP-címekkel rendelkező virtuális gép létrehozása

A következő lépések bemutatják, hogyan több IP-címmel, például virtuális gép létrehozásához a forgatókönyvben leírtak szerint. A végrehajtásához szükséges változók értékeinek módosítása.

1. Nyisson meg egy PowerShell-parancssort, és hajtsa végre a fennmaradó lépéseit ebben a szakaszban egy PowerShell-munkameneten belül. Ha még nem rendelkezik a PowerShell telepítése és konfigurálása, hajtsa végre a a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) cikk.
2. Jelentkezzen be a fiókot a `Connect-AzureRmAccount` parancsot.
3. Cserélje le *myResourceGroup* és *westus* a nevet és a helyet. Hozzon létre egy erőforráscsoportot. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

    ```powershell
    $RgName   = "MyResourceGroup"
    $Location = "westus"

    New-AzureRmResourceGroup `
    -Name $RgName `
    -Location $Location
    ```

4. Hozzon létre egy virtuális hálózathoz (VNet) és az alhálózatot és az erőforráscsoport ugyanazon a helyen:

    ```powershell
    
    # Create a subnet configuration
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name MySubnet `
    -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $VNet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyVNet `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $subnetConfig

    # Get the subnet object
    $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
    ```

5. A hálózati biztonsági csoport (NSG) és a szabály létrehozása. Az NSG-t a virtuális gép bejövő és kimenő szabályok használatával titkosítja. Ebben az esetben létrejön egy bejövő szabály a 3389-es porthoz, amely lehetővé teszi a bejövő távoli asztali kapcsolatokat.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzureRmNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Adja meg az elsődleges IP-konfigurációjának megadása a hálózati adaptert. 10.0.0.4 váltson érvényes cím az alhálózat hozott létre, ha nem a korábban megadott értéket. Egy statikus IP-cím hozzárendelése, előtt ajánlott, hogy először megerősíti nem már használatban van. Adja meg a parancsot `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Ha a cím érhető el, a kimenetet visszaadja *igaz*. Ha nem érhető el, a kimeneti adja vissza *hamis* és a rendelkezésre álló címek listáját. 

    Az alábbi parancsokban **< csere-az-a-egyedi-neve > cserélje le az egyedi DNS-név használatával.** A nevének egyedinek kell lennie a kívánt Azure-régiót belül minden nyilvános IP-címek között. Ez nem kötelező paraméter. Ha csak szeretné csatlakoztatni a virtuális gép használata a nyilvános IP-cím el kell távolítani.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Ha több IP-konfigurációk rendel egy hálózati Adaptert, egy konfigurációs hozzá kell rendelni a *-elsődleges*.

    > [!NOTE]
    > Nyilvános IP-címek névleges díjat kell. IP-cím árazással kapcsolatos további tudnivalókért olvassa el a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses) lap. Nyilvános IP-címek egy előfizetésben használható száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.

7. Adja meg a másodlagos IP-Címes konfiguráció a hálózati adaptert. Adja hozzá, vagy távolítsa el a szükséges konfigurációk. Minden IP-konfiguráció egy privát IP-cím kell rendelkeznie. Minden egyes konfigurációs szükség lehet egy nyilvános IP-cím hozzárendelve.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. A hálózati adapter létrehozása, és társítsa azt a három IP-konfigurációk:

    ```powershell
    
    $NIC = New-AzureRmNetworkInterface `
    -Name MyNIC `
    -ResourceGroupName $RgName `
    -Location $Location `
    -NetworkSecurityGroupId $NSG.Id `
    -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```

    >[!NOTE]
    >Bár az összes konfiguráció ebben a cikkben egy hálózati adapterre van hozzárendelve, minden hálózati adapternek a virtuális Géphez csatlakozik, rendelhet több IP-konfigurációk. Több hálózati adapterrel rendelkező virtuális gép létrehozása, olvassa el a [több hálózati adapterrel rendelkező virtuális gép létrehozása](../virtual-machines/windows/multiple-nics.md) cikk.

9. A virtuális gép létrehozása a következő parancsok beírásával:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a sername and password for the VM you're reating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzureRmVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzureRmVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. A privát IP-címek hozzáadása a virtuális gép operációs rendszer, az operációs rendszernek a lépések végrehajtásával a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegyen fel a nyilvános IP-címeket az operációs rendszer.

## <a name="add"></a>IP-címek hozzáadása a virtuális gépek

Az Azure-hálózat illesztőhöz privát és nyilvános IP-címek adhat hozzá a következő lépések végrehajtásával. A következő szakaszokban szereplő példák azt feltételezik, hogy már van egy virtuális gép leírt három IP-konfigurációk a [forgatókönyv](#Scenario) ezen cikk, de nem szükséges, hogy végezzen.

1. Nyisson meg egy PowerShell-parancssort, és hajtsa végre a fennmaradó lépéseit ebben a szakaszban egy PowerShell-munkameneten belül. Ha még nem rendelkezik a PowerShell telepítése és konfigurálása, hajtsa végre a a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) cikk.
2. A következő $Variables "értékek" módosítása a hálózati adapter IP-cím hozzáadásához és az erőforráscsoport és a hálózati adapter létezik a hely nevét:

    ```powershell
    $NicName  = "MyNIC"
    $RgName   = "MyResourceGroup"
    $Location = "westus"
    ```

    Ha szeretné módosítani, adja meg a következő parancsok hálózati adapter neve nem tudja, majd módosítsa az előző változók értékeit:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. Hozzon létre egy változót, és állítsa be a meglévő hálózati adapter a következő parancs beírásával:

    ```powershell
    $MyNIC = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName
    ```
4. A következő parancsokat, módosítsa *MyVNet* és *MySubnet* az alhálózatot a hálózati adapter csatlakoztatva van és a virtuális hálózat nevét. Adja meg a parancsok, hogy lekérje a VNet és alhálózat objektumokat, a hálózati adapter csatlakozik:

    ```powershell
    $MyVNet = Get-AzureRMVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
    $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
    ```
    Ha nem tudja a virtuális hálózat vagy az alhálózat neve, a hálózati adapter csatlakozik, írja be a következő parancsot:
    ```powershell
    $MyNIC.IpConfigurations
    ```
    A kimenetben keresse meg a következő egy példa a kimenetre hasonló szöveget:
    
    ```
    "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
    ```
    A kimenet *MyVnet* van a virtuális hálózat és *MySubnet* az alhálózat, a hálózati adapter csatlakozik.

5. Hajtsa végre a következő részeiből, a követelmények alapján:

    **Adja hozzá a magánhálózati IP-cím**

    A magánhálózati IP-cím hozzáadása a hálózati adapter, létre kell hoznia IP-konfigurációt. A következő parancs létrehoz egy konfigurációt 10.0.0.7 statikus IP-címmel. Egy statikus IP-cím megadása esetén az alhálózat nem használt címnek kell lennie. Javasoljuk, hogy először tesztelje a címet, hogy ellenőrizze az elérhető írja be a `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` parancsot. Ha az IP-cím áll rendelkezésre, a kimenetet visszaadja *igaz*. Ha nem érhető el, a kimeneti adja vissza *hamis*, és a rendelkezésre álló címek listáját.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    Tetszőleges számú konfigurációk összes kívánt beállítást, egyedi konfigurációs nevek és privát IP-címek használata (a statikus IP-címekkel rendelkező konfigurációk) létrehozása.

    A magánhálózati IP-cím hozzáadása a virtuális gép operációs rendszer, az operációs rendszernek a lépések végrehajtásával a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát.

    **A nyilvános IP-cím hozzáadása**

    Egy nyilvános IP-címet vagy egy új IP-konfiguráció, vagy egy meglévő IP-konfiguráció a nyilvános IP-cím erőforrás társításával jelenik meg. Hajtsa végre a következő szakaszok, egyikében összes kívánt beállítást.

    > [!NOTE]
    > Nyilvános IP-címek névleges díjat kell. IP-cím árazással kapcsolatos további tudnivalókért olvassa el a [IP-cím árképzési](https://azure.microsoft.com/pricing/details/ip-addresses) lap. Nyilvános IP-címek egy előfizetésben használható száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.
    >

    - **Társítsa a nyilvános IP-cím erőforrás új IP-konfigurációhoz**
    
        Egy nyilvános IP-címet ad hozzá egy új IP-konfiguráció, amikor is hozzá kell magánhálózati IP-cím, mert az összes IP-konfiguráció magánhálózati IP-címnek kell rendelkeznie. Egy meglévő nyilvános IP-cím erőforrás hozzáadása, vagy hozzon létre egy újat. Hozzon létre egy újat, írja be a következő parancsot:
    
        ```powershell
        $myPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "myPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location `
        -AllocationMethod Static
        ```

        Új IP-konfiguráció létrehozása hozzá statikus magánhálózati IP-cím és a társított *myPublicIp3* nyilvános IP-cím erőforrás címet, adja meg a következő parancsot:

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig `
        -Name IPConfig-4 `
        -NetworkInterface $myNIC `
        -Subnet $Subnet `
        -PrivateIpAddress 10.0.0.7 `
        -PublicIpAddress $myPublicIp3
        ```

    - **A nyilvános IP-cím erőforrás egy meglévő IP-konfiguráció való társítása**

        A nyilvános IP-cím erőforrás csak lehet társítva, amely még nincs társított IP-konfigurációt. Segítségével meghatározhatja, hogy rendelkezik-e IP-konfigurációt tartozó nyilvános IP-cím a következő parancs beírásával:

        ```powershell
        $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
        ```

        A kimenet az alábbihoz hasonló jelenik meg:

        ```     
        Name       PrivateIpAddress PublicIpAddress                                           Primary
        
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False
        ```

        Mivel a **PublicIpAddress** oszlopában *IpConfig-3* van üres, nem nyilvános IP-cím erőforrás jelenleg társítva. Egy meglévő nyilvános IP-cím erőforrás hozzáadása IpConfig-3, vagy hozzon létre egyet a következő parancsot írja be:

        ```powershell
        $MyPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "MyPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location -AllocationMethod Static
        ```

        Adja meg a következő parancsot a meglévő IP-konfiguráció a nyilvános IP-cím erőforrás hozzárendelni *IpConfig-3*:
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig `
        -Name IpConfig-3 `
        -NetworkInterface $mynic `
        -Subnet $Subnet `
        -PublicIpAddress $myPublicIp3
        ```

6. Állítsa be az új IP-konfigurációval a hálózati adapter a következő parancs beírásával:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $MyNIC
    ```

7. Tekintse meg a magánhálózati IP-címek és a nyilvános IP-cím erőforrás hozzárendelt hálózati adapter a következő parancs beírásával:

    ```powershell   
    $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. A magánhálózati IP-cím hozzáadása a virtuális gép operációs rendszer, az operációs rendszernek a lépések végrehajtásával a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegyen fel a nyilvános IP-cím az operációs rendszer.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
