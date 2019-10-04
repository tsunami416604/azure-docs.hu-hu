---
title: Azure Linux VM-méretek – gyorsított számítás | Microsoft Docs
description: Az Azure-beli linuxos virtuális gépekhez elérhető különböző GPU-ra optimalizált méretek listája. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: ed8af91701a5ed77636bb86e8798981f37546f23
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082132"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-ra optimalizált virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Támogatott disztribúciók és illesztőprogramok

Az Azure N sorozatú, Linux rendszerű virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat. Az [NVIDIA GPU illesztőprogram-bővítmény](../extensions/hpccompute-gpu-linux.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. Telepítse vagy kezelje a bővítményt a Azure Portal vagy az eszközök, például az Azure CLI vagy a Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](../extensions/hpccompute-gpu-linux.md) a támogatott disztribúciók és telepítés lépéseihez. A virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuális gépek bővítményei és szolgáltatásai](../extensions/overview.md).

Ha manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, tekintse meg a következő témakört: [N-sorozat GPU-illesztőprogram beállítása Linux](n-series-driver-setup.md) rendszerhez a támogatott disztribúciók, illesztőprogramok és telepítési és ellenőrzési lépések.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Az Ubuntu NC virtuális gépeken az `Nouveau` illesztőprogramot használó X-kiszolgáló vagy más rendszerek telepítése nem ajánlott. Az NVIDIA GPU-illesztőprogramok telepítése előtt le kell tiltania `Nouveau` az illesztőprogramot.  

## <a name="other-sizes"></a>Egyéb méretek
- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések
További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.