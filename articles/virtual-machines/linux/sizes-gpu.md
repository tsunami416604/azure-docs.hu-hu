---
title: Az Azure Linux rendszerű virtuális gép mérete – GPU |} A Microsoft Docs
description: A különböző GPU listák az Azure-ban Linux rendszerű virtuális gépekhez elérhető méretek optimalizálva. Tartalmazza a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség-sorozat méretei száma.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 435dc3a4815d9fe554ccba57a49ed9a772b53529
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904079"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-optimalizált virtuális gépek méretei

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

Linux rendszerű Azure N-sorozatú virtuális gépek a GPU-funkcióinak kihasználásához, NVIDIA GPU-illesztőprogramokat kell telepíteni. A [NVIDIA GPU illesztőprogramjának bővítmény](../extensions/hpccompute-gpu-linux.md) telepíti a megfelelő NVIDIA CUDA vagy GRID illesztőprogramok-N-sorozatú virtuális gépen. Telepítse, vagy a bővítmény az Azure portal vagy az eszközök, például az Azure CLI vagy az Azure Resource Manager-sablonok használata kezelheti. Tekintse meg a [NVIDIA GPU illesztőprogramjának bővítmény dokumentáció](../extensions/hpccompute-gpu-linux.md) a támogatott disztribúcióiról, valamint a telepítés lépéseit. Virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure virtuális gépi bővítmények és szolgáltatások](../extensions/overview.md).

Ha manuális telepítése NVIDIA GPU-illesztőprogramokat, ismertető [GPU N-sorozat illesztőinek Linux](n-series-driver-setup.md) támogatott disztribúcióiról, illesztőprogramok és a telepítés és ellenőrzés lépésein.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Ne telepítse az X kiszolgáló vagy a más rendszerekkel, amelyek használják a `Nouveau` illesztőprogram Ubuntu NC virtuális gépeken. NVIDIA GPU-illesztőprogramokat a telepítés előtt le kell tiltania a `Nouveau` illesztőprogramot.  

## <a name="other-sizes"></a>További méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációs szoftvereknél jobban](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések
Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.