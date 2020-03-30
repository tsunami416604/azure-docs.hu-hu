---
title: Biztonsági mentés és visszaállítás – Azure-adatbázis a MariaDB-hez
description: Ismerje meg az automatikus biztonsági mentéseket és az Azure Database visszaállítása MariaDB-kiszolgálóhoz.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: c4d5a9ca85237bde1277904a478a0b8828fc2b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369244"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Biztonsági mentés és visszaállítás a MariaDB Azure Database szolgáltatásában

A MariaDB Azure Database automatikusan létrehozza a kiszolgáló biztonsági másolatait, és tárolja azokat a felhasználó által konfigurált helyileg redundáns vagy georedundáns tárolóban. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és visszaállítás minden üzletmenet-folytonossági stratégia alapvető részét képezi, mivel megvédi az adatokat a véletlen sérüléstől vagy törléstől.

## <a name="backups"></a>Biztonsági másolatok

A MariaDB Azure Database teljes, különbözeti és tranzakciós naplóbiztonsági mentést készít. Ezek a biztonsági mentések lehetővé teszik, hogy a konfigurált biztonsági mentési megőrzési időszakon belül bármikor visszaállítsa a kiszolgálót a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Igény szerint legfeljebb 35 napig konfigurálhatja. Minden biztonsági mentés a 256 bites AES titkosítással van titkosítva.

Ezek a biztonságimásolat-fájlok nem felhasználói expozíciónak vannak kitéve, és nem exportálhatók. Ezek a biztonsági mentések csak a MariaDB Azure Database-ben végzett visszaállítási műveletekhez használhatók. A [mysqldump](howto-migrate-dump-restore.md) segítségével másolhat egy adatbázist.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

A teljes biztonsági mentés általában hetente, a különbözeti biztonsági mentés naponta kétszer, a tranzakciós naplók biztonsági mentése pedig ötpercenként történik. Az első teljes biztonsági mentés ütemezése a kiszolgáló létrehozása után azonnal megtörténik. A kezdeti biztonsági mentés hosszabb időt vehet igénybe egy nagy méretű, visszaállított kiszolgálón. Az új kiszolgáló legkorábbi visszaállítási időpontja az az időpont, amikor a kezdeti teljes biztonsági mentés befejeződött.

### <a name="backup-redundancy-options"></a>Biztonsági mentésredundanci adlehetőségek

A MariaDB Azure Database rugalmasságot biztosít a helyiredundáns vagy georedundáns biztonsági mentési tárolás közötti választáshoz az általános cél és a memóriaoptimalizált szinteken. Ha a biztonsági másolatokat georedundáns biztonsági mentési tárolóban tárolják, azok nem csak abban a régióban tárolódnak, ahol a kiszolgáló található, hanem egy [párosított adatközpontba](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)is replikálódnak. Ez jobb védelmet és lehetővé teszi a kiszolgáló visszaállítását egy másik régióban katasztrófa esetén. Az alapszintű szint csak helyileg redundáns biztonsági mentési tárolást kínál.

> [!IMPORTANT]
> A helyileg redundáns vagy georedundáns tárolás biztonsági mentéshez történő konfigurálása csak a kiszolgáló létrehozása során engedélyezett. A kiszolgáló kiépítése után nem módosíthatja a biztonsági mentési tároló redundancia beállítását.

### <a name="backup-storage-cost"></a>Biztonsági mentési tárolási költség

A MariaDB Azure Database a kiosztott kiszolgálói tárhely akár 100%-át is biztosítja biztonsági mentési tárhelyként, további költségek nélkül. Ez általában hét napos biztonsági mentési megőrzésre alkalmas. Minden további biztonsági mentési tárterület gb-hónapban kerül felszámolásra.

Ha például 250 GB-os kiszolgálót létesített, 250 GB biztonsági mentési tárhellyel rendelkezik, további díj nélkül. A 250 GB-ot meghaladó tárhely felszámításra kerül.

