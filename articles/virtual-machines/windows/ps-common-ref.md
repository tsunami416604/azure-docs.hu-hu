---
title: Általános PowerShell-parancsok az Azure virtuális gépek |} Microsoft Docs
description: Általános PowerShell-parancsok segítségével megismerheti, létrehozása és kezelése a Windows virtuális gépek Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: ff861c21250a042191651ab4a4cffbf3928e4f26
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738564"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>A létrehozása és kezelése az Azure virtuális gépek közös PowerShell-parancsok

Ez a cikk ismerteti az Azure PowerShell-parancsok hozhat létre és kezelhet a virtuális gépek Azure-előfizetése némelyike.  Segítségért parancssori kapcsolókkal és a lehetőségek részletes, használhatja a **Get-Help** *parancs*.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Ezek a változók akkor lehet hasznos, ha futó parancsok egynél több cikkben:

- $location – a virtuális gép helyét. Használhat [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) található egy [földrajzi régió](https://azure.microsoft.com/regions/) meg működik.
- $myResourceGroup – az a virtuális gépet tartalmazó erőforráscsoport nevét.
- $myVM – a virtuális gép nevét.

## <a name="create-a-vm---simplified"></a>Hozzon létre egy Virtuálisgép - egyszerűsített

| Tevékenység | Parancs |
| ---- | ------- |
| Egy egyszerű virtuális gép létrehozása | [Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -$myVM neve <BR></BR><BR></BR> Új AzureRMVM tartozik egy *egyszerűsített* paraméterek, ha szükséges csak egyetlen nevet. Az érték a - neve lesz a nevet az új virtuális gép létrehozásához szükséges erőforrásokat. Több megadhatja, de ez szükséges.|
| Virtuális gép létrehozása egyéni rendszerképből | Új AzureRmVm - ResourceGroupName $myResourceGroup-Name "myImage" $myVM ImageName-$location helye  <BR></BR><BR></BR>Már létrehozott saját kell [felügyelt képre](capture-image-resource.md). Lemezkép segítségével több, azonos virtuális gépeket. |



## <a name="create-a-vm-configuration"></a>A Virtuálisgép-konfiguráció létrehozása

| Tevékenység | Parancs |
| ---- | ------- |
| A Virtuálisgép-konfiguráció létrehozása |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) – VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>A Virtuálisgép-konfiguráció segítségével határozza meg, vagy a virtuális gép beállításainak frissítése. A konfigurációs inicializálva van a virtuális gép nevét és a [mérete](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Konfigurációs beállítások hozzáadása |$vm = [set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-$cred - ProvisionVMAgent hitelesítőadat - EnableAutoUpdate<BR></BR><BR></BR>Operációs rendszer beállításait, beleértve a [hitelesítő adatok](https://technet.microsoft.com/library/hh849815.aspx) kerülnek be a korábban létrehozott új-AzureRmVMConfig konfigurációs objektum. |
| Hálózati adapter hozzáadása |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) - VM $vm-azonosító $ hálózati adaptert. Azonosítója<BR></BR><BR></BR>Rendelkeznie kell egy virtuális Gépet egy [hálózati illesztő](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) virtuális hálózat számára. Is [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) egy meglévő hálózati illesztő objektum beolvasása. |
| Adja meg a platformlemezkép |$vm = [set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - PublisherName "publisher_name"-"publisher_offer" ajánlat - SKU "product_sku"-"legújabb" verzió<BR></BR><BR></BR>[Kép információk](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) hozzáadódik a korábban létrehozott új-AzureRmVMConfig konfigurációs objektum. Ez a parancs által visszaadott objektum csak akkor használatos, ha úgy állítja be az operációs rendszer lemezének platformlemezkép használatára. |
| Virtuális gép létrehozása |[Új AzureRmVM]() - ResourceGroupName $myResourceGroup-hely $location - VM $vm<BR></BR><BR></BR>Az összes erőforrás hozott létre egy [erőforráscsoport](../../azure-resource-manager/powershell-azure-resource-manager.md). Ez a parancs futtatása előtt futtassa a New-AzureRmVMConfig, a Set-AzureRmVMOperatingSystem, a Set-AzureRmVMSourceImage, a Add-AzureRmVMNetworkInterface és a Set-AzureRmVMOSDisk. |
| A virtuális gép frissítése |[Frissítés-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) - ResourceGroupName $myResourceGroup - VM $vm<BR></BR><BR></BR>Használja a Get-AzureRmVM aktuális Virtuálisgép-konfiguráció beolvasása, módosítsa a Virtuálisgép-objektum konfigurációs beállításait, és futtassa a parancsot. |

## <a name="get-information-about-vms"></a>Virtuális gépek adatainak beolvasása

| Tevékenység | Parancs |
| ---- | ------- |
| Lista virtuális gépek egy előfizetésben található |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Lista virtuális gépek egy erőforráscsoportban található |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Az erőforráscsoportok listáját az előfizetéshez, amelyet [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Virtuális gép adatainak lekérése |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Virtuális gépek kezelése
| Tevékenység | Parancs |
| --- | --- |
| Virtuális gép elindítása |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Virtuális gép leállítása |[STOP-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) - ResourceGroupName $myResourceGroup-$myVM neve |
| A virtuális gép újraindítása |[Újraindítás-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) - ResourceGroupName $myResourceGroup-$myVM neve |
| Virtuális gép törlése |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>További lépések
* A virtuális gép létrehozása az alapvető lépéseit [erőforrás-kezelő és a PowerShell használatával Windows virtuális gép létrehozása](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

