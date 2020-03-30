---
title: 'Azure Premium Storage: Tervezés linuxos virtuális gépeken | Microsoft dokumentumok'
description: Nagy teljesítményű alkalmazásokat tervezz az Azure prémium szintű SSD-vel kezelt lemezekkel. A Prémium szintű storage nagy teljesítményű, alacsony késleltetésű lemeztámogatást nyújt az Azure virtuális gépeken futó I/O-igényű számítási feladatokhoz.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267143"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Prémium szintű Azure-tárhely: tervezés a nagy teljesítmény érdekében
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Néha, mi feltűnik -hoz lenni korong előadás kérdés van valójában egy hálózat torlódás. Ilyen esetekben optimalizálnia kell a [hálózati teljesítményt.](../../virtual-network/virtual-network-optimize-network-bandwidth.md)
>
> Ha ön látszó-hoz benchmark a lemezt, lásd a cikket [benchmarking a lemez](disks-benchmarks.md).
>
> Ha a virtuális gép támogatja a gyorsított hálózati, győződjön meg arról, hogy engedélyezve van. Ha nincs engedélyezve, engedélyezheti a már telepített virtuális gépeken [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) és [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)rendszeren is.

Mielőtt elkezdené, ha még nem ért itáliai prémium szintű storage-t, először olvassa el az [Azure-lemeztípus kiválasztása iaaS virtuális gépekhez](disks-types.md) és [skálázhatósági célokat a prémium szintű lapblobstorage-fiókokhoz.](../../storage/blobs/scalability-targets-premium-page-blobs.md)


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Ha ön látszó-hoz benchmark a lemezt, lásd a cikket [benchmarking a lemez](disks-benchmarks.md).

További információ a rendelkezésre álló lemeztípusokról: [Lemeztípus kiválasztása](disks-types.md)  

SQL Server-felhasználók számára olvassa el az SQL Server teljesítményével kapcsolatos gyakorlati tanácsokról szóló cikkeket:

* [Gyakorlati tanácsok az SQL Server hez az Azure virtuális gépeken](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Az Azure Premium Storage a legnagyobb teljesítményt nyújtja az SQL Server számára az Azure VM-ben](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)