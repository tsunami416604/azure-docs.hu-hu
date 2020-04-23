---
title: Adatlemez leválasztása Windows virtuális gépről – Azure
description: Az Azure-beli virtuális gépről az Erőforrás-kezelő telepítési modelljével leválaszthat egy adatlemezt.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: c93bb5fd3e92c6a947fe997b58207b87b2717fd5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082764"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Adatlemez leválasztása Windows rendszerű virtuális gépről

Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ezzel eltávolítja a lemezt a virtuális gépről, de nem távolítja el a tárolóból.

> [!WARNING]
> Ha leválaszt egy lemezt, az nem törlődik automatikusan. Ha előfizetett a Prémium szintű tárhelyre, továbbra is tárolási díjat kell fizetnie a lemezért. További információ: [Díjszabás és számlázás a Prémium szintű storage használata esetén.](disks-types.md#billing)

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.

 

## <a name="detach-a-data-disk-using-powershell"></a>Adatlemez leválasztása a PowerShell használatával

A PowerShell használatával *gyorsan* eltávolíthatja az adatlemezeket, de győződjön meg arról, hogy semmi sem használja aktívan a lemezt, mielőtt leválasztja azt a virtuális gépről.

Ebben a példában eltávolítjuk a **myDisk** nevű lemezt a virtuális gép **myVM-ből** a **myResourceGroup** erőforráscsoportból. Először távolítsa el a lemezt az [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) parancsmag használatával. Ezután frissíti a virtuális gép állapotát az [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) parancsmag használatával az adatlemez eltávolításának befejezéséhez.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

A lemez a tárolóban marad, de már nem csatlakozik a virtuális géphez.

## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal

A *gyorsan* eltávolíthatja az adatlemezt, de győződjön meg arról, hogy semmi sem használja aktívan a lemezt, mielőtt leválasztja azt a virtuális gépről.

1. A bal oldali menüben válassza a **Virtuális gépek**lehetőséget.
1. Válassza ki azt a virtuális gépet, amely rendelkezik a leválasztani kívánt adatlemezzel.
1. A **Beállítások** alatt válassza a **Lemezek** lehetőséget.
1. A **Lemezek** ablaktábla tetején válassza a **Szerkesztés**lehetőséget.
1. A **Lemezek** ablaktáblán, a leválasztani kívánt adatlemez jobb széléről válassza a **Leválasztás**lehetőséget.
1. A módosítások mentéséhez válassza a lap tetején a **Mentés** gombot.

A lemez a tárolóban marad, de már nem csatlakozik a virtuális géphez.

## <a name="next-steps"></a>További lépések

Ha újra fel szeretné használni az adatlemezt, egyszerűen [csatolhatja egy másik virtuális géphez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
