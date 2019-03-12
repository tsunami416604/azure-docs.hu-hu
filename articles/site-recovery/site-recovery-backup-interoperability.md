---
title: Az Azure Site Recovery - együttműködés a biztonsági mentési |} A Microsoft Docs
description: Egy áttekintést nyújt az Azure Site Recovery és az Azure Backup segítségével hogyan biztosítható együtt.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731866"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>A Site Recovery és a biztonsági mentési együttműködés

Ez a cikk útmutatást sikeresen használják az Azure IaaS virtuális gépek biztonsági mentését és az Azure virtuális gép vész-helyreállítási nyújt.

## <a name="azure-backup"></a>Azure Backup

Az Azure Backup védi a helyszíni kiszolgálók, virtuális gépek, virtualizált számítási feladatok, SQL Server-kiszolgálók, SharePoint-kiszolgálók és további adatokat. Az Azure Site Recovery koordinálja a, és a vész-helyreállítási kezeli az Azure virtuális gépek, a helyszíni virtuális gépek és fizikai kiszolgálók.

## <a name="azure-site-recovery"></a>Azure Site Recovery

Az Azure Backup és az Azure Site Recovery konfigurálása egy virtuális Gépet vagy virtuális gépek lehetőség. Mindkét termék olyan együttműködésre. Néhány forgatókönyvek, ahol az együttműködést a biztonsági mentés és az Azure Site Recovery között akkor válik fontossá, a következők:

### <a name="file-backuprestore"></a>Fájl biztonsági mentése/visszaállítása

Ha biztonsági mentési és replikációs egyaránt engedélyezve van, és a egy biztonsági mentés készül, nincs probléma van a forrásoldali virtuális gép vagy a virtuális gépek csoportján bármely fájl visszaállítása a. Replikáció a szokásos módon továbbra is a replikáció állapota nem változik.

### <a name="disk-backuprestore"></a>Lemezek biztonsági mentése/visszaállítása

Ha a lemez visszaállítása biztonsági másolatból majd a virtuális gép védelmének van újra engedélyezni kell.

### <a name="vm-backuprestore"></a>Virtuális gép biztonsági mentése/visszaállítása

Biztonsági mentési és helyreállítási virtuális gép vagy a csoport a virtuális gépek nem támogatott. Ellenőrizze, hogy működik, védelmet kell ismét engedélyezni kell.

**Forgatókönyv** | **Az Azure Site Recovery által támogatott?** | **Megkerülő megoldás, ha van ilyen**  
--- | --- | ---
Fájl/mappa biztonsági mentése | Igen | Nem alkalmazható
Lemezes biztonsági mentés | Jelenleg nem | Tiltsa le, és a védelem engedélyezése
Virtuális gép biztonsági mentése | Nem | Tiltsa le, és a védelem engedélyezése
