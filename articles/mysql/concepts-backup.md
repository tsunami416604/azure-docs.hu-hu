---
title: Biztonsági mentés és visszaállítás – Azure Database for MySQL
description: Ismerje meg a Azure Database for MySQL-kiszolgáló automatikus biztonsági mentését és visszaállítását.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 3a6162bb381f4e54114e3cabbf138f5b1c6aaae0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373035"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Biztonsági mentés és visszaállítás Azure Database for MySQL

Azure Database for MySQL automatikusan létrehozza a kiszolgáló biztonsági másolatait, és a helyileg redundáns vagy földrajzilag redundáns tárolóban tárolja azokat. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és a visszaállítás fontos részét képezi az üzletmenet folytonossági stratégiájának, mivel ezek az adatok a véletlen sérüléstől vagy törléstől védve vannak.

## <a name="backups"></a>Biztonsági másolatok

Azure Database for MySQL biztonsági másolatokat készít az adatfájlokról és a tranzakciónaplóról. A maximálisan támogatott tárterülettől függően teljes és különbözeti biztonsági mentéseket (4 TB-os maximális tárolási kiszolgálókat) vagy pillanatképes biztonsági mentést (legfeljebb 16 TB-os maximális tárolási kiszolgálót) használhat. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók visszaállítását bármely időpontra a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is [beállíthatja](howto-restore-server-portal.md#set-backup-configuration) . Az összes biztonsági mentés titkosítása AES 256 bites titkosítás használatával történik.

Ezek a biztonságimásolat-fájlok nem felhasználók számára lettek kitéve, és nem exportálhatók. Ezek a biztonsági másolatok csak Azure Database for MySQL-beli visszaállítási műveletekhez használhatók. A [mysqldump](concepts-migrate-dump-restore.md) használatával másolhat egy adatbázist.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

Általánosságban elmondható, hogy a teljes biztonsági mentések hetente, a különbözeti biztonsági mentések naponta kétszer történnek a 4 TB-os maximális támogatott tárterülettel rendelkező kiszolgálók esetében. A pillanatképek biztonsági mentése legalább naponta egyszer történik a legfeljebb 16 TB tárterületet támogató kiszolgálók esetén. A tranzakciós naplók biztonsági mentése mindkét esetben öt percenként történik. A teljes biztonsági mentés első pillanatképét a rendszer a kiszolgáló létrehozása után azonnal ütemezi. A kezdeti teljes biztonsági mentés hosszabb időt vehet igénybe egy nagy visszaállított kiszolgálón. Az a legkorábbi időpont, ameddig egy új kiszolgáló visszaállítható a kezdeti teljes biztonsági mentés befejezésének időpontjára. Mivel a pillanatképek azonnaliek, a legfeljebb 16 TB tárhellyel rendelkező kiszolgálók bármikor visszaállíthatók a létrehozási időre.

### <a name="backup-redundancy-options"></a>A Backup redundancia beállításai

Azure Database for MySQL rugalmasságot biztosít a helyileg redundáns vagy geo-redundáns biztonsági mentési tárolók közötti választáshoz a általános célú és a memóriára optimalizált rétegekben. Ha a biztonsági mentések a földrajzilag redundáns biztonsági mentési tárolóban tárolódnak, azok nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemeltetve van, de egy [párosított adatközpontba](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)is replikálódnak. Ez jobb védelmet nyújt, és lehetővé teszi a kiszolgáló egy másik régióban való visszaállítását vészhelyzet esetén. Az alapszintű csomag csak a helyileg redundáns biztonsági mentési tárhelyet kínálja.

> [!IMPORTANT]
> A helyileg redundáns vagy geo-redundáns tárterület a biztonsági mentéshez való konfigurálása csak a kiszolgáló létrehozásakor engedélyezett. A kiszolgáló üzembe helyezését követően nem módosítható a biztonsági mentési tár redundáns beállítása.

### <a name="backup-storage-cost"></a>Biztonsági mentési tárolási díj

A Azure Database for MySQL a kiépített kiszolgáló tárterületének akár 100%-át is elérhetővé teszi a biztonsági mentési tárolóként, többletköltség nélkül. Ez általában alkalmas a biztonsági másolatok megtartására, amely hét nap. A további felhasznált biztonsági mentési tárhelyeket GB-onként számítjuk fel.

Ha például létrehozta a 250 GB-ot tartalmazó kiszolgálót, a biztonsági mentési tárterület 250 GB-nyi tartalék tárhelye díjmentes. A 250 GB-nál nagyobb tárterületért díjat számítunk fel.

## <a name="restore"></a>Visszaállítás

Azure Database for MySQL a Restore művelet elvégzésével létrehoz egy új kiszolgálót az eredeti kiszolgáló biztonsági másolatai közül, és visszaállítja a kiszolgálón található összes adatbázist.

Két típusú visszaállítás érhető el:

- Az **időponthoz való visszaállítás** a Backup redundancia beállítással érhető el, és egy új kiszolgálót hoz létre ugyanabban a régióban, mint az eredeti kiszolgáló, amely a teljes és a tranzakciónapló biztonsági mentésének kombinációját használja.
- A **geo-visszaállítás** csak akkor érhető el, ha a kiszolgálót a Geo-redundáns tároláshoz konfigurálta, és lehetővé teszi a kiszolgáló egy másik régióba való visszaállítását, amely a legutóbbi biztonsági mentést használja.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázisok méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az azonos régióban lévő adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. A kiszolgálói erőforrások, a telepítés után a véletlen törlés vagy a váratlan módosítások elleni védelem érdekében a rendszergazdák kihasználhatják a [felügyeleti zárolásokat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources).

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A biztonsági mentési redundancia-beállítástól függetlenül a biztonsági másolatok megőrzési időszakán belül bármikor elvégezheti a visszaállítást. A rendszer létrehoz egy új kiszolgálót ugyanabban az Azure-régióban, mint az eredeti kiszolgálót. A rendszer az eredeti kiszolgáló konfigurációját hozza létre a díjszabási csomag, a számítási generáció, a virtuális mag száma, a tárterület mérete, a biztonsági másolatok megőrzési időtartama és a biztonsági mentési redundancia beállítás esetében.

Az időponthoz való visszaállítás több esetben is hasznos lehet. Ha például egy felhasználó véletlenül törli az adatvesztést, elveszít egy fontos táblát vagy adatbázist, vagy ha egy alkalmazás hibája miatt véletlenül rossz adatmennyiséggel felülírja a megfelelő adatmennyiséget.

Előfordulhat, hogy meg kell várnia a következő tranzakciónapló biztonsági mentését, mielőtt az utolsó öt percen belül helyre tudja állítani az adott időpontot.

### <a name="geo-restore"></a>Georedundáns helyreállítás

A kiszolgálót visszaállíthatja egy másik Azure-régióba, ahol a szolgáltatás elérhető, ha konfigurálta a kiszolgálót a Geo-redundáns biztonsági mentésekhez. A legfeljebb 4 TB tárterületet támogató kiszolgálók visszaállíthatók a Geo-párosítású régióba vagy bármely olyan régióba, amely akár 16 TB tárterületet is támogat. A legfeljebb 16 TB tárterületet támogató kiszolgálók esetében a Geo-biztonsági másolatok bármely olyan régióban visszaállíthatók, amely támogatja a 16 TB-os kiszolgálót is. Tekintse át a támogatott régiók listáját a [Azure Database for MySQL díjszabási szintjein](concepts-pricing-tiers.md) .

A Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha a kiszolgáló nem érhető el, mert a kiszolgálót futtató régióban incidens található. Ha egy adott régióban a nagyméretű incidensek nem állnak rendelkezésre az adatbázis-alkalmazás számára, visszaállíthat egy kiszolgálót a Geo-redundáns biztonsági másolatokból egy másik régióban található kiszolgálóra. A Geo-visszaállítás a kiszolgáló legújabb biztonsági mentését használja. A biztonsági másolat készítése és más régióba való replikálása között késés történt. Ez a késleltetés akár egy óráig is eltarthat, így ha egy katasztrófa következik be, akár egy órányi adatvesztés is lehet.

A Geo-visszaállítás során a megváltoztatható kiszolgálói konfigurációk közé tartoznak a számítási generáció, a virtuális mag, a biztonsági másolatok megőrzési időtartama és a biztonsági mentési redundancia beállításai. Az árképzési szint (alapszintű, általános célú vagy memória optimalizálása) vagy a tárolási méret módosítása a Geo-visszaállítás során nem támogatott.

### <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása

A helyreállítási mechanizmusból való visszaállítás után a következő feladatokat kell elvégeznie a felhasználók és alkalmazások biztonsági mentésének és futtatásának visszaszerzéséhez:

- Ha az új kiszolgáló lecseréli az eredeti kiszolgálót, átirányítja az ügyfeleket és az ügyfélalkalmazások az új kiszolgálóra
- Győződjön meg arról, hogy a felhasználók csatlakozhatnak a megfelelő VNet-szabályokhoz. Ezeket a szabályokat a rendszer nem másolja át az eredeti kiszolgálóról.
- Győződjön meg arról, hogy a megfelelő bejelentkezések és az adatbázis-szintű engedélyek vannak érvényben
- Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>További lépések

- Az üzletmenet folytonosságával kapcsolatos további tudnivalókért tekintse meg az [üzletmenet folytonosságának áttekintése](concepts-business-continuity.md)című témakört.
- Ha a Azure Portal használatával szeretne visszaállítani egy időpontra, tekintse meg [a kiszolgáló visszaállítása a Azure Portal használatával](howto-restore-server-portal.md)című témakört.
- Az Azure CLI-vel való időpontra való visszaállításhoz lásd: [kiszolgáló visszaállítása egy adott időpontra a CLI használatával](howto-restore-server-cli.md).
