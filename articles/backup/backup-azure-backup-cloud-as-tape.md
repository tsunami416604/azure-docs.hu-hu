---
title: Az Azure Backup használata a szalagos infrastruktúra lecseréléséhez
description: Ismerje meg, hogy Azure Backup szolgáltatás biztosítja a szalag-szerű szemantikáját, amely lehetővé teszi biztonsági mentése és visszaállítása az adatok Azure-ban
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/10/2017
ms.author: saurse
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff30dd0e4c7cadabddbeddc38c28a773db68d8ff
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606494"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>A hosszú távú tárolás szalagon áthelyezése az Azure felhőben
Azure biztonsági mentési és a System Center Data Protection Manager-ügyfél a következőket teheti:

* Az ütemezést, amely a szervezet igényeinek legjobban adatok biztonsági mentését.
* A biztonsági mentési adatok megőrzése mellett hosszabb ideig
* Ellenőrizze a hosszú távú megőrzési részét (szalag) és nem kell Azure.

Ez a cikk azt ismerteti, hogyan ügyfelek biztonsági mentési és adatmegőrzési házirendek engedélyezéséhez. Használja a szalagokat a hosszú-távú adatmegőrzés megoldására kell most rendelkeznek egy hatékony és életképes alternatív, ez a szolgáltatás rendelkezésre állását. A szolgáltatás engedélyezve van az Azure Backup szolgáltatás legújabb kiadása (elérhető [Itt](http://aka.ms/azurebackup_agent)). A System Center DPM ügyfeleknek frissíteniük kell, legalább, a DPM 2012 R2 – UR5 az Azure Backup szolgáltatással a DPM használata előtt.

## <a name="what-is-the-backup-schedule"></a>Mi az a biztonsági mentési ütemezés?
A biztonsági mentési ütemezés a biztonsági mentés gyakoriságát jelöli. A beállítások a következő képernyőn például azt jelzi, hogy biztonsági mentést készít a napi 18: 00 és éjfélkor.

![Napi ütemezés](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Az ügyfelek is ütemezheti a heti biztonsági másolatot. Például a következő képernyő beállításait jelzi, hogy biztonsági mentés készül minden másodlagos vasárnap & szerda 9:30 -kor és 13:00:00.

![Heti ütemezés](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Mi az az adatmegőrzési?
Az adatmegőrzési Megadja azt az időtartamot, amelyre a biztonsági mentést kell tárolni. Ahelyett, hogy csak adja meg az összes biztonsági mentési pont "egyszerű policy", az ügyfelek házirendeket határozhatnak meg, a különböző megőrzési alapján, ha a biztonsági mentés használatban van. Például a biztonsági mentési pont végrehajtott naponta, ami működési helyreállítási pontjaként szolgál 90 napig őrzi meg. A naplózási célra minden negyedév végén végrehajtott biztonsági mentési pont hosszabb ideig megőrzi.

![Adatmegőrzési szabályzat](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

"Adatmegőrzési pontokat" Ebben a házirendben megadott teljes száma érték a 90 (napi pontok) + 40 (minden egy 10 éve negyedév) = 130.

## <a name="example--putting-both-together"></a>Példa – üzembe egyaránt
![A minta képernyő](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Napi adatmegőrzési**: naponta végrehajtott biztonsági másolatai hét napja.
2. **Heti adatmegőrzési**: négy hétig minden nap, éjfélkor és 18: 00 Szombat készített biztonsági másolatok megmaradnak
3. **Havi adatmegőrzési**: éjfél és 18: 00 minden hónap utolsó szombaton készített biztonsági másolatok megmaradnak 12 hónapig
4. **Éves adatmegőrzési**: minden március utolsó vasárnap éjfélkor készített biztonsági másolatok tíz éve megmaradnak

"Adatmegőrzési pontokat" teljes száma (a pont, amelyből az ügyfél az adatokat állíthatja vissza) a fenti ábrán számított az alábbiak szerint:

* két hét nap = 14 naponta pont helyreállítási pontok
* két négy hét = 8 hetente pont helyreállítási pontok
* két havonta a 12 hónappal = 24 pont helyreállítási pontok
* egy pont évi 10 év = 10 helyreállítási pontok

A helyreállítási pontok számát 56.

> [!NOTE]
> Azure biztonsági mentés nincs korlátozás a helyreállítási pontok száma.
>
>

## <a name="advanced-configuration"></a>Speciális konfiguráció
Kattintva **módosítás** az előző képernyőn a felhasználóknak kell további rugalmasságot adatmegőrzési ütemterv meghatározása.

![Módosítás](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>További lépések
Azure biztonsági mentéssel kapcsolatos további információkért lásd:

* [Az Azure Backup bemutatása](backup-introduction-to-azure-backup.md)
* [Próbálja meg az Azure biztonsági mentés](backup-try-azure-backup-in-10-mins.md)
