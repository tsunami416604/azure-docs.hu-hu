---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f6175a797b14077cafacaca1f2fd48f36e945d9e
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425152"
---
A megosztott lemezek engedélyezése csak a lemezek egy részhalmaza számára lehetséges. Jelenleg csak az ultra-lemezek és a prémium szintű SSD-k engedélyezhetik a megosztott lemezeket. Minden olyan felügyelt lemez, amelyen engedélyezve vannak a megosztott lemezek, a következő korlátozások vonatkoznak a lemez típusa szerint rendezve:

### <a name="ultra-disks"></a>Ultralemezek

Az ultra-lemezek külön listával rendelkeznek a korlátozásokról, amelyek nem kapcsolódnak a megosztott lemezekhez. Az ultra Disk korlátozásai esetében tekintse meg az [Azure Ultra Disks használatát](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)ismertető témakört.

Az ultra-lemezek megosztásakor a következő korlátozások vonatkoznak rájuk:

- Jelenleg csak Azure Resource Manager vagy SDK-támogatással rendelkezik. 
- Csak alaplemezek használhatók a Windows Server feladatátvevő fürt egyes verzióiban, részletekért lásd: a [feladatátvételi fürtszolgáltatás hardverkövetelmények és tárolási lehetőségei](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

A megosztott Ultra-lemezek az összes olyan régióban elérhetők, amelyek alapértelmezés szerint támogatják az ultra-lemezeket, és nem igénylik a használatra való regisztrációt.

### <a name="premium-ssds"></a>Prémium SSD-k

- Jelenleg csak az USA nyugati középső régiója támogatja.
- Jelenleg csak Azure Resource Manager vagy SDK-támogatással rendelkezik. 
- Csak adatlemezeken engedélyezhető, operációsrendszer-lemezekkel nem.
- A **írásvédett** SSD-k gyorsítótárazása nem érhető el a prémium szintű SSD-k esetében `maxShares>1` .
- A Disk burst szolgáltatás nem érhető el a Premium SSD-k és a esetében `maxShares>1` .
- A rendelkezésre állási csoportok és a virtuálisgép-méretezési csoportok Azure-beli megosztott lemezekkel való használata esetén a Storage-tartalék tartománynak a virtuálisgép-tartalék tartománnyal való [igazítása](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) nincs kikényszerítve a megosztott adatlemez esetében.
- A [közeli elhelyezési csoportok (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)használatakor az összes lemezt megosztó virtuális gépnek UGYANAHHOZ a PPG-hoz kell tartoznia.
- Csak alaplemezek használhatók a Windows Server feladatátvevő fürt egyes verzióiban, részletekért lásd: a [feladatátvételi fürtszolgáltatás hardverkövetelmények és tárolási lehetőségei](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Azure Backup és Azure Site Recovery támogatás még nem érhető el.

Ha érdeklik a közös prémium szintű SSD-k kipróbálása, [regisztráljon a hozzáférésre](https://aka.ms/AzureSharedDiskGASignUp).