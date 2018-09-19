---
title: Biztonsági mentés és visszaállítás az Azure Database for postgresql-hez
description: További tudnivalók az automatikus biztonsági mentés és az Azure Database for PostgreSQL-kiszolgáló visszaállítása.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 707803e1f69a3146772e71ff711a48b510d8c9fc
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127567"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql"></a>Biztonsági mentés és visszaállítás az Azure Database for postgresql-hez

Azure Database for postgresql-hez automatikusan hoz létre a kiszolgáló biztonsági mentése és konfigurált felhasználó helyileg redundáns vagy georedundáns tárolóban tárolja azokat. Biztonsági másolatokat állíthatja vissza a kiszolgáló egy-időponthoz használható. Biztonsági mentés és visszaállítás bármely üzletmenet-folytonossági stratégiát alapvető részét képezik, mert azok megvédheti adatait a véletlen adatsérülések vagy -törlések.

## <a name="backups"></a>Biztonsági másolatok

Azure Database for postgresql-hez a teljes, differenciális és tranzakciónapló biztonsági mentései vesz igénybe. Ezeket a biztonsági másolatokat lehetővé teszik bármely-időponthoz a kiszolgáló visszaállítása a konfigurált biztonsági másolatok megőrzési időtartamon belül. Az alapértelmezett biztonsági másolat megőrzési idejének hét nap. Igény szerint beállíthatja, másolatot 35 napon belül. Minden biztonsági mentés vannak titkosítva, 256 bites AES-titkosítással.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

Általában a teljes biztonsági mentés hetente, a különbségi biztonsági mentések naponta kétszer fordul elő, és tranzakciónaplók biztonsági mentését öt percenként történik. Az első teljes biztonsági mentés van ütemezve, a kiszolgáló létrehozása után azonnal. A kezdeti biztonsági mentés több időt vesz igénybe a nagy helyreállított kiszolgálón. A legkorábbi pont, amely egy új kiszolgálóra vissza tudja állítani az időben az az idő, amellyel a kezdeti biztonsági mentés befejeződött.

### <a name="backup-redundancy-options"></a>Biztonsági mentés redundanciabeállításai

