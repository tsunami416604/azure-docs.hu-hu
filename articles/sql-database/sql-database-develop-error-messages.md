---
title: SQL-hibakódok – adatbázis-csatlakoztatási hiba | Microsoft Docs
description: 'Ismerje meg az SQL Database ügyfélalkalmazások SQL-hibakódait, például a gyakori adatbázis-csatlakoztatási hibákat, az adatbázis-másolási problémákat és az általános hibákat. '
keywords: SQL-hibakód, Access SQL, adatbázis-kapcsolati hiba, SQL-hibakódok
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/02/2019
ms.openlocfilehash: 19febc5a0a6e4a72cfebfaecd917185538130152
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035022"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>SQL-hibakódok SQL Database ügyfélalkalmazások számára: adatbázis-csatlakoztatási hibák és egyéb problémák

Ez a cikk felsorolja az SQL Database ügyfélalkalmazások SQL-hibakódait, például az adatbázis-kapcsolatok hibáit, az átmeneti hibákat (más néven átmeneti hibák), az erőforrás-irányítási hibákat, az adatbázis-másolási problémákat, a rugalmas készletet és más hibákat. A legtöbb kategória különösen Azure SQL Database, és nem vonatkozik a Microsoft SQL Serverra. Lásd még: [rendszerhiba-üzenetek](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx).

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Adatbázis-csatlakoztatási hibák, átmeneti hibák és egyéb ideiglenes hibák

