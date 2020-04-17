---
title: Gyakori PowerShell-parancsok az Azure virtuális gépekhez
description: Gyakori PowerShell-parancsok a Windows-virtuális gépek létrehozásának és kezelésének megkezdéséhez az Azure-ban.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d08693b6b07b5a2f1fb40d0c4758ab43729bd76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456362"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Gyakran használt PowerShell-parancsok az Azure-beli virtuális gépek létrehozásához és kezeléséhez

Ez a cikk néhány Azure PowerShell-parancs, amely segítségével virtuális gépek et hozhat létre és kezelhet az Azure-előfizetésben.  Az adott parancssori kapcsolókkal és beállításokkal kapcsolatos részletesebb segítségért a Segítség *parancs segítségével*kaphat **segítséget.**

 

Ezek a változók akkor lehetnek hasznosak, ha a cikkben szereplő parancsok közül többet futtat:

- $location - A virtuális gép helye. A [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) segítségével megkeresheti az Ön számára működő [földrajzi régiót.](https://azure.microsoft.com/regions/)
- $myResourceGroup – a virtuális gépet tartalmazó erőforráscsoport neve.
- $myVM - A virtuális gép neve.

## <a name="create-a-vm---simplified"></a>Virtuális gép létrehozása – egyszerűsített

| Tevékenység | Parancs |
| ---- | ------- |
| Egyszerű virtuális gép létrehozása | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Név $myVM <BR></BR><BR></BR> A New-AzVM *egyszerűsített* paramétereket rendelkezik, ahol csak egyetlen névre van szükség. A -Name érték lesz az új virtuális gép létrehozásához szükséges összes erőforrás neve. Ennél többet is megadhat, de ez minden, ami szükséges.|
| Virtuális gép létrehozása egyéni rendszerképből | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Hely $location  <BR></BR><BR></BR>Be kell, hogy már létrehozta a saját [felügyelt lemezképet](capture-image-resource.md). A lemezkép segítségével több, azonos virtuális gépek. |



## <a name="create-a-vm-configuration"></a>Virtuálisgép-konfiguráció létrehozása

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuálisgép-konfiguráció létrehozása |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>A virtuális gép konfigurációja a virtuális gép beállításainak meghatározására vagy frissítésére szolgál. A konfiguráció inicializálása a virtuális gép nevével és [méretével](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)történik. |
| Konfigurációs beállítások hozzáadása |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -Számítógépnév $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Az operációs rendszer beállításai, beleértve a [hitelesítő adatokat](https://technet.microsoft.com/library/hh849815.aspx) is, hozzáadódnak a New-AzVMConfig használatával korábban létrehozott konfigurációs objektumhoz. |
| Hálózati adapter hozzáadása |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic. Id<BR></BR><BR></BR>A virtuális gépnek [hálózati adapterrel](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kell rendelkeznie a virtuális hálózatban való kommunikációhoz. A [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) segítségével egy meglévő hálózati illesztőobjektum ot is bekereshet. |
| Platformkép megadása |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Ajánlat "publisher_offer" -Skus "product_sku" -Verzió "legújabb"<BR></BR><BR></BR>[A képadatok](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) hozzáadódnak a Korábban a New-AzVMConfig használatával létrehozott konfigurációs objektumhoz. A parancsból visszaadott objektum csak akkor használatos, ha az operációs rendszer lemezét platformlemez használatára állítja be. |
| Virtuális gép létrehozása |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Hely $location -VM $vm<BR></BR><BR></BR>Minden erőforrás egy [erőforráscsoportban](../../azure-resource-manager/management/manage-resource-groups-powershell.md)jön létre. A parancs futtatása előtt futtassa a New-AzVMConfig, a Set-AzVMOperatingSystem, a Set-AzVMSourceImage, az Add-AzVMNetworkInterface és a Set-AzVMOSDisk parancsot. |
| Virtuális gép frissítése |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>A get-AzVM használatával szerezd be az aktuális virtuális gép konfigurációját, módosítsa a konfigurációs beállításokat a virtuális gép objektumán, majd futtassa ezt a parancsot. |

## <a name="get-information-about-vms"></a>A virtuális gépekről szóló információk

| Tevékenység | Parancs |
| ---- | ------- |
| Virtuális gépek listázása előfizetésben |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Virtuális gépek listázása erőforráscsoportban |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Az előfizetéserőforrás-csoportok listájának leéséhez használja a [Get-AzResourceGroup csoportot.](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup) |
| Virtuális gép adatainak lekérése |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Virtuális gépek kezelése
| Tevékenység | Parancs |
| --- | --- |
| Virtuális gép elindítása |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Virtuális gép leállítása |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Futó virtuális gép újraindítása |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Virtuális gép törlése |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>További lépések
* Tekintse meg a virtuális gépek létrehozásának alapvető lépéseit a Windows virtuális gép létrehozása az [Erőforrás-kezelő és a PowerShell használatával](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben.

