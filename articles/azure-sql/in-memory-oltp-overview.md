---
title: Memóriabeli technológiák
description: A memóriában tárolt technológiák nagy mértékben javítják a tranzakciós és elemzési számítási feladatok teljesítményét Azure SQL Database és az Azure SQL felügyelt példányain.
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 43527e8e5860e0bbfc50643210156be943d2f174
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985190"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Teljesítmény optimalizálása a memóriában lévő technológiák használatával Azure SQL Database és az Azure SQL felügyelt példányain
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A memóriában tárolt technológiák lehetővé teszik az alkalmazás teljesítményének növelését, és az adatbázis költségeit is csökkentheti.

## <a name="when-to-use-in-memory-technologies"></a>A memóriában tárolt technológiák használata

A memórián belüli technológiák használatával a teljesítménnyel kapcsolatos fejlesztéseket a különböző számítási feladatokkal érheti el:

- **Tranzakciós** (online tranzakciós feldolgozás (OLTP)), ahol a kérelmek többsége a kisebb adathalmazok (például a szifilisz-műveletek) olvasását vagy frissítését kéri.
- **Analitikus** (online analitikus feldolgozás (OLAP)), ahol a lekérdezések többsége összetett számításokat tartalmaz a jelentéskészítési célokra, bizonyos számú lekérdezéssel, amelyek betöltik és hozzáfűzik az adatokat a meglévő táblákhoz (úgynevezett tömeges betöltéssel), vagy törölhetik az adatokat a táblákból.
- **Vegyes** (hibrid tranzakció/analitikus feldolgozás (HTAP)), ahol a OLTP és az OLAP-lekérdezések végrehajtása ugyanazon az adathalmazon történik.

A memóriában tárolt technológiák javíthatják ezeknek a számítási feladatoknak a teljesítményét azáltal, hogy az adatokat a memóriába dolgozzák fel, a lekérdezések natív fordításával vagy speciális feldolgozással, például kötegelt feldolgozással és a mögöttes hardveren elérhető SIMD utasításokkal.

## <a name="overview"></a>Áttekintés

A Azure SQL Database és az Azure SQL felügyelt példányai a következő memóriabeli technológiákkal rendelkeznek:

