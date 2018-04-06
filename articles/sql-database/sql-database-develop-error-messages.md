---
title: SQL - adatbázis kapcsolati hiba hibakódok |} Microsoft Docs
description: 'SQL-adatbázis ügyfélalkalmazások, például a közös adatbázis-kapcsolati hibák, az adatbázis másolása problémák és a általános hibák hibakódok SQL megismerése. '
keywords: SQL-hibakód, hozzáférési sql, adatbázis-kapcsolati hiba, sql-hibakódjai
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 09/28/2017
ms.author: sstein
ms.openlocfilehash: a4fa0d542d755e783f422a62e9135d06133385f8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>Az SQL Database-ügyfélalkalmazások SQL hibakódjai: adatbázis-csatlakozási hibáinak és egyéb problémák

Ez a cikk az SQL-adatbázis ügyfélalkalmazások, például adatbázis-kapcsolati hibák, átmeneti hibák (más néven átmeneti), erőforrás irányítás hibák, adatbázis-másolat problémák, rugalmas készlet és egyéb hibák SQL hibakódok sorolja fel. A legtöbb kategóriák adott, az Azure SQL Database, és nem vonatkoznak a Microsoft SQL Server. Lásd még: [rendszer hibaüzenetek](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Adatbázis-kapcsolati hibák átmeneti hibák és egyéb átmeneti hibák
A következő táblázat ismerteti az adatveszteség-csatlakozási hibáinak és egyéb átmeneti hibák az alkalmazás megpróbál hozzáférni az SQL-adatbázis előforduló SQL hibakódok. Első lépések – oktatóanyagok az Azure SQL adatbázis csatlakoztatása, lásd: [csatlakozás az Azure SQL Database](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Leggyakrabban használt adatbázis-csatlakozási hibáinak és átmeneti hiba hibák
Az Azure-infrastruktúra képes a kiszolgálók dinamikusan átkonfigurálja, amikor a munkaterhelést merülhetnek fel az SQL Database szolgáltatásban.  A dinamikus viselkedés okozhat az ügyfélprogram elveszítik a kapcsolatot az SQL-adatbázishoz. Az ilyen hibaállapotot nevezik egy *átmeneti hiba*.

Erősen ajánlott, hogy az ügyfélprogram újrapróbálkozási logika, hogy sikerült helyreállítani a kapcsolatot adjon megjavul az átmeneti hiba ideje.  Azt javasoljuk, hogy addig elhalasztani, az 5 másodperc az első újrapróbálkozás előtti. Újrapróbálkozás rövidebb, mint a felhőszolgáltatás overwhelming 5 másodperc kockázatok késleltetéssel. A késleltetés kell méretének növelése exponenciálisan növekszik, minden ezt követő újrapróbálkozásra legfeljebb 60 másodperc.

Átmeneti hiba hibák általában a következő hibaüzeneteket a ügyfélprogramok egyik manifest:

* Adatbázis &lt;db_name&gt; kiszolgálón &lt;Azure_instance&gt; már nem érhető el. Próbálkozzon újra később. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg azokat a munkamenet nyomkövetési Azonosítóját: &lt;session_id&gt;
* Adatbázis &lt;db_name&gt; kiszolgálón &lt;Azure_instance&gt; már nem érhető el. Próbálkozzon újra később. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg azokat a munkamenet nyomkövetési Azonosítóját: &lt;session_id&gt;. (A Microsoft SQL Server, hibakód: 40613)
* Egy létező kapcsolat kényszerített módon be lett zárva a távoli állomás.
* System.Data.Entity.Core.EntityCommandExecutionException: Hiba történt a parancsdefiníció végrehajtása közben. Lásd a belső kivétel leírásában olvasható. ---> System.Data.SqlClient.SqlException: egy átviteli szintű hiba történt a kiszolgáló-eredmények fogadásakor. (szolgáltató: munkamenet-szolgáltató, hiba: 19 - fizikai kapcsolat már nem használható)
* Másodlagos adatbázishoz kapcsolódási kísérlet sikertelen volt, mert reconfguration folyamatban van az adatbázis elfoglalt új lapok közepén egy aktív traznakció alkalmazása az elsődleges adatbázisban. 

Újrapróbálkozási logika kód példákért lásd:

* [Adatkapcsolattárak SQL Database és SQL Server](sql-database-libraries.md) 
* [Az SQL-adatbázis kapcsolati hibáinak és átmeneti hibáinak elhárításához műveletek](sql-database-connectivity-issues.md)

Tárgyalja a *blokkolási időtartam* ADO.NET használó ügyfelek számára érhető el [SQL Server készletezését (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Átmeneti hiba hibakódok
Hibák a következők átmeneti jellegűek, és meg kell ismételni a úgy az alkalmazáslogikát: 

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 4060 |16 |Nem nyitható meg az adatbázis "%.&#x2a;ls" a bejelentkezés által kért. A bejelentkezés sikertelen volt. |
| 40197 |17 |A szolgáltatás a kérelem feldolgozása hibát észlelt. Kérjük, próbálkozzon újból. Hibakód: %d.<br/><br/>Ha a szolgáltatás szoftver vagy a hardverfrissítés, a hardver meghibásodása vagy a más feladatátvételi problémák miatt nem működik a hibaüzenetet kap. Az üzenet hiba 40197 ágyazott hibakód: (%d) milyen típusú hiba vagy a feladatátvétel történt további információkkal szolgál. Néhány példa a kódok vannak ágyazva az üzenet hiba 40197 hiba 40020, 40143, 40166 és 40540.<br/><br/>Az SQL Database-kiszolgálóhoz csatlakozni automatikusan csatlakoztatja az adatbázis megfelelő példányához. Az alkalmazás kell catch 40197, hibanapló hibaelhárítási üzeneten belüli beágyazott hibakód: (%d), és próbáljon újra csatlakozni az SQL-adatbázis, az erőforrások elérhetők, amíg újból létrejött a kapcsolat. |
| 40501 |20 |A szolgáltatás jelenleg túlterhelt. Próbálja megismételni a kérelmet 10 másodperc után. Incidens azonosítója: %ls. Kód: %d.<br/><br/>További információkért lásd:<br/>• [Erőforrás-korlátozások az azure SQL Database](sql-database-service-tiers.md). |
| 40613 |17 |Adatbázis '%.&#x2a;ls' kiszolgáló '%.&#x2a;ls' már nem érhető el. Próbálkozzon újra később. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg azokat a munkamenet nyomkövetési Azonosítóját: '%.&#x2a;ls'. |
| 49918 |16 |Nem tudja feldolgozni a kérelmet. Nincs elég erőforrás a kérelem feldolgozásához.<br/><br/>A szolgáltatás jelenleg túlterhelt. Próbálkozzon újra később a kérelmet. |
| 49919 |16 |Nem lehet folyamatot létrehozni vagy frissítési kérelem. Túl sok létrehozási és frissítési művelet előfizetéshez tartozó "% ld".<br/><br/>A szolgáltatás túlterhelt feldolgozása több létrehozása vagy kérelmek az előfizetés vagy a kiszolgáló frissítése. Kérelmek jelenleg nincs hozzáférésük erőforrás-optimalizálás. Lekérdezés [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) a függőben lévő műveletek. Várjon, amíg a létrehozás vagy frissítés függőben lévő kérelmek befejeződött vagy törölje az egyik a függőben lévő kérelmek, és próbálkozzon újra később. |
| 49920 |16 |Nem tudja feldolgozni a kérelmet. Túl sok művelet fut. a előfizetéshez tartozó "% ld".<br/><br/>A szolgáltatás jelenleg elfoglalt ehhez az előfizetéshez több kérelmek feldolgozásához. Kérelmek jelenleg nincs hozzáférésük erőforrás-optimalizálás. Lekérdezés [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) művelet állapot. Várjon, amíg a függőben lévő kérelmek befejeződött vagy törölje az egyik a függőben lévő kérések, és próbálkozzon újra később. |
| 4221 |16 |Olvasási-másodlagos bejelentkezési "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING" hosszú várakozási miatt sikertelen volt. A replika nem érhető el bejelentkezés, mert a sor verziók hiányoznak az egyes tranzakciókra vonatkozóan, amelyek az üzenetsoroktól a replika újraindult. A probléma megoldásához érdemes visszaállítása vagy az elsődleges replikán aktív tranzakciók véglegesítése. A probléma előfordulását hosszú írási tranzakción az elsődleges elkerülésével minimalizálható. |

## <a name="database-copy-errors"></a>Adatbázis-másolási hibák
A következő hiba is történt a adatbázis másolása az Azure SQL-adatbázis. További információk az [Azure SQL Database másolása](sql-database-copy.md) című részben.

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 40635 |16 |Ügyfél IP-címmel ( '%.&#x2a;ls' ) ideiglenesen le van tiltva. |
| 40637 |16 |Hozzon létre adatbázis-másolás jelenleg le van tiltva. |
| 40561 |16 |Adatbázis másolása sikertelen volt. A forrás vagy a céladatbázis nem létezik. |
| 40562 |16 |Adatbázis másolása sikertelen volt. A forrásadatbázis el lett dobva. |
| 40563 |16 |Adatbázis másolása sikertelen volt. A céladatbázis el lett dobva. |
| 40564 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40565 |16 |Adatbázis másolása sikertelen volt. 1-nél több egyidejű adatbázis-másolat ugyanarról a forrásról engedélyezett. Dobja el a céladatbázist, és próbálkozzon újra később. |
| 40566 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40567 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40568 |16 |Adatbázis másolása sikertelen volt. Forrásadatbázis elérhetetlenné vált. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40569 |16 |Adatbázis másolása sikertelen volt. Céladatbázis elérhetetlenné vált. Dobja el a céladatbázist, és próbálkozzon újra. |
| 40570 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra később. |
| 40571 |16 |Az adatbázis másolása belső hiba miatt sikertelen volt. Dobja el a céladatbázist, és próbálkozzon újra később. |

## <a name="resource-governance-errors"></a>Erőforrás-irányítás hibák
A következő hibák okozzák túlzott használt erőforrások az Azure SQL Database szolgáltatással végzett munka közben. Példa:

* Egy tranzakció van nyitva a túl hosszú.
* A tranzakció túl sok zárolva van.
* Az alkalmazás által felhasznált túl sok memóriát.
* Az alkalmazás nem használ-e túl sok `TempDb` terület.

Kapcsolódó témakörök:

* További részletes információk érhető el itt: [erőforrás-korlátozások az Azure SQL Database](sql-database-service-tiers.md).

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 10928 |20 |Erőforrás-azonosító: %d. Az adatbázis %s korlátozást %d, és el lett érve. Tovább információ: [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>Az erőforrás-azonosító azt jelzi, hogy az erőforrást, amely elérte a határértéket. A munkaszálak, az erőforrás-azonosító = 1. A munkamenetekben, az erőforrás-azonosító = 2.<br/><br/>Ez a hiba, és a megoldásának módjával kapcsolatos további információkért lásd:<br/>• [Erőforrás-korlátozások az azure SQL Database](sql-database-service-tiers.md). |
| 10929 |20 |Erőforrás-azonosító: %d. A %s minimális biztonsági: %d, maximális száma: %d, és az adatbázis aktuális kihasználását: %d. Azonban az a kiszolgáló jelenleg túlzottan elfoglalt, a nagyobb, mint %d kérelmek támogatásához ehhez az adatbázishoz. Tovább információ: [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Ellenkező esetben próbálkozzon újra később.<br/><br/>Az erőforrás-azonosító azt jelzi, hogy az erőforrást, amely elérte a határértéket. A munkaszálak, az erőforrás-azonosító = 1. A munkamenetekben, az erőforrás-azonosító = 2.<br/><br/>Ez a hiba, és a megoldásának módjával kapcsolatos további információkért lásd:<br/>• [Erőforrás-korlátozások az azure SQL Database](sql-database-service-tiers.md). |
| 40544 |20 |Az adatbázis elérte a üzenetméret-kvótája. Particionálhat vagy törölhet adatokat, dobjon el indexeket, vagy a dokumentációt a lehetséges megoldások megismeréséhez. |
| 40549 |16 |Munkamenet meg lett szakítva, mert egy hosszú ideig futó tranzakció van. Próbálja lerövidíteni a tranzakciót. |
| 40550 |16 |A munkamenet megszakadt, mert túl sok zárolások szerzett. Próbálja meg olvasása vagy egy tranzakción belül kevesebb sort módosítani. |
| 40551 |16 |A munkamenet megszakadt, mert túl sok `TEMPDB` használat. Próbálja módosítani a lekérdezést az ideiglenes táblák tárterületének felhasználása csökkentése érdekében.<br/><br/>Ideiglenes objektumok használatakor a lemezterületet takarítson meg a `TEMPDB` általi ideiglenes objektumok eldobása után a munkamenet már nem szükséges. |
| 40552 |16 |A munkamenet lett szakítva, mert túlzott mértékben használta a tranzakciós napló lemezterület-használat. Próbáljon egy tranzakción belül kevesebb sort módosítani.<br/><br/>Ha tömeges beszúrása használatával a `bcp.exe` segédprogram vagy a `System.Data.SqlClient.SqlBulkCopy` osztály, használja a a `-b batchsize` vagy `BatchSize` beállításokkal a sorok száma az egyes tranzakciókra kiszolgálóra történő átmásolása. Ha meg vannak az index újraépítése a `ALTER INDEX` utasítás, használja a `REBUILD WITH ONLINE = ON` lehetőséget. |
| 40553 |16 |A munkamenet túl sok memória használata miatt meg lett szakítva. Próbálja meg módosítani a lekérdezést kevesebb sort dolgozzon fel.<br/><br/>Számának csökkentése `ORDER BY` és `GROUP BY` műveletek során a Transact-SQL csökkenti a lekérdezés memóriára vonatkozó követelményeknek. |

## <a name="elastic-pool-errors"></a>A rugalmas készlet hibák
Hibák a következők létrehozása és a rugalmas készleteket használó kapcsolódnak:

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
|:--- |:--- |:--- |:--- |:--- |:--- |
| 1132 |EX_RESOURCE |A rugalmas készlet elérte a tárolási korlátját. A rugalmas készlet tárolási felhasználásának mértéke nem lehet hosszabb (%d) MB. |A rugalmas készlet lemezterület-korlát MB-ban. |Próbál adatokat írni egy adatbázist, a rugalmas készlet tárolási kapacitása elérésekor. |Érdemes megfontolni a dtu-inak száma és/vagy hozzáadása a rugalmas készlethez lehetőleg lehessen növelni a tárolási korlátját, csökkenti a tárolás, a rugalmas készlet belüli egyes adatbázisok által használt, vagy távolítsa el az adatbázisokat a rugalmas készletből. |
| 10929 |EX_USER |A %s minimális biztonsági: %d, maximális száma: %d, és az adatbázis aktuális kihasználását: %d. Azonban az a kiszolgáló jelenleg túlzottan elfoglalt, a nagyobb, mint %d kérelmek támogatásához ehhez az adatbázishoz. Lásd: [ http://go.microsoft.com/fwlink/?LinkId=267637 ](http://go.microsoft.com/fwlink/?LinkId=267637) segítségért. Ellenkező esetben próbálkozzon újra később. |DTU / vCore k adatbázisonkénti minimális értéke; DTU / vCore adatbázisonkénti maximális értéke |Egyidejű munkavállalók (kérelmek) között a rugalmas készlet az összes adatbázis teljes száma túllépi a készlet kapacitása történt kísérlet. |Érdemes megfontolni a dtu-inak vagy, ha lehetséges a rugalmas készlet vCores a munkavégző korlátjának növelésére, vagy távolítsa el az adatbázisokat a rugalmas készletből. |
| 40844 |EX_USER |Adatbázis: "%ls" kiszolgáló "%ls" egy "%ls" kiadású adatbázis, amely egy rugalmas készlet, és nem rendelkezhet a folyamatos másolás kapcsolatot. |adatbázis neve, az adatbázis-kiadás, a kiszolgáló neve |A nem prémium szintű db rugalmas készlethez tartozó StartDatabaseCopy parancs jelenik meg. |Hamarosan elérhető |
| 40857 |EX_USER |A rugalmas készlet nem található a következő kiszolgálón: "%ls", a rugalmas készlet nevét: "%ls". |kiszolgáló; neve a rugalmas készlet nevét |Megadott rugalmas készlet nem létezik a megadott kiszolgálón. |Adja meg egy érvényes rugalmas készlet nevét. |
| 40858 |EX_USER |A rugalmas készlet "%ls" már létezik a kiszolgálón: "%ls" |a rugalmas készlet neve, a kiszolgáló neve |Megadott rugalmas készlet már létezik a megadott logikai kiszolgálón. |Új rugalmas készlet nevét adja meg. |
| 40859 |EX_USER |A rugalmas készlet nem támogatja a szolgáltatási szint "%ls". |a rugalmas készlet szolgáltatási rétegben |Szolgáltatási szint nem támogatott rugalmas készlethez történő üzembe helyezéséhez. |Adja meg a megfelelő edition, vagy hagyja üresen az alapértelmezett szolgáltatásréteg használni szolgáltatási rétegben. |
| 40860 |EX_USER |A rugalmas készlet "%ls" és a szolgáltatás "%ls" cél kombinációja érvénytelen. |a rugalmas készlet neve; szolgáltatási szint célkitűzésének neve |A rugalmas készlet és a szolgáltatási cél adható meg egyszerre csak akkor, ha a "ElasticPool" szolgáltatási cél van megadva. |Adja meg a rugalmas készlet és a szolgáltatási cél megfelelő kombinációja. |
| 40861 |EX_USER |Az adatbázis-kiadás a(z) %. *ls' nem térhet el a rugalmas készlet szolgáltatási szintjétől, amelynek van a(z) %.* ls'. |adatbázis-kiadás, rugalmas készlet szolgáltatási szintjétől |Az adatbázis-kiadás nem egyezik a rugalmas készlet szolgáltatási szintjétől. |Ne adjon meg egy adatbázis-kiadás, amely eltér attól a rugalmas készlet szolgáltatási szintjétől.  Ne feledje, hogy az adatbázis-kiadás nem adható meg. |
| 40862 |EX_USER |Rugalmas készlet nevének kell lennie. Ha a rugalmas készlet szolgáltatási célja meg van adva. |None |Rugalmas készlet szolgáltatási célja nem azonosított egyértelműen egy rugalmas készlet. |Adja meg a rugalmas készlet nevét, ha használja a rugalmas készlet szolgáltatási célja. |
| 40864 |EX_USER |A rugalmas készlet dtu-i verziójúnak kell lennie (%d) dtu-inak száma a szolgáltatási réteg a(z) %. * ls'. |A rugalmas készlet; dtu-i rugalmas készlet szolgáltatási szintjétől. |Kísérlet a minimális korlát alá a rugalmas készlet dtu-i. |Ismételje meg a minimális korlát legalább tárolókészlet rugalmas dtu-i beállítást. |
| 40865 |EX_USER |A rugalmas készlet dtu-i nem lehet hosszabb (%d) dtu-inak száma a szolgáltatási réteg a(z) %. * ls'. |A rugalmas készlet; dtu-i rugalmas készlet szolgáltatási szintjétől. |Kísérlet a meghaladja a maximális rugalmas készlet dtu-i. |Próbálja meg újra a beállítást a rugalmas készlet dtu-i nem lehet nagyobb a maximálisan megengedettet. |
| 40867 |EX_USER |A dtu-k adatbázisonkénti maximális értéke nem lehet kevesebb legalább (%d) szolgáltatási szinthez a(z) %. * ls'. |DTU k adatbázisonkénti maximális értékét; a rugalmas készlet szolgáltatási rétegben |Kísérlet a DTU maximális támogatott meghaladja az adatbázisonként. | a rugalmas készlet szolgáltatási szintjétől, amely támogatja a kívánt beállítás használatával onsider. |
| 40868 |EX_USER |A dtu-k adatbázisonkénti maximális értéke nem lehet nagyobb, mint (%d) szolgáltatási réteg a(z) %. * ls'. |DTU k adatbázisonkénti maximális értékét; rugalmas készlet szolgáltatási szintjétől. |Kísérlet a DTU maximális száma a támogatott határon túlra adatbázisonként. | Érdemes lehet a rugalmas készlet szolgáltatási szintjétől, amely támogatja a kívánt beállítást. |
| 40870 |EX_USER |A DTU-k adatbázisonkénti minimális nem lehet nagyobb, mint (%d) szolgáltatási réteg a(z) %. * ls'. |DTU-k minimális száma az adatbázisban. rugalmas készlet szolgáltatási szintjétől. |A DTU-k száma a támogatott határon túlra adatbázisonkénti minimális kísérlet. | Érdemes lehet a rugalmas készlet szolgáltatási szintjétől, amely támogatja a kívánt beállítást. |
| 40873 |EX_USER |Adatbázisok (%d) és a DTU-k (%d) adatbázisonkénti minimális száma nem haladhatja meg a dtu-inak száma a rugalmas készlet (%d). |Adatbázisok rugalmas készlethez; száma DTU-k minimális száma az adatbázisban. A rugalmas készlet dtu-k. |Adja meg a DTU-k minimális az adatbázisok rugalmas készletben, amelynek hossza meghaladja a rugalmas készlet dtu-i próbál. | Érdemes megfontolni a rugalmas készlet dtu-i, vagy csökkentse a DTU-k adatbázisonkénti minimális csökkentheti a rugalmas készletben lévő adatbázisok számát. |
| 40877 |EX_USER |Egy rugalmas készlet nem törölhető, kivéve, ha az összes adatbázis nem tartalmaz. |Nincs |A rugalmas készlet egy vagy több adatbázist tartalmaz, és ezért nem törölhető. |Ahhoz, hogy törölni a rugalmas készlet adatbázisok távolítsa el. |
| 40881 |EX_USER |A rugalmas készlet a(z) %. * ls' elérte az adatbázis maximális száma.  Az adatbázis maximális száma a rugalmas készlet nem lehet nagyobb, mint (%d) egy rugalmas készlet (% d) dtu-inak száma. |A rugalmas készlet; neve adatbázis maximális száma a rugalmas készlet; az erőforráskészlet edtu-k. |Kísérlet létrehozása, vagy adja hozzá a rugalmas készlet adatbázis az adatbázis maximális száma a rugalmas készlet elérésekor. | Lehetőség szerint a rugalmas készlet Dtu növelje a adatbázis korlátjának növelésére, vagy távolítsa el az adatbázisokat a rugalmas készletből. |
| 40889 |EX_USER |A dtu-inak vagy a rugalmas készlet tárolási kapacitása a(z) %. * ls' csökkentése nem lehetséges, mivel így nem maradna elegendő tárhely az adatbázisok. |A rugalmas készlet neve. |Kísérlet történt a alatt a tárolási használatát a rugalmas készlet tárolási kapacitása csökkenthető. | Próbálja meg csökkenteni az egyes adatbázisok a rugalmas készlet tárolási felhasználásának mértéke, illetve a dtu-inak vagy tárolási kapacitásának csökkentése érdekében távolítsa el adatbázisokat a készletből. |
| 40891 |EX_USER |A DTU-k minimális adatbázis (%d) értéke nem haladhatja meg a DTU k adatbázisonkénti maximális értékét (%d). |DTU-k minimális száma az adatbázisban. A DTU adatbázisonkénti maximális értéke. |Kísérlet a DTU k adatbázisonkénti minimális értéke nagyobb, mint a DTU k adatbázisonkénti maximális értékét. |Győződjön meg arról, a DTU-k minimális értéke adatbázisok nem haladja meg a DTU k adatbázisonkénti maximális értékét. |
| TBD |EX_USER |A rugalmas készlethez egyedi adatbázis tároló mérete nem haladhatja meg az által engedélyezett maximális méretét a(z) %. * ls' szolgáltatási szint rugalmas készlet. |a rugalmas készlet szolgáltatási rétegben |Az adatbázis maximális mérete meghaladja a rugalmas készlet szolgáltatási szintjétől által engedélyezett maximális méretét. |A rugalmas készlet szolgáltatási szintjétől által engedélyezett maximális méretét keretein belül az adatbázis maximális méretének beállítása. |

Kapcsolódó témakörök:

* [Hozzon létre egy rugalmas készlet (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [(C#) rugalmas készletek kezelése](sql-database-elastic-pool-manage-csharp.md). 
* [Hozzon létre egy rugalmas készlet (PowerShell)](sql-database-elastic-pool-manage-powershell.md) 
* [Figyelheti és kezelheti a rugalmas készletekben (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Általános hiba
Hibák a következők nem minden korábbi kategóriába tartoznak.

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 15006 |16 |(AdministratorLogin) alhálózatnév nem érvényes, mert érvénytelen karaktereket tartalmaz. |
| 18452 |14 |A bejelentkezés nem sikerült. A bejelentkezés nem megbízható tartományból van, ezért nem használható a Windows authentication.%.&#x2a;ls (a Windows-bejelentkezések nem támogatottak az SQL Server jelen verziójában.) |
| 18456 |14 |Felhasználó bejelentkezése sikertelen volt a(z) '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls(a felhasználó bejelentkezése sikertelen volt: "%.&#x2a;ls". A jelszó módosítása sikertelen volt. Bejelentkezéskori jelszómódosítás nem támogatott az SQL Server jelen verziójában.) |
| 18470 |14 |A felhasználó bejelentkezése sikertelen volt: '%.&#x2a;ls'. OK: A fiók nem disabled.%.&#x2a;ls |
| 40014 |16 |Több adatbázis ugyanabban a tranzakcióban nem használható. |
| 40054 |16 |Egy fürtözött index nélküli táblákat nem támogatottak az SQL Server jelen verziójában. Hozzon létre egy fürtözött indexet, és próbálkozzon újra. |
| 40133 |15 |Ez a művelet nem támogatott az SQL Server jelen verziójában. |
| 40506 |16 |Megadott SID érvénytelen az SQL Server jelen verziójában. |
| 40507 |16 |a(z) '%.&#x2a;ls' nem hívható meg paraméterekkel az SQL Server jelen verziójában. |
| 40508 |16 |A USE utasítás nem támogatott adatbázisok közötti váltáshoz. Egy másik adatbázishoz való kapcsolódáshoz használjon új kapcsolatot. |
| 40510 |16 |Utasítás ('%.&#x2a;ls') az SQL Server jelen verziójában nem támogatott |
| 40511 |16 |Beépített függvény '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40512 |16 |"%Ls" elavult funkció nem támogatott az SQL Server jelen verziójában. |
| 40513 |16 |Kiszolgálói változó '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40514 |16 |az SQL Server jelen verziójában nem támogatott: "%ls". |
| 40515 |16 |Hivatkozás adatbázisra és/vagy a kiszolgáló neve a '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40516 |16 |A globális ideiglenes objektumok nem támogatottak az SQL Server jelen verziójában. |
| 40517 |16 |Kulcsszó vagy utasításkapcsoló ('%.&#x2a;ls') az SQL Server jelen verziójában nem támogatott. |
| 40518 |16 |DBCC parancs '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40520 |16 |Az SQL Server jelen verziójában nem támogatott a következő biztonságos osztály (% S_MSG). |
| 40521 |16 |A kiszolgálói hatókörben az SQL Server jelen verziójában nem támogatott S_MSG a következő biztonságos osztály (%). |
| 40522 |16 |Adatbázis egyszerű '%.&#x2a;ls' típus nem támogatott az SQL Server jelen verziójában. |
| 40523 |16 |Az implicit felhasználói '%.&#x2a;ls' létrehozása az SQL Server jelen verziójában nem támogatott. Explicit módon létrehozni a felhasználót annak használata előtt. |
| 40524 |16 |Adattípus '%.&#x2a;ls' az SQL Server jelen verziójában nem támogatott. |
| 40525 |16 |"%.Ls" nem támogatja az SQL Server jelen verziójában. |
| 40526 |16 |a(z) '%.&#x2a;ls' sorkészlet-szolgáltató az SQL Server jelen verziójában nem támogatott. |
| 40527 |16 |Csatolt kiszolgálók nem támogatottak az SQL Server jelen verziójában. |
| 40528 |16 |Felhasználói tanúsítványok, aszimmetrikus kulcsokra vagy Windows rendszerbeli bejelentkezési adatokra az SQL Server jelen verziójában nem rendelhető hozzá. |
| 40529 |16 |Beépített függvény '%.&#x2a;ls' megszemélyesítési környezetben nem támogatott az SQL Server jelen verziójában. |
| 40532 |11 |Nem nyitható meg a kiszolgáló "%.&#x2a;ls" a bejelentkezés által kért. A bejelentkezés sikertelen volt. |
| 40553 |16 |A munkamenet túl sok memória használata miatt meg lett szakítva. Próbálja meg módosítani a lekérdezést kevesebb sort dolgozzon fel.<br/><br/> Számának csökkentése `ORDER BY` és `GROUP BY` műveletek során a Transact-SQL csökkentheti a lekérdezés memóriára vonatkozó követelményeknek. |
| 40604 |16 |Nem a CREATE/ALTER DATABASE sikerült, mert ezzel meghaladná a kiszolgáló kvótáját. |
| 40606 |16 |Adatbázisok csatlakoztatása nem támogatott az SQL Server jelen verziójában. |
| 40607 |16 |A Windows-bejelentkezések nem támogatottak az SQL Server jelen verziójában. |
| 40611 |16 |Kiszolgálók rendelkezhet legfeljebb 128 tűzfalszabály definiálható. |
| 40614 |16 |A tűzfalszabály kezdő IP-cím nem haladhatja meg a záró IP-cím. |
| 40615 |16 |Nem nyitható meg a kiszolgáló a bejelentkezés által kért "{0}". Ügyfél IP-cím "{1}" nem engedélyezett a kiszolgálóhoz való hozzáféréshez.<br /><br />Engedélyezi a hozzáférést, az SQL-adatbázis portálon, vagy futtassa a sp\_beállítása\_tűzfal\_a főadatbázison való futtatásával hozzon létre egy tűzfalszabályt ehhez IP-cím vagy a címtartomány-szabályok. A módosítás érvénybe lépéséhez akár öt percet is igénybe vehet. |
| 40617 |16 |A tűzfalszabály-név, amely elindítja a (szabály neve) értéke túl hosszú. Maximális hossza 128. |
| 40618 |16 |A tűzfalszabály-név nem lehet üres. |
| 40620 |16 |A felhasználó bejelentkezése sikertelen volt: "%.&#x2a;ls". A jelszó módosítása sikertelen volt. Bejelentkezéskori jelszómódosítás nem támogatott az SQL Server jelen verziójában. |
| 40627 |20 |A kiszolgáló "{0}" és az adatbázis "{1}" művelet van folyamatban. Várjon néhány percet, majd próbálja újra. |
| 40630 |16 |Jelszó érvényesítése sikertelen volt. A jelszó nem felel meg házirend követelményeinek, mert túl rövid. |
| 40631 |16 |A megadott jelszó nem túl hosszú. A jelszót kell legfeljebb 128 karakterből állhat. |
| 40632 |16 |Jelszó érvényesítése sikertelen volt. Mivel az nem elég bonyolult a jelszó nem felel meg házirend követelményeinek. |
| 40636 |16 |Egy fenntartott adatbázisnév nem használható  '%.&#x2a;ls' Ebben a műveletben. |
| 40638 |16 |Érvénytelen előfizetés-azonosító (előfizetés-azonosító). Előfizetés nem létezik. |
| 40639 |16 |Igénylés nem felel meg a sémának: (séma hiba). |
| 40640 |20 |A kiszolgáló váratlan kivételt észlelt. |
| 40641 |16 |A megadott hely érvénytelen. |
| 40642 |17 |A kiszolgáló jelenleg túlzottan elfoglalt. Később próbálja meg újra. |
| 40643 |16 |A megadott x-ms-version fejlécértéke érvénytelen. |
| 40644 |14 |Nem sikerült a hozzáférés hitelesítése a megadott előfizetéshez. |
| 40645 |16 |Kiszolgálónév (kiszolgálónév) nem lehet üres vagy null. Azt is csak állhat kisbetűk "a" – "z", a 0 – 9 számokat és a kötőjel. A kötőjel nem okozhat, és a név végén. |
| 40646 |16 |Előfizetés-azonosító nem lehet üres. |
| 40647 |16 |Előfizetés (előfizetés-azonosító) nem rendelkezik a kiszolgáló (kiszolgálónév). |
| 40648 |17 |Túl sok kérelmet hajtottak végre. Próbálkozzon újra később. |
| 40649 |16 |Érvénytelen content-type van megadva. Csak az application/xml támogatott. |
| 40650 |16 |Előfizetési (előfizetés-azonosító) nem létezik vagy nem áll készen a műveletre. |
| 40651 |16 |Nem sikerült létrehozni a kiszolgálón, mert az előfizetés (előfizetés-azonosító) le van tiltva. |
| 40652 |16 |Nem helyezhető át, és a kiszolgáló létrehozása. (Előfizetés-azonosító) előfizetés meg fogja haladni a kiszolgáló kvótáját. |
| 40671 |17 |Kommunikációs hiba az átjáró és a felügyeleti szolgáltatás között. Próbálkozzon újra később. |
| 40852 |16 |Adatbázis nem nyitható meg a(z) %. \*ls' kiszolgáló "%. \*a bejelentkezés által kért ls'. Az adatbázis csak elérését a biztonságos kapcsolati karakterláncok használata. Az adatbázis eléréséhez a kapcsolati karakterláncokat tartalmazhat módosítása "biztonságos" a kiszolgáló FQDN - (server name).database.windows .net kell módosítani a "server name".database. `secure`. windows.net. |
| 40914 | 16 | Nem nyitható meg a kiszolgáló "*[kiszolgálónév]*" a bejelentkezés által kért. Ügyfél számára nem engedélyezett a kiszolgálóhoz való hozzáféréshez.<br /><br />Megoldása érdekében vegyen fel egy [virtuális hálózati szabály](sql-database-vnet-service-endpoint-rule-overview.md). |
| 45168 |16 |Az SQL Azure rendszer terhelésnek van kitéve, és egy önálló kiszolgálóhoz egyidejűleg DB CRUD műveletek felső korlát helyezi (például adatbázis létrehozása). A kiszolgáló, a hibaüzenetben megadott túllépte az egyidejű kapcsolatok maximális számát. Próbálkozzon újra később. |
| 45169 |16 |Az SQL azure rendszer terhelésnek van kitéve, és felső korlát helyezi egyidejű server CRUD műveletek az egy-egy előfizetéshez száma (például a kiszolgáló létrehozása). A hibaüzenetben megadott előfizetés túllépte az egyidejű kapcsolatok maximális számát, és a kérelem visszautasítva. Próbálkozzon újra később. |

## <a name="next-steps"></a>További lépések
* További információ a [Azure SQL adatbázis-szolgáltatások](sql-database-features.md).
* További információ a [szolgáltatásszintek](sql-database-service-tiers.md).

