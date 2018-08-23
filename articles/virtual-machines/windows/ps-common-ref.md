---
title: Gyakori PowerShell-parancsok az Azure Virtual Machines |} A Microsoft Docs
description: Gyakori PowerShell-parancsokat az első lépésekhez, létrehozása és kezelése a Windows virtuális gépek az Azure-ban
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
ms.openlocfilehash: cb9f03ab87079ba33135840e3e7599d2e8cc95e9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "42061369"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Gyakori PowerShell-parancsok hozhat létre és kezelhet az Azure Virtual machines szolgáltatásban

Ez a cikk ismerteti, amellyel az Azure-előfizetésben virtuális gépek létrehozása és kezelése az Azure PowerShell parancsai közül néhányat.  Az adott parancssori kapcsolókkal és beállításokkal kapcsolatban részletesebb, használhatja a **Get-Help** *parancs*.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

Ezeket a változókat akkor lehet hasznos, ha ebben a cikkben futó több, mint a parancsok egyikét:

- $location – a virtuális gép helyét. Használhat [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) található egy [földrajzi régió](https://azure.microsoft.com/regions/) is működik, az Ön számára.
- $myResourceGroup – a virtuális gépet tartalmazó erőforráscsoport nevét.
- $myVM – a virtuális gép nevét.

## <a name="create-a-vm---simplified"></a>A virtuális gép létrehozása – egyszerűsített

| Tevékenység | Parancs |
| ---- | ------- |
| Hozzon létre egy egyszerű virtuális Gépet | [Új-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -$myVM neve <BR></BR><BR></BR> Új-azurermvm parancsmaghoz tartozik egy *egyszerűsített* paraméterek, ahol szükséges csak egyetlen nevet. Az érték-neve lesz a nevet az új virtuális gép létrehozásához szükséges erőforrásokat. Megadhat további, de ez az összes szükséges.|
| Virtuális gép létrehozása egyéni rendszerképből | Új-AzureRmVm - ResourceGroupName $myResourceGroup-$myVM ImageName "myImage" nevet-$location helye  <BR></BR><BR></BR>Már létrehozta a saját kell [felügyelt rendszerkép](capture-image-resource.md). Lemezkép segítségével, hogy több, azonos virtuális gépek. |



## <a name="create-a-vm-configuration"></a>Hozzon létre egy Virtuálisgép-konfiguráció

| Tevékenység | Parancs |
| ---- | ------- |
| Hozzon létre egy Virtuálisgép-konfiguráció |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) – VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>A Virtuálisgép-konfiguráció definiálásához, vagy frissítse a virtuális gép beállításait szolgál. A konfiguráció inicializálása során a virtuális gép nevét és a hozzá tartozó [mérete](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adja hozzá a konfigurációs beállítások |$vm = [set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) - VM $vm – Windows - ComputerName $myVM-$cred - ProvisionVMAgent hitelesítőadat - EnableAutoUpdate<BR></BR><BR></BR>Operációs rendszer beállításait, beleértve a [hitelesítő adatok](https://technet.microsoft.com/library/hh849815.aspx) hozzáadódnak a konfigurációs objektumot, amely a korábban létrehozott New-AzureRmVMConfig használatával. |
| Hálózati adapter hozzáadása |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/Add-AzureRmVMNetworkInterface) - VM $vm-azonosító $ hálózati adapteren. Azonosító<BR></BR><BR></BR>Rendelkeznie kell egy virtuális Gépet egy [hálózati adapter](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) egy virtuális hálózatot a kommunikációhoz. Is [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) beolvasni egy meglévő hálózati adapter objektumot. |
| Adja meg a platformlemezkép |$vm = [set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) - VM $vm - közzétevő neve "publisher_name"-"publisher_offer" ajánlat - Skus "product_sku"-"legújabb" verzió.<BR></BR><BR></BR>[Kép információk](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adnak hozzá a konfigurációs objektumot, amely a korábban létrehozott New-AzureRmVMConfig használatával. Ez a parancs által visszaadott objektum csak akkor használható, az operációsrendszer-lemez használandó a platformlemezkép beállításakor. |
| Virtuális gép létrehozása |[Új-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) - ResourceGroupName $myResourceGroup-hely $location – virtuális gép $vm<BR></BR><BR></BR>A létrehozott összes erőforrást egy [erőforráscsoport](../../azure-resource-manager/powershell-azure-resource-manager.md). A parancs futtatása előtt futtassa a New-AzureRmVMConfig, a Set-AzureRmVMOperatingSystem, a Set-AzureRmVMSourceImage, az Add-AzureRmVMNetworkInterface és a Set-AzureRmVMOSDisk. |
| A virtuális gép frissítése |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) - ResourceGroupName $myResourceGroup - VM $vm<BR></BR><BR></BR>A jelenlegi Virtuálisgép-konfigurációt, használja a Get-AzureRmVM lekérése, a Virtuálisgép-objektum konfigurációs beállításainak módosítása, és futtassa ezt a parancsot. |

## <a name="get-information-about-vms"></a>Virtuális gépek adatainak lekérése

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuális gépek felsorolása egy előfizetésben |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Egy erőforráscsoportba tartozó virtuális gépek felsorolása |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Az előfizetésében erőforráscsoportok listájának lekéréséhez használja [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Virtuális gép adatainak lekérése |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Virtuális gépek kezelése
| Tevékenység | Parancs |
| --- | --- |
| Virtuális gép elindítása |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Virtuális gép leállítása |[STOP-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) - ResourceGroupName $myResourceGroup-$myVM neve |
| Indítsa újra a futó virtuális gépek |[Restart-azurermvm parancsmaghoz](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) - ResourceGroupName $myResourceGroup-$myVM neve |
| Virtuális gép törlése |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>További lépések
* Tekintse meg a virtuális gép létrehozásának alapvető lépéseit [Windows virtuális gép létrehozása Resource Manager és a PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