Az alábbi táblázat az SQL-hibakódokat tartalmazza a kapcsolat elvesztése miatti hibákra vonatkozóan, és egyéb átmeneti hibákat is tapasztalhat, amikor az alkalmazás megpróbál hozzáférni SQL Databasehoz. A Azure SQL Databasehoz való kapcsolódással kapcsolatos útmutatókért lásd: [csatlakozás Azure SQL Databasehoz](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Leggyakoribb adatbázis-csatlakoztatási hibák és átmeneti hibák

Az Azure-infrastruktúra az SQL Database szolgáltatásban fellépő nagy mértékű terhelés esetén dinamikusan újra tudja konfigurálni a kiszolgálókat.  E dinamikus viselkedés miatt megszakadhat az ügyfélprogram és az SQL Database közötti kapcsolat. Az ilyen típusú hiba feltételének neve *átmeneti hiba*.

Erősen ajánlott, hogy az ügyfélalkalmazás újra megismételje a logikát, hogy újra létre lehessen hozni a kapcsolatot, miután megadta az átmeneti hibák idejét.  Javasoljuk, hogy az első újrapróbálkozás előtt 5 másodpercig várjon. 5 másodpercnél rövidebb idő elteltével próbálkozzon újra a felhőalapú szolgáltatással. Minden további újrapróbálkozás esetén a késleltetés exponenciálisan növekszik, legfeljebb 60 másodpercig.

Az átmeneti hibák hibái általában a következő hibaüzenetek egyike jelennek meg az ügyfélprogramokban:

* Adatbázis &lt;db_name @ no__t-1 a kiszolgálón &lt;Azure_instance @ no__t-3 jelenleg nem érhető el. Később próbálja megismételni a kapcsolatokat. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg az &lt;session_id @ no__t-1 munkamenet-nyomkövetési AZONOSÍTÓját.
* Adatbázis &lt;db_name @ no__t-1 a kiszolgálón &lt;Azure_instance @ no__t-3 jelenleg nem érhető el. Később próbálja megismételni a kapcsolatokat. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg nekik a &lt;session_id @ no__t-1 munkamenet-nyomkövetési AZONOSÍTÓját. (Microsoft SQL Server, hiba: 40613)
* A távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat.
* System. Rea. Entity. Core. EntityCommandExecutionException: hiba történt a parancs definíciójának végrehajtása során. További részletekért tekintse meg a belső kivételt. ---> System. adatok. SqlClient. SqlException: átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. (szolgáltató: munkamenet-szolgáltató, hiba: 19 – a fizikai kapcsolat nem használható)
* Egy másodlagos adatbázishoz való kapcsolódási kísérlet sikertelen volt, mert az adatbázis újrakonfigurálási folyamatban van, és az új lapokat az elsődleges adatbázis aktív tranzakciójának közepén foglalt. 

Az újrapróbálkozási logikával kapcsolatos Példákért lásd:

* [SQL Database és SQL Serverhoz tartozó kapcsolatok kódtárai](sql-database-libraries.md) 
* [A kapcsolódási hibák és az átmeneti hibák kijavítására szolgáló műveletek a SQL Database](sql-database-connectivity-issues.md)

A ADO.NET-t használó ügyfelek *blokkolási időszakának* megtárgyalása [SQL Server kapcsolatok készletezésében (ADO.net)](https://msdn.microsoft.com/library/8xx3tyca.aspx)érhető el.

### <a name="transient-fault-error-codes"></a>Átmeneti hibák hibakódja

A következő hibák átmenetiek, és az alkalmazás logikájában újra kell próbálkozni: 

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 4060 |16 |A (z)% adatbázis nem nyitható meg. &#x2a;ls "a bejelentkezés által kért. A bejelentkezés sikertelen. További információ: [4000 – 4999. hibák](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |A szolgáltatás hibát észlelt a kérelem feldolgozásakor. Kérjük, próbálkozzon újból. Hibakód:% d.<br/><br/>Ez a hibaüzenet akkor jelenik meg, ha a szolgáltatás szoftver vagy hardveres frissítés, hardverhiba vagy bármilyen más feladatátvételi probléma miatt leáll. A 40197-es [hiba üzenetében beágyazott](sql-database-develop-error-messages.md#embedded-error-codes) hibakód (% d) további információkat nyújt a hiba vagy a feladatátvételi típusról. Néhány példa a hibakódokra a 40197-es hiba üzenetében található, 40020, 40143, 40166 és 40540.<br/><br/>A SQL Database-kiszolgálóhoz való újracsatlakozás automatikusan csatlakozik az adatbázis egy kifogástalan állapotú példányához. Az alkalmazásnak meg kell fognia a 40197-es hibát, be kell jelentkeznie a (z) "% d" beágyazott hibakódra az üzenetben a hibaelhárításhoz, és újra kell csatlakoznia SQL Database, amíg az erőforrások elérhetővé válnak, és a kapcsolat újból létrejön. További információ: [átmeneti hibák](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |A szolgáltatás jelenleg foglalt. Próbálja megismételni a kérést 10 másodperc múlva. Incidens azonosítója:% ls. Kód:% d. További információ eléréséhez lásd: <br/>&bull; &nbsp;[adatbázis-kiszolgáló erőforrás-korlátai](sql-database-resource-limits-database-server.md)<br/>@no__t – 0 &nbsp;[DTU-alapú korlát egyetlen adatbázishoz](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-alapú korlát a rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>@no__t – 0 &nbsp;[virtuális mag-alapú korlát egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[felügyelt példány erőforrás-korlátai](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Adatbázis:%. &#x2a;ls a (z)% kiszolgálón. &#x2a;az ls jelenleg nem érhető el. Később próbálja megismételni a kapcsolatokat. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz, és adja meg nekik a (z)% munkamenet-nyomkövetési AZONOSÍTÓját. &#x2a;ls ".<br/><br/> Ez a hiba akkor fordulhat elő, ha már létezik egy meglévő dedikált rendszergazdai kapcsolódás (DAC) az adatbázishoz. További információ: [átmeneti hibák](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |A kérelem nem dolgozható fel. Nincs elég erőforrás a kérelem feldolgozásához.<br/><br/>A szolgáltatás jelenleg foglalt. Próbálkozzon újra a kéréssel. További információ eléréséhez lásd: <br/>&bull; &nbsp;[adatbázis-kiszolgáló erőforrás-korlátai](sql-database-resource-limits-database-server.md)<br/>@no__t – 0 &nbsp;[DTU-alapú korlát egyetlen adatbázishoz](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-alapú korlát a rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>@no__t – 0 &nbsp;[virtuális mag-alapú korlát egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[felügyelt példány erőforrás-korlátai](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |A létrehozási vagy frissítési kérelem nem dolgozható fel. Túl sok létrehozási vagy frissítési művelet van folyamatban a (z) "% ld" előfizetéshez.<br/><br/>A szolgáltatás foglalt, több létrehozására vagy frissítésre vonatkozó kérelmet dolgoz fel az előfizetéshez vagy a kiszolgálóhoz. A kérések jelenleg le vannak tiltva az erőforrás-optimalizáláshoz. A függőben lévő műveletekhez tartozó [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) lekérdezése. Várjon, amíg a függőben lévő létrehozási vagy frissítési kérelmek befejeződik, vagy törölje a függőben lévő kérelmek valamelyikét, majd ismételje meg a kérést. További információ eléréséhez lásd: <br/>&bull; &nbsp;[adatbázis-kiszolgáló erőforrás-korlátai](sql-database-resource-limits-database-server.md)<br/>@no__t – 0 &nbsp;[DTU-alapú korlát egyetlen adatbázishoz](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-alapú korlát a rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>@no__t – 0 &nbsp;[virtuális mag-alapú korlát egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[felügyelt példány erőforrás-korlátai](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |A kérelem nem dolgozható fel. Túl sok művelet van folyamatban a (z) "% ld" előfizetéshez.<br/><br/>A szolgáltatás foglalt több kérést dolgoz fel ehhez az előfizetéshez. A kérések jelenleg le vannak tiltva az erőforrás-optimalizáláshoz. A művelet állapotának lekérdezése a [sys. DM _operation_status](https://msdn.microsoft.com/library/dn270022.aspx) . Várjon, amíg a függőben lévő kérelmek befejeződik, vagy törölje a függőben lévő kérelmek valamelyikét, majd ismételje meg a kérést. További információ eléréséhez lásd: <br/>&bull; &nbsp;[adatbázis-kiszolgáló erőforrás-korlátai](sql-database-resource-limits-database-server.md)<br/>@no__t – 0 &nbsp;[DTU-alapú korlát egyetlen adatbázishoz](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-alapú korlát a rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>@no__t – 0 &nbsp;[virtuális mag-alapú korlát egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[felügyelt példány erőforrás-korlátai](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |A "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING" hosszú várakozás miatt nem sikerült bejelentkezni a Read-másodlagosra. A replika nem érhető el a bejelentkezéshez, mert a rendszer a replikák újrahasznosítása során a folyamatban lévő tranzakciók esetében hiányzik a sorok verziószáma. A probléma megoldásához visszaállíthatja vagy véglegesítheti az aktív tranzakciókat az elsődleges replikán. Ennek az állapotnak az előfordulásait a hosszú írási tranzakciók elkerülésével lehet csökkenteni. |

## <a name="embedded-error-codes"></a>Beágyazott hibakódok

A következő hibák a 40197-es általánosabb hibakódba vannak beágyazva:

```
The service has encountered an error processing your request. Please try again. Error code %d.
```

| Hibakód | Súlyosság | Leírás | 
| ---:| ---:|:---|
|  1104 |17 |A TEMPDB kifogyott a területről a kiömlött folyamat során. Hozzon létre helyet az objektumok eldobásával, vagy írja át a lekérdezést úgy, hogy kevesebb sort fogyasszon. Ha a probléma továbbra is fennáll, érdemes lehet magasabb szolgáltatási szintű célkitűzésre frissíteni.|
| 40020 |16 |Az adatbázis átmeneti állapotban van, a tranzakciók leállítása folyamatban van.|
| 40143 |16 |Az adatcsomóponton a kért partícióhoz tartozó replika nem elsődleges.|
| 40166 |16 |A rendszer megszakítja a CloudDB újrakonfigurálását, és minden új felhasználói tranzakció megszakad.|
| 40540 |16 |A tranzakció meg lett szakítva, mert az adatbázis csak olvasható módba kerül. Ez egy ideiglenes helyzet, és próbálkozzon újra a művelettel.|

A más beágyazott hibák részletei a `sys.messages` lekérdezésével találhatók:

```sql
SELECT * FROM sys.[messages] WHERE [message_id] = <error_code>
```

## <a name="database-copy-errors"></a>Adatbázis-másolási hibák

A következő hibák fordulhatnak elő az adatbázisok Azure SQL Databaseban történő másolása során. További információk az [Azure SQL-adatbázis másolása](sql-database-copy.md) című részben.

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 40635 |16 |A (z)% IP-címmel rendelkező ügyfél. &#x2a;az ls átmenetileg le van tiltva. |
| 40637 |16 |Az adatbázis-másolat létrehozása jelenleg le van tiltva. |
| 40561 |16 |Az adatbázis másolása sikertelen volt. A forrás-vagy a céladatbázis nem létezik. |
| 40562 |16 |Az adatbázis másolása sikertelen volt. A forrásadatbázis el lett dobva. |
| 40563 |16 |Az adatbázis másolása sikertelen volt. A célként megadott adatbázis el lett dobva. |
| 40564 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40565 |16 |Az adatbázis másolása sikertelen volt. Nem engedélyezett több mint 1 egyidejű adatbázis-másolat ugyanabból a forrásból. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra később. |
| 40566 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40567 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40568 |16 |Az adatbázis másolása sikertelen volt. A forrásadatbázis elérhetetlenné vált. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40569 |16 |Az adatbázis másolása sikertelen volt. A céladatbázis elérhetetlenné vált. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra. |
| 40570 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra később. |
| 40571 |16 |Az adatbázis másolása belső hiba miatt nem sikerült. Dobja el a céladatbázis-adatbázist, és próbálkozzon újra később. |

## <a name="resource-governance-errors"></a>Erőforrás-irányítási hibák

A következő hibákat okozhatja az erőforrások túlzott használata az Azure SQL Database használata közben. Példa:

* Egy tranzakció túl hosszú ideig nyitva van.
* Egy tranzakció túl sok zárolást tart fenn.
* Egy alkalmazás túl sok memóriát vesz igénybe.
* Egy alkalmazás túl sok @no__t – 0 területet fogyaszt.

Kapcsolódó témakörök:

* További információ eléréséhez lásd:
  * [Adatbázis-kiszolgáló erőforrás-korlátai](sql-database-resource-limits-database-server.md)
  * [DTU-alapú korlátok önálló adatbázisokhoz](sql-database-service-tiers-dtu.md)
  * [Rugalmas készletek DTU-alapú korlátai](sql-database-dtu-resource-limits-elastic-pools.md)
  * [Virtuális mag-alapú korlátok önálló adatbázisokhoz](sql-database-vcore-resource-limits-single-databases.md)
  * [rugalmas készletek virtuális mag-alapú korlátai](sql-database-vcore-resource-limits-elastic-pools.md)
  * [Felügyelt példányok erőforrás-korlátai](sql-database-managed-instance-resource-limits.md). 

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 10928 |20 |Erőforrás-azonosító:% d. Az adatbázis% s korlátja% d, és elérte a következőt:. További információ: [az önálló és a készletezett adatbázisok SQL Database erőforrás-korlátai](sql-database-resource-limits-database-server.md).<br/><br/>Az erőforrás-azonosító azt az erőforrást jelzi, amely elérte a korlátot. Munkaszálak esetében az erőforrás-azonosító = 1. A munkamenetek esetében az erőforrás-azonosító = 2.<br/><br/>A hibával és megoldásával kapcsolatos további információkért lásd: <br/>&bull; &nbsp;[adatbázis-kiszolgáló erőforrás-korlátai](sql-database-resource-limits-database-server.md)<br/>@no__t – 0 &nbsp;[DTU-alapú korlát egyetlen adatbázishoz](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-alapú korlát a rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>@no__t – 0 &nbsp;[virtuális mag-alapú korlát egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[felügyelt példány erőforrás-korlátai](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Erőforrás-azonosító:% d. A (z)% s minimális garancia% d, a maximális korlát% d, az adatbázis jelenlegi használata pedig% d. Azonban a kiszolgáló jelenleg túl elfoglalt ahhoz, hogy támogassa a (z)% d-nál nagyobb kérelmeket ehhez az adatbázishoz. Az erőforrás-azonosító azt az erőforrást jelzi, amely elérte a korlátot. Munkaszálak esetében az erőforrás-azonosító = 1. A munkamenetek esetében az erőforrás-azonosító = 2. További információ eléréséhez lásd: <br/>&bull; &nbsp;[adatbázis-kiszolgáló erőforrás-korlátai](sql-database-resource-limits-database-server.md)<br/>@no__t – 0 &nbsp;[DTU-alapú korlát egyetlen adatbázishoz](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-alapú korlát a rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>@no__t – 0 &nbsp;[virtuális mag-alapú korlát egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[felügyelt példány erőforrás-korlátai](sql-database-managed-instance-resource-limits.md). <br/>Ellenkező esetben próbálkozzon újra később. |
| 40544 |20 |Az adatbázis elérte a méretre vonatkozó kvótát. Particionálja vagy törölje az adatvesztést, az indexek eldobását, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat. Az adatbázis-méretezéssel kapcsolatban lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).|
| 40549 |16 |A munkamenet meg lett szakítva, mert hosszú ideig futó tranzakció van. Próbálja meg lerövidíteni a tranzakciót. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |A munkamenet meg lett szakítva, mert túl sok zárolást szerzett. Próbáljon meg egy tranzakción belül kevesebb sort olvasni vagy módosítani. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |A munkamenet megszakadt, mert túl sok @no__t – 0 használat van. Próbálja meg módosítani a lekérdezést, hogy csökkentse az ideiglenes tábla területének használatát.<br/><br/>Ha ideiglenes objektumokat használ, az ideiglenes objektumok eldobásával szabadítson fel helyet a `TEMPDB` adatbázisban, ha a munkamenet már nem igényel többé. További információ a SQL Database tempdb használatáról: tempdb- [adatbázis SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |A munkamenet meg lett szakítva a tranzakciós naplók túlzott kihasználtsága miatt. Próbáljon meg kevesebb sort módosítani egyetlen tranzakcióban. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](sql-database-use-batching-to-improve-performance.md).<br/><br/>Ha a `bcp.exe` segédprogrammal vagy a `System.Data.SqlClient.SqlBulkCopy` osztály használatával hajtja végre a tömeges beszúrást, próbálja meg a `-b batchsize` vagy a `BatchSize` kapcsoló használatával korlátozni az egyes tranzakciókban a kiszolgálóra másolt sorok számát. Ha a `ALTER INDEX` utasítással újraépít egy indexet, próbálja meg használni a `REBUILD WITH ONLINE = ON` kapcsolót. A virtuális mag-vásárlási modell tranzakciós naplójának méretével kapcsolatos információkért lásd: <br/>@no__t – 0 &nbsp;[virtuális mag-alapú korlát egyetlen adatbázishoz](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletekhez](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[felügyelt példány erőforrás-korlátai](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |A munkamenet túl sok memóriahasználat miatt megszakadt. Próbálja meg módosítani a lekérdezést úgy, hogy kevesebb sort dolgozzon fel.<br/><br/>A `ORDER BY` és a `GROUP BY` műveletek számának csökkentése a Transact-SQL-kódban csökkenti a lekérdezés memóriabeli követelményeit. Az adatbázis-méretezéssel kapcsolatban lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Rugalmas készlettel kapcsolatos hibák

A rugalmas készletek létrehozásával és használatával kapcsolatos hibák a következők:

| Hibakód | Súlyosság | Leírás | Javító művelet |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |A rugalmas készlet elérte a tárolási korlátot. A rugalmas készlet tárolási kihasználtsága nem haladhatja meg a (z) (% d) MB-ot. Egy adatbázisba való adatírásra tett kísérlet, ha elérte a rugalmas készlet tárolási korlátját. Az erőforrás-korlátokkal kapcsolatos további információkért lásd: <br/>&bull; &nbsp;[DTU-alapú korlát a rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletek számára](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Ha lehetséges, növelje a rugalmas készlethez való DTU és/vagy a tárterület hozzáadását, csökkentse a rugalmas készletben lévő egyes adatbázisok által használt tárterületet, vagy távolítsa el az adatbázisokat a rugalmas készletből. A rugalmas készlet skálázásával kapcsolatban lásd: [rugalmas készlet erőforrásainak](sql-database-elastic-pool-scale.md)méretezése.|
| 10929 | 16 |A (z)% s minimális garancia% d, a maximális korlát% d, az adatbázis jelenlegi használata pedig% d. Azonban a kiszolgáló jelenleg túl elfoglalt ahhoz, hogy támogassa a (z)% d-nál nagyobb kérelmeket ehhez az adatbázishoz. Az erőforrás-korlátokkal kapcsolatos további információkért lásd: <br/>&bull; &nbsp;[DTU-alapú korlát a rugalmas készletekhez](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[virtuális mag-alapú korlát a rugalmas készletek számára](sql-database-vcore-resource-limits-elastic-pools.md). <br/> Ellenkező esetben próbálkozzon újra később. DTU/virtuális mag/perc/adatbázis; DTU/virtuális mag-adatbázis maximális száma. Az egyidejű feldolgozók (kérelmek) teljes száma a rugalmas készletben lévő összes adatbázisban, a készlet korlátjának túllépése miatt. |Ha lehetséges, érdemes lehet növelni a rugalmas készlet DTU vagy virtuális mag, hogy növelje a munkavégző korlátot, vagy távolítsa el az adatbázisokat a rugalmas készletből. |
| 40844 | 16 |A (z) "% ls" kiszolgáló "% ls" adatbázisa egy rugalmas készletben lévő "% ls" kiadási adatbázis, és nem lehet folytonos másolási kapcsolat.  |– |
| 40857 | 16 |Nem található rugalmas készlet a következő kiszolgálóhoz: "% ls", rugalmas készlet neve: "% ls". A megadott rugalmas készlet nem létezik a megadott kiszolgálón. | Adja meg a rugalmas készlet érvényes nevét. |
| 40858 | 16 |A (z) "% ls" rugalmas készlet már létezik a következő kiszolgálón: "% ls". A megadott rugalmas készlet már létezik a megadott SQL Database-kiszolgálón. | Adja meg az új rugalmas készlet nevét. |
| 40859 | 16 |A rugalmas készlet nem támogatja a (z)% ls szolgáltatási szintet. A megadott szolgáltatási szintet nem támogatja a rugalmas készlet kiépítés. |Adja meg a megfelelő kiadást, vagy hagyja üresen a szolgáltatási szintet, hogy az alapértelmezett szolgáltatási szintet használja. |
| 40860 | 16 |A (z) "% ls" rugalmas készlet és a (z) "% ls" szolgáltatási cél nem érvényes. A rugalmas készlet és a szolgáltatási szintek csak akkor adhatók meg együtt, ha az erőforrástípus a következőként van megadva: "ElasticPool". |A rugalmas készlet és a szolgáltatási szintek helyes kombinációjának meghatározása. |
| 40861 | 16 |A (z)% adatbázis-kiadás *az ls nem lehet eltér a rugalmas készlet szolgáltatási szintjétől, amely a következő:%.* ls ". Az adatbázis kiadása eltér a rugalmas készlet szolgáltatási szintjétől. |Ne határozzon meg olyan adatbázis-kiadást, amely eltér a rugalmas készlet szolgáltatási szintjétől.  Vegye figyelembe, hogy az adatbázis-kiadást nem kell megadni. |
| 40862 | 16 |Ha a rugalmas készlet szolgáltatási célja meg van adva, akkor meg kell adni a rugalmas készlet nevét. A rugalmas készlet szolgáltatási célja nem azonosítja egyedi módon a rugalmas készletet. |Ha a rugalmas készlet szolgáltatási célkitűzését használja, adja meg a rugalmas készlet nevét. |
| 40864 | 16 |A rugalmas készlet DTU legalább (% d) DTU kell lennie a (z)%. * ls szolgáltatási szinten. Kísérlet a minimális korlát alatti rugalmas készlet DTU beállítására. |Próbálja megismételni a rugalmas készlet DTU beállítását legalább a minimális korlátra. |
| 40865 | 16 |A rugalmas készlet DTU nem lehet hosszabb (% d) DTU a (z)%. * ls szolgáltatási szinten. Kísérlet a maximális korlátot meghaladó rugalmas készlet DTU beállítására. |Próbálja meg újra beállítani a rugalmas készlet DTU, hogy ne legyen nagyobb a maximális korlátnál. |
| 40867 | 16 |A (z)%. * ls szolgáltatási szinten a DTU Max/adatbázisnak legalább (% d) kell lennie. A DTU maximális száma a támogatott korlát alatt állítható be. | Érdemes lehet a rugalmas készlet szolgáltatási szintjét használni, amely támogatja a kívánt beállítást. |
| 40868 | 16 |A DTU maximális száma adatbázison nem lehet nagyobb (% d) a (z)%. * ls szolgáltatási szinten. Kísérlet történt a DTU maximális értékének beállítására az adatbázison a támogatott korláton túl. | Érdemes lehet a rugalmas készlet szolgáltatási szintjét használni, amely támogatja a kívánt beállítást. |
| 40870 | 16 |A (z)%. * ls szolgáltatási szinten a DTU minimális száma (% d) nem lehet hosszabb. Kísérlet a DTU minimális értékének beállítására a támogatott korláton túl. | Érdemes lehet a rugalmas készlet szolgáltatási szintjét használni, amely támogatja a kívánt beállítást. |
| 40873 | 16 |Az adatbázisok (% d) és a DTU minimális száma (% d) nem haladhatja meg a rugalmas készlet (% d) DTU. A rugalmas készletben lévő, a rugalmas készlet DTU meghaladó DTU minimális értékének megadására tett kísérlet. | Vegye fontolóra a rugalmas készlet DTU növelését, vagy csökkentse az adatbázis DTU, vagy csökkentse az adatbázisok számát a rugalmas készletben. |
| 40877 | 16 |A rugalmas készlet nem törölhető, ha nem tartalmaz adatbázisokat. A rugalmas készlet egy vagy több adatbázist tartalmaz, ezért nem törölhető. |A törléshez távolítsa el az adatbázisokat a rugalmas készletből. |
| 40881 | 16 |A (z) "%. * ls" rugalmas készlet elérte az adatbázisa számának korlátját.  A rugalmas készlet adatbázisának száma nem haladhatja meg a (z)% d DTU rendelkező rugalmas készletet. Kísérlet történt az adatbázis rugalmas készlethez való létrehozására vagy hozzáadására, ha a rugalmas készlet adatbázisának száma elérte a korlátot. | Ha lehetséges, érdemes lehet növelni a rugalmas készlet DTU, hogy növelje az adatbázis-korlátot, vagy távolítsa el az adatbázisokat a rugalmas készletből. |
| 40889 | 16 |A (z) "%. * ls" rugalmas készlet DTU vagy tárolási korlátja nem csökkenthető, mert nem biztosít elegendő tárterületet az adatbázisai számára. Kísérlet a rugalmas készlet tárolási korlátjának csökkentésére a tárterület-használat alatt. | Vegye figyelembe a rugalmas készletben lévő egyes adatbázisok tárolási használatát, vagy távolítsa el az adatbázisokat a készletből annak érdekében, hogy csökkentse annak DTU vagy tárolási korlátját. |
| 40891 | 16 |A DTU minimális száma (% d) nem haladhatja meg a DTU-adatbázis maximális számát (% d). Kísérlet a DTU minimális értékének megadására, mint az adatbázis-DTU maximális száma. |Győződjön meg arról, hogy a DTU-adatbázis min. száma nem haladja meg a maximális DTU. |
| TBD | 16 |A rugalmas készletben lévő önálló adatbázisok tárolási mérete nem haladhatja meg a (z) "%. * ls" szolgáltatási rétegek rugalmas készlete által megengedett maximális méretet. Az adatbázis maximális mérete meghaladja a rugalmas készlet szolgáltatási szintje által megengedett maximális méretet. |Állítsa be az adatbázis maximális méretét a rugalmas készlet szolgáltatási szintje által megengedett maximális méret korlátain belül. |

Kapcsolódó témakörök:

* [Rugalmas készlet létrehozása (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Rugalmas készlet kezelése (C#)](sql-database-elastic-pool-manage-csharp.md)
* [Rugalmas készlet létrehozása (PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [Rugalmas készlet figyelése és kezelése (PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>Általános hibák

A következő hibák nem tartoznak az előző kategóriákba.

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |A (AdministratorLogin) nem érvényes név, mert érvénytelen karaktereket tartalmaz.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |A bejelentkezés sikertelen volt. A bejelentkezés nem megbízható tartományból származik, és nem használható Windows-hitelesítéssel .%. &#x2a;ls (a Windows-bejelentkezések nem támogatottak a SQL Server jelen verziójában.) |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |A következő felhasználó bejelentkezése sikertelen volt:%. &#x2a;ls ' .%. &#x2a;ls%. &#x2a;ls (a (z) "%" felhasználó&#x2a; bejelentkezése sikertelen. ls ".) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |A következő felhasználó bejelentkezése sikertelen volt:%. &#x2a;ls ". Ok: a fiók le van tiltva .%. &#x2a;ls |
| 40014 |16 |Több adatbázis nem használható ugyanabban a tranzakcióban. |
| 40054 |16 |A fürtözött index nélküli táblák nem támogatottak a SQL Server jelen verziójában. Hozzon létre egy fürtözött indexet, és próbálkozzon újra. |
| 40133 |15 |Ez a művelet nem támogatott a SQL Server jelen verziójában. |
| 40506 |16 |A megadott SID érvénytelen a SQL Server ezen verziójában. |
| 40507 |16 |'%. &#x2a;az ls nem hívható meg paraméterekkel a SQL Server jelen verziójában. |
| 40508 |16 |A USE utasítás nem támogatott az adatbázisok közötti váltáshoz. Egy másik adatbázishoz való kapcsolódáshoz használjon új kapcsolatot. |
| 40510 |16 |Utasítás:%. &#x2a;az ls nem támogatott a SQL Server jelen verziójában. |
| 40511 |16 |Beépített függvény:%. &#x2a;az ls nem támogatott a SQL Server jelen verziójában. |
| 40512 |16 |A (z) "% ls" elavult funkció nem támogatott a SQL Server jelen verziójában. |
| 40513 |16 |Kiszolgálói változó:%. &#x2a;az ls nem támogatott a SQL Server jelen verziójában. |
| 40514 |16 |a (z) "% ls" nem támogatott a SQL Server jelen verziójában. |
| 40515 |16 |Hivatkozás az adatbázisra és/vagy a kiszolgálónévre a következőben:%. &#x2a;az ls nem támogatott a SQL Server jelen verziójában. |
| 40516 |16 |A globális ideiglenes objektumok nem támogatottak a SQL Server jelen verziójában. |
| 40517 |16 |Kulcsszó vagy utasítási beállítás:%. &#x2a;az ls nem támogatott a SQL Server jelen verziójában. |
| 40518 |16 |DBCC parancs:%. &#x2a;az ls nem támogatott a SQL Server jelen verziójában. |
| 40520 |16 |A (z) "% S_MSG" biztonságos osztály nem támogatott a SQL Server jelen verziójában. |
| 40521 |16 |A következő biztonságos osztály nem támogatott a kiszolgálói hatókörben SQL Server jelen verziójában: "% S_MSG". |
| 40522 |16 |Adatbázis rendszerbiztonsági tag:%. &#x2a;az ls típus nem támogatott a SQL Server jelen verziójában. |
| 40523 |16 |Implicit felhasználó:%. &#x2a;az ls létrehozása nem támogatott a SQL Server jelen verziójában. Explicit módon hozza létre a felhasználót a használat előtt. |
| 40524 |16 |Adattípus:%. &#x2a;az ls nem támogatott a SQL Server jelen verziójában. |
| 40525 |16 |A (z) "%. ls" nem támogatott a SQL Server jelen verziójában. |
| 40526 |16 |'%. &#x2a;az ls "sorhalmaz-szolgáltató nem támogatott a SQL Server jelen verziójában. |
| 40527 |16 |A csatolt kiszolgálók nem támogatottak a SQL Server jelen verziójában. |
| 40528 |16 |A felhasználók nem képezhetők le tanúsítványokra, aszimmetrikus kulcsokra vagy Windows-bejelentkezésekre a SQL Server jelen verziójában. |
| 40529 |16 |Beépített függvény:%. &#x2a;a SQL Server jelen verziója nem támogatja az ls-t a megszemélyesítési környezetben. |
| 40532 |11 |A (z) "%" kiszolgáló nem nyitható meg. &#x2a;ls "a bejelentkezés által kért. A bejelentkezés sikertelen. |
| 40553 |16 |A munkamenet túl sok memóriahasználat miatt megszakadt. Próbálja meg módosítani a lekérdezést úgy, hogy kevesebb sort dolgozzon fel.<br/><br/> A `ORDER BY` és a `GROUP BY` műveletek számának csökkentése a Transact-SQL-kódban segít csökkenteni a lekérdezés memóriabeli követelményeit. |
| 40604 |16 |Az adatbázis nem hozható létre/MÓDOSÍTható, mert túllépi a kiszolgáló kvótáját. |
| 40606 |16 |Az adatbázisok csatolása nem támogatott a SQL Server jelen verziójában. |
| 40607 |16 |A Windows-bejelentkezések nem támogatottak a SQL Server jelen verziójában. |
| 40611 |16 |A kiszolgálókon legfeljebb 128 tűzfalszabály adható meg. |
| 40614 |16 |A tűzfalszabály kezdő IP-címe nem haladhatja meg a záró IP-címet. |
| 40615 |16 |Nem nyitható meg a bejelentkezés által kért "{0}" kiszolgáló. A (z) "{1}" IP-címmel rendelkező ügyfél nem jogosult a kiszolgálóhoz való hozzáférésre.<br /><br />A hozzáférés engedélyezéséhez használja a SQL Database portált, vagy futtassa az SP @ no__t-0set @ no__t-1firewall @ no__t-2rule parancsot a főadatbázison egy tűzfalszabály létrehozásához ehhez az IP-címhez vagy címtartományból. A módosítás érvénybe léptetése akár öt percet is igénybe vehet. |
| 40617 |16 |A tűzfalszabály neve (szabály neve) túl hosszú. A maximális hossz 128. |
| 40618 |16 |A tűzfalszabály neve nem lehet üres. |
| 40620 |16 |A következő felhasználó bejelentkezése sikertelen volt: "%. &#x2a;ls ". A jelszó módosítása nem sikerült. A bejelentkezés során a jelszó módosítása nem támogatott a SQL Server jelen verziójában. |
| 40627 |20 |Folyamatban van a művelet végrehajtása a (z) {0} kiszolgálón és a (z) {1} adatbázison. Várjon néhány percet, majd próbálkozzon újra. |
| 40630 |16 |A jelszó érvényesítése nem sikerült. A jelszó nem felel meg a házirend követelményeinek, mert túl rövid. |
| 40631 |16 |A megadott jelszó túl hosszú. A jelszónak legfeljebb 128 karakterből kell állnia. |
| 40632 |16 |A jelszó érvényesítése nem sikerült. A jelszó nem felel meg a házirend követelményeinek, mert nem elég bonyolult. |
| 40636 |16 |Nem használható a fenntartott adatbázis neve (%). &#x2a;ls "ebben a műveletben. |
| 40638 |16 |Érvénytelen előfizetés-azonosító (előfizetés-azonosító). Az előfizetés nem létezik. |
| 40639 |16 |A kérelem nem felel meg a sémának: (sémakezelő hiba). |
| 40640 |20 |A kiszolgáló váratlan kivételt észlelt. |
| 40641 |16 |A megadott hely érvénytelen. |
| 40642 |17 |A kiszolgáló jelenleg túl elfoglalt. Később próbálja meg újra. |
| 40643 |16 |A megadott x-MS-Version fejléc értéke érvénytelen. |
| 40644 |14 |Nem sikerült engedélyezni a hozzáférést a megadott előfizetéshez. |
| 40645 |16 |Servername (servername) nem lehet üres vagy NULL. Csak kisbetűkből állhat: "név elején-kisbetűket", a számok 0-9 és a kötőjel. A kötőjel nem lehet a névben. |
| 40646 |16 |Az előfizetés-azonosító nem lehet üres. |
| 40647 |16 |Az előfizetés (előfizetés-azonosító) nem rendelkezik kiszolgálóval (servername). |
| 40648 |17 |Túl sok kérelem lett elvégezve. Próbálkozzon újra később. |
| 40649 |16 |Érvénytelen tartalomtípus van megadva. Csak az Application/XML támogatott. |
| 40650 |16 |Az előfizetés (előfizetés-azonosító) nem létezik, vagy nem áll készen a műveletre. |
| 40651 |16 |Nem sikerült létrehozni a kiszolgálót, mert az előfizetés (előfizetés-azonosító) le van tiltva. |
| 40652 |16 |Nem lehet áthelyezni vagy létrehozni a kiszolgálót. Az előfizetés (előfizetés-azonosító) túllépi a kiszolgálói kvótát. |
| 40671 |17 |Kommunikációs hiba az átjáró és a felügyeleti szolgáltatás között. Próbálkozzon újra később. |
| 40852 |16 |A (z) "%. \*LS" adatbázis nem nyitható meg a bejelentkezés által kért "%. \*LS" kiszolgálón. Az adatbázishoz való hozzáférés csak a biztonságos kapcsolati karakterlánc használatával engedélyezett. Az adatbázis eléréséhez módosítsa a kapcsolati karakterláncokat úgy, hogy a "Secure" értéket tartalmazza a kiszolgáló teljes tartományneve – "kiszolgálónév". az adatbázis. Windows. net nevet a "kiszolgálónév". database. `secure`.windows.net értékre kell módosítani. |
| 40914 | 16 | Nem nyitható meg a bejelentkezés által kért " *[Server-Name]* " kiszolgáló. Az ügyfél nem jogosult a kiszolgálóhoz való hozzáférésre.<br /><br />A javításhoz vegye fontolóra egy [virtuális hálózati szabály](sql-database-vnet-service-endpoint-rule-overview.md)hozzáadását. |
| 45168 |16 |A SQL Azure rendszer terhelés alatt van, és egy SQL Database-kiszolgáló esetében felső korlátot helyez el egyidejű DB szifilisz-műveletekhez (például adatbázis létrehozása). A hibaüzenetben megadott kiszolgáló túllépte az egyidejű kapcsolatok maximális számát. Próbálkozzon újra később. |
| 45169 |16 |Az SQL Azure-rendszer terhelés alatt van, és felső korlátot helyez az egyazon előfizetések egyidejű kiszolgálói szifilisz-műveleteinek számára (például kiszolgáló létrehozása). A hibaüzenetben megadott előfizetés túllépte az egyidejű kapcsolatok maximális számát, és a rendszer megtagadta a kérést. Próbálkozzon újra később. |

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure SQL Database szolgáltatásairól](sql-database-features.md).
* További információ a [DTU-alapú vásárlási modellről](sql-database-service-tiers-dtu.md).
* További információ a [virtuális mag-alapú vásárlási modellről](sql-database-service-tiers-vcore.md).

