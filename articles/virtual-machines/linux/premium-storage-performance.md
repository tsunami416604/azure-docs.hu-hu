---
title: 'Azure Premium Storage: a Windows rendszerű virtuális gépek teljesítményének kialakítása | Microsoft Docs'
description: Nagy teljesítményű alkalmazások tervezése az Azure Premium Storage használatával. A Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az Azure-Virtual Machines futó I/O-igényes számítási feladatokhoz.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8314c3147321e7daa0e71be33c57405139294c1d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005581"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: nagy teljesítményű kialakítás
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Időnként előfordulhat, hogy a lemez teljesítményével kapcsolatos probléma valójában a hálózat szűk keresztmetszete. Ilyen helyzetekben érdemes optimalizálni a [hálózati teljesítményt](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Ha a lemez teljesítménytesztét keresi, tekintse meg a [lemez teljesítményértékelését](disks-benchmarks.md)ismertető cikket.
>
> Ha a virtuális gép támogatja a gyorsított hálózatkezelést, győződjön meg arról, hogy engedélyezve van. Ha nincs engedélyezve, a már telepített virtuális gépeken is engedélyezheti a Windows és [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) [rendszereken](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) .

Mielőtt elkezdené, ha a Premium Storage új, először olvassa el az [Azure-lemez kiválasztása a IaaS virtuális gépekhez](disks-types.md) és az [Azure Storage méretezhetősége és a teljesítmény-célkitűzések a Storage-fiókok számára](../../storage/common/storage-scalability-targets.md)című cikkből.


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Ha a lemez teljesítménytesztét keresi, tekintse meg a [lemez teljesítményértékelését](disks-benchmarks.md)ismertető cikket.

További információ a rendelkezésre álló lemezek típusairól: [lemez típusának kiválasztása](disks-types.md)  

SQL Server felhasználók esetében olvassa el a SQL Server teljesítményére vonatkozó ajánlott eljárásokat ismertető cikket:

* [Az Azure-beli SQL Server teljesítményének bevált eljárásai Virtual Machines](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Az Azure Premium Storage a legmagasabb teljesítményt nyújtja az Azure-beli virtuális gépek SQL Server számára](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)