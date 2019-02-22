---
title: Több IP-címek az Azure virtual machines – PowerShell |} A Microsoft Docs
description: Ismerje meg, hogy több IP-cím hozzárendelése a virtuális gépek PowerShell segítségével. | Resource Manager
services: virtual-network
documentationcenter: na
author: jimdial
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
ms.author: jdial;annahar
ms.openlocfilehash: f9ce9bc9272eb76c4162ab0abedd93e959385124
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649050"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Több IP-cím hozzárendelése a virtuális gépek PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre egy virtuális gépet (VM) az Azure Resource Manager-alapú üzemi modellel, PowerShell-lel. Több IP-cím nem lehet hozzárendelni a klasszikus üzemi modellel létrehozott erőforrásokat. Azure üzembehelyezési modellekkel kapcsolatos további tudnivalókért olvassa el a [üzembe helyezési modellek ismertetése](../resource-manager-deployment-model.md) cikk.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

A következő lépések azt ismertetik, hogyan hozzon létre próbaképpen egy virtuális Gépet több IP-címmel a forgatókönyvben leírtak szerint. A megvalósítás szükség szerint változó értékeinek módosítása.

1. Nyisson meg egy PowerShell-parancssort, és a hátralévő lépéseket ebben a szakaszban egy egyetlen PowerShell-munkameneten belül. Ha még nem rendelkezik a PowerShell telepítése és konfigurálása, hajtsa végre a a [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) cikk.
2. Jelentkezzen be a fiókjába a `Connect-AzAccount` parancsot.
3. Cserélje le *myResourceGroup* és *westus* nevű és egy tetszőleges helyre. Hozzon létre egy erőforráscsoportot. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Hozzon létre egy virtuális hálózat (VNet) és egy alhálózatot az erőforráscsoport ugyanazon a helyen:

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

5. Hozzon létre egy hálózati biztonsági csoport (NSG) és a egy szabályt. Az NSG-KET a virtuális gép bejövő és kimenő szabályok használatával védi. Ebben az esetben létrejön egy bejövő szabály a 3389-es porthoz, amely lehetővé teszi a bejövő távoli asztali kapcsolatokat.

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

