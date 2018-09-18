---
title: SQL-hibakódok – adatbázis-kapcsolati hiba |} A Microsoft Docs
description: 'Ismerje meg az SQL Database-ügyfélalkalmazások, mint az általános adatbázis-kapcsolati hibák, adatbázis-másolási hibákat és általános SQL-hibakódok. '
keywords: SQL-hibakód, hozzáférés az sql, adatbázis-kapcsolati hiba, sql-hibakódok
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: sstein
ms.openlocfilehash: 643add4e0e4732734e9ef763a587755bf8700605
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731002"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Az SQL Database-ügyfélalkalmazások SQL-hibakódok: adatbázis-csatlakozási hibák és egyéb problémák

Ez a cikk az SQL Database-ügyfélalkalmazások, többek között az adatbázis-kapcsolati hibák, átmeneti hibák (más néven az átmeneti hibák), erőforrás-szabályozási hibák, adatbázis-másolat problémák, a rugalmas készlet és egyéb hibák SQL-hibakódok sorolja fel. A legtöbb kategóriák adott Azure SQL Database-adatbázishoz, és nem vonatkoznak a Microsoft SQL Server. Lásd még: [rendszer hibaüzenetek](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Adatbázis-kapcsolati hibák, átmeneti hibák és egyéb átmeneti hibák
A következő táblázat ismerteti az SQL-hibakódok kapcsolat adatvesztési hibák, és más az alkalmazást próbál meg hozzáférni az SQL Database előforduló átmeneti hibákra. Első lépések – oktatóanyagok való csatlakozás az Azure SQL Database, lásd: [csatlakozik az Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Leggyakrabban használt adatbázis-csatlakozási hibák és átmeneti hibák hibák
Az Azure-infrastruktúra képes dinamikusan konfigurálnia a kiszolgálót, ha az SQL Database szolgáltatás merülnek fel a terhelés.  E dinamikus viselkedés okozhat az ügyfélprogram és elveszítheti a kapcsolatot az SQL Database-adatbázishoz. Az ilyen típusú hiba történt a feltétel neve egy *átmeneti hibák*.

Erősen ajánlott, hogy az ügyfélprogram rendelkezik-e a újrapróbálkozási logikát, így azt sikerült helyreállítani a egy kapcsolatot az átmeneti hibák legyen idejük javítsa ki a saját maga után.  Azt javasoljuk, hogy elhalasztani, az 5 másodperc az első újrapróbálkozás előtt. Újrapróbálkozás rövidebb, mint a felhőszolgáltatás túlterhel 5 másodperc kockázatok késleltetéssel. A próbálkozások a késleltetés kell tágul legfeljebb 60 másodperc.

Átmeneti hibák hibák általában a következő hibaüzenetek az ügyfél-alkalmazásokból származó egyik manifest:

* Adatbázis &lt;db_name&gt; kiszolgálón &lt;Azure_instance&gt; jelenleg nem áll rendelkezésre. Próbálkozzon újra később a kapcsolatot. Ha a probléma tartósan fennáll, forduljon az ügyfélszolgálathoz, és adja meg azokat a munkamenet nyomkövetési Azonosítóját: &lt;munkamenet-azonosítónak&gt;
* Adatbázis &lt;db_name&gt; kiszolgálón &lt;Azure_instance&gt; jelenleg nem áll rendelkezésre. Próbálkozzon újra később a kapcsolatot. Ha a probléma tartósan fennáll, forduljon az ügyfélszolgálathoz, és adja meg azokat a munkamenet nyomkövetési Azonosítóját: &lt;munkamenet-azonosítónak&gt;. (A Microsoft SQL Server, a hiba: 40613)
* Egy létező kapcsolatot a távoli állomás kényszerítette volt.
* System.Data.Entity.Core.EntityCommandExecutionException: Hiba történt a parancsdefiníciót végrehajtása közben. Lásd a belső kivétel üzenetében olvashatók. ---> System.Data.SqlClient.SqlException: átviteli szintű hiba történt A eredményeinek fogadásakor a kiszolgálóról. (szolgáltató: munkamenet-szolgáltató: 19 – fizikai kapcsolat már nem használható)
* Egy másodlagos adatbázis-kapcsolódási kísérlet sikertelen, mert az adatbázis újrakonfigurálása folyamatban van, és foglalt alkalmazása új lapok közepén aktív tranzakciót az elsődleges adatbázison. 

Újrapróbálkozási logika kódot példákért lásd:

* [Adatkapcsolattárak az SQL Database és SQL Server](sql-database-libraries.md) 
* [Az SQL Database-csatlakozási hibák és átmeneti hibák elhárításához műveletek](sql-database-connectivity-issues.md)

Ismerteti azokat a a *blokkolási időtartam* ADO.NET használó ügyfelek számára érhető el [SQL Server készletezési (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Átmeneti hibák hibakódok
A következő hibák átmenetiek, és az alkalmazáslogikának meg kell ismételni: 

| Hibakód | Severity | Leírás |
| ---:| ---:|:--- |
| 4060 |16 |Nem nyitható meg az adatbázis "%.&#x2a;ls" a bejelentkezés által kért. A bejelentkezés sikertelen volt. |
| 40197 |17 |A szolgáltatás a kérelem feldolgozása hibát észlelt. Kérjük, próbálkozzon újból. Hibakód: %d.<br/><br/>Ezt a hibaüzenetet kapja, amikor a szolgáltatás szoftver vagy hardver verziófrissítések, hardver-meghibásodásokkal vagy bármely más feladatátvételi problémák miatt nem működik. Az üzenet hiba 40197 beágyazva hibakód: (%d) milyen típusú hiba vagy feladatátvétel történt további információkat tartalmaz. Néhány példa a kódok vannak beágyazva az üzenet hiba 40197 hiba 40020, 40143, 40166 és 40540.<br/><br/>Az SQL Database-kiszolgáló automatikusan újracsatlakozik kapcsolódik, kifogástalan állapotú másolatot készít az adatbázisról. Az alkalmazás kell a tényleges 40197, hibanaplójában található hibaelhárítási beágyazott hibakód (%d), és próbáljon újra csatlakozni az SQL Database mindaddig, amíg az erőforrás áll rendelkezésre, és újra létrejön a kapcsolat. |
| 40501 |20 |A szolgáltatás jelenleg elfoglalt. Ismételje meg a kérelmet 10 másodperc múlva. Incidens azonosítója: %ls. Hibakód: %d.<br/><br/>További információkért lásd:<br/>• [Azure SQL Database erőforrás-korlátozások](sql-database-service-tiers-dtu.md). |
| 40613 |17 |Adatbázis '%.&#x2a;ls' kiszolgáló '%.&#x2a;ls' már nem érhető el. Próbálkozzon újra később a kapcsolatot. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg azokat a munkamenet nyomkövetési Azonosítóját: '%.&#x2a;ls'. |
| 49918 |16 |Nem tudja feldolgozni a kérést. Nincs elég erőforrás a kérelem feldolgozása.<br/><br/>A szolgáltatás jelenleg elfoglalt. Próbálkozzon újra később a kérelmet. |
| 49919 |16 |Nem lehet folyamatot létrehozni vagy frissíteni a kérés. Túl sok létrehozási vagy frissítési művelet fut. a előfizetés "% ld".<br/><br/>A szolgáltatás nem foglalt feldolgozása több létre, vagy nem frissíthető az előfizetés vagy a kiszolgáló kéréseket. Erőforrás-optimalizálás kérelmek jelenleg le vannak tiltva. Lekérdezés [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) a függőben lévő műveletek. Várjon, amíg a függőben lévő létrehozási vagy frissítési kérelem befejeződött vagy töröljön egy függőben lévő kérelmet, és ismételje meg később a kérelmet. |
| 49920 |16 |Nem tudja feldolgozni a kérést. Túl sok művelet fut. a előfizetéshez tartozó "% ld".<br/><br/>A szolgáltatás kérelmeinek feldolgozásával elfoglalva több ehhez az előfizetéshez. Erőforrás-optimalizálás kérelmek jelenleg le vannak tiltva. Lekérdezés [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) művelet állapotát. Várjon, amíg a függőben lévő kérések befejeződött vagy töröljön egy függőben lévő kérelmet, és ismételje meg később a kérelmet. |
| 4221 |16 |Jelentkezzen be a másodlagos olvasási "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING" hosszú várakozási miatt nem sikerült. A replika nem érhető el bejelentkezés, mert a sor verziók a következők hiányzik a tranzakciók sorverziói, ha a replika újrahasznosításakor. A probléma visszaállítása vagy véglegesítése az elsődleges replika aktív tranzakcióinak megoldhatók. A probléma előfordulását tükrözésével hosszú írási tranzakciókat az elsődleges. |

## <a name="database-copy-errors"></a>Adatbázis-másolat hibák
Hibák a következők is történt a adatbázis másolása az Azure SQL Database-ben. További információk az [Azure SQL Database másolása](sql-database-copy.md) című részben.

| Hibakód | Severity | Leírás |
| ---:| ---:|:--- |
| 40635 |16 |Ügyfél IP-címmel ( '%.&#x2a;ls' ) ideiglenesen le van tiltva. |
| 40637 |16 |Hozzon létre adatbázis-másolás jelenleg le van tiltva. |
| 40561 |16 |Adatbázis másolása sikertelen volt. A forrás vagy a céladatbázis nem létezik. |
| 40562 |16 |Adatbázis másolása sikertelen volt. A forrásadatbázis el lett dobva. |
| 40563 |16 |Adatbázis másolása sikertelen volt. A céladatbázis el lett dobva. |
| 40564 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40565 |16 |Adatbázis másolása sikertelen volt. 1-nél több egyidejű adatbázis-másolat az azonos forrásból érkező engedélyezett. Dobja el a céladatbázist, és próbálkozzon újra később. |
| 40566 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40567 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40568 |16 |Adatbázis másolása sikertelen volt. Forrásadatbázis elérhetetlenné. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40569 |16 |Adatbázis másolása sikertelen volt. Céladatbázis elérhetetlenné vált. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40570 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra később. |
| 40571 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra később. |

## <a name="resource-governance-errors"></a>Erőforrás-szabályozási hibák
Az Azure SQL Database használata során az erőforrások túlzott használata által okozott hibák a következők. Példa:

* Egy tranzakció van nyitva a túl hosszú.
* A tranzakció túl sok zárolva van.
* Egy alkalmazás túl sok memóriát is használja.
* Egy alkalmazás túl sok fogyassza `TempDb` terület.

Kapcsolódó témakörök:

* Részletesebb információk érhetők el itt: [Azure SQL Database erőforrás-korlátozások](sql-database-service-tiers-dtu.md).

| Hibakód | Severity | Leírás |
| ---:| ---:|:--- |
| 10928 |20 |Erőforrás-azonosító: %d. Az adatbázis %s maximális %d, és elérte az. Tovább információ: [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>Az erőforrás-azonosító azt jelzi, hogy az erőforrást, amely elérte a határértéket. A munkaszálak, az erőforrás-azonosítója = 1. A munkamenetek, az erőforrás-azonosítója = 2.<br/><br/>Ez a hiba, és a megoldásának módjával kapcsolatos további információkért lásd:<br/>• [Azure SQL Database erőforrás-korlátozások](sql-database-service-tiers-dtu.md). |
| 10929 |20 |Erőforrás-azonosító: %d. A %s minimális biztonsági: %d, maximális érték: %d, és az adatbázishoz a jelenlegi felhasználás: %d. Azonban az a kiszolgáló jelenleg túlzottan elfoglalt, a nagyobb, mint %d kérelmek támogatásához ehhez az adatbázishoz. Tovább információ: [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Ellenkező esetben próbálkozzon újra később.<br/><br/>Az erőforrás-azonosító azt jelzi, hogy az erőforrást, amely elérte a határértéket. A munkaszálak, az erőforrás-azonosítója = 1. A munkamenetek, az erőforrás-azonosítója = 2.<br/><br/>Ez a hiba, és a megoldásának módjával kapcsolatos további információkért lásd:<br/>• [Azure SQL Database erőforrás-korlátozások](sql-database-service-tiers-dtu.md). |
| 40544 |20 |Az adatbázis elérte a kvótáját méretét. Particionálhat vagy törölhet adatokat, dobjon el indexeket, vagy tekintse át a dokumentációt a lehetséges megoldások megismeréséhez. |
| 40549 |16 |Munkamenet meg lett szakítva, mert egy hosszú ideig futó tranzakció rendelkezik. Próbálja lerövidíteni a tranzakciót. |
| 40550 |16 |A munkamenet megszakadt, mert túl sok zárolást szerzett meg. Próbálja meg olvasó vagy egy tranzakción belül kevesebb sort módosítani. |
| 40551 |16 |A munkamenet lett szakítva, mert túl sok `TEMPDB` használat. Próbálja meg módosítani a lekérdezést az ideiglenes táblák tárterületének felhasználása csökkentése érdekében.<br/><br/>Ha ideiglenes objektumokat használ, a lemezterületet takarítson meg a `TEMPDB` adatbázis ideiglenes objektumok elvetésével után a munkamenet már nincs szükség. |
| 40552 |16 |A munkamenet lett szakítva, mert túlzott mértékben használta a tranzakciós napló lemezterület-használat. Próbáljon egy tranzakción belül kevesebb sort módosítani.<br/><br/>Ha tömeges szúr be használatával a `bcp.exe` segédprogram vagy a `System.Data.SqlClient.SqlBulkCopy` osztály, használja a `-b batchsize` vagy `BatchSize` sorok számának korlátozásához beállítások az egyes tranzakciók a kiszolgálóra másolt. Ha meg vannak az index újraépítése a `ALTER INDEX` utasítással, próbáljon a `REBUILD WITH ONLINE = ON` lehetőséget. |
| 40553 |16 |A munkamenet meg lett szakítva túlzott mértékű memóriahasználat miatt. Próbálja meg módosítani a lekérdezést, hogy kevesebb sort dolgozzon fel.<br/><br/>Számának csökkentése `ORDER BY` és `GROUP BY` műveletek a Transact-SQL-kódot az csökkenti a lekérdezés memóriára vonatkozó követelményeknek. |

## <a name="elastic-pool-errors"></a>Rugalmas készlet hibák
Létrehozásáról és használatáról a rugalmas készletek kapcsolatos hibák a következők:

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |A rugalmas készlet elérte a tárolási kapacitását. A rugalmas készlet tárolási felhasználásának nem haladhatja meg a (%d) MB. |Rugalmas készlet lemezterület-korlát MB-ban. |Próbál adatokat írni egy adatbázist, amikor a rugalmas készlet tárolási korlátot elérte. |Érdemes megfontolni a dtu-k száma és/vagy a rugalmas készletet, ha lehetséges tárolási kapacitását, emeléséhez hozzáadása storage csökkentése a rugalmas készleten belüli egyes adatbázisok által használt tárterület, vagy eltávolíthat adatbázisokat a rugalmas készlet. |
| 10929 |EX_USER |A %s minimális biztonsági: %d, maximális érték: %d, és az adatbázishoz a jelenlegi felhasználás: %d. Azonban az a kiszolgáló jelenleg túlzottan elfoglalt, a nagyobb, mint %d kérelmek támogatásához ehhez az adatbázishoz. Lásd: [ http://go.microsoft.com/fwlink/?LinkId=267637 ](http://go.microsoft.com/fwlink/?LinkId=267637) segítségért. Ellenkező esetben próbálkozzon újra később. |Dtu-k / vcore magok adatbázisonkénti minimális száma; adatbázis Dtu-k / virtuális mag adatbázisonkénti maximális értéke |Egyidejű feldolgozók (kérelmek) között a rugalmas készletben található összes adatbázis teljes száma túllépi a készlet kapacitása történt kísérlet. |Érdemes megfontolni a Dtu- kkal lehetőség a rugalmas készlet virtuális magot annak érdekében, hogy a munkavégző korlátjának növelését, vagy eltávolíthat adatbázisokat a rugalmas készlet. |
| 40844 |EX_USER |Adatbázis "%ls" kiszolgáló "%ls" egy "%ls" kiadású adatbázis egy rugalmas készletben, és nem folyamatos másolás kapcsolat áll fenn. |adatbázis neve, az adatbázis-kiadás, a kiszolgáló neve |Egy StartDatabaseCopy parancs kiadása egy nem prémium szintű adatbázishoz rugalmas készletben. |Hamarosan elérhető |
| 40857 |EX_USER |A kiszolgáló nem található rugalmas készlet: "%ls", a rugalmas készlet neve: "%ls". |kiszolgáló neve rugalmas készlet neve |Megadott rugalmas készlet nem létezik a megadott kiszolgálón. |Adja meg egy érvényes a rugalmas készlet nevét. |
| 40858 |EX_USER |A(z) "%ls" rugalmas készlet már létezik a kiszolgálón: "%ls" |rugalmas készlet neve, a kiszolgáló neve |Megadott rugalmas készlet már létezik a megadott logikai kiszolgálón. |Új rugalmas készlet nevét adja meg. |
| 40859 |EX_USER |Rugalmas készlet nem támogatja a szolgáltatási szint "%ls". |rugalmas készlet szolgáltatási szintjétől |Adott szolgáltatási szinten a rugalmas készlet kiépítése nem támogatott. |Adja meg a megfelelő kiadása, vagy hagyja üresen, használja az alapértelmezett szolgáltatási szinten a szolgáltatási rétegben. |
| 40860 |EX_USER |Rugalmas készlet (%ls) és a szolgáltatás célja "%ls" kombinációja érvénytelen. |rugalmas készlet neve; Szolgáltatásszint |Rugalmas készlet és a szolgáltatási szinten is adhatók meg együtt csak akkor, ha a megadott erőforrástípus "ElasticPool". |Adja meg a rugalmas készlet és a szolgáltatási rétegben megfelelő kombinációja. |
| 40861 |EX_USER |Az adatbázis-kiadás: %. *ls' nem lehet a rugalmas készlet szolgáltatási szintjétől, ami eltér a(z) %.* ls'. |adatbázis-kiadás, rugalmas készlet szolgáltatási szintjétől |Az adatbázis-kiadás nem egyezik a rugalmas készlet szolgáltatási rétegben. |Ne adjon meg egy adatbázis-kiadás, amely eltér attól a rugalmas készlet szolgáltatási szintjétől.  Vegye figyelembe, hogy az adatbázis-kiadás nem kell megadni. |
| 40862 |EX_USER |Rugalmas készlet nevének kell lennie. Ha a rugalmas készlet szolgáltatási célja meg van adva. |None |Rugalmas készlet szolgáltatási célja nem azonosítója egyértelműen rugalmas készletben. |Adja meg a rugalmas készlet nevét, a rugalmas készlet szolgáltatási célja használatakor. |
| 40864 |EX_USER |A rugalmas készlet dtu-i verziójúnak kell lennie (%d) dtu-k szolgáltatási szinthez a(z) %. * ls'. |Rugalmas készlet; esetében a dtu-k rugalmas készlet szolgáltatási szintjétől. |Állítsa be a minimális korlát alatti a rugalmas készlet dtu-i tett kísérlet. |Ismételje meg a rugalmas készletnek a legalább a minimális korlát beállítása a dtu-k. |
| 40865 |EX_USER |A rugalmas készlet dtu-i (%d) dtu-k szolgáltatási szinten nem lehet a(z) % * ls'. |Rugalmas készlet; esetében a dtu-k rugalmas készlet szolgáltatási szintjétől. |Állítsa be a rugalmas készlet meghaladja a maximális dtu-i tett kísérlet. |Ismételje meg a rugalmas készlet dtu-i beállítása nem lehet nagyobb, mint a maximális korlátot. |
| 40867 |EX_USER |A dtu-k adatbázisonkénti maximális értéke nem lehet kisebb legalább (%d) a szolgáltatási szint "% * ls'. |Dtu-k adatbázisonkénti maximális értékét; rugalmas készlet szolgáltatási szintjétől |Kísérlet a dtu-k adatbázisonkénti maximális értékét a támogatott korlátot alatt állítsa be. | Fontolja meg a rugalmas készlet szolgáltatási rétegben, amely támogatja a kívánt beállítást. |
| 40868 |EX_USER |A dtu-k adatbázisonkénti maximális értéke nem lehet nagyobb, mint (%d) szolgáltatási szint "% * ls'. |Dtu-k adatbázisonkénti maximális értékét; rugalmas készlet szolgáltatási szintjétől. |Kísérlet a dtu-k adatbázisonkénti maximális értékét a támogatott gyűjthessen be. | Fontolja meg a rugalmas készlet szolgáltatási rétegben, amely támogatja a kívánt beállítást. |
| 40870 |EX_USER |A dtu-k adatbázisonkénti minimális értéke nem lehet nagyobb, mint (%d) szolgáltatási szint "% * ls'. |Dtu-k adatbázisonkénti minimális értéke; rugalmas készlet szolgáltatási szintjétől. |Kísérlet a dtu-k adatbázisonkénti minimális értéke a támogatott gyűjthessen be. | Fontolja meg a rugalmas készlet szolgáltatási rétegben, amely támogatja a kívánt beállítást. |
| 40873 |EX_USER |Adatbázis (%d) és a dtu-k adatbázisonkénti minimális értéke (%d) száma nem haladhatja meg a dtu-k száma a rugalmas készlet (%d). |A rugalmas készlet; adatbázisok száma Dtu-k adatbázisonkénti minimális értéke; Rugalmas készlet dtu-k. |Adja meg a rugalmas készlet, amely meghaladja a rugalmas készlet dtu-k adatbázisok esetében dtu-k minimális tett kísérlet. | Érdemes megfontolni a rugalmas készlet dtu-k, vagy csökkentse a dtu-k adatbázisonkénti minimális értéke a rugalmas készletben található adatbázisok számának csökkentéséhez. |
| 40877 |EX_USER |Rugalmas készlet nem törölhető, kivéve, ha az összes adatbázis nem tartalmaz. |None |A rugalmas készlet egy vagy több adatbázist tartalmaz, és ezért nem törölhető. |Annak érdekében, hogy a törléshez távolítsa el a rugalmas készlet adatbázisok. |
| 40881 |EX_USER |A rugalmas készlet a(z) % * ls' elérte az adatbázis maximális száma.  Az adatbázis maximális száma a rugalmas készlet esetében (%d) legfeljebb egy rugalmas készlet (% d) dtu-k esetében. |Rugalmas készlet; neve rugalmas készlet; adatbázis számának korlátja erőforrás-készlet edtu-k. |Hozzon létre, vagy adja hozzá adatbázist a rugalmas készlet, ha az adatbázis maximális száma a rugalmas készlet elérte a kísérlet. | Érdemes megfontolni, ha lehetséges a rugalmas készlet dtu-k annak érdekében, hogy az adatbázis korlátjának növelését, vagy eltávolíthat adatbázisokat a rugalmas készlet. |
| 40889 |EX_USER |A dtu-k vagy a rugalmas készlet tárolási kapacitása a(z) % * ls' nem csökkenthető, mivel az, hogy nem biztosítja a elegendő mennyiségű tárhely az adatbázisok számára. |Rugalmas készlet neve. |Kísérlet történt a alább a storage használatát a rugalmas készlet tárolási kapacitása csökkenthető. | Érdemes csökkenteni az egyes adatbázisok a rugalmas készlet tárolási felhasználásának, illetve eltávolíthat adatbázisokat a készletekből a dtu-k vagy tárolási csökkentése érdekében. |
| 40891 |EX_USER |A dtu-k adatbázisonkénti minimális értéke (%d) nem haladhatja meg a dtu-k adatbázisonkénti maximális értékét (%d). |Dtu-k adatbázisonkénti minimális értéke; A dtu-k adatbázisonkénti maximális értéke. |Kísérlet a dtu-k adatbázisonkénti minimális értéke nagyobb, mint a dtu-k adatbázisonkénti maximális értékét állítsa be. |Győződjön meg arról, a dtu-k adatbázisonkénti minimális értéke adatbázisok nem haladja meg a dtu-k adatbázisonkénti maximális értékét. |
| TBD |EX_USER |Az egyes adatbázisok a rugalmas készlet esetében a tároló mérete nem haladhatja meg az által engedélyezett maximális méretét a(z) % * ls' szolgáltatási szinten a rugalmas készlet. |rugalmas készlet szolgáltatási szintjétől |Az adatbázis maximális mérete meghaladja az a rugalmas készlet szolgáltatási szintjétől által engedélyezett maximális méretét. |Az a rugalmas készlet szolgáltatási szintjétől által engedélyezett maximális méretét keretein belül az adatbázis maximális méretének beállítása. |

Kapcsolódó témakörök:

* [Rugalmas készlet (C#) létrehozása](sql-database-elastic-pool-manage-csharp.md) 
* [Rugalmas készlet (C#) kezelése](sql-database-elastic-pool-manage-csharp.md). 
* [Hozzon létre egy rugalmas készlet (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Figyelése és kezelése (PowerShell) rugalmas készlet](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Általános hiba
A következő hibák minden előző kategóriákba nem tartoznak.

| Hibakód | Severity | Leírás |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) azért nem egy érvényes nevet mert érvénytelen karaktereket tartalmaz. |
| 18452 |14 |A bejelentkezés nem sikerült. A bejelentkezés nem megbízható tartományból van, ezért nem használható a Windows authentication.%.&#x2a;ls (a Windows-bejelentkezések nem támogatottak az SQL Server jelen verziójában.) |
| 18456 |14 |Felhasználó bejelentkezése sikertelen volt a(z) '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls(a felhasználó bejelentkezése sikertelen volt: "%.&#x2a;ls". A jelszó módosítása sikertelen volt. Bejelentkezés során a jelszó módosítása nem támogatott az SQL Server jelen verziójában.) |
| 18470 |14 |A felhasználó bejelentkezése sikertelen volt: '%.&#x2a;ls'. OK: A fiók nem disabled.%.&#x2a;ls |
| 40014 |16 |Több adatbázis ugyanabban a tranzakcióban nem használható. |
| 40054 |16 |Egy fürtözött index nélküli táblák nem támogatottak az SQL Server jelen verziójában. Hozzon létre egy fürtözött indexet, és próbálkozzon újra. |
| 40133 |15 |Ez a művelet nem támogatott az SQL Server jelen verziójában. |
| 40506 |16 |Az SQL Server jelen verziójában meghatározott biztonsági azonosító érvénytelen. |
| 40507 |16 |a(z) '%.&#x2a;ls' nem hívható meg paraméterekkel az SQL Server jelen verziójában. |
| 40508 |16 |A USE utasítás nem támogatott az adatbázisok közötti váltáshoz. Egy másik adatbázishoz való kapcsolódáshoz használjon új kapcsolatot. |
| 40510 |16 |Utasítás ('%.&#x2a;ls') az SQL Server jelen verziójában nem támogatott |
| 40511 |16 |Beépített függvény '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40512 |16 |Elavult funkció, "%ls" az SQL Server jelen verziójában nem támogatott. |
| 40513 |16 |Kiszolgálói változó '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40514 |16 |a(z) "%ls" az SQL Server jelen verziójában nem támogatott. |
| 40515 |16 |Hivatkozás adatbázisra és/vagy a kiszolgáló neve a '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40516 |16 |A globális ideiglenes objektumok nem támogatottak az SQL Server jelen verziójában. |
| 40517 |16 |Kulcsszó vagy utasításkapcsoló ('%.&#x2a;ls') az SQL Server jelen verziójában nem támogatott. |
| 40518 |16 |DBCC parancs '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40520 |16 |Az SQL Server jelen verziójában nem támogatott a következő biztonságos osztály (% S_MSG). |
| 40521 |16 |A következő biztonságos osztály (% S_MSG) a kiszolgálói hatókörben az SQL Server jelen verziójában nem támogatott. |
| 40522 |16 |Adatbázis egyszerű '%.&#x2a;ls' típus nem támogatott az SQL Server jelen verziójában. |
| 40523 |16 |Az implicit felhasználói '%.&#x2a;ls' létrehozása az SQL Server jelen verziójában nem támogatott. Explicit módon hozza létre a felhasználó használja azt. |
| 40524 |16 |Adattípus '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40525 |16 |A "%.ls" nem támogatott az SQL Server jelen verziójában. |
| 40526 |16 |a(z) '%.&#x2a;ls' sorkészlet-szolgáltató az SQL Server jelen verziójában nem támogatott. |
| 40527 |16 |Csatolt kiszolgálók nem támogatottak az SQL Server jelen verziójában. |
| 40528 |16 |Felhasználók nem képezhetők le a tanúsítványokat, aszimmetrikus kulcsokra vagy Windows rendszerbeli bejelentkezési adatokra az SQL Server jelen verziójában. |
| 40529 |16 |Beépített függvény '%.&#x2a;ls' megszemélyesítési környezetben nem támogatott az SQL Server jelen verziójában. |
| 40532 |11 |Nem nyitható meg a kiszolgáló "%.&#x2a;ls" a bejelentkezés által kért. A bejelentkezés sikertelen volt. |
| 40553 |16 |A munkamenet meg lett szakítva túlzott mértékű memóriahasználat miatt. Próbálja meg módosítani a lekérdezést, hogy kevesebb sort dolgozzon fel.<br/><br/> Számának csökkentése `ORDER BY` és `GROUP BY` a Transact-SQL kód műveletek csökkenthető a lekérdezés memóriára vonatkozó követelményeknek. |
| 40604 |16 |Nem a CREATE/ALTER DATABASE nem, mert ezzel túllépné a kiszolgáló kvótáját. |
| 40606 |16 |Adatbázisok csatlakoztatása az SQL Server jelen verziójában nem támogatott. |
| 40607 |16 |Windows-bejelentkezések nem támogatottak az SQL Server jelen verziójában. |
| 40611 |16 |Kiszolgálók rendelkezhet egyszerre legfeljebb 128 tűzfalszabály definiálható. |
| 40614 |16 |Tűzfalszabály kezdő IP-címe nem haladhatja meg a záró IP-cím. |
| 40615 |16 |Nem nyitható meg a kiszolgáló "{0}" a bejelentkezés által kért. Ügyfél IP-címmel rendelkező{1}"nem engedélyezett a kiszolgálóhoz való hozzáféréshez.<br /><br />Engedélyezi a hozzáférést, az SQL adatbázis a portálon, vagy futtassa sp\_beállítása\_tűzfal\_szabály hozzon létre egy tűzfalszabályt az IP-címet vagy címtartományt a master adatbázison. A módosítás érvénybe lépéséhez akár öt percig is eltarthat. |
| 40617 |16 |A tűzfalszabály neve, amely elindítja a (szabály neve) neve túl hosszú. Maximális hossza 128. |
| 40618 |16 |A tűzfalszabály neve nem lehet üres. |
| 40620 |16 |A felhasználó bejelentkezése sikertelen volt: "%.&#x2a;ls". A jelszó módosítása sikertelen volt. Bejelentkezés során a jelszó módosítása nem támogatott az SQL Server jelen verziójában. |
| 40627 |20 |A művelet a kiszolgálón "{0}"és az adatbázis"{1}" folyamatban van. Kérjük, várjon néhány percet, majd próbálkozzon újra. |
| 40630 |16 |Jelszó érvényesítése sikertelen volt. A jelszó nem felel meg házirend követelményeinek, mert túl rövid. |
| 40631 |16 |A megadott jelszava túl hosszú. A jelszó legfeljebb 128 karakterrel kell rendelkeznie. |
| 40632 |16 |Jelszó érvényesítése sikertelen volt. A jelszó nem felel meg házirend követelményeinek, mert nem elég bonyolult. |
| 40636 |16 |Egy fenntartott adatbázisnév nem használható  '%.&#x2a;ls' Ebben a műveletben. |
| 40638 |16 |Érvénytelen előfizetés-azonosító (előfizetés-azonosító). Előfizetés nem létezik. |
| 40639 |16 |Kérelem nem felel meg a sémának: (séma hiba). |
| 40640 |20 |A kiszolgáló váratlan kivételbe ütközött. |
| 40641 |16 |A megadott hely érvénytelen. |
| 40642 |17 |A kiszolgáló jelenleg túlzottan elfoglalt. Később próbálja meg újra. |
| 40643 |16 |A megadott x-ms-version Fejlécérték érvénytelen. |
| 40644 |14 |Nem sikerült engedélyezni a hozzáférést a megadott előfizetéshez. |
| 40645 |16 |Kiszolgálónév (kiszolgálónév) nem lehet üres vagy null értékű. Azt is csak feladatátvételicsoport-kisbetűk "a" – "z", a 0 – 9 számokat és kötőjelet. A kötőjel nem vezethet vagy a név végén. |
| 40646 |16 |Előfizetés-azonosító nem lehet üres. |
| 40647 |16 |Előfizetés (előfizetés-azonosító) nem rendelkezik a kiszolgáló (kiszolgálónév). |
| 40648 |17 |Túl sok kérelmet hajtottak végre. Próbálkozzon újra később. |
| 40649 |16 |Érvénytelen tartalomtípus van megadva. Csak az application/xml támogatott. |
| 40650 |16 |Előfizetés (előfizetés-azonosító) nem létezik, vagy nem áll készen a műveletet. |
| 40651 |16 |Nem sikerült létrehozni a kiszolgáló, mert az előfizetés (előfizetés-azonosító) le van tiltva. |
| 40652 |16 |Nem lehet áthelyezni, vagy hozzon létre kiszolgálót. Előfizetés (előfizetés-azonosító) fog haladni a kiszolgáló kvótáját. |
| 40671 |17 |Kommunikációs hiba az átjáró és a felügyeleti szolgáltatás között. Próbálkozzon újra később. |
| 40852 |16 |Adatbázis nem nyitható meg a(z) %. \*ls' kiszolgálón a(z) %. \*ls' a bejelentkezés által kért. Hozzáférés az adatbázishoz biztonságos kapcsolati karakterlánc használatával csak engedélyezett. Az adatbázis eléréséhez a kapcsolati karakterláncokat tartalmazó módosítása "biztonságos" a kiszolgáló teljes Tartománynevében - (server name).database.windows .net kell módosítani, hogy "kiszolgálónév".database. `secure`. windows.net. |
| 40914 | 16 | Nem nyitható meg a kiszolgáló "*[kiszolgálónév]*" a bejelentkezés által kért. Ügyfél számára nem engedélyezett a kiszolgálóhoz való hozzáféréshez.<br /><br />Javításához érdemes lehet hozzáadni egy [virtuális hálózati szabályt](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |Az SQL Azure rendszer terhelés alatt van, és a egy felső korlátot egyidejű DB CRUD-műveletek egy kiszolgálóra helyezi (például adatbázis létrehozása). A kiszolgáló a hibaüzenetben megadott túllépte az egyidejű kapcsolatok maximális számát. Próbálkozzon újra később. |
| 45169 |16 |Az SQL azure-rendszer terhelés alatt van, és felső korlát helyezi a szolgáltatást egy előfizetéshez egyidejű kiszolgáló CRUD-műveletek száma (például a kiszolgáló létrehozása). A hibaüzenetben megadott előfizetés túllépte az egyidejű kapcsolatok maximális számát, és a kérelem visszautasítva. Próbálkozzon újra később. |

## <a name="next-steps"></a>További lépések
* További információ [Azure SQL Database szolgáltatások](sql-database-features.md).
* További információ [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md).
* További információ [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

