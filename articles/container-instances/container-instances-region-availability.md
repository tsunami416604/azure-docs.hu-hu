---
title: Erőforrás rendelkezésre állása régiónként
description: A Azure Container Instances szolgáltatás számítási és memória-erőforrásainak rendelkezésre állása különböző Azure-régiókban.
ms.topic: article
ms.date: 12/17/2019
ms.author: danlep
ms.openlocfilehash: 9de5b08eed3aa10015813cbb4724ef4e947005fb
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888005"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Erőforrások rendelkezésre állása Azure Container Instances Azure-régiókban

Ez a cikk részletesen ismerteti Azure Container Instances számítási és memória-erőforrások rendelkezésre állását az Azure-régiókban. 

A megjelenített értékek a [tároló csoportok](container-instances-container-groups.md)üzembe helyezése során elérhető maximális erőforrások. Az értékek a kiadvány aktuális időpontjában jelennek meg. 

> [!NOTE]
> Az ezen erőforrás-korlátokon belül létrehozott tároló csoportok a telepítési régión belül rendelkezésre állásra vonatkoznak. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Az ilyen üzembe helyezési hibák enyhítése érdekében próbálja meg alacsonyabb erőforrás-beállításokkal üzembe helyezni a példányokat, vagy próbálja megismételni a telepítést.

További információ a központi telepítések kvótái és egyéb korlátairól: a [Azure Container instances kvótái és korlátai](container-instances-quotas.md).

## <a name="availability---general"></a>Rendelkezésre állás – általános

A következő régiók és erőforrások elérhetők a Linux és a [támogatott](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-alapú tárolók számára.

| Térségek | Operációs rendszer | Maximális CPU | Maximális memória (GB) |
| -------- | -- | :---: | :-----------: |
| Dél-Brazília, Közép-India, Közép-Kanada, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Nyugat-Európa, USA nyugati régiója, USA 2 | Linux | 4 | 16 |
| Kelet-Ausztrália, Kelet-Japán | Linux | 2 | 8 |
| USA északi középső régiója | Linux | 2 | 3.5 |
| Dél-Brazília, Kelet-Japán, Nyugat-Európa | Windows | 4 | 16 |
| USA keleti régiója, USA nyugati régiója | Windows | 4 | 14 |
| Kelet-Ausztrália, Közép-Kanada, Közép-India, Közép-USA, Kelet-Ázsia, USA 2. keleti régiója, északi középső régió, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, USA 2. nyugati régiója | Windows | 2 | 3.5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Rendelkezésre állás – Windows Server 2019 LTSC, 1809 központi telepítések (előzetes verzió)

A következő régiók és erőforrások elérhetők a Windows Server 2019-alapú tárolókkal (előzetes verzió) rendelkező tárolók csoportjai számára.

| Térségek | Operációs rendszer | Maximális CPU | Maximális memória (GB) |
| -------- | -- | :---: | :-----------: |
| Kelet-Ausztrália, Dél-Brazília, Közép-India, Közép-Kanada, az USA Kelet-Ázsia középső régiója, az USA keleti régiója, Kelet-Japán, északi középső régió, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Nyugat-Európa | Windows | 4 | 16 |
| USA 2. keleti régiója, USA 2. nyugati régiója | Windows | 2 | 3.5 |


## <a name="availability---virtual-network-deployment"></a>Rendelkezésre állás – virtuális hálózat központi telepítése

Az alábbi régiók és erőforrások érhetők el egy Azure-beli [virtuális hálózatban](container-instances-vnet.md)üzembe helyezett tároló csoport számára.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Elérhetőség – GPU-erőforrások (előzetes verzió)

A következő régiók és erőforrások elérhetők a [GPU-erőforrásokkal](container-instances-gpu.md) (előzetes verzió) üzembe helyezett tárolók csoportjához.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Következő lépések

Tájékoztassa a csapatot, ha további régiókat szeretne látni, vagy megnövelt erőforrás-elérhetőséget a [aka.MS/ACI/feedback](https://aka.ms/aci/feedback)címen.

A tároló-példányok telepítésével kapcsolatos hibaelhárításról lásd: [üzembe helyezési problémák elhárítása Azure Container instances](container-instances-troubleshooting.md).