Azure Database for PostgreSQL rugalmasságot biztosít, és válassza a helyileg redundáns vagy georedundáns biztonsági mentési tár az általános célú és memóriahasználatra optimalizált csomagok között. Ha a biztonsági másolatai georedundáns biztonsági mentési tár, csak nem is tárolja a régiót, amelyben a kiszolgáló van tárolva, de emellett replikálva vannak egy [párosított adatközpontba](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Ez biztosítja a hatékonyabb védelmére és helyreállítására a kiszolgáló egy másik régióban egy esetleges vészhelyzet esetén. Az alapszintű csomag csak a helyileg redundáns biztonsági mentési tárhelyet kínál.

> [!IMPORTANT]
> Konfigurálása helyileg redundáns vagy a georedundáns tárolás során a kiszolgáló csak engedélyezett a biztonsági mentés létrehozása. Ha a kiszolgáló ki van építve, a biztonsági mentési tár adatredundáns tárolási mód nem módosítható.

### <a name="backup-storage-cost"></a>Biztonsági másolat tárolási költség

Azure Database for PostgreSQL biztonsági mentési tár díjmentesen biztosít tárhelyet a kiszolgáló kiépített tárolója 100 %. Ez általában egy hét napos megőrzésének alkalmas. Bármely használt biztonsági mentési tár GB-hónap kell fizetnie.

Például ha egy 250 GB-os kiszolgálót létrehozta, azok 250 GB-os biztonsági tárhelyet használhat külön díj nélkül. Ajánlatunkban 250 GB-os tárolási díjat számítunk fel.

## <a name="restore"></a>Visszaállítás

Az Azure Database for postgresql-hez a visszaállítás végrehajtása hoz létre egy új kiszolgálót az eredeti kiszolgáló biztonsági mentése.

Nincsenek elérhető visszaállítási két típusa:

- **Visszaállítási pont kötött** vagy a biztonsági másolat redundancia beállítás érhető el, és létrehoz egy új kiszolgálót az eredeti kiszolgáló ugyanabban a régióban.
- **A GEO-visszaállítás** érhető el, csak akkor, ha a kiszolgáló georedundáns tárolás konfigurált, és lehetővé teszi, hogy a kiszolgáló helyreállítása egy másik régióba.

A becsült helyreállítási idő az adatbázis maximális mérete, a tranzakciós napló méretétől, a hálózati sávszélességet és az adatbázis helyreállítása zajlik egyidejűleg ugyanabban a régióban száma több tényezőtől függ. A helyreállítási idő általában a kevesebb mint 12 óra.

> [!IMPORTANT]
> Törölt kiszolgálók **nem** állítható vissza. Ha törli a kiszolgálót, akkor a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre.

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A biztonsági másolat redundancia beállítás független, is végrehajthatja a visszaállítás bármely időpontra idő a biztonsági másolatok megőrzési időtartamon belül. Új kiszolgáló és az eredeti kiszolgálón Azure ugyanabban a régióban jön létre. Az eredeti kiszolgáló konfigurációját a tarifacsomag-létrehozást, a számítási generáció és a virtuális magok, a tárméret, a biztonsági másolat megőrzési idejének és a biztonsági mentési adatredundáns tárolási mód száma.

Visszaállítási pont kötött hasznos számos módja létezik. Ha például egy felhasználó véletlenül törli az adatokat, ha csökken egy fontos táblát vagy -adatbázist, vagy ha egy alkalmazás véletlenül felülírja a helyes adatokat rossz adatokkal egy alkalmazás valamilyen alkalmazáshiba miatt.

Szükség lehet a következő tranzakciónapló biztonsági mentését kell venni az utolsó öt percen belül a pontra visszaállítása előtt várja.

### <a name="geo-restore"></a>Georedundáns helyreállítás

Visszaállíthatja egy kiszolgáló egy másik Azure-régió a szolgáltatás elérhető, ha már konfigurálta a kiszolgáló georedundáns biztonsági mentésekhez. A GEO-visszaállítás az alapértelmezett helyreállítási beállítást esetén a kiszolgáló nem érhető el a régióban, ahol a kiszolgáló üzemel incidens miatt. Ha egy nagy méretű incidens egy elérhetetlensége régió eredményez az adatbázis-alkalmazását, egy kiszolgálóhoz az összes kiszolgáló helyreállíthatja a georedundáns biztonsági másolatokból. Ha egy biztonsági mentés készül, és ha replikálása másik régióba késleltetés van. Ez a késleltetés is lehet akár egy órát, tehát ha katasztrófa történik, is lehet másolatot az adatvesztést egy óra.

Geo-visszaállítás során a server-beállítások módosíthatók számítási generáció, a virtuális mag, a biztonsági másolat megőrzési idejének és a biztonsági mentési adatredundanciával kapcsolatos lehetőségek közé tartozik. Változó tarifacsomag (alapszintű, általános célú, vagy az optimalizált memóriájú) vagy a tároló mérete nem támogatott.

### <a name="perform-post-restore-tasks"></a>Hajtsa végre visszaállítás utáni feladatok

Helyreállítási mechanizmusok visszaállítás után végre kell hajtania a következő feladatokat beolvasni a felhasználók és alkalmazások biztonsági és:

- Ha az eredeti kiszolgálót cserélje le az új kiszolgáló hivatott, átirányítva az ügyfelek és az ügyfélalkalmazások az új kiszolgálóra
- Ellenőrizze, hogy megfelelő kiszolgálószintű tűzfalszabályok vannak érvényben a felhasználók számára
- Győződjön meg, hogy megfelelő bejelentkezések és az adatbázis-szintű engedélyeit helyen
- Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>További lépések

- Üzletmenet-folytonossági kapcsolatos további információkért tekintse meg a [üzleti folytonosság – áttekintés](concepts-business-continuity.md).
- Visszaállítását egy időpontra az Azure portal használatával, lásd: [database visszaállítása egy időpontra az Azure portal használatával](howto-restore-server-portal.md).
- Visszaállítását egy időpontra az Azure CLI használatával, lásd: [database visszaállítása egy CLI-vel](howto-restore-server-cli.md).