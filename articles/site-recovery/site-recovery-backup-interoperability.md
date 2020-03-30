---
title: Az Azure Site Recovery azure backup szolgáltatással való használatának támogatása
description: Áttekintést nyújt arról, hogyan használható együtt az Azure Site Recovery és az Azure Backup.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376218"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>A Site Recovery azure backup szolgáltatással való használatának támogatása

Ez a cikk összefoglalja a [Site Recovery szolgáltatás](site-recovery-overview.md) és az Azure Backup [szolgáltatás](https://docs.microsoft.com/azure/backup/backup-overview)használatát.

**Művelet** | **Site Recovery támogatása** | **Részletek**
--- | --- | ---
**Szolgáltatások együttes üzembe helyezése** | Támogatott | A szolgáltatások interoperábilisak, és együtt konfigurálhatók.
**Fájl biztonsági mentése/visszaállítása** | Támogatott | Ha a virtuális gép biztonsági mentése és a replikáció engedélyezve van, és biztonsági mentések készülnek, nincs probléma a forrásoldali virtuális gépeken vagy virtuális gépek csoportjában lévő fájlok visszaállításában. A replikáció a szokásos módon folytatódik, a replikáció állapotának megváltoztatása nélkül.
**Lemez visszaállítása** | Nincs jelenlegi támogatás | Ha visszaállít egy biztonsági másolat lemezt, újra le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikációját.
**Virtuális gép visszaállítása** | Nincs jelenlegi támogatás | Ha visszaállít egy virtuális gépet vagy virtuális gépek csoportját, le kell tiltania, és újra engedélyeznie kell a virtuális gép replikációját.  