A biztonsági mentés tárolási költségével kapcsolatos további információkért látogasson el a [MariaDB árképzési oldalára.](https://azure.microsoft.com/pricing/details/mariadb/)

## <a name="restore"></a>Visszaállítás

A MariaDB Azure Database-ben a visszaállítás új kiszolgálót hoz létre az eredeti kiszolgáló biztonsági másolataiból, és visszaállítja a kiszolgálón található összes adatbázist.

Kétféle visszaállítás érhető el:

- **Az időponthoz való hozzáférés** a biztonsági mentésredundanci beállításával érhető el, és a teljes és a tranzakciós napló biztonsági másolatainak kombinációját használó eredeti kiszolgálóval azonos régióban hoz létre új kiszolgálót.
- **A geo-visszaállítás** csak akkor érhető el, ha a kiszolgálót georedundáns tárolásra konfigurálta, és lehetővé teszi, hogy a legutóbbi biztonsági mentést kihasználva visszaállítsa a kiszolgálót egy másik régióba.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázis méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az ugyanabban a régióban egyidejűleg helyreállító adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. A kiszolgáló erőforrásainak, a telepítés utáni állapotnak a véletlen törléstől vagy a váratlan változásoktól való védelme érdekében a rendszergazdák kihasználhatják [a felügyeleti zárolásokat.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A biztonsági mentési redundancia beállítástól függetlenül a biztonsági mentés megőrzési időszakán belül bármikor végrehajthatja a visszaállítást. Egy új kiszolgáló jön létre ugyanabban az Azure-régióban, mint az eredeti kiszolgáló. Az eredeti kiszolgáló konfigurációjával jön létre a tarifacsomaghoz, a számítási kapacitáshoz, a virtuális magok számához, a tárhely méretéhez, a biztonsági mentés idorolási időszakához és a biztonsági mentés redundancia beállításához.

Az időponthoz való időpont visszaállítása több esetben is hasznos. Ha például egy felhasználó véletlenül adatokat töröl, eldob egy fontos táblát vagy adatbázist, vagy ha egy alkalmazás alkalmazáshiba miatt véletlenül felülírja a jó adatokat rossz adatokkal.

Előfordulhat, hogy meg kell várnia a következő tranzakciónapló biztonsági mentését, mielőtt az utolsó öt percben visszaállíthatja az időpontot.

### <a name="geo-restore"></a>Georedundáns helyreállítás

Visszaállíthatja a kiszolgálót egy másik Azure-régióba, ahol a szolgáltatás elérhető, ha konfigurálta a kiszolgálót a georedundáns biztonsági mentések. Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha a kiszolgáló nem érhető el egy incidens miatt a régióban, ahol a kiszolgáló található. Ha egy adott régióban egy nagyméretű incidens az adatbázis-alkalmazás elérhetetlenségét eredményezi, visszaállíthatja a kiszolgálót a georedundáns biztonsági mentések ből egy másik régió kiszolgálójára. A geo-visszaállítás a kiszolgáló legutóbbi biztonsági másolatát használja. A biztonsági mentés készítése és a más régióba replikált replikálása között késleltetés van. Ez a késleltetés akár egy órát is elérhet, így ha katasztrófa történik, akár egy órás adatvesztés is bekövetkezhet.

A geo-visszaállítás során a módosítható kiszolgálókonfigurációk közé tartozik a számítási generálás, a virtuális mag, a biztonsági mentés megőrzési időszaka és a biztonsági mentés redundancia beállításai. A földrajzi visszaállítás során nem támogatott a tarifacsomag (Alapszintű, általános célú vagy memóriaoptimalizált) vagy a tárhely méretének módosítása.

### <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása

A helyreállítási mechanizmusból történő visszaállítás után a következő feladatokat kell végrehajtania a felhasználók és alkalmazások biztonsági mentéséhez és futtatásához:

- Ha az új kiszolgáló az eredeti kiszolgáló takarására szolgál, irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra
- Győződjön meg arról, hogy a felhasználók csatlakozhatnak a megfelelő virtuális hálózati szabályokhoz. Ezeket a szabályokat a rendszer nem másolja át az eredeti kiszolgálóról.
- Győződjön meg arról, hogy megfelelő bejelentkezési és adatbázisszintű engedélyek vannak érvényben
- Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az üzletmenet folytonosságáról, olvassa el az [üzletmenet folytonosságának áttekintését.](concepts-business-continuity.md)
- Ha vissza szeretne állítani egy időpontra az Azure Portal használatával, olvassa el a [kiszolgáló visszaállítása egy adott időpontra](howto-restore-server-portal.md)az Azure Portal használatával című témakört.
- Ha vissza szeretne állítani egy időpontra az Azure CLI használatával, olvassa [el a kiszolgáló visszaállítása időponthoz való visszaállítása a CLI használatával.](howto-restore-server-cli.md)
