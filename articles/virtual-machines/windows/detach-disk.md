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
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: bbbd31313db44d32a829e9e4c6c9b5fd9c0e533e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Hogyan válassza le a Windows rendszerű virtuális gép adatlemezét
Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ez a lemez eltávolítása a virtuális gépről, de ez nem távolítja el a tárolóból.

> [!WARNING]
> A lemez leválasztása esetén nem automatikusan törlődik. Ha prémium szintű Storage, a lemez tárolási költségek továbbra. További információt talál [árak és számlázás prémium szintű Storage használatakor](premium-storage.md#pricing-and-billing).
>
>

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.

## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal
1. Válassza ki a portál hub **virtuális gépek**.
2. Válassza ki a virtuális gépet, amely rendelkezik a adatlemez leválasztása, és kattintson a kívánt **leállítása** felszabadítani a virtuális Gépet.
3. Válassza ki a virtuális gépek paneljét **lemezek**.
4. Felső részén a **lemezek** panelen válassza **szerkesztése**.
5. Az a **lemezek** panelen a adatlemez, amelyeket szeretne leválasztani, kattintson a jobb a ![leválasztási gomb képe](./media/detach-disk/detach.png) leválasztani gombra.
5. A lemez eltávolítása után a panel tetején kattintson a Mentés gombra.
6. A virtuális gép paneljén kattintson **áttekintése** , majd a **Start** gombra a virtuális gép újraindítására a panel tetején.



A lemez a tárolóban marad, de már nincs virtuális géphez csatlakoztatva.

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell-lel adatlemez leválasztása
Ebben a példában az első parancs beolvasása a virtuális gép nevű **MyVM07** a a **RG11** erőforráscsoport, a Get-AzureRmVM parancsmaggal. A parancs tárolja a virtuális gép a **$VirtualMachine** változó.

A második parancs eltávolítja a adatlemez DataDisk3 nevű a virtuális gépről.

A záró parancs frissíti a befejezéséhez a adatlemez eltávolításával a virtuális gép állapotát.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```

További információkért lásd: [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Következő lépések
Ha szeretné ismét felhasználni a adatlemez, most [csatlakoztassa azt egy másik virtuális géphez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

