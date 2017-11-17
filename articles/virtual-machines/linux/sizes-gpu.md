---
title: "Azure Linux virtuális gép méretének - GPU |} Microsoft Docs"
description: "A különböző GPU listák az Azure-ban a Linux virtuális gépekhez elérhető méretek optimalizált. Vcpu, adatlemezek és hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség az a sorozat-méretek számát tartalmazza."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 6650bb2620f43053836a49f39337852d94d8dbc5
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU optimalizált virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Illesztőprogram telepítése és ellenőrzési lépések, tekintse meg [N-series illesztőprogram telepítése Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* X ne telepítse kiszolgáló vagy a más rendszerekkel, amelyek használják a `Nouveau` illesztőprogram Ubuntu NC virtuális gépeken. NVIDIA GPU-illesztőprogramok a telepítés előtt le kell tiltania a `Nouveau` illesztőprogram.  

## <a name="other-sizes"></a>Más méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)

## <a name="next-steps"></a>Következő lépések
További tudnivalók [Azure számítási egység (ACU)](acu.md) segíthetnek a számítási teljesítmény összehasonlítása Azure termékváltozatok mentén.