---
title: Támogatás az Azure Site Recovery használatával az Azure Backup szolgáltatással |} A Microsoft Docs
description: Egy áttekintést nyújt az Azure Site Recovery és az Azure Backup segítségével hogyan biztosítható együtt.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035775"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Site Recovery használatával az Azure Backup szolgáltatással támogatása

Ez a cikk összegzi használatának támogatása a [Site Recovery szolgáltatás](site-recovery-overview.md) együtt a [Azure Backup szolgáltatás](https://docs.microsoft.com/azure/backup/backup-overview).

**Művelet** | **Site Recovery támogatási** | **Részletek**
--- | --- | ---
**Szolgáltatások együttes telepítését** | Támogatott | Szolgáltatások együttműködésre, és együttesen is konfigurálható.
**Fájl biztonsági mentése/visszaállítása** | Támogatott | Ha egy virtuális gép esetében engedélyezve vannak a biztonsági mentési és replikációs és biztonsági másolatokat készít, nincs probléma van a forrásoldali virtuális gépeket, vagy a virtuális gépek csoportján fájlok visszaállítása a. Replikáció a szokásos módon folytatja az replikációs állapota nem változik.
**Lemezek biztonsági mentése/visszaállítása** | Nincs jelenlegi támogatás | Ha a biztonsági másolat a lemez visszaállításához ki kell tiltsa le, és újra a virtuális gép replikálását újraengedélyezni.
**Virtuális gép biztonsági mentése/visszaállítása** | Nincs jelenlegi támogatás | Ha biztonsági mentése vagy visszaállítása egy virtuális gép vagy a virtuális gépek csoportján, tiltsa le, és a virtuális gép újraengedélyezni szüksége.  


