---
title: Erőforrás rendelkezésre állásának Azure Container Instances
description: A Azure Container Instances szolgáltatás számítási és memória-erőforrásainak rendelkezésre állása különböző Azure-régiókban.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 24edce511c2d07050db1e77edeae4e587fcd79b0
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172395"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Erőforrások rendelkezésre állása Azure Container Instances Azure-régiókban

Ez a cikk részletesen ismerteti Azure Container Instances számítási és memória-erőforrások rendelkezésre állását az Azure-régiókban. 

A megjelenített értékek a [tároló csoportok](container-instances-container-groups.md)üzembe helyezése során elérhető maximális erőforrások. Az értékek a kiadvány aktuális időpontjában jelennek meg. 

> [!NOTE]
> Az ezen erőforrás-korlátokon belül létrehozott tároló csoportok a telepítési régión belül rendelkezésre állásra vonatkoznak. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Az ilyen üzembe helyezési hibák enyhítése érdekében próbálja meg alacsonyabb erőforrás-beállításokkal üzembe helyezni a példányokat, vagy próbálja megismételni a telepítést.

További információ a központi telepítések kvótái és egyéb korlátairól: a [Azure Container instances kvótái és korlátai](container-instances-quotas.md).

## <a name="availability---general"></a>Rendelkezésre állás – általános

A következő régiók és erőforrások elérhetők a Linux és a [támogatott](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-alapú tárolók számára.

| Location | OS | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Közép-Kanada, Közép-India, Közép-USA, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, USA nyugati | Linux | 4 | 16 |
| Nyugat-Európa, USA 2. nyugati régiója | Linux | 4 | 14 |
| Kelet-Ausztrália, Kelet-Japán | Linux | 2 | 8 |
| USA északi középső régiója, Dél-India | Linux | 2 | 3.5 |
| Nyugat-Európa | Windows | 4 | 16 |
| USA keleti régiója, USA nyugati régiója | Windows | 4 | 14 |
| Kelet-Ausztrália, Közép-Kanada, Közép-India, Közép-USA, Kelet-Ázsia, USA 2. keleti régiója, Kelet-Japán, Észak-Európa, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, USA nyugati régiója 2 | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Rendelkezésre állás – Windows Server 2019 LTSC, 1809 központi telepítések (előzetes verzió)

A következő régiók és erőforrások elérhetők a Windows Server 2019-alapú tárolókkal (előzetes verzió) rendelkező tárolók csoportjai számára.

| Location | OS | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Délkelet-Ázsia, Észak-Európa, Nyugat-Európa, USA középső régiója, USA keleti régiója, USA nyugati régiója, USA 2. nyugati régiója | Windows | 4 | 16 |
| USA 2. keleti régiója | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment-preview"></a>Rendelkezésre állás – virtuális hálózat központi telepítése (előzetes verzió)

A következő régiók és erőforrások érhetők el egy Azure-beli [virtuális hálózatban](container-instances-vnet.md) (előzetes verzióban) üzembe helyezett tároló csoport számára.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Elérhetőség – GPU-erőforrások (előzetes verzió)

A következő régiók és erőforrások elérhetők a [GPU](container-instances-gpu.md) -erőforrásokkal (előzetes verzió) üzembe helyezett tárolók csoportjához.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>További lépések

Tájékoztassa a csapatot, ha további régiókat szeretne látni, vagy megnövelt erőforrás-elérhetőséget a [aka.MS/ACI/feedback](https://aka.ms/aci/feedback)címen.

A tároló-példányok telepítésével kapcsolatos hibaelhárításról lásd: [üzembe helyezési problémák elhárítása Azure Container instances](container-instances-troubleshooting.md).
