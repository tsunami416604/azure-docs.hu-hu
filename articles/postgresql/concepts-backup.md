---
title: Biztonsági mentés és visszaállítás az Azure-adatbázisban a PostgreSQL
description: Információ az automatikus biztonsági mentés és az Azure-adatbázis PostgreSQL-kiszolgáló visszaállításához.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0f7ec38d2c271ebaa15e681a71eb32be7151921f
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="backup-and-restore-in-azure-database-for-postgresql"></a>Biztonsági mentés és visszaállítás az Azure-adatbázisban a PostgreSQL

Azure PostgreSQL-adatbázishoz automatikusan hoz létre a biztonsági mentések és konfigurált felhasználói helyileg redundáns vagy georedundáns tárolás-ban tárolja azokat. Biztonsági mentések segítségével visszaállíthatja a kiszolgáló egy-időpontban. Biztonsági mentés és visszaállítás bármilyen folytonos üzletmenetre vonatkozó stratégiája alapvető részét képezik, mert ezek az adatok védelméhez véletlen sérülése vagy törlése.

## <a name="backups"></a>Biztonsági másolatok

Azure-adatbázis PostgreSQL teljes különbségi és a tranzakciónapló biztonsági mentései vesz igénybe. Ezek a biztonsági másolatok lehetővé teszi bármely-időpontban a konfigurált biztonsági mentési megőrzési időtartamon belül a kiszolgáló visszaállításához. A biztonsági mentés megőrzési idő érték hét nap. Igény szerint konfigurálható mentése 35 nap. Minden biztonsági mentés titkosítása AES 256 bites titkosítás használata.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

Általában teljes biztonsági mentés hetente, kerül sor különbözeti biztonsági mentések naponta kétszer fordul elő, és a tranzakciónapló biztonsági mentései fordulhat elő, ötpercenként. Az első teljes biztonsági mentés van ütemezve, közvetlenül az után a kiszolgáló akkor jön létre. A kezdeti biztonsági másolatot több időt vesz igénybe egy nagy visszaállított kiszolgálón. A legkorábbi pont időben, hogy az új kiszolgáló vissza tudja állítani az az idő, ahol a kezdeti teljes biztonsági mentés befejeződött.

### <a name="backup-redundancy-options"></a>Biztonsági mentési redundancia beállítások

