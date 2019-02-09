---
title: Leválasztása adatlemez Windows virtuális gép – Azure |} A Microsoft Docs
description: Leválasztása adatlemez egy virtuális gépről az Azure-ban a Resource Manager üzemi modell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 750bb275ef936b3911503cd4c0f50674d3dff2d1
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981145"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>A Windows virtuális gépről adatlemez leválasztása

Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ez eltávolítja a lemezt a virtuális gépről, de ez nem távolítja el a storage-ból.

> [!WARNING]
> Ha leválaszt egy lemezt, nem törlődnek automatikusan. Ha prémium szintű storage, amelyre Ön feliratkozott, továbbra is a lemez tárolási költségeket fizetni. További információkért lásd: [árak és számlázás a Premium Storage használatakor](premium-storage.md#pricing-and-billing).
>
>

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.


[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell-lel adatlemez leválasztása

Is *gyakori elérésű* eltávolítani egy adatlemezt a PowerShell használatával, de ellenőrizze, hogy semmi sem aktívan használja a lemezt, a virtuális gép leválasztása előtt.

Ebben a példában eltávolítjuk a nevű lemez **myDisk** a virtuális gépről **myVM** a a **myResourceGroup** erőforráscsoportot. Először, távolítsa el a lemez használata a [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) parancsmagot. Ezután frissíti a virtuális gép állapotát használatával a [Update-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) parancsmagot, az adatlemez eltávolítása, a folyamat befejezéséhez.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

A lemez storage-ban marad, de már nincs csatolva egy virtuális géphez.


## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal

1. A bal oldali menüben válassza ki a **virtuális gépek**.
2. Válassza ki a virtuális gépen, amelyen az adatlemezt, leválasztja, majd kattintson a kívánt **leállítása** felszabadítani a virtuális Gépet.
3. A virtuális gép panelén válassza **lemezek**.
4. Felső részén a **lemezek** ablaktáblán válassza előbb **szerkesztése**.
5. Az a **lemezek** ablaktáblán az adatlemezt, amelyeket szeretne leválasztása, kattintson a jobb szélén a ![leválasztási gomb képe](./media/detach-disk/detach.png) Leválasztás gombra.
5. Kattintson a lemez el lett távolítva, miután **mentése** a panel felső részén.
6. A virtuális gép paneljén kattintson **áttekintése** és kattintson a **Start** gombra a virtuális gép újraindítása a panel tetején.

A lemez storage-ban marad, de már nincs csatolva egy virtuális géphez.

## <a name="next-steps"></a>További lépések
Ha szeretné újra felhasználhatja az adatlemezt, csak [csatlakoztassa azt egy másik virtuális Géphez](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

