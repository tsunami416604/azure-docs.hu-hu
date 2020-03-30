---
title: Statikus privát IP-címmel rendelkező virtuális gép létrehozása – Azure PowerShell
description: Ismerje meg, hogyan hozhat létre egy virtuális gépet egy privát IP-címmel a PowerShell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244757"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Statikus privát IP-címmel rendelkező virtuális gép létrehozása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Létrehozhat egy statikus privát IP-címmel rendelkező virtuális gépet (VM).You can create a virtual machine (VM) with a static private IP address. Rendeljen hozzá egy statikus privát IP-címet, nem pedig egy dinamikus címet, ha azt szeretné, hogy melyik cím van hozzárendelve egy virtuális géphez. További információ a [statikus privát IP-címekről.](virtual-network-ip-addresses-overview-arm.md#allocation-method) Ha egy meglévő virtuális géphez rendelt privát IP-címet dinamikusról statikusra szeretne módosítani, vagy nyilvános IP-címekkel szeretne dolgozni, olvassa el az [IP-címek hozzáadása, módosítása vagy eltávolítása című témakört.](virtual-network-network-interface-addresses.md)

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A következő lépéseket a helyi számítógépről vagy az Azure Cloud Shell használatával hajthatja végre. A helyi számítógép használatához győződjön meg arról, hogy az [Azure PowerShell telepítve](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)van. Az Azure Cloud Shell használatához válassza a **Try It (Kipróbálás a** jobb felső sarokban) lehetőséget az alábbi parancsmező jobb felső sarkában. A Cloud Shell bejelentkezik az Azure-ba.

1. Ha a Cloud Shellt használja, ugorjon a 2. Nyisson meg egy parancsmunkamenetet, és jelentkezzen be az Azure-ba a segítségével. `Connect-AzAccount`
2. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. A következő példa létrehoz egy erőforráscsoportot az USA keleti részén az Azure régióban:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Hozzon létre alhálózati konfigurációt és virtuális hálózatot a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) és a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) parancsokkal:

   ```azurepowershell-interactive
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

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Hozzon létre egy hálózati adaptert a virtuális hálózatban, és rendeljen hozzá egy privát IP-címet az alhálózatból a hálózati adapterhez a [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) és a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) parancsokkal:

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Hozzon létre egy virtuális gép konfigurációját a [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)segítségével, majd hozza létre a virtuális gép [új-AzVM.Create](/powershell/module/az.Compute/New-azVM)a VM configuration with New-AzVM Config , and then create the VM with New-AzVM . Amikor a rendszer kéri, adjon meg egy felhasználónevet és egy jelszót, amelyet a virtuális gép bejelentkezési hitelesítő adataiként kell használni:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Bár hozzáadhat privát IP-címbeállításokat az operációs rendszerhez, azt javasoljuk, hogy csak a [Privát IP-cím hozzáadása az operációs rendszerhez](virtual-network-network-interface-addresses.md#private)című elolvasása után tegye meg ezt.
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> A virtuális gép az internetről való eléréséhez hozzá kell rendelnie egy nyilvános IP-címet a virtuális géphez. Dinamikus személyes IP-címhozzárendelésstatikus hozzárendelésre is módosítható. További információt az [IP-címek hozzáadása vagy módosítása](virtual-network-network-interface-addresses.md)című témakörben talál. Emellett azt is javasoljuk, hogy korlátozza a hálózati forgalmat a virtuális gép társításával hálózati biztonsági csoport a hálózati adapter, az alhálózat, amelyben létrehozta a hálózati adapter, vagy mindkettő. További információt a [Hálózati biztonsági csoportok kezelése (Manage network security groups) (Hálózati biztonsági csoportok kezelése) (Hálózati biztonsági csoportok kezelése) (Hálózati biztonsági csoportok](manage-network-security-group.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) segítségével eltávolíthatja az erőforráscsoportot és az összes benne lévő erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

- További információ a [magánhálózati IP-címekről,](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és egy [statikus privát IP-cím](virtual-network-network-interface-addresses.md#add-ip-addresses) hozzárendelése egy Azure virtuális géphez.
- További információ a [Linux és](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [windowsos](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek létrehozásáról.
