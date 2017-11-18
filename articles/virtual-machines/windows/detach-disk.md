---
title: "Válassza le a Windows virtuális gépek - Azure adatlemezét |} Microsoft Docs"
description: "Ismerje meg, egy data lemezt leválasztani a virtuális gép az Azure-ban a Resource Manager üzembe helyezési modellben."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 7013e7ff3cb14dcad8e3e9a926bcee771180259d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Hogyan válassza le a Windows rendszerű virtuális gép adatlemezét
Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ez a lemez eltávolítása a virtuális gépről, de ez nem távolítja el a tárolóból.

> [!WARNING]
> A lemez leválasztása esetén nem automatikusan törlődik. Ha prémium szintű Storage, a lemez tárolási költségek továbbra. További információt talál [árak és számlázás prémium szintű Storage használatakor](premium-storage.md#pricing-and-billing).
>
>

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.

## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal

1. A bal oldali menüben válassza ki a **virtuális gépek**.
2. Válassza ki a virtuális gépet, amely rendelkezik a adatlemez leválasztása, és kattintson a kívánt **leállítása** felszabadítani a virtuális Gépet.
3. Jelölje ki a virtuális gép ablaktáblán **lemezek**.
4. Felső részén a **lemezek** ablaktáblán válassza előbb **szerkesztése**.
5. Az a **lemezek** ablaktáblán, a adatlemez, amelyeket szeretne leválasztani, kattintson a jobb a ![leválasztási gomb képe](./media/detach-disk/detach.png) leválasztani gombra.
5. A lemez eltávolítása után kattintson **mentése** az ablaktábla tetején.
6. A virtuális gép ablaktáblán kattintson **áttekintése** , majd a **Start** gombra, ha a virtuális gép újraindítására a panel tetején.



A lemez a tárolóban marad, de már nincs virtuális géphez csatlakoztatva.

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell-lel adatlemez leválasztása
Ebben a példában az első parancs beolvasása a virtuális gép nevű **MyVM07** a a **RG11** erőforrás csoport használata a [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmag és tárolja a **$VirtualMachine** változó.

A második sor eltávolítja a virtuális gép a DataDisk3 nevű adatlemezt a [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk) parancsmag.

A harmadik sorban frissíti a virtuális gép állapotának használatával a [frissítés-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) parancsmag, az adatok lemez eltávolítása a folyamat befejezéséhez.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

További információkért lásd: [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Következő lépések
Ha szeretné ismét felhasználni a adatlemez, most [csatlakoztassa azt egy másik virtuális géphez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

