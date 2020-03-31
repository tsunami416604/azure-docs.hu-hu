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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471692"
---
Előzetes verzióban a megosztott lemezekkel rendelkező felügyelt lemezekre a következő korlátozások vonatkoznak:

- Jelenleg csak prémium SSD-k esetén érhető el.
- Jelenleg csak az USA nyugati középső régiójában támogatott.
- A lemezen osztozó összes virtuális gépet ugyanabban a [közelségi elhelyezési csoportban](../articles/virtual-machines/windows/proximity-placement-groups.md)kell telepíteni.
- Csak adatlemezeken engedélyezhető, operációsrendszer-lemezeken nem.
- Csak alaplemezek használhatók a Windows Server feladatátvevő fürt egyes verzióiban, további információt a [Feladatátvevő fürthardver-követelmények és a tárolási lehetőségek](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)című témakörben talál.
- A ReadOnly állomásgyorsítótár-gyorsítótárazás nem érhető `maxShares>1`el a prémium szintű SSD-k esetében.
- A rendelkezésre állási csoportok és `FaultDomainCount` a virtuálisgép-méretezési csoportok csak 1-es beállítással használhatók.
- Az Azure Backup és az Azure Site Recovery támogatása még nem érhető el.

Ha érdekli a megosztott lemezek kipróbálása, akkor [iratkozzon fel az előnézetre.](https://aka.ms/AzureSharedDiskPreviewSignUp)