- *[A memóriában tárolt OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* növeli a tranzakciók másodpercenkénti számát, és csökkenti a tranzakció-feldolgozás késését. A memóriában tárolt OLTP előnyei a következők: nagy átviteli sebességű tranzakciós feldolgozás, például kereskedelmi és szerencsejátékok, adatok betöltése eseményekből vagy IoT-eszközökről, gyorsítótárazás, adatterhelés és ideiglenes tábla-és Table változó forgatókönyvek.
- A *fürtözött oszlopcentrikus indexek* csökkentik a tárolási lábnyomot (akár 10 alkalommal), és javítják a jelentéskészítési és elemzési lekérdezések teljesítményét. Az adatközpontokban lévő egyedkapcsolat-táblázatokkal több adathoz fér hozzá az adatbázisban, és javíthatja a teljesítményt. Azt is megteheti, hogy az operatív adatbázisban lévő korábbi adatokkal archiválja az adatait, és akár 10-szer több adattal is tud lekérdezni.
- A HTAP-alapú, nem *fürtözött oszlopcentrikus-indexek* segítségével valós idejű betekintést nyerhet a vállalatba az operatív adatbázis közvetlen lekérdezésével anélkül, hogy költséges kinyerési, átalakítási és betöltési (etl) folyamatot kellene futtatnia, és várnia kell az adattárház feltöltését. A nem fürtözött oszlopcentrikus indexek lehetővé teszik az elemzési lekérdezések gyors végrehajtását a OLTP-adatbázisban, miközben csökkentik az üzemeltetési terhelésre gyakorolt hatást.
- A HTAP *memória-optimalizált fürtözött oszlopcentrikus indexei* lehetővé teszik a gyors tranzakció-feldolgozást, valamint az elemzési lekérdezések *egyidejű* futtatását ugyanazon az adatokon.

A oszlopcentrikus indexek és a memórián belüli OLTP is a 2012-es és a 2014-es verzió óta a SQL Server termék részét képezték. Azure SQL Database, az Azure SQL felügyelt példánya és a SQL Server a memóriában lévő technológiák ugyanazon implementációját használja.

## <a name="benefits-of-in-memory-technology"></a>A memóriában lévő technológiák előnyei

A hatékonyabb lekérdezési és tranzakciós feldolgozás miatt a memóriában lévő technológiák is segítenek csökkenteni a költségeket. Általában nem kell frissítenie az adatbázis díjszabási szintjét a teljesítménybeli nyereség eléréséhez. Bizonyos esetekben csökkentheti az árképzési szintet, miközben továbbra is a memóriában lévő technológiák teljesítménybeli fejlesztéseit látja.

Az alábbi két példa azt mutatja be, hogyan segített a memóriában tárolt OLTP a teljesítmény jelentős javítása érdekében:

- A memóriában tárolt OLTP használatával a [kvórum üzleti megoldásai megduplázzák a munkaterhelést, miközben 70%-kal javítják a DTU](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu).
- Az alábbi videó az erőforrás-használat jelentős javulását mutatja be egy számítási feladattal: [memóriabeli OLTP-videó](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). További információt a blogbejegyzésben talál: [memóriabeli OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> A memóriában tárolt technológiák a prémium és a üzletileg kritikus szinten érhetők el.

Az alábbi videó a memóriában lévő technológiákkal kapcsolatos lehetséges teljesítménybeli előnyöket ismerteti. Ne feledje, hogy a megjelenő teljesítmény egyre több tényezőtől függ, többek között a munkaterhelés és az adatok természetétől, az adatbázis hozzáférési mintájának és így tovább.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

Ez a cikk a memóriában lévő OLTP-és oszlopcentrikus-indexek azon szempontjait ismerteti, amelyek az Azure SQL Database és az Azure SQL felügyelt példányaira jellemzőek, valamint példákat is tartalmaznak:

- Ezek a technológiák a tárolási és adatméreti korlátokon láthatók.
- Megtudhatja, hogyan kezelheti az ezeket a technológiákat használó adatbázisok mozgását a különböző díjszabási szintek között.
- A memóriában lévő OLTP, valamint a oszlopcentrikus indexek használatát bemutató két minta jelenik meg.

További információ a SQL Server memóriában történő használatáról:

- [Memóriában tárolt OLTP – áttekintés és használati forgatókönyvek](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (az első lépésekhez kapcsolódó esettanulmányokra és információkra mutató hivatkozásokat tartalmaz)
- [A memóriában tárolt OLTP dokumentációja](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Útmutató a oszlopcentrikus indexekhez](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hibrid tranzakciós/analitikus feldolgozás (HTAP), más néven [valós idejű operatív elemzés](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>Memóriában tárolt OLTP

A memóriában tárolt OLTP technológia rendkívül gyors adatelérési műveleteket tesz lehetővé azáltal, hogy az összes adatmennyiséget megtartja a memóriában. Emellett a speciális indexeket, a lekérdezések natív fordítását és az adatzárolás nélküli adatelérést is használja a OLTP számítási feladatok teljesítményének növeléséhez. A memóriában tárolt OLTP-adatmennyiséget kétféleképpen rendezheti:

- A **memóriára optimalizált sortárindex létrehozását** formátuma, ahol minden sor külön memória-objektum. Ez egy klasszikus memóriabeli OLTP formátum, amely nagy teljesítményű OLTP számítási feladatokhoz van optimalizálva. A memóriára optimalizált sortárindex létrehozását-formátumokban kétféle memória-optimalizált táblázat használható:

  - *Tartós táblák* (SCHEMA_AND_DATA), ahol a memóriába helyezett sorok megmaradnak a kiszolgáló újraindítása után. Az ilyen típusú táblák úgy viselkednek, mint egy hagyományos sortárindex létrehozását tábla, amely a memórián belüli optimalizálások további előnyeit is tartalmazza.
  - *Nem tartós táblák* (SCHEMA_ONLY), amelyek esetében a sorok nem őrződnek meg az újraindítás után. Ez a típusú tábla ideiglenes (például Temp Tables) vagy olyan táblákat tartalmaz, amelyekben gyorsan be kell töltenie az adatmennyiséget, mielőtt áthelyezi azt valamilyen megőrzött táblába (úgynevezett átmeneti táblákba).

- **Memória-optimalizált oszlopcentrikus** formátum, amelyben az adatok oszlopos formátumban vannak rendezve. Ez a struktúra olyan HTAP-forgatókönyvekhez készült, ahol analitikai lekérdezéseket kell futtatnia ugyanarra az adatszerkezetre, ahol a OLTP-munkaterhelés fut.

> [!Note]
> A memóriában tárolt OLTP technológia olyan adatstruktúrákhoz lett tervezve, amelyek teljes mértékben a memóriában találhatók. Mivel a memóriában tárolt adatmennyiség nem hajtható végre lemezre, győződjön meg arról, hogy olyan adatbázist használ, amely elegendő memóriával rendelkezik. További részletekért lásd: [adatméret és tárterület-korlát a memóriában tárolt OLTP](#data-size-and-storage-cap-for-in-memory-oltp) .

Gyors áttekintés a memóriában lévő OLTP: [1. rövid útmutató: memóriabeli OLTP technológiák a gyorsabb T-SQL teljesítmény](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) érdekében (egy másik cikk, amely segítséget nyújt az első lépésekhez)

Részletes videók a technológiákról:

- [Memóriában tárolt OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (amely a teljesítménnyel kapcsolatos előnyök bemutatóját tartalmazza, valamint az eredmények saját maga általi újbóli létrehozásához szükséges lépéseket)
- [Memóriában tárolt OLTP-videók: Mi az, és mikor/hogyan kell használni](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Van egy programozott mód annak megismerésére, hogy egy adott adatbázis támogatja-e a memóriabeli OLTP. A következő Transact-SQL-lekérdezést végezheti el:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Ha a lekérdezés **1**értéket ad vissza, a memóriában tárolt OLTP ebben az adatbázisban támogatott. A következő lekérdezések azonosítják azokat az objektumokat, amelyeket el kell távolítani az adatbázis visszaminősítése előtt általános célú, standard vagy alapszintű értékre:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Adatméret és tárterület-korlát a memóriában tárolt OLTP

A memóriában tárolt OLTP tartalmazza a memóriára optimalizált táblákat, amelyek a felhasználói adatok tárolására szolgálnak. Ezek a táblák a memóriába való illeszkedéshez szükségesek. Mivel a memóriát közvetlenül a SQL Database felügyeli, a felhasználói adatmennyiségre vonatkozó kvóta fogalma van. Ezt a gondolatot *a memóriában tárolt OLTP-tárolónak*nevezzük.

Az egyes támogatott önálló adatbázisok díjszabási szintjei és a rugalmas készletek díjszabási szintje bizonyos mennyiségű memóriában tárolt OLTP-tárterületet tartalmaz.

- [DTU-alapú erőforrás-korlátok – önálló adatbázis](database/resource-limits-dtu-single-databases.md)
- [DTU-alapú erőforrás-korlátok – rugalmas készletek](database/resource-limits-dtu-elastic-pools.md)
- [Virtuális mag-alapú erőforrás-korlátok – önálló adatbázisok](database/resource-limits-vcore-single-databases.md)
- [Virtuális mag-alapú erőforrás-korlátok – rugalmas készletek](database/resource-limits-vcore-elastic-pools.md)
- [Virtuális mag-alapú erőforrás-korlátok – felügyelt példány](managed-instance/resource-limits.md)

A következő elemek a memóriában tárolt OLTP-tárolók maximális száma felé mutatnak:

- Aktív felhasználói adatsorok a memória-optimalizált táblákban és a táblázat változókban. Vegye figyelembe, hogy a régi sor verziói nem számítanak bele a korlátba.
- Indexeli a memóriára optimalizált táblákat.
- Az ALTER TABLE műveletek működési terhelése.

Ha eléri a korlátot, a rendszer kvótán kívüli hibaüzenetet kap, és már nem tudja beszúrni vagy frissíteni az adatmennyiséget. A hiba elhárítása érdekében törölje az adatmennyiséget, vagy növelje az adatbázis vagy a készlet díjszabási szintjét.

A memóriában tárolt OLTP tárolásával és a riasztások konfigurálásával kapcsolatos részletekért tekintse meg a [memóriabeli tárolás](in-memory-oltp-monitor-space.md)monitorozása című témakört.

#### <a name="about-elastic-pools"></a>A rugalmas készletek

A rugalmas készletekkel a memóriában tárolt OLTP-tárolók a készlet összes adatbázisában meg vannak osztva. Ezért az egyik adatbázisban való használat más adatbázisokra is hatással lehet. A következő két megoldás:

- Konfigurálja a `Max-eDTU` vagy olyan `MaxvCore` adatbázisokat, amelyek a készlet teljes EDTU vagy virtuális mag alacsonyabbak. Ez a maximális érték a memóriában tárolt OLTP-kihasználtságot, a készletben lévő bármely adatbázisban a eDTU számának megfelelő méretre.
- A vagy érték beállítása 0-nál `Min-eDTU` `MinvCore` nagyobb. Ez a minimális garancia annak biztosítására, hogy a készletben lévő összes adatbázis a rendelkezésre álló memóriabeli OLTP-tárolóban legyen, amely megfelel a konfigurált vagy a értéknek `Min-eDTU` `vCore` .

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>A memóriában tárolt OLTP technológiákat használó adatbázisok szolgáltatási szintjeinek módosítása

Az adatbázist vagy a példányt bármikor frissítheti magasabb szintre, például általános célúról üzletileg kritikusra (vagy standard – prémium). A rendelkezésre álló funkciók és erőforrások csak növekednek.

A csomag visszaminősítése azonban negatív hatással lehet az adatbázisra. A hatás különösen akkor látható, ha a üzletileg kritikusról általános célúra (vagy prémiumról standard vagy alapszintű csomagra), ha az adatbázis memóriában tárolt OLTP objektumokat tartalmaz. A memóriára optimalizált táblázatok a visszalépést követően nem érhetők el (még akkor is, ha azok láthatók maradnak). Ugyanezek a szempontok érvényesek a rugalmas készlet díjszabási szintjének csökkentésére, illetve az adatbázisok memóriában tárolt technológiákkal való áthelyezésére egy általános célú, standard vagy alapszintű rugalmas készletbe.

> [!Important]
> A memóriában tárolt OLTP nem támogatottak a általános célú, a standard és az alapszintű csomag esetében. Ezért nem lehet olyan adatbázist áthelyezni, amely bármilyen memóriában lévő OLTP-objektummal rendelkezik az egyik ilyen rétegben.

Mielőtt visszaminősíti az adatbázist általános célú, standard vagy alapszintű verzióra, távolítsa el az összes memóriára optimalizált táblát és táblát, valamint az összes natív módon lefordított T-SQL-modult.

*Erőforrások méretezése üzletileg kritikus*szinten: a memóriában optimalizált táblákban lévő adatoknak az adatbázis vagy a felügyelt példány szintjéhez társított memóriában tárolt OLTP-tárolóban kell lenniük, vagy elérhetők a rugalmas készletben. Ha a réteg leskálázását vagy az adatbázis egy olyan készletbe való áthelyezését kísérli meg, amely nem rendelkezik elegendő memóriával rendelkező OLTP-tárolóval, a művelet meghiúsul.

## <a name="in-memory-columnstore"></a>Memóriában tárolt oszlopcentrikus

A memóriában tárolt oszlopcentrikus technológia lehetővé teszi a táblák nagy mennyiségű adattárolását és lekérdezését. A oszlopcentrikus technológia oszlop-alapú adattárolási formátumot használ, és a kötegelt lekérdezés feldolgozásával akár 10 alkalommal is elérheti a lekérdezési teljesítményt az OLAP számítási feladatokban a hagyományos, sor-orientált tárolón keresztül. Akár 10 alkalommal is elérheti az adattömörítést a tömörítetlen adatmérethez képest.
Kétféle oszlopcentrikus-modellt használhat az adatai rendszerezéséhez:

- **Fürtözött oszlopcentrikus** , ahol a táblában lévő összes érték oszlopos formában van rendszerezve. Ebben a modellben a tábla összes sora oszlopos formátumban van elhelyezve, amely nagy mértékben tömöríti az adatokkal, és lehetővé teszi, hogy gyors analitikai lekérdezéseket és jelentéseket hajtson végre a táblán. Az adatmennyiségtől függően előfordulhat, hogy az adatméretet 10x-100x értékre csökkenti. A fürtözött oszlopcentrikus modell lehetővé teszi a nagy mennyiségű (tömeges terhelésű) adatmennyiség gyors betöltését, mivel a több mint 100 000 sorból álló nagy kötegek tömörítése a lemezen való tárolás előtt történik. Ez a modell jó választás a klasszikus adattárház-forgatókönyvek számára.
- **Nem fürtözött oszlopcentrikus** , ahol az adattárolás a hagyományos sortárindex létrehozását-táblában történik, és az analitikai lekérdezésekhez használt oszlopcentrikus-formátum indexe. Ez a modell lehetővé teszi a hibrid tranzakciós analitikus feldolgozást (HTAP): képes a valós idejű elemzések futtatására tranzakciós munkaterhelésen. A OLTP lekérdezések végrehajtása olyan sortárindex létrehozását-táblán történik, amely a sorok kis halmazának elérésére van optimalizálva, míg az OLAP-lekérdezések a vizsgálatokhoz és elemzésekhez jobb választást biztosító oszlopcentrikus-indexen futnak. A lekérdezés-optimalizáló dinamikusan kiválasztja a sortárindex létrehozását vagy a oszlopcentrikus formátumot a lekérdezés alapján. A nem fürtözött oszlopcentrikus indexek nem csökkentik az adatmennyiséget, mivel az eredeti adatkészletet az eredeti sortárindex létrehozását-táblázat tárolja módosítás nélkül. A további oszlopcentrikus-indexek méretének azonban az egyenértékű B-Tree indexnél kisebbnek kell lennie.

> [!Note]
> A memóriában tárolt oszlopcentrikus technológia csak azokat az adatmennyiséget őrzi meg, amelyek a memóriában való feldolgozáshoz szükségesek, míg a memóriába nem illeszkedő adatmennyiséget a lemez tárolja. Ezért a memóriában tárolt oszlopcentrikus-struktúrákban lévő adatmennyiség túllépheti a rendelkezésre álló memória mennyiségét.

Részletes videó a technológiáról:

- [Oszlopcentrikus index: memóriabeli elemzési videók a Ignite 2016-ből](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Az adatméret és a oszlopcentrikus indexek tárolása

A oszlopcentrikus indexek nem szükségesek a memóriához való illeszkedéshez. Ezért az indexek méretének egyetlen korlátja a teljes adatbázis mérete, amely a [DTU-alapú vásárlási modellben](database/service-tiers-dtu.md) és a [virtuális mag-alapú vásárlási modell](database/service-tiers-vcore.md) cikkeiben van dokumentálva.

Fürtözött oszlopcentrikus indexek használatakor az oszlopos tömörítést az alaptábla tárolója használja. Ez a tömörítés jelentősen csökkentheti a felhasználói adatai tárolási lábnyomát, ami azt jelenti, hogy az adatbázisban több adatmennyiség is elfér. A tömörítés pedig az [oszlopos archiválási tömörítéssel](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression)tovább növelhető. Az elérni kívánt tömörítési mennyiség az adatok természetétől függ, a tömörítés azonban tízszer nem ritka.

Ha például olyan adatbázisa van, amelynek maximális mérete 1 terabájt (TB), és a oszlopcentrikus indexek használatával 10 alkalommal éri el a tömörítést, akkor az adatbázisban összesen 10 TB felhasználói adat fér el.

Ha nem fürtözött oszlopcentrikus indexeket használ, az alaptábla továbbra is a hagyományos sortárindex létrehozását formátumban tárolódik. Ezért a tárterület-megtakarítás nem annyira jelentős, mint a fürtözött oszlopcentrikus indexek esetében. Ha azonban egy oszlopcentrikus indextel rendelkező hagyományos, nem fürtözött indexeket cserél le, továbbra is általános megtakarítást érhet el a tábla tárolási lábnyomában.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Oszlopcentrikus indexeket tartalmazó adatbázisok szolgáltatási szintjeinek módosítása

Előfordulhat, *hogy az önálló adatbázis alapszintű vagy standard szintre történő visszaminősítése* nem lehetséges, ha a célként megadott szint S3 alatt van. A oszlopcentrikus indexek csak a üzletileg kritikus/Premium díjszabási szinten, valamint a standard szint, az S3 és a felett, és nem az alapszintű csomagban támogatottak. Ha az adatbázist nem támogatott szintre vagy szintre minősíti, a oszlopcentrikus index elérhetetlenné válik. A System fenntartja a oszlopcentrikus indexet, de soha nem használja fel az indexet. Ha később visszavált egy támogatott szintre vagy szintre, a oszlopcentrikus-index azonnal kihasználható lesz.

Ha **fürtözött** oszlopcentrikus indextel rendelkezik, a teljes táblázat elérhetetlenné válik a visszalépés után. Ezért azt javasoljuk, hogy az adatbázis egy nem támogatott szintjére vagy szintre való visszalépése előtt dobja el az összes *fürtözött* oszlopcentrikus indexet.

> [!Note]
> Az SQL felügyelt példánya minden szinten támogatja a Oszlopcentrikus indexeket.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>További lépések

- [1. gyors útmutató: memóriában tárolt OLTP-technológiák a T-SQL teljesítményének növeléséhez](https://msdn.microsoft.com/library/mt694156.aspx)
- [Memóriában tárolt OLTP használata meglévő Azure SQL-alkalmazásokban](in-memory-oltp-configure.md)
- [Memóriában tárolt OLTP-tárolók figyelése](in-memory-oltp-monitor-space.md) a memóriában lévő OLTP
- [Memóriabeli funkciók kipróbálása](in-memory-sample.md)

## <a name="additional-resources"></a>További források

### <a name="deeper-information"></a>Mélyebb információk

- [Ismerje meg, hogy a kvórum megduplázza a kulcsfontosságú adatbázis számítási feladatait, miközben 70%-kal csökkenti a DTU a memóriában tárolt OLTP SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Memóriában tárolt OLTP blogbejegyzés](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Tudnivalók a memóriában tárolt OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Tudnivalók a oszlopcentrikus indexekről](https://msdn.microsoft.com/library/gg492088.aspx)
- [Tudnivalók a valós idejű operatív elemzésekről](https://msdn.microsoft.com/library/dn817827.aspx)
- Tekintse meg a [gyakori számítási feladatok mintáit és az áttelepítési megfontolásokat](https://msdn.microsoft.com/library/dn673538.aspx) (amelyek a munkaterhelési mintákat ismertetik, ahol a memóriabeli OLTP gyakran jelentős teljesítmény

### <a name="application-design"></a>Az alkalmazás kialakítása

- [Memóriában tárolt OLTP (memórián belüli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Memóriában tárolt OLTP használata meglévő Azure SQL-alkalmazásokban](in-memory-oltp-configure.md)

### <a name="tools"></a>Eszközök

- [Azure Portalra](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
