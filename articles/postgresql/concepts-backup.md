---
title: Biztonsági mentés és visszaállítás - Azure Database for PostgreSQL - Single Server
description: Ismerje meg az automatikus biztonsági mentéseket és az Azure Database postgreSQL-kiszolgálóra való visszaállítását – egyetlen kiszolgáló.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3e6dfd5882e49ad903e8cff6f0ec7f3d6bd4a8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619627"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Biztonsági mentés és visszaállítás az Azure Database for PostgreSQL szolgáltatásban – Egyetlen kiszolgáló

A PostgreSQL Azure Database automatikusan létrehozza a kiszolgálóbiztonsági mentéseket, és tárolja azokat a felhasználó által konfigurált helyileg redundáns vagy georedundáns tárolóban. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és visszaállítás minden üzletmenet-folytonossági stratégia alapvető részét képezi, mivel megvédi az adatokat a véletlen sérüléstől vagy törléstől.

## <a name="backups"></a>Biztonsági másolatok

Az Azure Database for PostgreSQL biztonsági másolatot készít az adatfájlokról és a tranzakciós naplóról. A támogatott maximális tárterület méretétől függően teljes és különbözeti biztonsági mentéseket (4 TB max tárolókiszolgálók) vagy pillanatkép-biztonsági mentéseket (akár 16 TB-os maximális tárolókiszolgálókat) készítünk. Ezek a biztonsági mentések lehetővé teszik, hogy a konfigurált biztonsági mentési megőrzési időszakon belül bármikor visszaállítsa a kiszolgálót a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Igény szerint legfeljebb 35 napig konfigurálhatja. Minden biztonsági mentés a 256 bites AES titkosítással van titkosítva.

Ezek a biztonsági másolat fájlok nem exportálhatók. A biztonsági mentések csak a PostgreSQL Azure Database-ben végzett visszaállítási műveletekhez használhatók. Az [adatbázis másolásával pg_dump](howto-migrate-using-dump-and-restore.md) is használhatók.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

Általában a teljes biztonsági mentések hetente történnek, a különbözeti biztonsági mentések naponta kétszer történnek a 4 TB-os maximálisan támogatott tárolóval rendelkező kiszolgálókesetében. A pillanatképek biztonsági mentése legalább naponta egyszer történik a legfeljebb 16 TB tárterületet támogató kiszolgálók esetén. A tranzakciós naplók biztonsági mentése mindkét esetben öt percenként történik. A teljes biztonsági mentés első pillanatképét a kiszolgáló létrehozása után azonnal ütemezi a rendszer. A kezdeti teljes biztonsági mentés hosszabb időt vehet igénybe egy nagy visszaállított kiszolgálón. Az új kiszolgáló legkorábbi visszaállítási időpontja az az időpont, amikor a kezdeti teljes biztonsági mentés befejeződött. Mivel a pillanatképek azonnaliak, a 16 TB-os tárhelyet támogató kiszolgálók visszaállíthatók egészen a létrehozási időig.

### <a name="backup-redundancy-options"></a>Biztonsági mentésredundanci adlehetőségek

A PostgreSQL Azure Database szolgáltatása rugalmasságot biztosít a helyiredundáns vagy georedundáns biztonsági mentési tárolás közötti választáshoz az általános célú és a memóriaoptimalizált szinteken. Ha a biztonsági másolatokat georedundáns biztonsági mentési tárolóban tárolják, azok nem csak abban a régióban tárolódnak, ahol a kiszolgáló található, hanem egy [párosított adatközpontba](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)is replikálódnak. Ez jobb védelmet és lehetővé teszi a kiszolgáló visszaállítását egy másik régióban katasztrófa esetén. Az alapszintű szint csak helyileg redundáns biztonsági mentési tárolást kínál.

> [!IMPORTANT]
> A helyileg redundáns vagy georedundáns tárolás biztonsági mentéshez történő konfigurálása csak a kiszolgáló létrehozása során engedélyezett. A kiszolgáló kiépítése után nem módosíthatja a biztonsági mentési tároló redundancia beállítását.

### <a name="backup-storage-cost"></a>Biztonsági mentési tárolási költség

Az Azure Database for PostgreSQL a kiosztott kiszolgálói tárhely akár 100%-át is biztosítja biztonsági mentési tárhelyként, további költségek nélkül. Ez általában hét napos biztonsági mentési megőrzésre alkalmas. Minden további biztonsági mentési tárterület gb-hónapban kerül felszámolásra.

Ha például 250 GB-os kiszolgálót létesített, 250 GB biztonsági mentési tárhellyel rendelkezik, további díj nélkül. A 250 GB-ot meghaladó tárhely felszámításra kerül.

## <a name="restore"></a>Visszaállítás

