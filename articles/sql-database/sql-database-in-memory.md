---
title: Az Azure SQL Database In-Memory technologies |} A Microsoft Docs
description: Az Azure SQL Database In-Memory technologies jelentősen javíthatja a tranzakciós teljesítményét és elemzési számítási feladatok.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 399a0e6dd2b5c83a599aa50973417ba5a9be708d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813355"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Teljesítmény optimalizálása, memóriabeli technológiákat az SQL Database használatával

Az Azure SQL Database-ben a memóriabeli technológiák lehetővé teszi az alkalmazás teljesítményének javítása érdekében, és potenciálisan költségcsökkentés az adatbázis. Memóriabeli technológiákat az Azure SQL Database használatával érheti el a különböző számítási feladatokat a teljesítménnyel kapcsolatos fejlesztések:
- **Tranzakciós** (online tranzakciófeldolgozás (OLTP)), a kérések többségét olvasni, vagy kisebb adatkészletet (például CRUD-műveletek) frissítése.
- **Elemzési** (online analitikus feldolgozási (OLAP)), amelyekben az lekérdezések nagy részénél rendelkezik összetett számításokat a jelentéskészítés céljából, az adott számú lekérdezéseket, amelyek betöltése és adatok hozzáfűzése a meglévő táblák (tehát tömeges betöltési is nevezik), vagy törölje a a táblák adatait. 
- **Vegyes** (hibrid tranzakció/analitikus feldolgozás (HTAP)) ahol egyaránt OLTP és OLAP típusú lekérdezések végrehajtásakor az adatok ugyanahhoz az adatkészlethez.

Memóriabeli technológiákat javíthatja a teljesítményt a munkaterhelések tartja az adatokat a memóriába, a lekérdezések összeállítását natív használatával fel kell dolgozni, vagy feldolgozási ilyen speciális, kötegelt feldolgozási és elérhető SIMD utasításokat a az alapul szolgáló hardver.

Az Azure SQL Database In-Memory technologies alábbi rendelkezik:
- *[Memóriabeli OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)*  tranzakciók másodpercenkénti száma növekszik, és csökkenti a késést, tranzakció-feldolgozás. Memóriabeli OLTP előnyeit kihasználó forgatókönyvek: nagy átviteli sebességű tranzakció-feldolgozási, például a kereskedelmi és a játékok, adatbetöltést események vagy IoT-eszközök, gyorsítótárazás, az adatok betöltése, és az ideiglenes tábla és a tábla változó forgatókönyvek.
- *Fürtözött oszlopcentrikus indexek* csökkentheti a storage erőforrás-igényű (legfeljebb 10 alkalommal), és javíthatja a teljesítményt, jelentéskészítési és elemzési lekérdezések. Használhatja azt a ténytáblák adatközpontjait a további adatok elfér az adatbázis és a teljesítmény javítása. Is használhatja azt az előzményadatok az operatív adatbázis archiválása, és a legfeljebb 10-szer több adat lekérdezésére.
- *Nem fürtözött oszlopcentrikus indexek* a HTAP segítséget nyújtanak a valós idejű betekintést az üzleti keresztül az operatív adatbázis közvetlen lekérdezése nélkül kell futtatni a költséges kinyerési, átalakítási, és load (ETL) folyamat, és várjon, amíg a adatraktár kell feltöltenie. Fürtözetlen oszlopcentrikus indexek az OLTP adatbázis csökkenti a működési munkaterhelés gyakorolt elemzési lekérdezések gyors végrehajtásának engedélyezése.
- *A memóriaoptimalizált fürtözött oszlopcentrikus indexek* HTAP lehetővé teszi, hogy gyors tranzakció-feldolgozás, a, és *egyidejűleg* elemzési lekérdezések futtatása nagyon gyorsan ugyanazokat az adatokat.

Oszlopcentrikus indexek és az In-Memory OLTP óta része az SQL Server-termék 2012 és a 2014-es, illetve. Az Azure SQL Database és az SQL Server megosztani, memóriabeli technológiákat azonos megvalósítását. Továbbítja, ezek a technológiák az új képességek jelennek meg az Azure SQL Database először előtt a az SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Memóriabeli technológiát előnyei

Hatékonyabb lekérdezési és tranzakciós feldolgozást memóriabeli technológiákat is segítenek költségek csökkentése érdekében. Általában nem kell a teljesítménynövekedést érhet el az adatbázis tarifacsomagjának frissítése. Bizonyos esetekben is lehet csökkenteni a a tarifacsomagot, miközben továbbra is megjelenik a teljesítménnyel kapcsolatos fejlesztések a memóriabeli technológiák.

