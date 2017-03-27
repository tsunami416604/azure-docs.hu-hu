---
title: "Azure gyors üzembe helyezés – Windows virtuális gép létrehozása PowerShell segítségével | Microsoft Docs"
description: "Gyorsan megismerheti a Windows rendszerű virtuális gép PowerShell segítségével való létrehozásának módját"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 9c14a009de1bfa6b3c5baa5cd0c09498760c9549
ms.lasthandoff: 03/21/2017


---

# <a name="create-a-windows-virtual-machine-with-powershell"></a>Windows rendszerű virtuális gép létrehozása PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató részletesen bemutatja, hogyan lehet egy Windows Server 2016-ot futtató Azure virtuális gépet létrehozni a PowerShell használatával. 

A kezdés előtt győződjön meg arról, hogy az Azure PowerShell-modul legújabb verziója telepítve van. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azureps-cmdlets-docs) foglalkozó témakörben talál.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Azure-erőforráscsoport létrehozása Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-networking-resources"></a>Hálózati erőforrások létrehozása

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location westeurope `
-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location westeurope `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Hozzon létre egy hálózati biztonsági csoportot és egy hálózati biztonsági csoportszabályt. A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. Ebben az esetben létrejön egy bejövő szabály a 3389-es porthoz, amely lehetővé teszi a bejövő távoli asztali kapcsolatokat.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
```

Hozzon létre egy hálózati kártyát a virtuális géphez. A hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location westeurope `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. Ebben a konfigurációban a virtuális gép üzembe helyezése során használt beállítások szerepelnek, például a virtuális gép rendszerképe, mérete és hitelesítési konfigurációja. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Hozza létre a virtuális gépet.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

Futtassa a következő parancsokat a virtuális gép nyilvános IP-címének visszaadásához.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Használja az alábbi parancsot egy távoli asztali kapcsolat létrehozásához a virtuális géppel. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>Virtuális gép törlése

Ha már nincs rá szükség, az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Szerepkörök telepítését és a tűzfal konfigurálását ismertető oktatóanyag](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[A virtuális gépek PowerShell használatával történő üzembe helyezését ismertető minták megismerése](./virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
