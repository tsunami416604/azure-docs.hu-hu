---
title: 'Azure Premium Storage: Windows virtuális gépeken teljesítményű rendszer tervezése |} A Microsoft Docs'
description: Az Azure Premium Storage nagy teljesítményű alkalmazások tervezéséhez. A Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatás I/O-igényes számítási feladatokhoz az Azure Virtual machines szolgáltatásban futó kínál.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c6de3144a87a5bfad38e1b33148f292b26c0f181
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658250"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Egyes esetekben egy lemez teljesítményprobléma tűnik ténylegesen hálózati szűk keresztmetszeteket. Ezekben a helyzetekben, optimalizálja a [hálózati teljesítményt](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Ha a lemezre a benchmark szeretne, tekintse meg a cikket a [egy lemezt a teljesítménytesztek](disks-benchmarks.md).
>
> Ha a virtuális gép támogatja a gyorsított hálózatkezelés, győződjön meg arról, hogy engedélyezve van. Ha nincs engedélyezve, engedélyezheti a már üzembe helyezett virtuális gépeken is [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) és [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Mielőtt elkezdené, ha új prémium szintű Storage, először olvassa el a [IaaS virtuális gépekhez egy Azure-lemez típusának kiválasztása](disks-types.md) és [tárfiókok az Azure Storage méretezhetőségi és teljesítménycéljai](../../storage/common/storage-scalability-targets.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