Az alábbiakban két példát, hogyan segített a In-Memory OLTP jelentősen javíthatja a teljesítményt:

- Az In-Memory OLTP, [kvórum üzleti megoldások, miközben nő a dtu-k 70 %-kal munkaterhelési duplán képes volt](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - Azt jelenti, hogy a dtu-k *adatbázis-tranzakciós egységek*, és a egy erőforrás-használat mértéke tartalmazza.
- A következő videó bemutatja a jelentős fejlesztéseket tartalmaz az erőforrás-használat az egy mintául szolgáló számítási feladatok: [Az Azure SQL Database videóban in-Memory OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - További információkért tekintse meg a következő blogbejegyzésben:: [Ebben a blogbejegyzésben az Azure SQL Database in-Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
>  
>  A prémium és üzletileg kritikus szintű Azure SQL-adatbázisok és a prémium szintű rugalmas készletek, memóriabeli technológiákat érhetők el.

Az alábbi videó az Azure SQL Database-ben a memóriabeli technológiák lehetséges teljesítménynövekedést ismerteti. Ne feledje, hogy az mindig látható teljesítményt nyereség függ, hogy sok tényező befolyásolja, többek között a számítási feladatok és az adatokat, az adatbázis-hozzáférési minta jellegét, és így tovább.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Ez a cikk írja le In-Memory OLTP és oszloptár-indexek konkrétan az Azure SQL Database aspektusait, és mintát is tartalmazza:
- Ezek a technológiák hatását a tárolás és adatok méretkorlátozások megjelenik.
- Látni fogja, hogyan kezelheti a ezeknek a technológiáknak a különböző árképzési szintek közötti használó adatbázisok áthelyezését.
- Látni fogja a két minta, amelyek In-Memory OLTP, valamint az oszlopcentrikus indexek az Azure SQL Database használatát mutatják be.

További információkért lásd:
- [A memóriában tárolt OLTP-k áttekintése és a használati forgatókönyvek](https://msdn.microsoft.com/library/mt774593.aspx) (hivatkozások ügyféleset-tanulmányok és a kezdéshez információkat tartalmazza)
- [Memóriabeli OLTP dokumentációja](https://msdn.microsoft.com/library/dn133186.aspx)
- [Oszlopcentrikus indexek áttekintésével](https://msdn.microsoft.com/library/gg492088.aspx)
- Hibrid tranzakciós/analitikus feldolgozás (HTAP), más néven [valós idejű működési elemzések](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>Memóriabeli OLTP beállítása

A memóriában tárolt OLTP-k technológia rendkívül gyors adatelérési műveletek biztosít az összes adat a memóriában tartja. Azt is speciális indexek, a natív lekérdezéseket és zárolástól mentes adatelérési összeállítása fejlesztésére használja az OLTP számítási feladatok teljesítményére. Az In-Memory OLTP adatok rendszerezéséhez két módja van:
- **Memóriára optimalizált sortárindex** formátumot, ahol minden sor egy külön memóriabeli objektum. Ez a klasszikus In-Memory OLTP formátum optimalizált nagy teljesítményű OLTP számítási feladatokhoz. Memóriaoptimalizált táblákhoz, a memóriára optimalizált sortárindex formátumban is használható két típusa van:
  - *Tartós táblák* (SCHEMA_AND_DATA), a memória a sor megmaradnak a kiszolgáló újraindítása után. Táblák az ilyen típusú úgy viselkedik, mint egy hagyományos sortárindex táblázat további előnyeinek memóriabeli optimalizálás.
  - *Nem tartós táblák* (SCEMA_ONLY) hol tárolja a sorokat a rendszer nem őrződnek meg az újraindítás után. Ez a tábla típusú ideiglenes adatokat (például az ideiglenes táblák csere) lett tervezve, vagy táblákat, ahol meg kell gyorsan adatok betöltése előtt helyezze át néhány megőrzött tábla (tehát nevű előkészítési táblák).
- **Memóriára optimalizált oszlopcentrikus** hol vannak rendszerezve Oszlopalapú formátumú formátumban. Ez a struktúra HTAP forgatókönyvek, ahol kell elemzési lekérdezések futtatása a ugyanazzal az adatszerkezettel az OLTP típusú számítási feladatokat futtató lett tervezve.

> [!Note]
> In-Memory OLTP technológiát is teljes mértékben található memória adatstruktúrák lett tervezve. Mivel a memóriában lévő adatok nem szabad kiszervezni lemez, ellenőrizze, hogy elegendő memóriával rendelkező adatbázist használ. Lásd: [adatok mérete és tartozó napi korlát In-Memory OLTP](#data-size-and-storage-cap-for-in-memory-oltp) további részletekért.

Az In-Memory OLTP rövid ismertetése: [1 a rövid útmutató: Memóriabeli OLTP technológiák gyorsabb a T-SQL-teljesítmény](https://msdn.microsoft.com/library/mt694156.aspx) (egy másik cikkben segítséget első lépések)

A technológiák részletes szolgáltatásról:

- [Az Azure SQL Database in-Memory OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (amely tartalmaz egy bemutatót a teljesítménybeli előnyei és saját kezűleg ezekkel az eredményekkel reprodukálás lépései)
- [Memóriabeli OLTP videók: Mi és mikor és hogyan lehet a használatára](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Nincs a megismeréséhez, hogy támogatja-e egy adott adatbázishoz In-Memory OLTP programozott módon. A következő Transact-SQL-lekérdezést futtathat:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Ha a lekérdezés visszaadja az **1**, In-Memory OLTP támogatott ebben az adatbázisban. A következő lekérdezéseket azonosítsa az összes objektum, amely előtt az adatbázis is lehet visszaminősíteni, Standard és alapszintű, el kell távolítani:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Adatok mérete és a tárolási korlátot In-Memory OLTP-hez

In-Memory OLTP magában foglalja a memóriaoptimalizált táblákhoz, amelyeken a felhasználói adatok tárolására szolgálnak. Ezek a táblák férnek el a memóriában van szükség. Mivel Ön kezeli az SQL Database szolgáltatás közvetlenül a memória, kell, hogy a felhasználói adatok kvóta fogalmát. Ezt az elképzelést nevezzük *In-Memory OLTP storage*.

Tarifacsomag és minden egyes tarifacsomagja a rugalmas készlet minden egyes támogatott egyetlen adatbázis bizonyos mennyiségű In-Memory OLTP-tár tartalmazza. Lásd: [DTU-alapú erőforráskorlátok – önálló adatbázis](sql-database-dtu-resource-limits-single-databases.md), [DTU-alapú erőforráskorlátok – rugalmas készletek](sql-database-dtu-resource-limits-elastic-pools.md),[Virtuálismag-alapú erőforráskorlátok – önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [Virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

A következő elemek beleszámítanak az In-Memory OLTP Tárhelykorlát:

- A memóriaoptimalizált táblák és Táblaváltozók adatsor aktív felhasználó. Vegye figyelembe, hogy a régi sor verziók nem számítanak bele a napi korlát felé.
- A memóriaoptimalizált táblák indexei.
- Az ALTER TABLE műveletek üzemeltetési terheit.

Ha eléri a korlátot, ki a kvótát, hibaüzenetet kap, és mostantól nem Ön beszúrása vagy frissítheti az adatokat. Ez a hiba elhárításához, törli az adatokat, vagy növelje az adatbázis vagy készlet tarifacsomagját.

További információk a In-Memory OLTP tárterület kihasználtsága figyelés és riasztások konfigurálása, ha szinte eléri a korlátot: [memóriabeli megfigyelés tárolási](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Tudnivalók a rugalmas készletek

A rugalmas készletekkel az In-Memory OLTP storage közösen használja a készletben található összes adatbázishoz. Ezért a használat egy adott adatbázis hatással lehet más adatbázisok. A két megoldások a következők:

- Konfigurálja a `Max-eDTU` vagy `MaxvCore` adatbázisok, alacsonyabb, mint a teljes készlet edtu-k vagy virtuális mag száma. Ez a maximális érték caps a memórián belüli online Tranzakciófeldolgozási tárterület kihasználtsága, a készlet mérete, amely megfelel az edtu-k száma minden adatbázisban.
- Konfigurálja a `Min-eDTU` vagy `MinvCore` 0-nál nagyobb. Ez a minimum garantálja, hogy a készletben lévő minden egyes adatbázishoz rendelkezik, amely megfelel a konfigurált elérhető In-Memory OLTP-tárhely `Min-eDTU` vagy `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Az In-Memory OLTP technológiákat használó adatbázisok szolgáltatásszintek módosítása

Mindig frissítheti az adatbázis vagy példány magasabb szintre, mint például az általános célú üzletileg kritikus (vagy Standard, prémium). A rendelkezésre álló funkciók és erőforrások csak növelni.

De alacsonyabb verziójúra változtatása a réteg negatívan befolyásolhatja az adatbázishoz. A hatás különösen nyilvánvalóvá válik, amikor az általános célú (vagy a Standard vagy Basic) üzletileg kritikus fontosságú a csomagok esetén az adatbázis In-Memory OLTP objektumokat tartalmazza. Memóriaoptimalizált táblák nem érhetők el az alacsonyabb szintű után (még akkor is, ha azok továbbra is látható). A fenti szempontok érvényesek, amikor csökkentése a rugalmas készlet tarifacsomagját, vagy áthelyezi, memóriabeli technológiákat, az adatbázis egy standard szintű vagy alapszintű rugalmas készlet.

> [!Important]
> Memóriabeli OLTP az általános célú, Standard vagy Basic szint nem támogatott. Ezért nem lehet áthelyezni egy adatbázist, amelynek a standard szintű vagy alapszintű csomagra In-Memory OLTP objektumok.

Mielőtt Visszaléptetés a Standard és alapszintű az adatbázist, távolítsa el az összes memóriaoptimalizált táblák és táblatípusok, valamint a T-SQL natív módon lefordított modulok. 

*Üzletileg kritikus szintű skálázás lefelé erőforrások*: A memóriaoptimalizált táblákban lévő adatokat hozzá kell férnie az In-Memory OLTP storage társított a réteg az adatbázis vagy a felügyelt példány, vagy érhető el a rugalmas készlet. Ha a csomag skálázási próbál, vagy az adatbázis egy készletbe, amely nem rendelkezik elegendő rendelkezésre álló In-Memory OLTP-tár áthelyezése a művelet sikertelen lesz.

## <a name="in-memory-columnstore"></a>Memóriabeli oszlopcentrikus

Amely lehetővé teszi tárolása és lekérdezése a tábla adatainak nagy mennyiségű memóriabeli oszloptárolási technológia. Oszloptárolási technológia oszlop-alapú adatok tárolási formátumot használja, és akár 10 alkalommal a lekérdezési teljesítmény OLAP számítási feladatokhoz a batch lekérdezés-feldolgozással való elérése révén, mint a hagyományos, soralapú tárolással. Keresztül a tömörítetlen adatok mérete legfeljebb 10 alkalommal az adatok tömörítésének nyereséget is megvalósítható.
Oszlopcentrikus modelleket, amelyek segítségével az adatok rendezése két típusa van:
- **Fürtözött oszlopcentrikus** ahol a táblázatban lévő összes adatot az Oszlopalapú formátumban vannak rendezve. Ebben a modellben a tábla összes sorát kerülnek, amely magas tömöríti az adatokat, és lehetővé teszi a hajthat végre elemzési lekérdezéseket és jelentéseket a tábla oszlopos formátumban. Az adatok természetétől függően az adatok mérete lehet csökkent 10 x-100 x. Fürtözött oszlopcentrikus modellt is lehetővé teszi nagy mennyiségű adat (tömeges betöltési) óta több mint 100 ezer sorok lemezen kerülésük előtt tömörített adatok nagy kötegeket gyors Adatbetöltési. Ebben a modellben egy jó választás a klasszikus data warehouse-forgatókönyvek esetén. 
- **A nem fürtözött oszlopcentrikus** ahol hagyományos sortárindex tábla tárolja az adatokat, és az elemzési lekérdezésekhez használt formátumban oszlopcentrikus index van. Ez a modell lehetővé teszi, hogy a hibrid tranzakciós analitikus feldolgozás (HTAP): a tranzakciós munkaterhelések nagy teljesítményű, valós idejű elemzések futtatására. OLTP-lekérdezések végrehajtása eléréséhez egy kis készletét a sorokat, amíg az OLAP-lekérdezések végrehajtása oszlopcentrikus index, amely jobb választás a vizsgálatok és analitika optimalizált sortárindex táblán. Az Azure SQL Database lekérdezésoptimalizáló a sortárindex vagy oszlopcentrikus formátum, a lekérdezés alapján dinamikusan kiválasztása. A nem fürtözött oszlopcentrikus indexek nem csökkenhetnek az adatok mérete, mivel az eredeti adatkészlet az eredeti sortárindex tábla változás nélkül maradjanak. Azonban további oszlopcentrikus index mérete nagyságrendű kisebb, mint az egyenértékű B-fa indexet kell lennie.

> [!Note]
> A memóriabeli oszloptárolási technológia biztosítja, hogy csak a szükséges a memória, a feldolgozás során az adatokat, amelyek nem sorolhatók a memóriában tárolt adatokat lemezen. Ezért az adatok memóriabeli oszlopcentrikus struktúrák lépheti túl a rendelkezésre álló memória mennyiségét. 

A technológiával kapcsolatos részletesebb videó:
- [Az Oszlopcentrikus Index: Memóriabeli elemzésekhez videóit Ignite 2016-ra](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Adatok mérete és az oszlopcentrikus indexek

Az Oszlopcentrikus indexek nem férnek el a memóriában szükséges. A csak kap az indexek mérete ezért a maximális általános adatbázis méret, amely ismerteti a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) cikkeket.

Fürtözött oszlopcentrikus indexek használata esetén a Oszlopalapú tömörítés alaptábla tárolására szolgál. Ez a fajta tömörítés jelentősen csökkentheti a felhasználói adatokat, ami azt jelenti, hogy további adatokat is elférjen az adatbázisban a storage erőforrás-igényű. A tömörítés tovább növelhető az és [Oszlopalapú archiválási tömörítési](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Az adatok természetétől függ, amely akkor érhető el, tömörítés mértéke, de 10 alkalommal a tömörítés nem ritka.

Például ha egy adatbázis maximális mérete 1 terabájt (TB) és 10 alkalommal a tömörítés elérése érdekében az oszlopcentrikus indexek, elhelyezhessen összesen 10 TB-os felhasználói adatokat az adatbázisban.

Nem fürtözött oszlopcentrikus indexek használata esetén az alaptábla továbbra is a hagyományos sortárindex formátumban tárolódik. Ezért a tárhely-megtakarítás nem, big Data típusú, a fürtözött oszloptár-indexekben. Azonban ha hagyományos fürtözetlen index számos egyetlen oszlopcentrikus indexszel rendelkező, továbbra is megjelenik egy átfogó, akár a storage erőforrás-igényű táblához.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Az Oszlopcentrikus indexek tartalmazó adatbázisok szolgáltatásszintek módosítása

*Alapszintű vagy Standard önálló adatbázis alacsonyabb szolgáltatásszintre* nem feltétlenül lehetséges, ha a célként megadott szint S3 alatt van. Az Oszlopcentrikus indexek csak a vállalati kritikus/prémium tarifacsomagban és a Standard szintű, S3 és a fenti, nem pedig az alapszintű csomag támogatottak. Ha az adatbázis egy nem támogatott szint vagy a szintet, csomagok, az oszlopcentrikus index nem érhető el. A rendszer megőrzi az oszlopcentrikus index, de a modul soha nem az index. Ha később frissít, térjen vissza a támogatott szint vagy a szintet, újra adatbáziscsoportok azonnal készen áll az oszlopcentrikus index.

Ha rendelkezik egy **fürtözött** oszlopcentrikus index, az egész tábla elérhetetlenné válik a visszalépés után. Ezért azt javasoljuk, hogy törölte-e az összes *fürtözött* oszlopcentrikus indexek előtt egy nem támogatott szint vagy a szintet az adatbázisról.

> [!Note]
> A felügyelt példány által támogatott Columstore indexek minden szinten.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>További lépések

- [1 a rövid útmutató: Memóriabeli OLTP technológiák gyorsabb T-SQL-teljesítmény](https://msdn.microsoft.com/library/mt694156.aspx)

- [Egy meglévő Azure SQL-alkalmazásban használható In-Memory OLTP](sql-database-in-memory-oltp-migration.md)

- [A figyelő In-Memory OLTP storage](sql-database-in-memory-oltp-monitoring.md) In-Memory OLTP-hez

- [Próbálja ki az Azure SQL Database In-memory funkcióit](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>További források

#### <a name="deeper-information"></a>Részletesebb információhoz juthat

- [Ismerje meg, hogy kvórum megduplázódik DTU takarítható meg és az SQL Database In-Memory OLTP 70 %-kal kulcsának adatbázis-munkaterhelés](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Ebben a blogbejegyzésben az Azure SQL Database in-Memory OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [További információ a memóriában tárolt OLTP-k](https://msdn.microsoft.com/library/dn133186.aspx)

- [Ismerje meg az oszlopcentrikus indexek](https://msdn.microsoft.com/library/gg492088.aspx)

- [Ismerje meg a valós idejű működési elemzések](https://msdn.microsoft.com/library/dn817827.aspx)

- Lásd: [közös munkaterhelési mintákat és az áttelepítés szempontjai](https://msdn.microsoft.com/library/dn673538.aspx) (amely azt ismerteti, ahol In-Memory OLTP gyakran biztosít jelentős teljesítménynövekedést munkaterhelési mintákat)

#### <a name="application-design"></a>Alkalmazás-tervezés

- [Memóriában tárolt OLTP-k (memóriabeli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)

- [Egy meglévő Azure SQL-alkalmazásban használható In-Memory OLTP](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Eszközök

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