Az Azure Database for PostgreSQL-ben a visszaállítás végrehajtása új kiszolgálót hoz létre az eredeti kiszolgáló biztonsági másolataiból.

Kétféle visszaállítás érhető el:

- **Az időponthoz való intézés visszaállítása** biztonsági mentési redundancia beállítással érhető el, és az eredeti kiszolgálóval azonos régióban hoz létre új kiszolgálót.
- **A geo-visszaállítás** csak akkor érhető el, ha a kiszolgálót georedundáns tárolásra konfigurálta, és lehetővé teszi a kiszolgáló visszaállítását egy másik régióba.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázis méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az ugyanabban a régióban egyidejűleg helyreállító adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. A kiszolgáló erőforrásainak, a telepítés utáni állapotnak a véletlen törléstől vagy a váratlan változásoktól való védelme érdekében a rendszergazdák kihasználhatják [a felügyeleti zárolásokat.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A biztonsági mentési redundancia beállítástól függetlenül a biztonsági mentés megőrzési időszakán belül bármikor végrehajthatja a visszaállítást. Egy új kiszolgáló jön létre ugyanabban az Azure-régióban, mint az eredeti kiszolgáló. Az eredeti kiszolgáló konfigurációjával jön létre a tarifacsomaghoz, a számítási kapacitáshoz, a virtuális magok számához, a tárhely méretéhez, a biztonsági mentés idorolási időszakához és a biztonsági mentés redundancia beállításához.

Az időponthoz való időpont visszaállítása több esetben is hasznos. Ha például egy felhasználó véletlenül adatokat töröl, eldob egy fontos táblát vagy adatbázist, vagy ha egy alkalmazás alkalmazáshiba miatt véletlenül felülírja a jó adatokat rossz adatokkal.

Előfordulhat, hogy meg kell várnia a következő tranzakciónapló biztonsági mentését, mielőtt az utolsó öt percben visszaállíthatja az időpontot.

### <a name="geo-restore"></a>Georedundáns helyreállítás

Visszaállíthatja a kiszolgálót egy másik Azure-régióba, ahol a szolgáltatás elérhető, ha konfigurálta a kiszolgálót a georedundáns biztonsági mentések. A legfeljebb 4 TB-os tárhelyet támogató kiszolgálók visszaállíthatók a földrajzilag párosított régióba, vagy bármely olyan régióba, amely legfeljebb 16 TB tárhelyet támogat. Az akár 16 TB-os tárhelyet támogató kiszolgálók esetében a geobiztonsági mentések bármely régióban visszaállíthatók, amely 16 TB-kiszolgálót is támogat. Tekintse át [az Azure Database for PostgeSQL tarifacsomagok](concepts-pricing-tiers.md) a támogatott régiók listájához.

Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha a kiszolgáló nem érhető el egy incidens miatt a régióban, ahol a kiszolgáló található. Ha egy adott régióban egy nagyméretű incidens az adatbázis-alkalmazás elérhetetlenségét eredményezi, visszaállíthatja a kiszolgálót a georedundáns biztonsági mentések ből egy másik régió kiszolgálójára. A biztonsági mentés készítése és a más régióba replikált replikálása között késleltetés van. Ez a késleltetés akár egy órát is elérhet, így ha katasztrófa történik, akár egy órás adatvesztés is bekövetkezhet.

A geo-visszaállítás során a módosítható kiszolgálókonfigurációk közé tartozik a számítási generálás, a virtuális mag, a biztonsági mentés megőrzési időszaka és a biztonsági mentés redundancia beállításai. A tarifacsomag (alapszintű, általános célú vagy memóriaoptimalizált) vagy a tárhely méretének módosítása nem támogatott.

### <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása

A helyreállítási mechanizmusból történő visszaállítás után a következő feladatokat kell végrehajtania a felhasználók és alkalmazások biztonsági mentéséhez és futtatásához:

- Ha az új kiszolgáló az eredeti kiszolgáló takarására szolgál, irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra
- Győződjön meg arról, hogy a felhasználók csatlakozhatnak a megfelelő kiszolgálószintű tűzfal- és virtuálishálózat-szabályokhoz. Ezeket a szabályokat a rendszer nem másolja át az eredeti kiszolgálóról.
- Győződjön meg arról, hogy megfelelő bejelentkezési és adatbázisszintű engedélyek vannak érvényben
- Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan állíthatja vissza [az Azure Portalhasználatával.](howto-restore-server-portal.md)
- Ismerje meg, hogyan állíthatja vissza [az Azure CLI](howto-restore-server-cli.md)használatával.
- Ha többet szeretne megtudni az üzletmenet folytonosságáról, olvassa el az [üzletmenet folytonosságának áttekintését.](concepts-business-continuity.md)
