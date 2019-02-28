---
title: Hozzon létre egy virtuális Géphez statikus magánhálózati IP-cím – Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy virtuális gépet PowerShell-lel magánhálózati IP-cím.
services: virtual-network
documentationcenter: na
author: jimdial
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 536ca8895d130fd9273d556332830d020a485dc3
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958287"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Hozzon létre egy virtuális gépet PowerShell-lel statikus magánhálózati IP-cím

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Létrehozhat egy virtuális gépet (VM) a statikus magánhálózati IP-cím. Rendelje hozzá egy statikus magánhálózati IP-cím, nem pedig a dinamikus címet, ha azt szeretné kiválasztani, mely címet egy alhálózatról egy virtuális Géphez van rendelve. Tudjon meg többet [statikus magánhálózati IP-címek](virtual-network-ip-addresses-overview-arm.md#allocation-method). Módosítsa a statikus, dinamikus meglévő virtuális géphez rendelt magánhálózati IP-cím, vagy nyilvános IP-címek használata esetén lásd: [hozzáadása, módosítása vagy eltávolítása IP-címek](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az alábbi lépéseket is elvégezheti, a helyi számítógépről, vagy az Azure Cloud Shell használatával. Szeretné használni a helyi számítógépen, ellenőrizze, hogy a [telepített Azure PowerShell](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Válassza ki az Azure Cloud Shell használatához **Kipróbálom** bármely a következő parancs mező jobb felső sarkában található. A Cloud Shellt az Azure-bA jelentkezik.

1. Ha a Cloud Shellt használja, ugorjon a 2. Nyisson meg egy parancssori munkamenetet, és jelentkezzen az Azure-bA `Connect-AzAccount`.
2. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsot. A következő példában létrehozunk egy erőforráscsoportot az East US Azure-régióban:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Hozzon létre egy alhálózati konfigurációt és a virtuális hálózat a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) és [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) parancsokat:

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

4. Hozzon létre egy hálózati adaptert a virtuális hálózatban, és az alhálózat egy magánhálózati IP-cím hozzárendelése a hálózati adaptert a [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) és [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) parancsok:

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

5. Hozzon létre egy Virtuálisgép-konfigurációt a [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig), majd hozza létre a virtuális Gépet a [New-azvm parancsmag](/powershell/module/az.Compute/New-azVM). Amikor a rendszer kéri, adja meg egy felhasználónevet és jelszót a bejelentkezési hitelesítő adatait, a virtuális gép szolgál:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Magánhálózati IP-címbeállítások adhat hozzá az operációs rendszer, bár azt javasoljuk, nem csak olvasási után [magánhálózati IP-cím hozzáadása operációs rendszer](virtual-network-network-interface-addresses.md#private).
>
>
<a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>

> [!IMPORTANT]
> A virtuális gép elérését az internetről, nyilvános IP-cím kell rendelnie a virtuális géphez. A dinamikus magánhálózati IP-cím hozzárendelése a statikus hozzárendelés is módosíthatja. További információkért lásd: [hozzáadása vagy módosítása egy IP-címek](virtual-network-network-interface-addresses.md). Emellett ajánlott korlátozza a hálózati forgalmat a virtuális géphez társít egy hálózati biztonsági csoportot a hálózati adaptert, az alhálózaton lévő hálózati adapter létrehozott vagy mindkettőt. További információkért lásd: [hálózati biztonsági csoportok kezelése](manage-network-security-group.md).
>
>

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhat [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [magánhálózati IP-címek](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) és hozzárendelése egy [statikus magánhálózati IP-cím](virtual-network-network-interface-addresses.md#add-ip-addresses) Azure virtuális gépeken.
- További információ a létrehozásával [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépeket.
