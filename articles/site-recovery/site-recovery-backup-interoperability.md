---
title: A Azure Site Recovery és a Azure Backup használatának támogatása
description: Áttekintést nyújt a Azure Site Recovery és Azure Backup együttes használatáról.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146874"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>A Site Recovery és a Azure Backup használatának támogatása

Ez a cikk a [site Recovery szolgáltatás](site-recovery-overview.md) és a [Azure Backup szolgáltatás](https://docs.microsoft.com/azure/backup/backup-overview)együttes használatának támogatását foglalja össze.

**Művelet** | **Site Recovery támogatás** | **Részletek**
--- | --- | ---
**Szolgáltatások együttes üzembe helyezése** | Támogatott | A szolgáltatások együttműködésre alkalmasak, és konfigurálhatók egymással.
**Fájl biztonsági mentése/visszaállítása** | Támogatott | Ha a biztonsági mentés és a replikáció engedélyezve van egy virtuális gépen, és biztonsági mentést végez, a fájlok a forrás-és a virtuális gépeken való visszaállítása nem jelent problémát. A replikáció állapota a szokásosnál folytatódik, a replikálás állapotának változása nélkül.
**Lemezes biztonsági mentés/visszaállítás** | Nincs jelenlegi támogatás | Ha egy biztonsági másolattal rendelkező lemezt állít vissza, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikálását.
**Virtuális gép biztonsági mentése/visszaállítása** | Nincs jelenlegi támogatás | Ha biztonsági mentést készít, vagy helyreállít egy virtuális gépet vagy virtuális gépek csoportját, le kell tiltania és újra engedélyeznie kell a virtuális gép replikálását.  


