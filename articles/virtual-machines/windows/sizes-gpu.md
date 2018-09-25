---
title: Az Azure Windows VM-méretek – GPU |} A Microsoft Docs
description: A különböző GPU listák az Azure-beli Windows virtuális gépekhez elérhető méretek optimalizálva. Tartalmazza a vcpu-k, az adatlemezeket és a hálózati adapterek, valamint tárolási átviteli sebesség és a hálózati sávszélesség-sorozat méretei száma.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jonbeck
ms.openlocfilehash: 222f131e92a6018b4f70c65476cc2c47fb1d1dd5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038562"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-optimalizált virtuális gépek méretei

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Windows rendszerű Azure N-sorozatú virtuális gépek a GPU-funkciókkal kihasználásához NVIDIA GPU-illesztőprogramokat kell telepíteni. A [NVIDIA GPU illesztőprogramjának bővítmény](../extensions/hpccompute-gpu-windows.md) telepíti a megfelelő NVIDIA CUDA vagy GRID illesztőprogramok-N-sorozatú virtuális gépen. Telepítse, vagy a bővítmény az Azure portal vagy az eszközök, például az Azure PowerShell vagy az Azure Resource Manager-sablonok használata kezelheti. Tekintse meg a [NVIDIA GPU illesztőprogramjának bővítmény dokumentáció](../extensions/hpccompute-gpu-windows.md) támogatott operációs rendszerek és a telepítés lépéseit. Virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure virtuális gépi bővítmények és szolgáltatások](../extensions/overview.md).

Ha manuális telepítése NVIDIA GPU-illesztőprogramokat, ismertető [GPU N-sorozat illesztőinek Windows](n-series-driver-setup.md) támogatott operációs rendszerek, az illesztőprogramok és a telepítés és ellenőrzés lépésein.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>További méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [Előző generációs szoftvereknél jobban](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések
Tudjon meg többet [Azure számítási egységek (ACU)](acu.md) Azure-termékváltozatok hasonlítsa össze a számítási teljesítményt nyújt segítséget.

