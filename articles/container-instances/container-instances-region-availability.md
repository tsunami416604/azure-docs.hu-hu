---
title: Az Azure Container Instances erőforrás rendelkezésre állása
description: Az Azure Container Instances szolgáltatás különböző Azure-régióban a számítási és memória-erőforrások rendelkezésre állását.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554526"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Erőforrás rendelkezésre állása az Azure Container Instances szolgáltatásban az Azure-régióban

Ez a cikk részletesen az Azure Container Instances számítási és memória-erőforrások az Azure-régiók rendelkezésre állását. 

Értékek jelennek meg az üzemelő példányonként rendelkezésre álló erőforrások maximális száma egy [tárolócsoport](container-instances-container-groups.md). Értékek: jelenlegi időpontjában aktuálisak. Naprakész információkat a [szolgáltatásai](/rest/api/container-instances/listcapabilities/listcapabilities) API-t. 

> [!NOTE]
> Tárolócsoportok ezen belül létrehozott van a központi telepítési régión belül rendelkezésre. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Az ilyen üzembe helyezési hibák elhárításához, próbálkozzon az üzembe helyezni a példányokat alacsonyabb erőforrás-beállításokat, vagy egy későbbi időpontban a központi telepítés.

További információ a kvótákkal és egyéb korlátok a központi telepítések: [kvóták és korlátozások az Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Rendelkezésre állás – általános

| Hely | Operációs rendszer | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada középső régiója, USA középső RÉGIÓJA, USA keleti RÉGIÓJA 2, USA déli középső RÉGIÓJA | Linux | 4 | 16 |
| USA keleti régiója, Észak-Európa, Nyugat-Európa, USA nyugati régiója, USA 2. nyugati régiója | Linux | 4 | 14 |
| Kelet-Japán | Linux | 2 | 8 |
| Kelet-Ausztrália, Délkelet-Ázsia | Linux | 2 | 7 |
| Közép-India, Kelet-Ázsia, USA északi középső RÉGIÓJA, Dél-India | Linux | 2 | 3.5 |
| Kelet-Európa, Nyugat-Európa, USA nyugati régiója | Windows | 4 | 14 |
| Kelet-Ausztrália, közép-Kanada, közép-India, USA középső RÉGIÓJA, Kelet-Ázsia, USA keleti RÉGIÓJA 2, kelet-japán, USA északi középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Dél-India, Délkelet-Ázsia, USA 2. nyugati | Windows | 2 | 3.5 |

## <a name="availability---virtual-network-deployment-preview"></a>Rendelkezésre állás – Virtual network üzembe helyezése (előzetes verzió)

A következő régiókban és erőforrások érhetők el egy üzembe helyezett tárolócsoporthoz egy [az Azure virtual network](container-instances-vnet.md) (előzetes verzió)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Rendelkezésre állás – GPU-erőforrások (előzetes verzió)

A következő régiókban és erőforrások állnak rendelkezésre a együtt üzembe helyezett tárolócsoporthoz [GPU-erőforrások](container-instances-gpu.md) (előzetes verzió)

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>További lépések

Értesítheti a csapatot tudja, hogy szeretné-e további régiók vagy nagyobb erőforrás rendelkezésre állásáról [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

A tárolópéldány üzembe helyezésének hibaelhárításáról további információkért lásd: [Azure Container Instances üzembe helyezés hibáinak elhárítása](container-instances-troubleshooting.md).
