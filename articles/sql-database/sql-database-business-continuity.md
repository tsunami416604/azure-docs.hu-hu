---
title: Felhőbeli üzletmenet folytonossága - adatbázis-helyreállítás
description: Ismerje meg, hogyan támogatja az Azure SQL Database a felhőalapú üzletmenet-folytonosságot és az adatbázis-helyreállítást, és hogyan segít az üzletmenet szempontjából kritikus fontosságú felhőalapú alkalmazások folyamatos működésének biztosításában.
keywords: üzletmenet-folytonosság,felhőalapú üzletmenet-folytonosság,adatbázis-vészhelyreállítás,adatbázis-helyreállítás
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 4f30bf112175742566c2957d78154e5a7abd1733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096862"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése

Az Azure SQL Database **üzletmenet-folytonossága** azokra a mechanizmusokra, szabályzatokra és eljárásokra utal, amelyek lehetővé teszik, hogy vállalkozása továbbra is a zavarokkal szembesülve működjön, különösen a számítástechnikai infrastruktúrában. Az esetek többségében az Azure SQL Database kezeli a felhőalapú környezetben esetleg bekövetkező zavaró eseményeket, és továbbra is futtatja az alkalmazásokat és az üzleti folyamatokat. Vannak azonban olyan zavaró események, amelyeket az SQL Database nem tud automatikusan kezelni, például:

- A felhasználó véletlenül törölt vagy frissített egy sort a táblában.
- A rosszindulatú támadónak sikerült adatokat törölnie vagy adatbázist dobnia.
- Földrengés okozta áramkimaradás és ideiglenes fogyatékkal adatközpont.

Az áttekintés az Azure SQL Database az üzletmenet-folytonosság és a vészhelyreállítás biztosítására szolgáló funkcióit írja le. Ismerje meg a zavaró eseményekből való helyreállításra vonatkozó beállításokat, javaslatokat és oktatóanyagokat, amelyek adatvesztést okozhatnak, vagy az adatbázis és az alkalmazás elérhetetlenné válhatnak. Megtudhatja, hogy mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió ban van egy kimaradás, vagy az alkalmazás karbantartást igényel.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Az SQL Database üzletmenet-folytonosságot biztosító funkciói

Az adatbázis szempontjából négy fő lehetséges megszakítási forgatókönyv létezik:

- Az adatbázis-csomópontot érintő helyi hardver- vagy szoftverhibák, például lemezmeghajtó-hiba.
- Adatsérülés vagy törlés általában egy alkalmazás hiba vagy emberi hiba okozza. Az ilyen hibák alkalmazásspecifikusak, és az adatbázis-szolgáltatás általában nem észlelhető.
- Adatközpont-kimaradás, valószínűleg természeti katasztrófa okozta. Ebben a forgatókönyvben bizonyos szintű georedundancia igényel alkalmazás feladatátvétel egy másik adatközpontba.
- Frissítési vagy karbantartási hibák, a tervezett infrastruktúra karbantartása vagy frissítése során felmerülő nem várt problémák gyors visszaállítást igényelhetnek egy korábbi adatbázisállapotba.

A helyi hardver- és szoftverhibák csökkentése érdekében az SQL Database [magas rendelkezésre állású architektúrát](sql-database-high-availability.md)tartalmaz, amely garantálja az automatikus helyreállítást ezekből a hibákból, akár 99,995%-os rendelkezésre állási SLA-val.  

Az adatvesztés elleni védelem érdekében az SQL Database hetente automatikusan létrehozza a teljes adatbázis-biztonsági mentéseket, a különbözeti adatbázis biztonsági mentését 12 óránként, a tranzakciós napló biztonsági mentéseit pedig 5–10 percenként. A biztonsági mentések tárolják RA-GRS tároló legalább 7 napig az összes szolgáltatási szint. Az alapszintű támogatási konfigurálható biztonsági mentési megőrzési időszak kivételével minden szolgáltatási szint, akár 35 napig is használható. 

Az SQL Database számos üzletmenet-folytonossági szolgáltatást is biztosít, amelyek segítségével számos nem tervezett forgatókönyvet enyhíthet. 

