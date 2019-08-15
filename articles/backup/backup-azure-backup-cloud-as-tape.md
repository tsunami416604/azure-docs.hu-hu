---
title: Az Azure Backup használata a szalagos infrastruktúra lecseréléséhez
description: Ismerje meg, hogyan biztosíthatja az Azure-ban tárolt adatok biztonsági mentését és helyreállítását lehetővé tevő szalagos szemantikai Azure Backup?
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2017
ms.author: dacurwin
ms.openlocfilehash: 3be3a2e3355793a8d0b4fcaf0e7f62668f78f0c8
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954881"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>A hosszú távú tároló áthelyezése szalagról az Azure-felhőbe
A Azure Backup és a System Center Data Protection Manager ügyfelei a következőket tehetik:

* A szervezeti igényeknek leginkább megfelelő ütemtervek adatairól biztonsági másolatot készíthet.
* A biztonsági mentési adat hosszabb ideig tartása
* Az Azure-t a hosszú távú adatmegőrzési igényeknek (szalag helyett) egy részévé teheti.

Ez a cikk azt ismerteti, hogy az ügyfelek hogyan engedélyezhetik a biztonsági mentési és adatmegőrzési házirendeket. Azok az ügyfelek, akik szalagokat használnak a hosszú távú adatmegőrzési igények kielégítésére, mostantól hatékony és életképes alternatívát biztosítanak a szolgáltatás rendelkezésre állásával. A szolgáltatás a Azure Backup legújabb kiadásában van engedélyezve (amely [itt](https://aka.ms/azurebackup_agent)érhető el). A System Center DPM ügyfeleinek legalább DPM 2012 R2 UR5 kell frissíteniük, mielőtt a DPM szolgáltatást használják a Azure Backup szolgáltatással.

## <a name="what-is-the-backup-schedule"></a>Mi a biztonsági mentési ütemterv?
A biztonsági mentési ütemterv a biztonsági mentési művelet gyakoriságát jelzi. A következő képernyőn látható beállítások például azt jelzik, hogy a biztonsági mentések naponta, 18:00 és éjfélkor készülnek.

![Napi ütemterv](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Az ügyfelek heti biztonsági mentést is ütemezhetnek. A következő képernyőn látható beállítások például azt jelzik, hogy a biztonsági másolatok minden alternatív vasárnap &, szerdánként, 9:00 és 1 órakor.

![Heti ütemterv](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Mi az adatmegőrzési szabály?
Az adatmegőrzési szabály meghatározza azt az időtartamot, ameddig a biztonsági másolatot tárolni kell. Ahelyett, hogy az összes biztonsági mentési ponthoz megadta a "lapos házirendet", az ügyfelek eltérő adatmegőrzési házirendeket adhatnak meg a biztonsági mentés során. Például a napi biztonsági mentési pont, amely operatív helyreállítási pontként szolgál, 90 napig megmarad. Az egyes negyedévek végén végrehajtott biztonsági mentési pont naplózási célokra hosszabb ideig megmarad.

![Adatmegőrzési szabályzat](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Az ebben a házirendben megadott "adatmegőrzési pontok" teljes száma 90 (napi pont) + 40 (az egyik negyedév 10 évre) = 130.

## <a name="example--putting-both-together"></a>Példa – a kettő együtt történő elhelyezés
![Minta képernyő](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Napi adatmegőrzési szabály**: A naponta készített biztonsági mentéseket hét napig tároljuk.
2. **Heti adatmegőrzési szabály**: A minden nap éjfélkor készített biztonsági másolatok és a 18:00 óra megőrzött négy hétig
3. **Havi adatmegőrzési szabály**: Az egyes hónapok utolsó szombatján éjfélkor és 18:00 órakor készített biztonsági mentések 12 hónapig őrződnek meg
4. **Éves adatmegőrzési szabály**: Minden március utolsó szombatján éjfélkor készített biztonsági másolatok 10 évig őrződnek meg

A "megőrzési pontok" (az ügyfelek által az adatok visszaállítására szolgáló pontok) teljes száma az előző ábrán a következőképpen számítható ki:

* napi két pont, hét napig = 14 helyreállítási pont
* két pont hetente négy hétig = 8 helyreállítási pont
* havonta két pont 12 hónapig = 24 helyreállítási pont
* egy pont/év/10 év = 10 helyreállítási pont

A helyreállítási pontok teljes száma 56.

> [!NOTE]
> A Azure Backup használatával a védett példányok esetében akár 9999 helyreállítási pontot is létrehozhat. A védett példányok olyan számítógépek, kiszolgálók (fizikai vagy virtuális) vagy munkaterhelések, amelyek az Azure-ba készülnek.
>

## <a name="advanced-configuration"></a>Speciális konfiguráció
Ha az előző képernyőn a **módosítás** gombra kattint, az ügyfelek továbbra is rugalmasságot biztosítanak az adatmegőrzési ütemtervek megadásával.

![Módosítás](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>További lépések
További információ a Azure Backupról:

* [Az Azure Backup bemutatása](backup-introduction-to-azure-backup.md)
* [Azure Backup kipróbálása](backup-try-azure-backup-in-10-mins.md)
