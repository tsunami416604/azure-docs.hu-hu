---
title: 'Azure Premium Storage: a Windows rendszerű virtuális gépek teljesítményének kialakítása | Microsoft Docs'
description: Nagy teljesítményű alkalmazások tervezése az Azure Premium SSD Managed Disks használatával. A Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az Azure-Virtual Machines futó I/O-igényes számítási feladatokhoz.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 06ac680ef48d9602f6e7487eb9ebecee9f3ae080
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017603"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: nagy teljesítményű kialakítás
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Időnként előfordulhat, hogy a lemez teljesítményével kapcsolatos probléma valójában a hálózat szűk keresztmetszete. Ilyen helyzetekben érdemes optimalizálni a [hálózati teljesítményt](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Ha a lemez teljesítménytesztét keresi, tekintse meg a [lemez teljesítményértékelését](disks-benchmarks.md)ismertető cikket.
>
> Ha a virtuális gép támogatja a gyorsított hálózatkezelést, győződjön meg arról, hogy engedélyezve van. Ha nincs engedélyezve, a már telepített virtuális gépeken is engedélyezheti a Windows és [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) [rendszereken](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) .

Mielőtt elkezdené, ha a Premium Storage új, először olvassa el az [Azure-lemez kiválasztása a IaaS virtuális gépekhez](disks-types.md) és [méretezhetőségi célokat a prémium szintű blob Storage-fiókokhoz](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Ha a lemez teljesítménytesztét keresi, tekintse meg a [lemez teljesítményértékelését](disks-benchmarks.md)ismertető cikket.

További információ a rendelkezésre álló lemezek típusairól: [lemez típusának kiválasztása](disks-types.md)  

SQL Server felhasználók esetében olvassa el a SQL Server teljesítményére vonatkozó ajánlott eljárásokat ismertető cikket:

* [Az Azure-beli SQL Server teljesítményének bevált eljárásai Virtual Machines](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Az Azure Premium Storage a legmagasabb teljesítményt nyújtja az Azure-beli virtuális gépek SQL Server számára](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
