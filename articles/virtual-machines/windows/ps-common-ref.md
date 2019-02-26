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
ms.openlocfilehash: 9d0e27c2427d53554b454e0c319ce9cf180f1633
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820790"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Gyakori PowerShell-parancsok hozhat létre és kezelhet az Azure Virtual machines szolgáltatásban

Ez a cikk ismerteti, amellyel az Azure-előfizetésben virtuális gépek létrehozása és kezelése az Azure PowerShell parancsai közül néhányat.  Az adott parancssori kapcsolókkal és beállításokkal kapcsolatban részletesebb, használhatja a **Get-Help** *parancs*.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Ezeket a változókat akkor lehet hasznos, ha ebben a cikkben futó több, mint a parancsok egyikét:

- $location – a virtuális gép helyét. Használhat [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) található egy [földrajzi régió](https://azure.microsoft.com/regions/) is működik, az Ön számára.
- $myResourceGroup – a virtuális gépet tartalmazó erőforráscsoport nevét.
- $myVM – a virtuális gép nevét.

## <a name="create-a-vm---simplified"></a>A virtuális gép létrehozása – egyszerűsített

| Tevékenység | Parancs |
| ---- | ------- |
| Hozzon létre egy egyszerű virtuális Gépet | [Új-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -$myVM neve <BR></BR><BR></BR> Új-azvm parancsmag rendelkezik egy *egyszerűsített* paraméterek, ahol szükséges csak egyetlen nevet. Az érték-neve lesz a nevet az új virtuális gép létrehozásához szükséges erőforrásokat. Megadhat további, de ez az összes szükséges.|
| Virtuális gép létrehozása egyéni rendszerképből | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>Már létrehozta a saját kell [felügyelt rendszerkép](capture-image-resource.md). Lemezkép segítségével, hogy több, azonos virtuális gépek. |



## <a name="create-a-vm-configuration"></a>Hozzon létre egy Virtuálisgép-konfiguráció

| Tevékenység | Parancs |
| ---- | ------- |
| Hozzon létre egy Virtuálisgép-konfiguráció |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) – VMName $myVM - VMSize "Standard_D1_v1"<BR></BR><BR></BR>A Virtuálisgép-konfiguráció definiálásához, vagy frissítse a virtuális gép beállításait szolgál. A konfiguráció inicializálása során a virtuális gép nevét és a hozzá tartozó [mérete](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adja hozzá a konfigurációs beállítások |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Operációs rendszer beállításait, beleértve a [hitelesítő adatok](https://technet.microsoft.com/library/hh849815.aspx) hozzáadódnak a konfigurációs objektumot, amely a korábban létrehozott új-AzVMConfig használatával. |
| Hálózati adapter hozzáadása |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) - VM $vm-azonosító $ hálózati adapteren. Azonosító<BR></BR><BR></BR>Rendelkeznie kell egy virtuális Gépet egy [hálózati adapter](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) egy virtuális hálózatot a kommunikációhoz. Is [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) beolvasni egy meglévő hálózati adapter objektumot. |
| Adja meg a platformlemezkép |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[Kép információk](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adnak hozzá a konfigurációs objektumot, amely a korábban létrehozott új-AzVMConfig használatával. Ez a parancs által visszaadott objektum csak akkor használható, az operációsrendszer-lemez használandó a platformlemezkép beállításakor. |
| Virtuális gép létrehozása |[Új-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) - ResourceGroupName $myResourceGroup-hely $location – virtuális gép $vm<BR></BR><BR></BR>A létrehozott összes erőforrást egy [erőforráscsoport](../../azure-resource-manager/manage-resource-groups-powershell.md). Before you run this command, run New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface, and Set-AzVMOSDisk. |
| A virtuális gép frissítése |[Frissítés-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) - ResourceGroupName $myResourceGroup - VM $vm<BR></BR><BR></BR>Get-azvm parancsmag használatával az aktuális virtuális gép konfigurációjának lekérése, a Virtuálisgép-objektum a konfigurációs beállításokat módosítaná, és futtassa ezt a parancsot. |

## <a name="get-information-about-vms"></a>Virtuális gépek adatainak lekérése

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuális gépek felsorolása egy előfizetésben |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Egy erőforráscsoportba tartozó virtuális gépek felsorolása |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Az előfizetésében erőforráscsoportok listájának lekéréséhez használja [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Virtuális gép adatainak lekérése |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Virtuális gépek kezelése
| Tevékenység | Parancs |
| --- | --- |
| Virtuális gép elindítása |[Start-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) - ResourceGroupName $myResourceGroup-$myVM neve |
| Virtuális gép leállítása |[STOP-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) - ResourceGroupName $myResourceGroup-$myVM neve |
| Indítsa újra a futó virtuális gépek |[Újraindítás-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) - ResourceGroupName $myResourceGroup-$myVM neve |
| Virtuális gép törlése |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>További lépések
* Tekintse meg a virtuális gép létrehozásának alapvető lépéseit [Windows virtuális gép létrehozása Resource Manager és a PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

