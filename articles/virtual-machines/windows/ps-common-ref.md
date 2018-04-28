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
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 313a313764f8ba14c9661429d1f6a8463778c934
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>A létrehozása és kezelése az Azure virtuális gépek közös PowerShell-parancsok

Ez a cikk ismerteti az Azure PowerShell-parancsok hozhat létre és kezelhet a virtuális gépek Azure-előfizetése némelyike.  Segítségért parancssori kapcsolókkal és a lehetőségek részletes, használhat **Get-Help** *parancs*.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Ezek a változók akkor lehet hasznos, ha futó parancsok egynél több cikkben:

- $location – a virtuális gép helyét. Használhat [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) található egy [földrajzi régió](https://azure.microsoft.com/regions/) meg működik.
- $myResourceGroup – az a virtuális gépet tartalmazó erőforráscsoport nevét.
- $myVM – a virtuális gép nevét.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

| Tevékenység | Parancs |
| ---- | ------- |
| A Virtuálisgép-konfiguráció létrehozása |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) – VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>A Virtuálisgép-konfiguráció segítségével határozza meg, vagy a virtuális gép beállításainak frissítése. A konfigurációs inicializálva van a virtuális gép nevét és a [mérete](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Konfigurációs beállítások hozzáadása |$vm = [set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm-Windows - ComputerName $myVM-$cred - ProvisionVMAgent hitelesítőadat - EnableAutoUpdate<BR></BR><BR></BR>Operációs rendszer beállításait, beleértve a [hitelesítő adatok](https://technet.microsoft.com/library/hh849815.aspx) kerülnek be a korábban létrehozott új-AzureRmVMConfig konfigurációs objektum. |
| Hálózati adapter hozzáadása |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) - VM $vm-azonosító $ hálózati adaptert. Azonosítója<BR></BR><BR></BR>Rendelkeznie kell egy virtuális Gépet egy [hálózati illesztő](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) virtuális hálózat számára. Is [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) egy meglévő hálózati illesztő objektum beolvasása. |
| Adja meg a platformlemezkép |$vm = [set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - PublisherName "publisher_name"-"publisher_offer" ajánlat - SKU "product_sku"-"legújabb" verzió<BR></BR><BR></BR>[Kép információk](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) hozzáadódik a korábban létrehozott új-AzureRmVMConfig konfigurációs objektum. Ez a parancs által visszaadott objektum csak akkor használatos, ha úgy állítja be az operációs rendszer lemezének platformlemezkép használatára. |
| Állítsa be a platformlemezkép használandó operációsrendszer-lemez |$vm = [set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) - VM $vm-Name "myOSDisk" - VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" - CreateOption FromImage<BR></BR><BR></BR>Neve az operációsrendszer-lemez és a helyére [tárolási](../../storage/common/storage-powershell-guide-full.md) hozzáadódik a korábban létrehozott konfigurációs objektum. |
| Általános lemezkép használandó operációsrendszer-lemez beállítása |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>Az operációsrendszer-lemez, a forrás lemezkép helyét és a lemez helye a neve [tárolási](../../storage/common/storage-powershell-guide-full.md) hozzáadódik a konfigurációs objektum. |
| Az operációsrendszer-lemez egy speciális lemezképen beállítása |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Virtuális gép létrehozása |[Új AzureRmVM]() - ResourceGroupName $myResourceGroup-hely $location - VM $vm<BR></BR><BR></BR>Az összes erőforrás hozott létre egy [erőforráscsoport](../../azure-resource-manager/powershell-azure-resource-manager.md). Ez a parancs futtatása előtt futtassa a New-AzureRmVMConfig, a Set-AzureRmVMOperatingSystem, a Set-AzureRmVMSourceImage, a Add-AzureRmVMNetworkInterface és a Set-AzureRmVMOSDisk. |

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
| A virtuális gépek generalize |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) - ResourceGroupName $myResourceGroup-Name $myVM – általános<BR></BR><BR></BR>Futtassa ezt a parancsot, mielőtt elindítaná a Save-AzureRmVMImage. |
| Virtuális gép rögzítése |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>A virtuális gépnek [elő, állítsa le és általánosítva](prepare-for-upload-vhd-image.md) lemezképek létrehozására használható. Ez a parancs futtatása előtt futtassa a Set-AzureRmVm. |
| A virtuális gép frissítése |[Frissítés-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) - ResourceGroupName $myResourceGroup - VM $vm<BR></BR><BR></BR>Használja a Get-AzureRmVM aktuális Virtuálisgép-konfiguráció beolvasása, módosítsa a Virtuálisgép-objektum konfigurációs beállításait, és futtassa a parancsot. |
| Adatlemez hozzáadása egy virtuális géphez |[Adja hozzá AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) - VM $vm-Name "myDataDisk" - VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" - LUN #-gyorsítótárazás ReadWrite - DiskSizeinGB # - CreateOption üres<BR></BR><BR></BR>Get-AzureRmVM segítségével a Virtuálisgép-objektum. Adja meg a logikai egység száma és mérete a lemez. Futtassa a konfigurációs módosítások alkalmazása a virtuális gép frissítés-AzureRmVM. A lemez hozzáadott nincs inicializálva. |
| Adatlemez eltávolítása egy virtuális gépből |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Get-AzureRmVM segítségével a Virtuálisgép-objektum. Futtassa a konfigurációs módosítások alkalmazása a virtuális gép frissítés-AzureRmVM. |
| Bővítmény felvétele a virtuális gépek |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>A parancs futtatásához a megfelelő [konfigurációs adatok](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) a telepíteni kívánt bővítmény. |
| Virtuálisgép-bővítmény eltávolítása |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) - ResourceGroupName $myResourceGroup-Name "bővítménynév" – VMName $myVM |

## <a name="next-steps"></a>További lépések
* A virtuális gép létrehozása az alapvető lépéseit [erőforrás-kezelő és a PowerShell használatával Windows virtuális gép létrehozása](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

