---
title: In-Memory technológiák
description: Az Azure SQL Database In-Memory technológiák nagyban javítják a tranzakciós és elemzési számítási feladatok teljesítményét.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: de60712451d4c2e8a7d931f7a09352f55be05694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73810266"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>A teljesítmény optimalizálása a memórián belüli technológiák használatával az SQL Database-ben

Az Azure SQL Database memóriabeli technológiái lehetővé teszik az alkalmazás teljesítményének növelését és az adatbázis költségeinek potenciális csökkentését. 

## <a name="when-to-use-in-memory-technologies"></a>Mikor kell használni a memórián belüli technológiákat?

Az Azure SQL Database memóriabeli technológiáinak használatával teljesítménybeli javulást érhet el a különböző munkaterhelésekkel:

- **Tranzakciós** (online tranzakciós feldolgozás (OLTP)), ahol a legtöbb kérelem kisebb adathalmazt olvas vagy frissít (például CRUD-műveletek).
- **Analitikus** (online analitikus feldolgozás (OLAP)), ahol a legtöbb lekérdezések összetett számítások at a jelentési célokra, egy bizonyos számú lekérdezések, amelyek betöltése és hozzáfűzése adatokat a meglévő táblák (úgynevezett tömeges betöltése), vagy törli az adatokat a táblákból. 
- **Vegyes** (hibrid tranzakció/analitikus feldolgozás (HTAP)), ahol mind az OLTP, mind az OLAP lekérdezések ugyanazon az adathalmazon történnek.

A memórián belüli technológiák javíthatják ezeknek a számítási feladatoknak a teljesítményét azáltal, hogy a memóriában tartja a feldolgozandó adatokat, a lekérdezések natív összeállításával, vagy olyan fejlett feldolgozással, mint a kötegelt feldolgozás és a SIMD-utasítások, amelyek a mögöttes hardver. 

## <a name="overview"></a>Áttekintés

