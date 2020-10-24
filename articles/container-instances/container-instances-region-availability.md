---
title: Erőforrás rendelkezésre állása régiónként
description: A Azure Container Instances szolgáltatás számítási és memória-erőforrásainak rendelkezésre állása különböző Azure-régiókban.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 1ed3f50198c0410d9c893fe87523fa214ca03d88
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521458"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Erőforrások rendelkezésre állása Azure Container Instances Azure-régiókban

Ez a cikk részletesen ismerteti Azure Container Instances számítási, memória-és tárolási erőforrások rendelkezésre állását az Azure-régiókban és a célként megadott operációs rendszer alapján. A Azure Container Instances elérhető régiói általános listáját az [elérhető régiók](https://azure.microsoft.com/regions/services/)című részben tekintheti meg.

A megjelenített értékek a [tároló csoportok](container-instances-container-groups.md)üzembe helyezése során elérhető maximális erőforrások. Az értékek a kiadvány aktuális időpontjában jelennek meg.

> [!NOTE]
> Az ezen erőforrás-korlátokon belül létrehozott tároló csoportok a telepítési régión belül rendelkezésre állásra vonatkoznak. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Az ilyen üzembe helyezési hibák enyhítése érdekében próbálja meg alacsonyabb erőforrás-beállításokkal üzembe helyezni a példányokat, vagy próbálja megismételni a telepítést egy későbbi időpontban, vagy egy másik régióban, amely elérhető erőforrásokkal rendelkezik.

További információ a központi telepítések kvótái és egyéb korlátairól: a [Azure Container instances kvótái és korlátai](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Linux-tárolók csoportjai

A következő régiók és maximális erőforrások elérhetők a Linux-tárolókkal rendelkező tárolók számára az általános telepítések, az [Azure virtuális hálózati](container-instances-vnet.md) telepítések és a [GPU-erőforrásokkal](container-instances-gpu.md) (előzetes verzió) üzemelő példányok számára.

> [!IMPORTANT]
> Egy adott régióban a maximális erőforrások eltérőek a telepítéstől függően. Előfordulhat például, hogy egy adott régió esetében a processzor és a memória mérete eltérő lehet egy Azure-beli virtuális hálózatban, mint az általános telepítésnél. Ugyanez a régió a GPU-erőforrásokkal rendelkező üzemelő példányok esetében is eltérő értékeket tartalmazhat. Ellenőrizze a központi telepítési típust, mielőtt ellenőrzi az alábbi táblákat a régiójában lévő maximális értékekhez.

| Régió | Max CPU (Max. CPU) | Maximális memória (GB) | VNET Max CPU | VNET maximális mérete (GB) | Tárterület (GB) | GPU SKU-i (előzetes verzió) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Kelet-Ausztrália | 4 | 16 | 4 | 16 | 50 | N.A. |
| Dél-Brazília | 4 | 16 | 2 | 8 | 50 | N.A. |
| Közép-Kanada | 4 | 16 | 4 | 16 | 50 | N.A. |
| Közép-India | 4 | 16 | N.A. | N.A. | 50 | V100 |
| USA középső régiója | 4 | 16 | 4 | 16 | 50 | N.A. |
| Kelet-Ázsia | 4 | 16 | 4 | 16 | 50 | N.A. |
| USA keleti régiója | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA 2. keleti régiója | 4 | 16 | 4 | 16 | 50 | N.A. |
| Kelet-Japán | 2 | 8 | 4 | 16 | 50 | N.A. |
| Dél-Korea középső régiója | 4 | 16 | N.A. | N.A. | 50 | N.A. |
| USA északi középső régiója | 2 | 3.5 | 4 | 16 | 50 | N.A. |
| Észak-Európa | 4 | 16 | 4 | 16 | 50 | K80 |
| USA déli középső régiója | 4 | 16 | 4 | 16 | 50 | N.A. |
| Délkelet-Ázsia | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Dél-India | 4 | 16 | N.A. | N.A. | 50 | N.A. |
| Az Egyesült Királyság déli régiója | 4 | 16 | 4 | 16 | 50 | N.A. |
| USA nyugati középső régiója| 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Nyugat-Európa | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA nyugati régiója | 4 | 16 | 2 | 4 | 16| N.A. |
| USA 2. nyugati régiója | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

A következő maximális erőforrások érhetők el egy [GPU-erőforrásokkal](container-instances-gpu.md) (előzetes verzió) üzembe helyezett tároló csoport számára.

| GPU SKU-i | GPU-szám | Max CPU (Max. CPU) | Maximális memória (GB) | Tárterület (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows-tárolók csoportjai

A következő régiók és maximális erőforrások elérhetők a [támogatott és előzetes](container-instances-faq.md#what-windows-base-os-images-are-supported) verziójú Windows Server-tárolókkal rendelkező tároló-csoportok számára.

| Régió | Windows Server 2016 maximális CPU | Windows Server 2016 maximális memória (GB) | Windows Server 2019 LTSC Max CPU | Windows Server 2019 LTSC maximális memória (GB) | Tárterület (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Kelet-Ausztrália | 2 | 3.5 | 4 | 16 | 20 |
| Dél-Brazília | 4 | 16 | 4 | 16 | 20 |
| Közép-Kanada | 2 | 3.5 | 4 | 16 | 20 |
| Közép-India | 2 | 3.5 | 4 | 16 | 20 |
| USA középső régiója | 2 | 3.5 | 4 | 16 | 20 |
| Kelet-Ázsia | 2 | 3.5 | 4 | 16 | 20 |
| USA keleti régiója | 2 | 8 | 4 | 16 | 20 |
| USA 2. keleti régiója | 2 | 3.5 | 2 | 3.5 | 20 |
| Közép-Franciaország | 4 | 16 | 4 | 16 | 20 |
| Kelet-Japán | 4 | 16 | 4 | 16 | 20 |
| Dél-Korea középső régiója | 4 | 16 | 4 | 16 | 20 |
| USA északi középső régiója | 2 | 3.5 | 4 | 16 | 20 |
| Észak-Európa | 2 | 3.5 | 4 | 16 | 20 |
| USA déli középső régiója | 2 | 3.5 | 4 | 16 | 20 |
| Dél-India | 2 | 3.5 | 4 | 16 | 20 |
| Délkelet-Ázsia | 2 | 3.5 | 4 | 16 | 20 |
| Az Egyesült Királyság déli régiója | 2 | 3.5 | 4 | 16 | 20 |
| USA nyugati középső régiója | 4 | 16 | 4 | 16 | 20 |
| Nyugat-Európa | 4 | 16 | 4 | 16 | 20 |
| USA nyugati régiója | 4 | 14 | N.A. | N.A. | 20 |
| USA 2. nyugati régiója | 2 | 3.5 | 2 | 3.5 | 20 |

## <a name="next-steps"></a>Következő lépések

Tájékoztassa a csapatot, ha további régiókat szeretne látni, vagy megnövelt erőforrás-elérhetőséget a [aka.MS/ACI/feedback](https://aka.ms/aci/feedback)címen.

A tároló-példányok telepítésével kapcsolatos hibaelhárításról lásd: [üzembe helyezési problémák elhárítása Azure Container instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