- A [historikus táblák](sql-database-temporal-tables.md) segítségével a sorverziókat bármely időpontra visszaállíthatja.
- [A beépített automatikus biztonsági mentések](sql-database-automated-backups.md) és [a Point in Time Restore](sql-database-recovery-using-backups.md#point-in-time-restore) lehetővé teszi a teljes adatbázis visszaállítását a beállított megőrzési időszakon belül 35 napig.
- A [törölt adatbázis visszaállítható](sql-database-recovery-using-backups.md#deleted-database-restore) arra a pontra, ahol törölték, ha az **SQL Adatbázis-kiszolgálót nem törölték.**
- [A hosszú távú biztonsági mentés megőrzése](sql-database-long-term-retention.md) lehetővé teszi, hogy a biztonsági mentések legfeljebb 10 évig.
- [Az aktív georeplikáció](sql-database-active-geo-replication.md) lehetővé teszi, hogy olvasható replikákat hozzon létre, és manuálisan feladatátvételt bármely kópiára adatközpont-kimaradás vagy alkalmazásfrissítés esetén.
- [Az automatikus feladatátvételi csoport](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) lehetővé teszi, hogy az alkalmazás adatközpont-kimaradás esetén automatikusan helyreálljon.

## <a name="recover-a-database-within-the-same-azure-region"></a>Adatbázis helyreállítása ugyanabban az Azure-régióban

Az adatbázis automatikus biztonsági másolatai segítségével visszaállíthatja az adatbázist egy korábbi időpontra. Így az emberi hibák által okozott adatsérülések helyreállítása. A pont-az-time visszaállítás lehetővé teszi, hogy hozzon létre egy új adatbázist ugyanazon a kiszolgálón, amely az adatok állapotát jelöli a sérült esemény előtt. A legtöbb adatbázis esetében a visszaállítási műveletek kevesebb mint 12 órát vesznek igénybe. Egy nagyon nagy vagy nagyon aktív adatbázis helyreállítása hosszabb időt vehet igénybe. A helyreállítási időről további információt az [adatbázis helyreállítási idején](sql-database-recovery-using-backups.md#recovery-time)talál. 

Ha a pont-in-time visszaállítás (PITR) maximális támogatott biztonsági mentési megőrzési ideje nem elegendő az alkalmazásszámára, kiterjesztheti azt az adatbázis(ok) hosszú távú megőrzési (LTR) házirendjének konfigurálásával. További információ: [Hosszú távú biztonsági mentés megőrzése](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Georeplikáció összehasonlítása feladatátvevő csoportokkal

[Az automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) leegyszerűsítik a [georeplikáció](sql-database-active-geo-replication.md) telepítését és használatát, és hozzáadják az alábbi táblázatban ismertetett további képességeket:

|                                              | Georeplikáció | Feladatátvételi csoportok  |
|:---------------------------------------------| :-------------- | :----------------|
| Automatikus feladatátvétel                           |     Nem          |      Igen         |
| Több adatbázis egyidejű átvétele  |     Nem          |      Igen         |
| A felhasználónak frissítenie kell a kapcsolati karakterláncot a feladatátvétel után      |     Igen         |      Nem          |
| Felügyelt példány támogatott                   |     Nem          |      Igen         |
| Lehet ugyanabban a régióban, mint az elsődleges             |     Igen         |      Nem          |
| Több kópia                            |     Igen         |      Nem          |
| Támogatja az olvasási skálát                          |     Igen         |      Igen         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Adatbázis helyreállítása a meglévő kiszolgálóra

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzletmenet esetleg csak néhány percre, de akár több órára is megszakadhat.

- Az egyik lehetőség, hogy megvárja, amíg az adatközpont leállását követően az adatbázis újra elérhető lesz. Ez az olyan alkalmazások esetében működik, amelyek esetében megengedhető, hogy az adatbázis offline állapotú legyen. Például egy fejlesztési projekt vagy egy ingyenes próbaverzió esetében, amelyeken nem kell folyamatosan dolgoznia. Ha egy adatközpontban kimaradás van, nem tudja, hogy a kimaradás mennyi ideig tarthat, így ez a beállítás csak akkor működik, ha egy ideig nincs szüksége az adatbázisra.
- Egy másik lehetőség az, hogy visszaállítsa az adatbázis bármely kiszolgálóbármely [Azure-régióban georedundáns adatbázis biztonsági mentések](sql-database-recovery-using-backups.md#geo-restore) (geo-visszaállítás). A georedundáns biztonsági másolat forrása egy georedundáns biztonsági mentést használ, és akkor is használható az adatbázis helyreállítására, ha az adatbázis vagy az adatközpont kimaradás miatt nem érhető el.
- Végül gyorsan helyreállíthatja a kimaradást, ha [az aktív georeplikáció](sql-database-active-geo-replication.md) vagy az adatbázis vagy adatbázisok [automatikus feladatátvételi csoportja](sql-database-auto-failover-group.md) segítségével geoszekundert konfigurált. Attól függően, hogy a választott ezeket a technológiákat, használhatja a kézi vagy automatikus feladatátvétel. Míg maga a feladatátvétel mindössze néhány másodpercet vesz igénybe, a szolgáltatás aktiválása legalább 1 órát vesz igénybe. Erre azért van szükség, hogy a feladatátvételt a kimaradás mértéke indokolja. Emellett a feladatátvétel az aszinkron replikáció jellege miatt kis adatvesztést is okozhat. 

Az üzletmenet-folytonossági terv kidolgozása során meg kell állapítania az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható időkorlátját. Az alkalmazás teljes helyreállításához szükséges idő a helyreállítási idő célkitűzése (RTO). Azt is meg kell értenie, hogy a legutóbbi adatfrissítések maximális időtartama (időintervallum) az alkalmazás elviseli a veszteséget, amikor egy nem tervezett zavaró esemény ből felépül. A lehetséges adatvesztést helyreállítási pont célnak (RPO) nevezik.

A különböző helyreállítási módszerek különböző szintű RPO-t és RTO-t kínálnak. Választhat egy adott helyreállítási módszert, vagy módszerek kombinációját használhatja a teljes alkalmazás-helyreállítás eléréséhez. Az alábbi táblázat összehasonlítja az rpo-t és az RTO-t az egyes helyreállítási lehetőségekből. Az automatikus feladatátvételi csoportok leegyszerűsítik a georeplikáció telepítését és használatát, és hozzáadja az alábbi táblázatban ismertetett további képességeket.

| Helyreállítási módszer | RTO | RPO |
| --- | --- | --- | 
| Geo-visszaállítás georeplikált biztonsági mentések | 12 óra | 1 óra |
| Automatikus feladatátvételi csoportok | 1 óra | 5 s |
| Manuális adatbázis-feladatátvétel | 30 s | 5 s |

> [!NOTE]
> *A manuális adatbázis-feladatátvétel* egyetlen adatbázis nem tervezett módban történő georeplikált másodlagos adatbázisba történő [átvételére](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities)utal.
Tekintse meg a cikk korábbi táblázatában az automatikus feladatátvételrúvó és az RPO részleteit.


Automatikus feladatátvételi csoportok használata, ha az alkalmazás megfelel az alábbi feltételek bármelyikének:

- Az üzletmenet szempontjából kritikus fontosságú.
- Olyan szolgáltatásiszint-szerződéssel (SLA) rendelkezik, amely nem tesz lehetővé 12 vagy több állásidőt.
- Az állásidő pénzügyi kötelezettséget vonhat le.
- Nagy adatváltozással rendelkezik, és az 1 órás adatvesztés nem fogadható el.
- Az aktív georeplikáció többletköltsége alacsonyabb, mint a potenciális pénzbeli kötelezettség és a kapcsolódó üzletvesztés összege.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Dönthet úgy, hogy az alkalmazás követelményeitől függően adatbázis-biztonsági mentések és aktív georeplikáció kombinációját használja. Az önálló adatbázisok és az ezekkel az üzletmenet-folytonossági funkciókat használó rugalmas készletek tervezési szempontjairól a [Felhőbeli vész-helyreállítási](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [rugalmas készletvész-helyreállítási stratégiák](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)alkalmazásának tervezése című témakörben található.

A következő szakaszok áttekintést nyújtanak az adatbázis biztonsági másolatai vagy az aktív georeplikáció használatával történő helyreállítás lépéseiről. A részletes lépéseket, beleértve a tervezési követelményeket, a helyreállítási lépések utáni lépéseket, valamint a vész-helyreállítási gyakorlat végrehajtásához szükséges kimaradás szimulálásáról szóló témakört az [SQL-adatbázis helyreállítása kimaradásból](sql-database-disaster-recovery.md)című témakörben talál.

### <a name="prepare-for-an-outage"></a>Előkészületek leállás esetére

A használt üzletmenet-folytonossági funkciótól függetlenül végre kell hajtania a következőket:

- Azonosítsa és készítse elő a célkiszolgálót, beleértve a kiszolgálószintű IP-tűzfalszabályokat, bejelentkezési és fő adatbázisszintű engedélyeket.
- Határozza meg, hogyan legyenek átirányítva az ügyfelek és az ügyfélalkalmazások az új kiszolgálóra.
- Dokumentálja az egyéb függőségeket, mint a naplózási beállítások és a riasztások.

Ha nem készít megfelelően, az alkalmazások online állapotba hozása a feladatátvétel vagy az adatbázis-helyreállítás után további időt vesz igénybe, és valószínűleg a stressz idején is hibaelhárítást igényel - rossz kombináció.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Feladatátvétel georeplikált másodlagos adatbázisba

Ha aktív georeplikációs vagy automatikus feladatátvételi csoportokat használ helyreállítási mechanizmusként, konfigurálhat egy automatikus feladatátvételi házirendet, vagy használhatja a [manuális nem tervezett feladatátvételt.](sql-database-active-geo-replication-portal.md#initiate-a-failover) A feladatátvétel megkezdése után a feladatátvétel hatására a másodlagos lesz az új elsődleges, és készen áll az új tranzakciók rögzítésére és a lekérdezések megválaszolására - minimális adatvesztés sel az adatok még nem replikált. A feladatátvételi folyamat tervezéséről a [Felhőbeli vész-helyreállítási alkalmazás tervezése című témakörben](sql-database-designing-cloud-solutions-for-disaster-recovery.md)talál további információt.

> [!NOTE]
> Amikor az adatközpont újra online állapotba kerül, a régi elsődleges adatok automatikusan újracsatlakoznak az új elsődleges állapothoz, és másodlagos adatbázisokká válnak. Ha vissza kell helyeznie az elsődleges területet az eredeti régióba, manuálisan kezdeményezheti a tervezett feladatátvételt (feladat-visszavétel).

### <a name="perform-a-geo-restore"></a>Georedundáns visszaállítás végrehajtása

Ha az automatikus biztonsági másolatokat georedundáns tárolással használja (alapértelmezés szerint engedélyezve van), az adatbázis tállítható helyre a [geo-visszaállítás](sql-database-disaster-recovery.md#recover-using-geo-restore)segítségével. A helyreállítás általában 12 órán belül történik - az adatvesztésleg legfeljebb egy óra, amelyet az utolsó naplóbiztonsági mentés készítése és replikálása határozza meg. A helyreállítás befejezéséig az adatbázis nem tudja rögzíteni a tranzakciókat, illetve nem tud válaszolni a lekérdezésekre. Megjegyzés: a geo-visszaállítás csak az adatbázist állítja vissza az utolsó elérhető időpontra.

> [!NOTE]
> Ha az adatközpont újra online állapotba kerül, mielőtt átváltana az alkalmazásra a helyreállított adatbázisra, megszakíthatja a helyreállítást.

### <a name="perform-post-failover--recovery-tasks"></a>Feladatátvétel/helyreállítás utáni feladatok végrehajtása

A helyreállítási mechanizmusok végrehajtása után a következő további feladatokat kell végrehajtania a felhasználók és az alkalmazások újbóli üzembe helyezéséhez:

- Irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra és a helyreállított adatbázisra.
- Győződjön meg arról, hogy a megfelelő kiszolgálószintű IP-tűzfalszabályok megfelelőek a megfelelő szabályok engedélyezéséhez, illetve [használhatják az adatbázis-szintű tűzfalakat.](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)
- Biztosítsa a megfelelő bejelentkezési adatok és főadatbázis-szintű jogosultságok rendelkezésre állását (vagy használjon [tartalmazott felhasználókat](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Konfigurálja a naplózást, ha szükséges.
- Konfigurálja a riasztásokat, ha szükséges.

> [!NOTE]
> Ha feladatátvételi csoportot használ, és az írási és írási lstener használatával csatlakozik az adatbázisokhoz, a feladatátvétel utáni átirányítás automatikusan és átláthatómódon történik az alkalmazáshoz.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Alkalmazások frissítése minimális állásidővel

Előfordulhat, hogy egy alkalmazást a tervezett karbantartás, például egy alkalmazásfrissítés miatt offline állapotba kell helyezni. [Az alkalmazásfrissítések kezelése](sql-database-manage-application-rolling-upgrade.md) azt ismerteti, hogy miként használhatja az aktív georeplikációt a felhőalapú alkalmazás működés közbeni frissítéseinek engedélyezéséhez, hogy minimalizálja az állásidőt a frissítések során, és helyreállítási útvonalat biztosítson, ha valami elromlik.

## <a name="next-steps"></a>További lépések

Az önálló adatbázisok és a rugalmas készletek alkalmazástervezési szempontjainak megvitatásáról a [Felhőbeli vész-helyreállítási](sql-database-designing-cloud-solutions-for-disaster-recovery.md) és [rugalmas készletvész-helyreállítási stratégiák](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)alkalmazásának tervezése című témakörben található.