Azure-adatbázis PostgreSQL rugalmasságot biztosít, és az általános célú és Memóriaoptimalizált szinteket helyileg redundáns vagy georedundáns biztonsági másolatok tárolásának választhat. Ha a biztonsági másolatai georedundáns biztonsági másolatok tárolásának, nem csak tárolásuk abban a régióban, amelyben a kiszolgáló üzemelteti, de is replikálódnak a [párosított adatközpont](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Ez biztosítja, hatékonyabb védelmére és lehetőség, hogy a kiszolgáló egy másik régióban legyen katasztrófahelyzet esetén. Az alapvető csak kínál, helyileg redundáns biztonságimásolat-tároláshoz.

> [!IMPORTANT]
> Konfigurálásával helyileg redundáns vagy georedundáns tárolás során a kiszolgáló csak engedélyezett a biztonsági mentés létrehozása. Miután a kiszolgáló ki van építve, a biztonsági másolatok tárolásának redundancia beállítás nem módosítható.

### <a name="backup-storage-cost"></a>Biztonsági másolatok tárolási költségek

Azure PostgreSQL-adatbázishoz létesített kiszolgáló tárhelyét legfeljebb 100 %-os biztonsági mentési tároló minden további költség nélkül biztosít. Ez általában a biztonsági másolatok megőrzésének hétnapos alkalmas. Minden további, a használt biztonsági mentési tárterület GB-hónap fel van töltve.

Például ha ellátta 250 GB tartalmazó kiszolgálóra, akkor a biztonsági másolatok tárolásának 250 GB használatáért nem kell külön fizetni. Amelyek átlépik ezt a 250 GB tárhely fel van töltve.

## <a name="restore"></a>Visszaállítás

Azure-adatbázisban PostgreSQL a visszaállítási folyamat végrehajtása hoz létre egy új kiszolgálót az eredeti kiszolgáló biztonsági másolatból.

Kétféle visszaállítási érhetők el:

- **Pont időponthoz kötött visszaállítás** bármelyik biztonsági mentési redundancia beállítás érhető el, és létrehoz egy új kiszolgálót az eredeti kiszolgáló ugyanabban a régióban.
- **Georedundáns helyreállítás** áll rendelkezésre, csak akkor, ha a kiszolgálónak a georedundáns tárolás konfigurált, és lehetővé teszi, hogy a kiszolgáló helyreállítása egy másik régióban.

A helyreállítási becsült időtartama többek között az adatbázis mérete, a tranzakciós napló mérete, a hálózati sávszélesség, és az összes-adatbázisok helyreállítása ugyanabban a régióban egy időben számos tényezőtől függ. A helyreállítási idő az általában 12 óránál kevesebb.

> [!IMPORTANT]
> Ha törli a kiszolgálót, akkor a kiszolgálóhoz tartozó összes adatbázis is törlődnek, és nem állítható helyre. A Törölt kiszolgáló nem tudja visszaállítani.

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A biztonsági mentési redundancia beállítás független, végezheti el a visszaállítás bármely időben a biztonsági mentés megőrzési időn belül. Új kiszolgáló és az eredeti kiszolgáló Azure ugyanabban a régióban jön létre. Azt a tarifacsomagok az eredeti kiszolgáló beállításokkal jön létre, számítási generációs, vCores, a tároló méretét, a biztonsági mentés megőrzési időtartam és a biztonsági mentési redundancia beállítás száma.

Több forgatókönyv pont időponthoz kötött visszaállítás érdemes alkalmazni. Például amikor egy felhasználó véletlenül törli az adatokat, csökken az fontos tábla vagy az adatbázis, vagy ha egy alkalmazás véletlenül felülírja jó adatokra helytelen adatot az alkalmazás hiba miatt.

Várja meg a következő tranzakciós napló biztonsági mentési pontra az elmúlt 5 percben belül visszaállítása előtt végrehajtandó szeretne.

### <a name="geo-restore"></a>Georedundáns helyreállítás

Is helyreállíthatja a kiszolgáló egy másik Azure-régió, ahol a szolgáltatás nem érhető el, ha a kiszolgáló, a georedundáns biztonsági mentéshez konfigurált. Georedundáns helyreállítás az alapértelmezett helyreállítási beállítás esetén a kiszolgáló nem érhető el a régióban, ahol a kiszolgáló tárolása esemény miatt. Ha a felügyeleti teendők központjaként, amelyhez a régió eredmények hiányában az adatbázis-alkalmazás, a kiszolgáló más régióban a georedundáns biztonsági másolatból visszaállíthatja egy kiszolgálót. Amikor egy biztonsági mentést készít-e, és amikor azt a rendszer replikálja más régióban késleltetés van. Ez a késés lehet akár egy óra alatt tehát ha katasztrófa történik, lehet fel egy órával adatok veszhetnek el.

### <a name="perform-post-restore-tasks"></a>Hajtsa végre visszaállítás utáni feladatok

Után visszaállítása vagy helyreállítás mechanizmus végre kell hajtania a következő feladatok végrehajtásával a felhasználók és alkalmazások biztonsági működik, és:

- Célja, hogy az új kiszolgáló lecseréli az eredeti kiszolgálón, ha az ügyfelek és az új kiszolgálóra ügyfélalkalmazások átirányítása
- Biztosítsa, hogy megfelelő kiszolgálószintű tűzfal-szabályokat helyen való csatlakozáskor
- Biztosítsa, hogy megfelelő bejelentkezések és adatbázis-szintű engedélyek helyen
- Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>További lépések

- Az üzletmenet folytonossága kapcsolatos további tudnivalókért tekintse meg a [üzleti folytonosság – áttekintés](concepts-business-continuity.md).
- A pont visszaállítása az Azure portál használatával időpontra, lásd: [ponttá adatbázis visszaállítása az Azure portál használatával időpontra](howto-restore-server-portal.md).
- Visszaállítása egy alkalommal az Azure parancssori felület használatával, lásd: [adatbázis visszaállítása pontra parancssori felület használatával időpontra](howto-restore-server-cli.md).