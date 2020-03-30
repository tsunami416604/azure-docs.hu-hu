---
title: A szalagos infrastruktúra cseréje
description: Ismerje meg, hogy az Azure Backup hogyan biztosít szalagszerű szemantikát, amely lehetővé teszi az adatok biztonsági mentését és visszaállítását az Azure-ban
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: aeda1cefc84d425855c40b793f8334936541e63f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425102"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Hosszú távú tárhely áthelyezése szalagról az Azure-felhőbe

Az Azure Backup és a System Center Data Protection Manager ügyfelei:

* A szervezeti igényeknek leginkább megfelelő ütemezésekben biztonsági másolatot kell fésülnie az adatokról.
* A biztonsági mentési adatok megőrzése hosszabb ideig.
* Tegye az Azure-t a hosszú távú megőrzési igényeik részévé (szalag helyett).

Ez a cikk bemutatja, hogyan engedélyezhetik az ügyfelek a biztonsági mentési és adatmegőrzési házirendeket. Azok az ügyfelek, akik kazettákat használnak a hosszú távú megőrzési igényeik kielégítésére, most már hatékony és életképes alternatívával rendelkeznek a funkció rendelkezésre állásával. A funkció engedélyezve van az Azure Backup legújabb kiadásában (amely [itt](https://aka.ms/azurebackup_agent)érhető el). A System Center DPM-ügyfeleinek legalább a DPM 2012 R2 UR5-re kell frissíteniük, mielőtt a DPM-et az Azure Backup szolgáltatással használnák.

## <a name="what-is-the-backup-schedule"></a>Mi a biztonsági mentés ütemezése?

A biztonsági mentésütemezés a biztonsági mentési művelet gyakoriságát jelzi. A következő képernyő beállításai például azt jelzik, hogy a biztonsági mentések naponta 18:00-kor és éjfélkor készülnek.

![Napi ütemezés](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Az ügyfelek heti biztonsági mentést is ütemezhetnek. A következő képernyőn látható beállítások például azt jelzik, hogy a biztonsági mentések minden alternatív vasárnap & szerdán 9:30-kor és 1:00-kor készülnek.

![Heti ütemezés](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Mi az adatmegőrzési szabály?

Az adatmegőrzési szabály határozza meg, hogy a biztonsági mentés tárolható-e. Ahelyett, hogy csak egy "egyszerű házirendet" ad meg az összes biztonsági mentési ponthoz, az ügyfelek különböző adatmegőrzési házirendeket adhatnak meg a biztonsági mentés készítéseke alapján. Például a biztonsági mentési pont naponta, amely egy működő helyreállítási pontként szolgál, 90 napig megmarad. Az egyes negyedévek végén ellenőrzési célokra készített biztonsági mentési pont hosszabb ideig megmarad.

![Adatmegőrzési házirend](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

A szabályzatban meghatározott "megőrzési pontok" teljes száma 90 (napi pont) + 40 (10 éven keresztül negyedévente egy) = 130.

## <a name="example--putting-both-together"></a>Példa – Mindkettő összeállítása

![Mintaképernyő](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Napi adatmegőrzési szabályzat:** A naponta készített biztonsági mentések hét napig tárolódnak.
2. **Heti adatmegőrzési szabály:** Az éjfélkor és szombaton 18:00 órakor készített biztonsági mentések négy hétig maradnak meg.
3. **Havi adatmegőrzési szabály:** Minden hónap utolsó szombatján éjfélkor és 18:00 órakor készített biztonsági mentések 12 hónapig maradnak meg.
4. **Éves adatmegőrzési szabály:** Minden március utolsó szombatján éjfélkor készített biztonsági mentések 10 évig megmaradnak.

Az előző ábrán a "megőrzési pontok" (azon pontok, amelyekből az ügyfél visszaállíthatja az adatokat) teljes számát a rendszer a következőképpen számítja ki:

* napi két pont hét napon keresztül = 14 helyreállítási pont
* heti két pont négy héten át = 8 helyreállítási pont
* havi két pont 12 hónapon keresztül = 24 helyreállítási pont
* 10 évenként egy pont = 10 helyreállítási pont

A helyreállítási pontok száma összesen 56.

> [!NOTE]
> Az Azure Backup használatával védett példányonként akár 9999 helyreállítási pontot is létrehozhat. A védett példányok olyan számítógépek, (fizikai vagy virtuális) kiszolgálók vagy számítási feladatok, amelyeknek a biztonsági másolatai az Azure-ba kerülnek.
>

## <a name="advanced-configuration"></a>Speciális konfiguráció

Az előző képernyőn a **Módosítás gombra** kattintva az ügyfelek további rugalmasságot kapnak a megőrzési ütemezések megadásában.

![Módosítás](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>További lépések

Az Azure Backup ról további információt a következő témakörben talál:

* [Az Azure Backup bemutatása](backup-introduction-to-azure-backup.md)
* [Az Azure Backup kipróbálása](backup-try-azure-backup-in-10-mins.md)
