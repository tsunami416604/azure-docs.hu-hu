---
title: Az Azure Container Instances erőforrás rendelkezésre állása
description: Az Azure Container Instances szolgáltatás különböző Azure-régióban a számítási és memória-erőforrások rendelkezésre állását.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 64b60178413e470cc7fe9b3991c6fc29b5a0f860
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794299"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Erőforrás rendelkezésre állása az Azure Container Instances szolgáltatásban az Azure-régióban

Ez a cikk részletesen az Azure Container Instances számítási és memória-erőforrások az Azure-régiók rendelkezésre állását. 

Értékek jelennek meg az üzemelő példányonként rendelkezésre álló erőforrások maximális száma egy [tárolócsoport](container-instances-container-groups.md). Értékek: jelenlegi időpontjában aktuálisak. 

> [!NOTE]
> Tárolócsoportok ezen belül létrehozott van a központi telepítési régión belül rendelkezésre. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Az ilyen üzembe helyezési hibák elhárításához, próbálkozzon az üzembe helyezni a példányokat alacsonyabb erőforrás-beállításokat, vagy egy későbbi időpontban a központi telepítés.

További információ a kvótákkal és egyéb korlátok a központi telepítések: [kvóták és korlátozások az Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Rendelkezésre állás – általános

A következő régiók és erőforrások állnak rendelkezésre a Linux rendszerű tárolócsoportok és [támogatott](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-alapú tárolókhoz.

| Location egység | Operációs rendszer | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Közép-Kanada, közép-India, USA középső RÉGIÓJA, Kelet-Ázsia, USA keleti RÉGIÓJA, USA 2. keleti régiója, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsia, Egyesült Királyság déli régiója, USA nyugati RÉGIÓJA | Linux | 4 | 16 |
| Nyugat-Európa, USA 2. nyugati | Linux | 4 | 14 |
| Kelet-Ausztrália, kelet-japán | Linux | 2 | 8 |
| USA északi középső RÉGIÓJA, Dél-India | Linux | 2 | 3.5 |
| Nyugat-Európa | Windows | 4 | 16 |
| USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA | Windows | 4 | 14 |
| Kelet-Ausztrália, közép-Kanada, közép-India, USA középső RÉGIÓJA, Kelet-Ázsia, USA keleti RÉGIÓJA 2, kelet-japán, USA északi középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, USA 2. nyugati | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Rendelkezésre állás – a Windows Server 2019 LTSC 1809 központi telepítések (előzetes verzió)

Tárolócsoportok a Windows Server a 2019-alapú tárolókkal (előzetes verzió) a következő régiók és erőforrások elérhetők.

| Location egység | Operációs rendszer | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Délkelet-Ázsiában, Észak-Európa, Nyugat-Európa, USA középső RÉGIÓJA, USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA, USA 2. nyugati | Windows | 4 | 16 |
| USA 2. keleti régiója | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment-preview"></a>Rendelkezésre állás – Virtual network üzembe helyezése (előzetes verzió)

A következő régiókban és erőforrások érhetők el egy üzembe helyezett tárolócsoporthoz egy [az Azure virtual network](container-instances-vnet.md) (előzetes verzió).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Rendelkezésre állás – GPU-erőforrások (előzetes verzió)

A következő régiókban és erőforrások állnak rendelkezésre a együtt üzembe helyezett tárolócsoporthoz [GPU-erőforrások](container-instances-gpu.md) (előzetes verzió).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>További lépések

Értesítheti a csapatot tudja, hogy szeretné-e további régiók vagy nagyobb erőforrás rendelkezésre állásáról [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

A tárolópéldány üzembe helyezésének hibaelhárításáról további információkért lásd: [Azure Container Instances üzembe helyezés hibáinak elhárítása](container-instances-troubleshooting.md).
