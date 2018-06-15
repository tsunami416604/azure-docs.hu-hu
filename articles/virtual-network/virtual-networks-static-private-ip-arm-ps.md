---
title: Magánhálózati IP-címek konfigurálása virtuális gépek - Azure PowerShell |} Microsoft Docs
description: Útmutató a PowerShell használatával virtuális gépek magánhálózati IP-címek konfigurálásához.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0e8153f1d0cecd4efe66dc7cce64addd6ed62aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424057"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-powershell"></a>PowerShell használatával virtuális gépek magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

Az Azure két üzemi modellel rendelkezik, az Azure Resource Managerrel és a klasszikussal. A Microsoft azt javasolja, hogy az erőforrások létrehozásához használja a Resource Manager-alapú üzemi modellt. A két modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md) című cikket. Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [statikus magánhálózati IP-cím, a klasszikus üzembe helyezési modellel kezelése](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

A minta az alábbi parancsok várt már létrehozott egy egyszerű környezetben PowerShell a fenti forgatókönyv alapján. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először leírt tesztkörnyezet felépítéséhez [hozzon létre egy virtuális hálózatot](quick-create-powershell.md).

## <a name="create-a-vm-with-a-static-private-ip-address"></a>Statikus magánhálózati IP-címmel rendelkező virtuális gép létrehozása
Nevű virtuális gép létrehozása *DNS01* a a *előtér* egy vnet nevű alhálózat *TestVNet* statikus magánhálózati IP-címe a *192.168.1.101*, kövesse az alábbi lépéseket:

1. A tárfiók, hely, erőforráscsoport és hitelesítő adatokat használni a változók értékét. Szüksége lesz egy felhasználónév és jelszó megadását a virtuális gép számára. A fiók- és erőforrás csoport már léteznie kell.

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. A virtuális hálózati és alhálózati szeretne létrehozni a virtuális gép beolvasása.

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. Szükség esetén hozzon létre egy nyilvános IP-címet a virtuális gép az internetről való eléréséhez.

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. Hozzon létre egy hálózati Adaptert szeretne hozzárendelni a virtuális gép statikus magánhálózati IP-cím használatával. Győződjön meg arról, hogy az IP-cím a alhálózati tartományból ad hozzá a virtuális Gépet. Ez az ebben a cikkben, amelyen beállíthatja a saját IP-cím lehet statikus kötése fő lépést.

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. Hozzon létre a virtuális hálózati adapter:

    ```powershell
    $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01 `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri `
    -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
    ```

Javasoljuk, hogy nem statikusan rendelje a magánhálózati IP-címe az operációs rendszerben a virtuális gépek az Azure virtuális géphez rendelt kivéve, ha szükséges, például amikor [több IP-címek hozzárendelése egy Windows virtuális gépre](virtual-network-multiple-ip-addresses-powershell.md). Ha manuálisan állítsa be a magánhálózati IP-cím, az operációs rendszerből, győződjön meg arról, hogy az a magánhálózati IP-cím, az Azure rendelt megegyező címre [hálózati illesztő](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. További információ [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Manuálisan soha ne rendelje a nyilvános IP-cím, egy Azure virtuális gépen belül a virtuális gép operációs rendszerének rendelt.

## <a name="retrieve-static-private-ip-address-information-for-a-network-interface"></a>Statikus magánhálózati IP-címadatok a hálózati illesztő beolvasása
A statikus magánhálózati IP-címadatok a fenti parancsfájl létrehozza a virtuális gép megtekintéséhez futtassa a következő PowerShell-parancsot, és tekintse meg az értékeit *privateipaddress tulajdonságot* és *címkiosztási*:

```powershell
Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
```

Várt kimenet:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="remove-a-static-private-ip-address-from-a-network-interface"></a>A statikus magánhálózati IP-cím eltávolítása a hálózati illesztő
Távolítsa el a statikus magánhálózati IP-cím hozzá a virtuális gépre, a fenti, a parancsfájl a következő PowerShell-parancsokat:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Várt kimenet:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="add-a-static-private-ip-address-to-a-network-interface"></a>A statikus magánhálózati IP-cím hozzáadása a hálózati illesztő
A statikus magánhálózati IP-cím hozzáadása a fenti parancsfájl használatával létrehozott virtuális Gépet, a következő parancsokat:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Javasoljuk, hogy nem statikusan rendelje a magánhálózati IP-címe az operációs rendszerben a virtuális gépek az Azure virtuális géphez rendelt kivéve, ha szükséges, például amikor [több IP-címek hozzárendelése egy Windows virtuális gépre](virtual-network-multiple-ip-addresses-powershell.md). Ha manuálisan állítsa be a magánhálózati IP-cím, az operációs rendszerből, győződjön meg arról, hogy az a magánhálózati IP-cím, az Azure rendelt megegyező címre [hálózati illesztő](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. További információ [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Manuálisan soha ne rendelje a nyilvános IP-cím, egy Azure virtuális gépen belül a virtuális gép operációs rendszerének rendelt.

## <a name="change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>A kiosztási módszer egy adott hálózati csatoló rendelt privát IP-cím módosítása

A magánhálózati IP-címet a statikus vagy dinamikus kiosztási módszerrel van rendelve egy hálózati Adaptert. Dinamikus IP-címek egy virtuális gép leállított (felszabadított) állapotában korábban elindítása után módosíthatja. A potenciálisan hibákat is okozhat, ha a virtuális gép leállított (felszabadított) állapotból újraindítása után is ugyanazt a címet, igénylő futtatja. Statikus IP-címek vannak maradnak, amíg a virtuális gép nem. IP-cím foglalási módjának módosításához futtassa a következő parancsfájlt, amely megváltoztatja a kiosztási módszer a dinamikus statikus. Ha a kiosztási módszer az aktuális magánhálózati IP-címet a statikus, módosítsa *statikus* való *dinamikus* a parancsfájl végrehajtása előtt.

```powershell
$RG = "TestRG"
$NIC_name = "testnic1"

$nic = Get-AzureRmNetworkInterface -ResourceGroupName $RG -Name $NIC_name
$nic.IpConfigurations[0].PrivateIpAllocationMethod = 'Static'
Set-AzureRmNetworkInterface -NetworkInterface $nic 
$IP = $nic.IpConfigurations[0].PrivateIpAddress

Write-Host "The allocation method is now set to"$nic.IpConfigurations[0].PrivateIpAllocationMethod"for the IP address" $IP"." -NoNewline
```

Ha a hálózati adapter neve nem tudja, erőforráscsoporton belül a hálózati adapterek listáját tekintheti meg a következő parancs beírásával:

```powershell
Get-AzureRmNetworkInterface -ResourceGroupName $RG | Where-Object {$_.ProvisioningState -eq 'Succeeded'} 
```

## <a name="next-steps"></a>További lépések

Kezelésével kapcsolatos [IP-címbeállítások](virtual-network-network-interface-addresses.md).