---
title: Az Azure Backup használata a szalagos infrastruktúra lecseréléséhez
description: Bemutatjuk, miként Azure Backup nyújt szalaghoz hasonló szemantikát, ami lehetővé teszi a biztonsági mentése és visszaállítása az adatokat az Azure-ban
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/10/2017
ms.author: saurse
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59236774f98af927082c78f4b75a1f5880a7cac4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259604"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Szalag a hosszú távú tárolás áthelyezése az Azure-felhőben
Azure Backup és a System Center Data Protection Manager-ügyfelek a következőket teheti:

* Ütemezések, amely a szervezet igényeinek leginkább az adatok biztonsági másolatát.
* A biztonsági másolatok adatainak megőrzése hosszabb ideig
* Győződjön meg róla, Azure, a hosszú távú megőrzés részét kielégítése (szalag).

Ez a cikk bemutatja, hogyan engedélyezheti a felhasználók a biztonsági mentési és adatmegőrzési szabályzatok. Ügyfeleink, akik szalagokat a hosszú-távú adatmegőrzés megoldása érdekében kell most már rendelkezik egy hatékony és működőképes alternatívája az Ez a szolgáltatás rendelkezésre állását. A szolgáltatás engedélyezve van a legújabb kiadása az Azure Backup (elérhető [Itt](https://aka.ms/azurebackup_agent)). A System Center DPM ügyfeleknek frissíteniük kell a, legalább, a DPM 2012 R2 – UR5 az Azure Backup szolgáltatással a DPM használata előtt.

## <a name="what-is-the-backup-schedule"></a>Mi az a biztonsági mentés ütemezése?
A biztonsági mentési ütemezés azt jelzi, hogy a biztonsági mentés gyakoriságát. Például a beállításokat az alábbi képernyőn azt jelzik, hogy biztonsági mentések menetrendjét naponta este 6 órára, és éjfél.

![Napi ütemezés](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Ügyfelek is ütemezheti a heti biztonsági másolatok. Például a beállításokat az alábbi képernyőn adja meg, hogy biztonsági másolatokat készít minden másodlagos vasárnap & szerda 9:30 -kor és 1:00 -kor.

![Heti ütemezés](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Mi az a megtartási házirendben?
A megőrzési házirend megadja az időtartamot, amelynek a biztonsági mentést kell tárolni. Helyett mindössze az összes biztonsági mentési ponthoz "lapos házirendet", az ügyfelek alapján, ha a biztonsági mentés készül a különböző megőrzési házirendeket adhat meg. Például a biztonsági mentési pontok napi, hozott műveleti helyreállítási pontjaként szolgál, amely 90 napig őrződnek meg. A biztonsági mentési pont minden negyedév naplózási célra időpontokban hosszabb ideig megőrzi.

![Adatmegőrzési szabályzat](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Ebben a házirendben megadott "adatmegőrzési pontokat" teljes száma a 90 (napi pontok) + 40 (egyet a 10 évig negyedév) = 130.

## <a name="example--putting-both-together"></a>Példa – bármik lehetnek, mindkettő
![Mintaképernyő](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Napi adatmegőrzési**: naponta készített biztonsági másolatok hét napig tárolja.
2. **Zabály hetenkénti megőrzéshez**: négy hétig minden nap, éjfélkor és 18 Órakor szombat készített biztonsági másolatok megmaradnak
3. **Y havonkénti megőrzéshez**: minden hónap utolsó szombat 6 óra és éjfél mentésekre megmaradnak, 12 hónapig
4. **Abály évenkénti megőrzéshez**: 10 évig minden márciusának utolsó szombaton éjfélkor készített biztonsági másolatok megmaradnak

"Adatmegőrzési pontokat" teljes száma (a pont, amelyről a vásárlói adatokat állíthatja) a fenti ábrán az kiszámítása a következőképpen:

* két hét nap = 14 napi pont helyreállítási pontok
* két négy héttel = 8 hetente pont helyreállítási pontok
* két havonta a 12 hónapos = 24 pont helyreállítási pontok
* egy pont / év / 10 év = 10 helyreállítási pontok

A helyreállítási pontok teljes száma: 56.

> [!NOTE]
> Az Azure backup nincs korlátozás a helyreállítási pontok száma.
>
>

## <a name="advanced-configuration"></a>Speciális konfiguráció
Kattintva **módosítás** az előző képernyőn az ügyfél rendelkezik további rugalmasságot adatmegőrzési ütemterv meghatározása.

![Módosítás](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>További lépések
Az Azure Backuppal kapcsolatos további információkért lásd:

* [Az Azure Backup bemutatása](backup-introduction-to-azure-backup.md)
* [Az Azure Backup kipróbálása](backup-try-azure-backup-in-10-mins.md)