6. Adja meg az elsődleges IP-konfigurációt a hálózati adaptert. 10.0.0.4 váltson érvényes cím az alhálózat hozta létre, ha nem használja a korábban meghatározott érték. Statikus IP-cím hozzárendelése, előtt javasoljuk, hogy először meggyőződött még nincs használatban. Adja meg a parancsot `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Ha a cím érhető el, a kimenetet ad vissza *igaz*. Ha nem érhető el, a kimenetet ad vissza *hamis* és a rendelkezésre álló címek listáját. 

    Az alábbi parancsokban **< csere-az-az egyedi neve-> cserélje le az egyedi DNS-név használatához.** A név az összes nyilvános IP-címek Azure régiókon belül egyedinek kell lennie. Ez egy nem kötelező paraméter. Ha csak szeretné csatlakoztatni a virtuális gép nyilvános IP-cím használatával el kell távolítani.

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

    Ha több IP-konfiguráció rendel egy hálózati Adapterhez, egy konfigurációs kell hozzárendelni a *-elsődleges*.

    > [!NOTE]
    > Nyilvános IP-címek egy névleges díj rendelkezik. IP-címek díjszabása kapcsolatos további információkért olvassa el a [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses) lapot. Egy előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.

7. Adja meg a másodlagos IP-konfigurációk esetében a hálózati adaptert. Adja hozzá, vagy távolítsa el a szükséges konfigurációval. Minden IP-konfigurációhoz rendelt magánhálózati IP-cím kell rendelkeznie. Minden egyes konfiguráció igény szerint is van rendelve egy nyilvános IP-címet.

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

8. Hozzon létre a hálózati Adaptert, és társítsa azt a három IP-konfigurációk:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Bár az összes konfiguráció hozzá van rendelve egy hálózati adapter ebben a cikkben, hozzárendelheti a virtuális géphez csatolt minden hálózati adapter több IP-konfiguráció. Több hálózati adapterrel rendelkező virtuális gép létrehozása, olvassa el a [több hálózati adapterrel rendelkező virtuális gép létrehozása](../virtual-machines/windows/multiple-nics.md) cikk.

9. A virtuális gép létrehozásához írja be a következő parancsokat:

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

10. A magánhálózati IP-címek hozzáadása a virtuális gép operációs rendszeréhez, az operációs rendszernek a lépéseket követve a [hozzáadása IP-címek a virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegye fel a nyilvános IP-címek az operációs rendszer.

## <a name="add"></a>IP-címek hozzáadása egy virtuális Géphez

Az Azure hálózati adapterhez privát és nyilvános IP-címek az alábbi lépéseket követve adhat hozzá. Az alábbi szakaszokban található példák feltételezik, hogy már rendelkezik egy virtuális gép leírt három IP-konfigurációk a [forgatókönyv](#Scenario) a jelen cikk, de nem szükséges, hogy végrehajtja.

1. Nyisson meg egy PowerShell-parancssort, és a hátralévő lépéseket ebben a szakaszban egy egyetlen PowerShell-munkameneten belül. Ha még nem rendelkezik a PowerShell telepítése és konfigurálása, hajtsa végre a a [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) cikk.
2. Módosítsa a következő $Variables "values" a hálózati adapter IP-címet hozzáadni kívánt és az erőforráscsoportot és a hálózati adapter létezik a hely neve:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Ha nem ismeri a hálózati Adaptert szeretne módosítani, a következő parancsokat a nevére, majd módosítsa az előző változók értékeit:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Hozzon létre egy változót, és állítsa be a meglévő hálózati adapter a következő parancs beírásával:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. A következő parancsokat, módosítsa a *MyVNet* és *MySubnet* nevének a virtuális hálózatot és alhálózatot a hálózati adapter csatlakozik. Adja meg a hálózati adapter csatlakoztatva van a virtuális hálózatot és alhálózatot objektumokat beolvasni a parancsokat:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Ha nem ismeri a hálózati adapter csatlakoztatva van a virtuális hálózat vagy alhálózat nevét, adja meg a következő parancsot:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   A kimenetben keresse meg az alábbi példa kimenetében hasonló szöveget:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    Ez a kimenet a *MyVnet* van a virtuális hálózat és *MySubnet* az alhálózat, a hálózati adapter csatlakozik.

5. Hajtsa végre az alábbi szakaszokban, igényei alapján lépéseit:

   **Magánhálózati IP-cím hozzáadása**

   A hálózati adapter magánhálózati IP-cím hozzá, létre kell hoznia egy IP-konfigurációt. A következő parancsot egy konfigurációs hoz létre egy statikus IP-cím 10.0.0.7. Statikus IP-cím megadása esetén egy nem használt cím az alhálózat kell lennie. Javasoljuk, hogy először tesztelje, hogy elérhető írja be a címet a `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` parancsot. Ha az IP-cím áll rendelkezésre, a kimenetet ad vissza *igaz*. Ha nem érhető el, a kimenetet ad vissza *hamis*, és a rendelkezésre álló címek listáját.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Hozzon létre annyi konfigurációk, amennyi szükséges, egyedi konfigurációs nevek és magánhálózati IP-címek használata (a statikus IP-címekkel rendelkező konfigurációk).

   A magánhálózati IP-cím hozzáadása a virtuális gép operációs rendszeréhez, az operációs rendszernek a lépéseket követve a [hozzáadása IP-címek a virtuális gép operációs rendszerre](#os-config) című szakaszát.

   **Nyilvános IP-cím hozzáadása**

   Nyilvános IP-cím vagy egy új IP-konfigurációt, vagy egy meglévő IP-konfiguráció nyilvános IP-cím erőforrás társításával egészül ki. Hajtsa végre a következő szakaszokban áttekintendő, egyik lépéseit, amennyi szükséges.

   > [!NOTE]
   > Nyilvános IP-címek egy névleges díj rendelkezik. IP-címek díjszabása kapcsolatos további információkért olvassa el a [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses) lapot. Egy előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.
   >

   **Társítsa a nyilvános IP-cím erőforrás egy új IP-konfigurációhoz**

   Minden alkalommal, amikor egy új IP-konfiguráció nyilvános IP-címet ad hozzá, hozzá kell adnia is magánhálózati IP-cím, mert minden IP-konfigurációval kell rendelkeznie a magánhálózati IP-cím. Adjon hozzá egy meglévő nyilvános IP-cím erőforrás, vagy hozzon létre egy újat. Hozzon létre egy újat, adja meg a következő parancsot:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Új IP-konfiguráció létrehozása egy statikus magánhálózati IP-cím és a társított *myPublicIp3* nyilvános IP-cím erőforrás, adja meg a következő parancsot:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **A nyilvános IP-cím erőforrás egy meglévő IP-konfiguráció hozzárendelése**

   Egy nyilvános IP-cím erőforrás csak társíthatók, amely még nincs ilyen társított IP-konfigurációval. Segítségével meghatározhatja, hogy rendelkezik-e egy IP-konfiguráció társított nyilvános IP-cím a következő parancs beírásával:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Az alábbihoz hasonló kimenet jelenik meg:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Mivel a **PublicIpAddress** oszlopában *IpConfig-3* van üres, nem nyilvános IP-cím erőforrás jelenleg társítva. Adjon hozzá egy meglévő nyilvános IP-cím erőforrás IpConfig-3, vagy hozzon létre egyet a következő parancsot írja be:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Adja meg a következő parancsot a nyilvános IP-cím erőforrás a meglévő IP-konfigurációhoz nevű társítása *IpConfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Állítsa be az új IP-konfigurációhoz a hálózati adapter a következő parancs beírásával:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. A magánhálózati IP-címek és a következő parancs beírásával a hálózati adapterhez hozzárendelt nyilvános IP-cím erőforrások megtekintése:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. A magánhálózati IP-cím hozzáadása a virtuális gép operációs rendszeréhez, az operációs rendszernek a lépéseket követve a [hozzáadása IP-címek a virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegye fel a nyilvános IP-címet az operációs rendszer.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]