Az Azure SQL Database a következő memóriatechnológiákkal rendelkezik:
- *[A memórián belüli OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* növeli a tranzakciók másodpercenkénti számát, és csökkenti a tranzakciófeldolgozás késését. A memórián belüli OLTP előnyeit a következők éppen a következőképpen jár lehetővé: nagy átviteli sebességű tranzakciófeldolgozás, például kereskedés és játék, eseményekvagy IoT-eszközök ből történő adatbetöltés, gyorsítótárazás, adatbetöltés, valamint ideiglenes tábla- és táblaváltozó-forgatókönyvek.
- *A fürtözött oszlopcentrikus indexek csökkentik* a tárolási lábnyomot (akár 10-szer), és javítják a jelentéskészítési és elemzési lekérdezések teljesítményét. Az adatárkák ténytábláival több adat fér el az adatbázisban, és javíthatja a teljesítményt. Emellett az operatív adatbázis előzményadataival archiválhatja, és akár tízszer több adatot is lekérdezhet.
- A HTAP *nonclustered oszlopcentrikus indexei* segítségével valós idejű betekintést nyerhet az üzleti tevékenységébe az operatív adatbázis közvetlen lekérdezésével, anélkül, hogy költséges kivonatot, átalakítást és betöltési (ETL) folyamatot kellene futtatnia, és meg kell várnia az adattárház feltöltését. A nem fürtözött oszlopcentrikus indexek lehetővé teszik az OLTP-adatbázis elemzési lekérdezéseinek gyors végrehajtását, miközben csökkentik a működési terhelésre gyakorolt hatást.
- A HTAP *memóriaoptimalizált fürtözött oszlopcentrikus indexei* lehetővé teszik a gyors tranzakciófeldolgozást, és az elemzési lekérdezések *egyidejű* futtatását nagyon gyorsan ugyanazon az adaton.

Az oszlopcentrikus indexek és a memórián belüli OLTP 2012 és 2014 óta az SQL Server termék része. Az Azure SQL Database és az SQL Server a memórián belüli technológiák ugyanazon implementációját tartalmazza. A továbbiakban ezeknek a technológiáknak az új funkciói először az Azure SQL Database-ben jelennek meg, mielőtt azok az SQL Server ben megjelennek.

## <a name="benefits-of-in-memory-technology"></a>A memórián belüli technológia előnyei

A hatékonyabb lekérdezés- és tranzakciófeldolgozás miatt a memórián belüli technológiák is segítenek a költségek csökkentésében. Általában nem kell frissítenie az adatbázis tarifacsomagját a teljesítménynövekedés eléréséhez. Bizonyos esetekben előfordulhat, hogy még csökkentheti a tarifacsomagot, miközben továbbra is teljesítménybeli javulást tapasztal a memórián belüli technológiákkal.

Íme két példa arra, hogy a memórián belüli OLTP hogyan segített jelentősen javítani a teljesítményt:

- A memórián belüli OLTP használatával a [Quorum Business Solutions megtudta duplázni a munkaterhelését, miközben 70%-kal javította a DKU-kat.](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

  - A DTU *adatbázis-tranzakciós egységet*jelent, és tartalmazza az erőforrás-felhasználás mérését.
- A következő videó bemutatja az erőforrás-felhasználás jelentős javulását egy minta számítási feladattal: [In-Memory OLTP az Azure SQL Database Video programban.](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB)
  - További információ: [In-Memory OLTP az Azure SQL Database blogpostban](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> A memórián belüli technológiák prémium és üzleti legkritikusabb szintű Azure SQL-adatbázisokban és prémium rugalmas készletekben érhetők el.

Az alábbi videó ismerteti a potenciális teljesítménynövekedést az Azure SQL Database memórián belüli technológiáival. Ne feledje, hogy a teljesítménynyereség, amely et lát, mindig számos tényezőtől függ, beleértve a munkaterhelés és az adatok jellegét, az adatbázis hozzáférési mintáját és így tovább.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Ez a cikk ismerteti a memórián belüli OLTP és oszlopcentrikus indexek, amelyek az Azure SQL Database, és mintákat is tartalmaz:

- Látni fogja, hogy ezek a technológiák milyen hatással vannak a tárolási és adatméretkorlátokra.
- Megtudhatja, hogyan kezelheti az ezeket a technológiákat használó adatbázisok mozgását a különböző tarifaszintek között.
- Két mintát fog látni, amelyek a memórián belüli OLTP használatát, valamint az Azure SQL Database oszlopcentrikus indexeit mutatják be.

További információkért lásd:

- [In-Memory OLTP áttekintése és használati forgatókönyvek](https://msdn.microsoft.com/library/mt774593.aspx) (hivatkozásokat tartalmaz az ügyfél esettanulmányok és információk az első lépésekhez)
- [Dokumentáció a memórián belüli OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Oszlopcentrikus indexek útmutató](https://msdn.microsoft.com/library/gg492088.aspx)
- Hibrid tranzakciós/analitikai feldolgozás (HTAP), más néven [valós idejű működési elemzés](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>Memórián belüli OLTP

A memórián belüli OLTP technológia rendkívül gyors adatelérési műveleteket biztosít az összes adat memóriában tartásával. Speciális indexeket, a lekérdezések natív összeállítását és a zár nélküli adatelérést is használ az OLTP számítási feladatok teljesítményének javítása érdekében. A memórián belüli OLTP-adatok kétféleképpen rendszerezhetők:

- **Memóriaoptimalizált sorbolt** formátum, ahol minden sor külön memóriaobjektum. Ez egy klasszikus memóriabeli OLTP formátum, amely et nagy teljesítményű OLTP-számítási feladatokra optimalizált. A memóriaoptimalizált tábláknak két típusa van, amelyek a memóriaoptimalizált sorbolt formátumban használhatók:
  - *Tartós táblák* (SCHEMA_AND_DATA), ahol a memóriába helyezett sorok a kiszolgáló újraindítása után is megőrződnek. Az ilyen típusú táblák úgy viselkednek, mint egy hagyományos rowstore tábla a memórián belüli optimalizálás további előnyeivel.
  - *Nem tartós táblák* (SCHEMA_ONLY), ahol a sorok nem maradnak meg az újraindítás után. Az ilyen típusú tábla ideiglenes adatokhoz (például ideiglenes táblák cseréjéhez) vagy olyan táblákhoz készült, ahol gyorsan be kell töltenie az adatokat, mielőtt áthelyezi néhány megőrzött táblába (úgynevezett átmeneti táblákba).
- **Memóriaoptimalizált oszlopcentrikus** formátum, ahol az adatok oszlopos formátumban vannak rendezve. Ez a struktúra HTAP-forgatókönyvekhez készült, ahol elemzési lekérdezéseket kell futtatnia ugyanazon az adatstruktúrán, ahol az OLTP számítási feladatok futnak.

> [!Note]
> In-Memory OLTP technológia célja az adatstruktúrák, amelyek teljes mértékben tartózkodnak a memóriában. Mivel a memórián belüli adatok nem rakodhatók ki a lemezre, győződjön meg arról, hogy elegendő memóriával rendelkező adatbázist használ. További [részletekért lásd: A memórián belüli OLTP adatméret és tárolókorlát.](#data-size-and-storage-cap-for-in-memory-oltp)

Egy gyors alapozó a memórián belüli OLTP: [Quickstart 1: In-Memory OLTP Technologies a gyorsabb T-SQL teljesítmény](https://msdn.microsoft.com/library/mt694156.aspx) (egy másik cikk, amely segít az induláshoz)

Részletes videók a technológiákról:

- [Memóriabeli OLTP az Azure SQL Database-ben](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (amely a teljesítményelőnyök és az eredmények saját kezű reprodukálásának lépéseit tartalmazza)
- [In-Memory OLTP Videók: Mi ez, és mikor / Hogyan kell használni](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Van egy programozott módon megérteni, hogy egy adott adatbázis támogatja a memórián belüli OLTP. A következő Transact-SQL lekérdezést hajthatja végre:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Ha a lekérdezés **1**értéket ad vissza, a memórián belüli OLTP támogatja ezt az adatbázist. A következő lekérdezések azonosítják az összes olyan objektumot, amelyet el kell távolítani ahhoz, hogy az adatbázis taszítható legyen Standard/Basic rendszerre:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Adatméret és tárolósapka a memórián belüli OLTP-hoz

A memórián belüli OLTP memóriaoptimalizált táblákat tartalmaz, amelyek a felhasználói adatok tárolására szolgálnak. Ezek a táblák szükségesek, hogy elférjenek a memóriában. Mivel a memóriát közvetlenül az SQL Database szolgáltatásban kezelheti, a felhasználói adatokra vonatkozó kvóta fogalmát használjuk. Ezt az ötletet *memóriabe való bekerülési egységOLTP-tárolónak nevezzük.*

Minden támogatott egyetlen adatbázis-tarifaréteg és minden egyes rugalmas készlet tarifacsomag tartalmaz egy bizonyos mennyiségű memórián belüli OLTP-tároló. Lásd: [DTU-alapú erőforráskorlátok – egyetlen adatbázis](sql-database-dtu-resource-limits-single-databases.md), [DTU-alapú erőforráskorlátok – rugalmas készletek,](sql-database-dtu-resource-limits-elastic-pools.md)[virtuálismagalapú erőforráskorlátok – egyetlen adatbázis](sql-database-vcore-resource-limits-single-databases.md) és [virtuálismag-alapú erőforráskorlátok – rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md).

A következő elemek számítanak bele a memórián belüli OLTP tárolókorlátba:

- Aktív felhasználói adatsorok memóriaoptimalizált táblákban és táblaváltozókban. Ne feledje, hogy a régi sorverziók nem számítanak bele a kupakba.
- A memóriaoptimalizált táblák indexei.
- Az ALTER TABLE műveletek működési terhelése.

Ha eléri a korlátot, kvótán kívüli hibaüzenetet kap, és már nem tud adatokat beszúrni vagy frissíteni. A hiba enyhítése érdekében törölje az adatokat, vagy növelje az adatbázis vagy készlet tarifacsomagját.

A memórián belüli OLTP-tárolókihasználtság figyeléséről és a riasztások konfigurálásáról a korlát betöltése kor című témakörben talál további információt a [Memórián belüli tárhely figyelése című témakörben.](sql-database-in-memory-oltp-monitoring.md)

#### <a name="about-elastic-pools"></a>A rugalmas medencékről

Rugalmas készletek, a memórián belüli OLTP-tároló a készlet összes adatbázisa között megvan osztva. Ezért az egyik adatbázisban való használat hatással lehet más adatbázisokra. Ennek két mérséklése a következő:

- Konfiguráljon `Max-eDTU` `MaxvCore` egy vagy adatbázisok, amelyek alacsonyabbak, mint az eDTU vagy virtuálismag-szám a készlet egészére. Ez a maximális sapkák a memórián belüli OLTP tárolási kihasználtság, a készlet bármely adatbázisában, az eDTU-számnak megfelelő méretre.
- 0-nál `MinvCore` nagyobb beállítás. `Min-eDTU` Ez a minimális érték garantálja, hogy a készlet minden adatbázisa rendelkezik a `Min-eDTU` konfigurált vagy `vCore`a rendszernek megfelelő, memórián belüli OLTP-tárolómennyiséggel.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>A memórián belüli OLTP-technológiákat használó adatbázisok szolgáltatásrétegeinek módosítása

Az adatbázist vagy példányt bármikor magasabb szintre frissítheti, például az általános céltól az üzleti szempontból kritikus (vagy standardtól a prémium szintig). A rendelkezésre álló funkciók és erőforrások csak növekednek.

A szint visszaminősítése azonban negatívan befolyásolhatja az adatbázist. A hatás különösen akkor nyilvánvaló, ha az üzleti legkritikusabb általános célúról általános célokra (vagy prémiumról standardra vagy alapszintűre) vált vissza, ha az adatbázis memórián belüli OLTP-objektumokat tartalmaz. A memóriára optimalizált táblák a visszalépés után nem érhetők el (még akkor sem, ha láthatóak maradnak). Ugyanezek a szempontok vonatkoznak, ha egy rugalmas készlet tarifacsomagjának csökkentése, vagy egy adatbázis áthelyezése a memórián belüli technológiák, egy standard vagy alapszintű rugalmas készlet.

> [!Important]
> A memórián belüli OLTP nem támogatott az általános célú, standard vagy alapszintű rétegben. Ezért nem lehet áthelyezni egy adatbázist, amely rendelkezik a memórián belüli OLTP objektumok a standard vagy alapszintű szintre.

Mielőtt az adatbázist Standard/Basic rendszerre minősítené, távolítsa el az összes memóriaoptimalizált táblát és táblatípust, valamint az összes natívan lefordított T-SQL modult. 

*Az üzleti legkritikusabb rétegben lévő erőforrások leskálázása:* A memóriaoptimalizált táblákban lévő adatoknak el kell érniük az adatbázis vagy a felügyelt példány rétegéhez társított memórián belüli OLTP-tárolóban, vagy a rugalmas készletben érhetők el. Ha megpróbálja lekicsinyelni a réteget, vagy áthelyezni az adatbázist egy olyan készletbe, amely nem rendelkezik elegendő memóriabeli OLTP-tárhellyel, a művelet sikertelen lesz.

## <a name="in-memory-columnstore"></a>Memórián belüli oszlopcentrikus

A memórián belüli oszlopcentrikus technológia lehetővé teszi, hogy nagy mennyiségű adatot tároljon és kérdezzen le a táblákban. Az Oszlopcentrikus technológia oszlopalapú adattárolási formátumot és kötegelt lekérdezésfeldolgozást használ, hogy az OLAP-munkaterhelésekben a lekérdezési teljesítmény akár tízszeresét érje el a hagyományos sororientált tárolással szemben. Az adattömörítés akár tízszeresét is elérheti a tömörítetlen adatmérethez képest.
Az oszlopcentrikus modelleknek két típusa van, amelyek segítségével rendszerezheti az adatokat:

- **Fürtözött oszlopcentrikus,** ahol a táblázat ban lévő összes adat oszlopos formátumban van rendezve. Ebben a modellben a tábla összes sora oszlopos formátumban van elhelyezve, amely nagymértékben tömöríti az adatokat, és lehetővé teszi a gyors analitikus lekérdezések és jelentések végrehajtását a táblán. Az adatok jellegétől függően az adatok mérete 10x-100x-re csökkenhet. Fürtözött oszlopcentrikus modell is lehetővé teszi a gyors betöltése nagy mennyiségű adat (tömeges betöltése), mivel a nagy kötegek adatok nagyobb, mint 100K sorok tömörített, mielőtt azok a lemezen tárolt. Ez a modell egy jó választás a klasszikus adattárház forgatókönyvek. 
- **Nem fürtözött oszlopcentrikus,** ahol az adatok hagyományos rowstore táblában tárolódnak, és az oszlopcentrikus formátumban van egy index, amely az analitikus lekérdezésekhez használatos. Ez a modell lehetővé teszi a hibrid tranzakciós-analitikus feldolgozás (HTAP): a tranzakciós számítási feladatok on performant valós idejű elemzés futtatásának képessége. Oltp-lekérdezések végrehajtása rowstore tábla, amely egy kis sorkészlet elérésére van optimalizálva, míg az OLAP lekérdezések végrehajtása az oszlopcentrikus index, amely jobb választás a vizsgálatok és elemzések eléréséhez. Az Azure SQL Database Query optimizer dinamikusan választja rowstore vagy oszlopcentrikus formátum a lekérdezés alapján. A nem fürtözött oszlopcentrikus indexek nem csökkentik az adatok méretét, mivel az eredeti adatkészlet az eredeti rowstore-táblában van, módosítás nélkül. A további oszlopcentrikus index méretének azonban nagyságrenddel kisebbnek kell lennie, mint az egyenértékű B-faindex.

> [!Note]
> A memórián belüli oszlopcentrikus technológia csak a memóriában lévő feldolgozáshoz szükséges adatokat tárolja, míg a memóriába nem férő adatok a lemezen tárolódnak. Ezért a memórián belüli oszlopcentrikus struktúrákban lévő adatok mennyisége meghaladhatja a rendelkezésre álló memória mennyiségét. 

Részletes videó a technológiáról:

- [Oszlopcentrikus index: In-Memory Analytics videók az Ignite 2016-ról](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Az oszlopcentrikus indexek adatmérete és tárolása

Az oszlopcentrikus indexek nem szükségesek a memóriába való beilleszkedéshez. Ezért az indexek méretének egyetlen felső határa a maximális teljes adatbázisméret, amely a [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) ben és a [virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md) cikkekben van dokumentálva.

Fürtözött oszlopcentrikus indexek használataesetén az alaptábla-tároló oszlopos tömörítést használja. Ez a tömörítés jelentősen csökkentheti a felhasználói adatok tárolási lábnyomát, ami azt jelenti, hogy több adatot is eltud férni az adatbázisban. És a tömörítés tovább növelhető [oszlopos archiválási tömörítéssel](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). Az elérhető tömörítés mennyisége az adatok jellegétől függ, de a tömörítés tízszerese nem ritka.

Ha például egy legfeljebb 1 terabájt (TB) méretű adatbázissal rendelkezik, és az oszlopcentrikus indexek használatával a tömörítés tízszeresét éri el, összesen 10 TB felhasználói adatot illeszthet be az adatbázisba.

Ha nem fürtözött oszlopcentrikus indexeket használ, az alaptábla továbbra is a hagyományos rowstore formátumban tárolódik. Ezért a tárolási megtakarítások nem olyan nagy, mint a fürtözött oszlopcentrikus indexek. Ha azonban számos hagyományos, nem fürtözött indexet egyetlen oszlopcentrikus indexre cserél, továbbra is láthatja a tábla tárolási lábnyomának általános megtakarítását.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Oszlopcentrikus indexeket tartalmazó adatbázisok szolgáltatásrétegeinek módosítása

*Előfordulhat, hogy az egyes adatbázisok alapszintű vagy standard ra való visszaminősítése* nem lehetséges, ha a célréteg az S3 alatt van. Az oszlopcentrikus indexek csak az üzleti kritikus/prémium szintű tarifacsomagon és a standard szinten, s3-as és újabb szinten támogatottak, az alapszinten nem. Ha az adatbázist nem támogatott szintre vagy szintre minősíti vissza, az oszlopcentrikus index elérhetetlenné válik. A rendszer fenntartja az oszlopcentrikus indexet, de soha nem használja ki az indexet. Ha később egy támogatott szintre vagy szintre frissít, az oszlopcentrikus index azonnal készen áll az újra használható használatra.

Ha **fürtözött** oszlopcentrikus indexe van, a teljes tábla a visszaminősítés után elérhetetlenné válik. Ezért azt javasoljuk, hogy dobja el az összes *fürtözött* oszlopcentrikus indexek, mielőtt visszaminősíti az adatbázist egy nem támogatott szintre vagy szintre.

> [!Note]
> A felügyelt példány minden rétegben támogatja a ColumnStore indexeket.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>További lépések

- [1. rövid útmutató: In-Memory OLTP Technologies a gyorsabb T-SQL teljesítmény érdekében](https://msdn.microsoft.com/library/mt694156.aspx)
- [A memórián belüli OLTP használata egy meglévő Azure SQL-alkalmazásban](sql-database-in-memory-oltp-migration.md)
- [Memóriabe való OLTP-tároló monitora](sql-database-in-memory-oltp-monitoring.md) a memórián belüli OLTP-hoz
- [Próbálja ki a memóriában lévő funkciókat az Azure SQL Database-ben](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>További források

### <a name="deeper-information"></a>Mélyebb információk

- [Ismerje meg, hogyan duplázza meg a Quorum a kulcsadatbázis munkaterhelését, miközben 70%-kal csökkenti a DTU-t a memórián belüli OLTP segítségével az SQL Database-ben](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Memórián belüli OLTP az Azure SQL Database blogbejegyzésében](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Tudjon meg többet a memórián belüli OLTP-ról](https://msdn.microsoft.com/library/dn133186.aspx)
- [További információ az oszlopcentrikus indexekről](https://msdn.microsoft.com/library/gg492088.aspx)
- [Ismerje meg a valós idejű operatív elemzést](https://msdn.microsoft.com/library/dn817827.aspx)
- Lásd: [Gyakori számítási feladatok mintái és áttelepítési szempontok](https://msdn.microsoft.com/library/dn673538.aspx) (amely leírja a számítási feladatok mintáit, ahol a memórián belüli OLTP általában jelentős teljesítménynövekedést biztosít)

### <a name="application-design"></a>Az alkalmazás kialakítása

- [Memórián belüli OLTP (memórián belüli optimalizálás)](https://msdn.microsoft.com/library/dn133186.aspx)
- [A memórián belüli OLTP használata egy meglévő Azure SQL-alkalmazásban](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Eszközök

- [Azure-portál](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
