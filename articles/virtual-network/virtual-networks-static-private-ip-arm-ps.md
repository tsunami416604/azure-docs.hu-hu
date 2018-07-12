---
title: Magánhálózati IP-címek konfigurálása virtuális gépek – Azure PowerShell-lel |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a magánhálózati IP-címek a virtuális gépek PowerShell használatával.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38307669"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-powershell"></a>PowerShell-lel virtuális gép magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

Az Azure két üzemi modellel rendelkezik, az Azure Resource Managerrel és a klasszikussal. A Microsoft azt javasolja, hogy az erőforrások létrehozásához használja a Resource Manager-alapú üzemi modellt. A két modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md) című cikket. Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [kezelése a klasszikus üzemi modellben statikus magánhálózati IP-cím](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

A minta PowerShell-parancsokat az alábbi hatással vannak a már létrehozott egy egyszerű környezetet a fenti forgatókönyv alapján. Ha azt szeretné, akkor jelennek meg ebben a dokumentumban a parancsok futtatásához először hozhat létre a leírt tesztkörnyezet [hozzon létre egy virtuális hálózatot](quick-create-powershell.md).

## <a name="create-a-vm-with-a-static-private-ip-address"></a>Statikus magánhálózati IP-címmel rendelkező virtuális gép létrehozása
Nevű virtuális gép létrehozása *DNS01* a a *előtérbeli* nevű virtuális hálózat alhálózatának *TestVNet* egy statikus magánhálózati IP-címét *192.168.1.101*, hajtsa végre a az alábbi lépéseket:

1. A storage-fiók, hely, erőforráscsoport és hitelesítő adatokat használni a változók beállítása. Adjon meg egy felhasználónevet és jelszót a virtuális gép kell. A storage-fiók és az erőforrás csoport már léteznie kell.

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. A virtuális hálózatot és alhálózatot a virtuális gép a létrehozni kívánt lekéréséhez.

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. Ha szükséges, hozzon létre egy nyilvános IP-címet a virtuális gép elérését az internetről.

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. Hozzon létre egy hálózati Adaptert a virtuális géphez hozzárendelendő statikus magánhálózati IP-cím használatával. Ellenőrizze, hogy az IP-cím az alhálózat címtartományából ad hozzá a virtuális Gépet. Ez az a fő lépés ebben a cikkben Itt lehet megadni a privát IP-cím lehet statikus.

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. A virtuális gép létrehozása a hálózati adapterhez:

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

Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-powershell.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="retrieve-static-private-ip-address-information-for-a-network-interface"></a>Statikus magánhálózati IP-címadatok egy hálózati adapter lekérése
Az a fenti szkript létrehozni a virtuális gép statikus privát IP-címadatok megtekintéséhez futtassa a következő PowerShell-parancsot, és tekintse meg a tartozó értékeket *PrivateIpAddress* és *címkiosztási*:

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

## <a name="remove-a-static-private-ip-address-from-a-network-interface"></a>Egy hálózati adaptert statikus magánhálózati IP-cím eltávolítása
Távolítsa el a statikus magánhálózati IP-címet, adja hozzá a fenti szkript a virtuális gép futtassa a következő PowerShell-parancsokat:

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

## <a name="add-a-static-private-ip-address-to-a-network-interface"></a>Egy hálózati adaptert statikus magánhálózati IP-cím hozzáadása
A fenti szkript használatával létrehozott virtuális géphez statikus magánhálózati IP-cím hozzáadásához futtassa a következő parancsokat:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-powershell.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait. Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím.

## <a name="change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>A kiosztási módszert a hozzárendelt hálózati adapter magánhálózati IP-cím

Magánhálózati IP-cím hozzá van rendelve egy hálózati Adaptert a statikus vagy dinamikus kiosztási módszerrel oszthatók ki. Dinamikus IP-címek, amelyek korábban a leállított (felszabadított) állapotú virtuális gép elindítása után módosíthatja. Emiatt problémák, ha a virtuális gép leállított (felszabadított) állapotba az újraindítás után is az azonos IP-címet igénylő futtatja. Statikus IP-címek a virtuális gép törléséig megmaradnak. A kiosztási módszer az IP-cím módosításához futtassa a következő parancsfájlt, amely megváltoztatja a kiosztási módszer a dinamikus statikusra. Ha a kiosztási módszert a jelenlegi magánhálózati IP-cím statikus, módosítsa *statikus* való *dinamikus* előtt hajtsa végre a parancsprogramot.

```powershell
$RG = "TestRG"
$NIC_name = "testnic1"

$nic = Get-AzureRmNetworkInterface -ResourceGroupName $RG -Name $NIC_name
$nic.IpConfigurations[0].PrivateIpAllocationMethod = 'Static'
Set-AzureRmNetworkInterface -NetworkInterface $nic 
$IP = $nic.IpConfigurations[0].PrivateIpAddress

Write-Host "The allocation method is now set to"$nic.IpConfigurations[0].PrivateIpAllocationMethod"for the IP address" $IP"." -NoNewline
```

Ha nem ismeri a hálózati adapter nevét, egy erőforráscsoporton belül hálózati adapterek listáját tekintheti meg a következő parancs beírásával:

```powershell
Get-AzureRmNetworkInterface -ResourceGroupName $RG | Where-Object {$_.ProvisioningState -eq 'Succeeded'} 
```

## <a name="next-steps"></a>További lépések

Kezelésével kapcsolatos [IP-címbeállítások](virtual-network-network-interface-addresses.md).