---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008342"
---
Előzetes verzióban a megosztott lemezek engedélyezése csak a lemeztípusok egy részhalmaza számára érhető el. Jelenleg csak az ultra lemezek és a prémium szintű SSD-k engedélyezhetik a megosztott lemezeket. Minden olyan felügyelt lemezre, amelynek engedélyezve van a megosztott lemeze, a következő korlátozások vonatkoznak, lemeztípus szerint rendezve:

### <a name="ultra-disks"></a>Ultralemezek

Az ultralemezek saját külön korlátozásokkal rendelkeznek, amelyek nem kapcsolódnak a megosztott lemezekhez. Az ultralemezek korlátairól az [Azure ultralemezek használata .for ultra disks.](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)

Az ultralemezek megosztásakor a következő további korlátozások vannak:

- Jelenleg csak az USA nyugati részén támogatott.
- Jelenleg csak az Azure Resource Manager vagy SDK-támogatás.
- Csak alaplemezek használhatók a Windows Server feladatátvevő fürt egyes verzióiban, további információt a [Feladatátvevő fürthardver-követelmények és a tárolási lehetőségek](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)című témakörben talál.

### <a name="premium-ssds"></a>Prémium szintű SSD-k

- Jelenleg csak az USA nyugati középső régiójában támogatott.
- A lemezen osztozó összes virtuális gépet ugyanabban a [közelségi elhelyezési csoportban](../articles/virtual-machines/windows/proximity-placement-groups.md)kell telepíteni.
- Csak adatlemezeken engedélyezhető, operációsrendszer-lemezeken nem.
- Csak alaplemezek használhatók a Windows Server feladatátvevő fürt egyes verzióiban, további információt a [Feladatátvevő fürthardver-követelmények és a tárolási lehetőségek](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)című témakörben talál.
- A ReadOnly állomásgyorsítótár-gyorsítótárazás nem érhető `maxShares>1`el a prémium szintű SSD-k esetében.
- A rendelkezésre állási csoportok és `FaultDomainCount` a virtuálisgép-méretezési csoportok csak 1-es beállítással használhatók.
- Az Azure Backup és az Azure Site Recovery támogatása még nem érhető el.

Ha érdekli a megosztott lemezek kipróbálása, akkor [iratkozzon fel az előnézetre.](https://aka.ms/AzureSharedDiskPreviewSignUp)
