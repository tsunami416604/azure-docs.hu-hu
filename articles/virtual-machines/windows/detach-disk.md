---
title: Adatlemez leválasztása Windows rendszerű virtuális gépről – Azure
description: Adatlemez leválasztása az Azure-beli virtuális gépről a Resource Manager-alapú üzemi modell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 93db2935fdc41787bb1820d1f8ce85ac05ef0863
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033349"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Adatlemez leválasztása egy Windows rendszerű virtuális gépről

Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ezzel eltávolítja a lemezt a virtuális gépről, de nem távolítja el a tárolóból.

> [!WARNING]
> Ha leválaszt egy lemezt, nem törlődik automatikusan. Ha prémium szintű tárterületre iratkozott fel, akkor továbbra is a lemez tárolási díjait számítjuk fel. További információkért tekintse meg a [díjszabást és a számlázást Premium Storage használatakor](disks-types.md#billing).

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.

 

## <a name="detach-a-data-disk-using-powershell"></a>Adatlemez leválasztása a PowerShell használatával

Az adatlemezeket a PowerShell *használatával is* elvégezheti, de ügyeljen arra, hogy a virtuális gép leválasztása előtt ne használja aktívan a lemezt.

Ebben a példában eltávolítjuk a **myDisk** nevű lemezt a **myResourceGroup** erőforráscsoport virtuálisgép- **myVM** . Először távolítsa el a lemezt a [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) parancsmag használatával. Ezután frissítse a virtuális gép állapotát az [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) parancsmag használatával az adatlemez eltávolítási folyamatának befejezéséhez.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

A lemez a tárolóban marad, de már nincs csatlakoztatva a virtuális géphez.

## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal

1. A bal oldali menüben válassza a **Virtual Machines**lehetőséget.
2. Válassza ki azt a virtuális gépet, amelyen a leválasztani kívánt adatlemez található, és kattintson a **Leállítás** gombra a virtuális gép lefoglalásához.
3. A virtuális gép ablaktáblán válassza a **lemezek**elemet.
4. A **lemezek** ablaktábla tetején válassza a **Szerkesztés**lehetőséget.
5. A **lemezek** ablaktáblán a leválasztani kívánt adatlemez jobb szélén kattintson a ![leválasztási gomb képe](./media/detach-disk/detach.png) Leválasztás gombra.
5. A lemez eltávolítása után kattintson a panel tetején található **Mentés** gombra.
6. A virtuális gép ablaktáblán kattintson az **Áttekintés** elemre, majd kattintson a panel tetején található **Start** gombra a virtuális gép újraindításához.

A lemez a tárolóban marad, de már nincs csatlakoztatva a virtuális géphez.

## <a name="next-steps"></a>Következő lépések

Ha újra fel szeretné használni az adatlemezt, egyszerűen [csatolhatja azt egy másik virtuális géphez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .