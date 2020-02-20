---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471692"
---
Az előzetes verzióban a megosztott lemezzel rendelkező felügyelt lemezekre a következő korlátozások vonatkoznak:

- Jelenleg csak prémium SSD-k esetén érhető el.
- Jelenleg csak az USA nyugati középső régiója támogatja.
- A lemezt megosztó virtuális gépeket ugyanabban a [közelségi elhelyezési csoportban](../articles/virtual-machines/windows/proximity-placement-groups.md)kell telepíteni.
- Csak adatlemezeken engedélyezhető, operációsrendszer-lemezekkel nem.
- Csak alaplemezek használhatók a Windows Server feladatátvevő fürt egyes verzióiban, részletekért lásd: a [feladatátvételi fürtszolgáltatás hardverkövetelmények és tárolási lehetőségei](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- A írásvédett SSD-k gyorsítótárazása nem érhető el a `maxShares>1`-vel rendelkező prémium SSD-k esetén.
- A rendelkezésre állási csoportok és a virtuálisgép-méretezési csoportok csak az 1 értékkel használhatók `FaultDomainCount`.
- Azure Backup és Azure Site Recovery támogatás még nem érhető el.

Ha érdekli a megosztott lemezek kipróbálása, akkor [regisztráljon az előzetes](https://aka.ms/AzureSharedDiskPreviewSignUp)verzióra.
