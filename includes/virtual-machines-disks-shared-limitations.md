---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ce964ac197fbff64bbb7cc36e8c2bf762f93663f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84337350"
---
Az előzetes verzióban a megosztott lemezek engedélyezése csak a lemezek egy részhalmaza számára lehetséges. Jelenleg csak az ultra-lemezek és a prémium szintű SSD-k engedélyezhetik a megosztott lemezeket. Minden olyan felügyelt lemez, amelyen engedélyezve vannak a megosztott lemezek, a következő korlátozások vonatkoznak a lemez típusa szerint rendezve:

### <a name="ultra-disks"></a>Ultralemezek

Az ultra-lemezek külön listával rendelkeznek a korlátozásokról, amelyek nem kapcsolódnak a megosztott lemezekhez. Az ultra Disk korlátozásai esetében tekintse meg az [Azure Ultra Disks használatát](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)ismertető témakört.

Az ultra-lemezek megosztásakor a következő korlátozások vonatkoznak rájuk:

- Jelenleg csak Azure Resource Manager vagy SDK-támogatással rendelkezik.
- Csak alaplemezek használhatók a Windows Server feladatátvevő fürt egyes verzióiban, részletekért lásd: a [feladatátvételi fürtszolgáltatás hardverkövetelmények és tárolási lehetőségei](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Prémium szintű SSD-k

- Jelenleg csak az USA nyugati középső régiója támogatja.
- A lemezt megosztó virtuális gépeket ugyanabban a [közelségi elhelyezési csoportban](../articles/virtual-machines/windows/proximity-placement-groups.md)kell telepíteni.
- Csak adatlemezeken engedélyezhető, operációsrendszer-lemezekkel nem.
- Csak alaplemezek használhatók a Windows Server feladatátvevő fürt egyes verzióiban, részletekért lásd: a [feladatátvételi fürtszolgáltatás hardverkövetelmények és tárolási lehetőségei](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- A írásvédett SSD-k gyorsítótárazása nem érhető el a prémium szintű SSD-k esetében `maxShares>1` .
- A rendelkezésre állási csoportok és a virtuálisgép-méretezési csoportok csak `FaultDomainCount` 1 értékkel használhatók.
- Azure Backup és Azure Site Recovery támogatás még nem érhető el.

Ha érdekli a megosztott lemezek kipróbálása, akkor [regisztráljon az előzetes](https://aka.ms/AzureSharedDiskPreviewSignUp)verzióra.
