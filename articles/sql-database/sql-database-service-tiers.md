---
title: Az Azure SQL Database szolgáltatásszintek |} Microsoft Docs
description: Ismerje meg egyetlen szolgáltatásszintek és teljesítményszintek és tárolási méretek készlet adatbázisok.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 6ca78596ad6c4ed2e14b127fe55a70bd2f55411a
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309889"
---
# <a name="service-tier-purchasing-models-and-resources"></a>Szolgáltatási réteg beszerzési modellek és erőforrások 

A logikai kiszolgáló [Azure SQL Database](sql-database-technical-overview.md) két vásárlási modell számítási, tárolási és IO erőforrásokat kínál: a DTU-alapú alapjául szolgáló vásárlási modell és a vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió). 

> [!NOTE]
> [Felügyelt példányoknak](sql-database-managed-instance.md) az Azure SQL Database kínálnak a vCore-alapú alapjául szolgáló vásárlási modell.

A következő diagram és táblázat összehasonlítása és két vásárlási modell.

> [!IMPORTANT]
> VCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió), lásd: [alapjául szolgáló vásárlási modell vCore-alapú](sql-database-service-tiers-vcore.md)

|**Alapjául szolgáló vásárlási modell**|**Leírás**|**A legjobb**|
|---|---|---|
|DTU-alapú modell|Ez a modell egy számítási, tárolási és IO erőforrások csomagolt mértéken alapul. A teljesítményszintek különálló adatbázisok esetében DTU-k (adatbázis-tranzakciós egységek), rugalmas készletek esetében pedig rugalmas DTU-k formájában vannak meghatározva. Dtu és edtu-k kapcsolatban bővebben lásd: [Dtu és edtu-k](sql-database-what-is-a-dtu.md)?|Ajánlott az ügyfelek számára, akik egyszerű, előre konfigurált beállításai.| 
|vCore alapuló modell|Ez a modell lehetővé teszi, hogy egymástól függetlenül válik a számítási és tárolási erőforrások. Lehetővé teszi az SQL Server Azure hibrid juttatás segítségével költséghatékony.|Az ügyfelek, akik a rugalmasságot, a vezérlési és az átláthatóság érték legmegfelelőbb.|
||||  

![árképzési modellt](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió)

Egy virtuális core jelenti. a logikai Processzor érhető el, hogy a hardver generációja közül választhat. VCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) biztosít, a rugalmasságot, vezérlő, az egyéni erőforrás-felhasználás átláthatóság és egy egyszerű módon lefordítani a helyszíni munkaterhelési követelményeinek a felhőbe. Ez a modell méretezési számítási, memória és az alkalmazások és szolgáltatások igényeik alapján tároló lehetővé teszi. A vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió), az ügyfelek választhat általános célú és üzleti kritikus szolgáltatási szinteket (előzetes verzió) mindkét [adatbázisok egyetlen](sql-database-single-database-scale.md) és [rugalmas készletek](sql-database-elastic-pool.md). 

He vCore alapú alapjául szolgáló vásárlási modell (előzetes verzió) lehetővé teszi egymástól függetlenül válik a számítási és tárolási erőforrások felel meg a helyszíni teljesítmény és ár optimalizálása. Ha az adatbázis vagy a rugalmas készlethez használ több mint 300 DTU átalakítás vCore csökkentheti az költsége. Átválthat a választott API használatával vagy az Azure-portálon az állásidő nélkül. Azonban az átváltási nincs szükség. A DTU alapjául szolgáló vásárlási modell megfelel-e a teljesítményt és üzleti követelményeinek megfelelően, ha Ön továbbra is használja. Ha úgy dönt, hogy a DTU-modellből átalakítása vCore-modell, ki kell jelölni a használatával a következő tapasztalatok teljesítményszintet: minden 100 DTU Standard szint szükséges legalább 1 vCore általános célú réteg; Prémium szinten lévő minden egyes 125 DTU legalább 1 vCore fontos üzleti szint szükséges.

A vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió), az ügyfelek díj ellenében:
- Számítási (szolgáltatásréteg + vCores + hardver generációja száma) *
- Típus és adatainak és naplókönyvtárainak tárolókapacitást 
- Hány IOs **
- Biztonsági mentés tárolás (RA-GRS) ** 

\* A kezdeti nyilvános előzetes verziójában a generációból 4 logikai processzorok alapján Intel E5-2673 v3 (Haswell) 2.4-GHz-es processzor.

\*\* Előzetes és IOs 7 nap szabadon.

> [!IMPORTANT]
> Számítás-, IOs, adatok, és a naplók tárolásához van szó, adatbázis vagy a rugalmas készlet. Biztonsági másolatok tárolási fel van töltve, egyes adatbázisonként. Felügyelt példány díjak részletekért tekintse meg a [Azure SQL adatbázis felügyelt példány](sql-database-managed-instance.md).
> **Régió korlátozások:** vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió) még nem érhető el a következő régióban: Nyugat-Európában, Franciaország központi, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója és Ausztrália délkeleti.

## <a name="dtu-based-purchasing-model"></a>DTU-alapú alapjául szolgáló vásárlási modell

Az adatbázis-átviteli Unit (DTU) jelöli kevert méri, Processzor, memória, beolvassa, és. A DTU-alapú alapjául szolgáló vásárlási modell előre konfigurált együtteséből áll számítási erőforrások olyan készletét kínálja, és a meghajtó különböző szintű alkalmazásteljesítmény-tároló tartalmazza. Az ügyfelek, akik inkább az egyszerűség kedvéért egy előre konfigurált csomagot és a rögzített nagyságú befizetések havonta, előfordulhat, hogy keresse meg az DTU-alapú igényeiknek ideális. A DTU-alapú alapjául szolgáló vásárlási modell, az ügyfelek választhat **alapvető**, **szabványos**, és **prémium** szolgáltatásszintek mindkét [adatbázisokegyszeri](sql-database-single-database-scale.md) és [rugalmas készletek](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Mik az adatbázis-tranzakciós egységek (dtu-k)?
Egy adott szinten belül egyetlen Azure SQL-adatbázis egy [szolgáltatásréteg](sql-database-single-database-scale.md), Microsoft garantálja, hogy az adatbázis (vagy bármely más adatbázis Azure felhőben független), az erőforrások bizonyos szintű biztosít egy kiszámítható teljesítmény szintje. Erőforrások mennyisége akkor a program egy adatbázis-tranzakciós egységek vagy dtu-inak száma és számítási, tárolási és IO erőforrások csomagolt mértékét. Ezek az erőforrások között arány eredetileg alapján egy [teljesítményteszt OLTP-munkaterhelés](sql-database-benchmark-overview.md), tervezett jellemző az OLTP-munkaterhelések valós. A terhelés meghaladja az bármely ezeket az erőforrásokat, az átviteli sebesség esetén az szabályozottan halmozott – így a lassabb teljesítmény és időtúllépéseket okoz. A számítási feladatok által használt erőforrások nincs hatással a többi SQL adatbázis Azure felhőben számára elérhető erőforrások, és más munkaterhelések által használt erőforrások nem érintik az SQL-adatbázis számára elérhető erőforrások.

![határolókeret](./media/sql-database-what-is-a-dtu/bounding-box.png)

A relatív mennyiségű erőforrást más-más teljesítménybeli szinten Azure SQL-adatbázisok és a szolgáltatásszintek közötti ismertetése esetén a leghasznosabb-i nem. Például így dupla i adatbázis teljesítményszintjét növelésével megfelel így dupla, hogy az adatbázis számára elérhető erőforrások készletét. Például egy 1750 DTU-val rendelkező Prémium P11 adatbázis 350x több DTU számítási teljesítményt nyújt, mint egy 5 DTU-val rendelkező Alapszintű adatbázis.  

Használja a munkaterhelésnek (DTU) erőforrás-felhasználásának mélyebb betekintést [Azure SQL adatbázis-lekérdezési Terheléselemző](sql-database-query-performance.md) számára:

- A leggyakoribb lekérdezések azonosíthatja a CPU/időtartama/végrehajtási száma, amely potenciálisan a jobb teljesítmény kell beállítani. Például egy IO intenzív lekérdezés előnye származhat használatát [memórián belüli optimalizálási technikákat](sql-database-in-memory.md) a rendelkezésre álló memória egy bizonyos szolgáltatás szintjén és teljesítményszintet jobb kihasználása érdekében.
- Részletekbe menően tárhatják fel a lekérdezés részleteit, és tekintse meg a szöveg- és erőforrás-használat előzményeit.
- Hozzáférés teljesítményhangolás által végrehajtott műveletek megjelenítéséhez javaslatokat [SQL Database Advisor](sql-database-advisor.md).

Módosíthatja [DTU szolgáltatásszintek](sql-database-service-tiers-dtu.md) bármikor minimális állásidővel az alkalmazáshoz (általában átlagolási a négy másodperc). Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy rugalmas készlet edtu-k között a készlet több adatbázis oszt meg egy bizonyos mennyiségű használja.

![Bevezetés az SQL Database-be: önálló adatbázis DTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Mik azok a rugalmas adatbázis-tranzakciós egységek (edtu-k)?
Ahelyett, hogy adjon meg egy dedikált erőforrások (dtu-k), előfordulhat, hogy nem mindig szükséges, amely mindig elérhető SQL-adatbázis, mint elhelyezheti az adatbázisok egy [rugalmas készlet](sql-database-elastic-pool.md) osztja meg az erőforrásokat között SQL adatbázis-kiszolgálón Ezeket az adatbázisokat. A rugalmas készletekben található megosztott erőforrások rugalmas adatbázis-tranzakciós egységek vagy edtu-k mérik. Rugalmas készletek kezelése több adatbázisból, amelyek jelentősen eltérő és előre nem látható használati minták teljesítményértékeket egyszerű költséghatékony megoldást nyújt. Egy rugalmas készlet biztosítja, hogy az erőforrások nem tudja feldolgozni egy adatbázis a tárolókészletben, míg a készlet minden egyes adatbázis mindig biztosítása a minimálisan szükséges rendelkezésre álló erőforrások mennyiségét. 

![Bevezetés az SQL Database-be: eDTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Egy készlet edtu-k meghatározott számú rögzített áron kap. A rugalmas készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek a konfigurált határok között. Egy adatbázis nehezebb terhelés alatt szükség további edtu-k fog használni. Világosabb terhelések tartozó adatbázisok fogyaszt kevesebb edtu-k. Adatbázisok esetén nem fog használni, nincs edtu-k. Által használt dinamikus kiosztásnál a teljes készlet erőforrások helyett adatbázisonként, felügyeleti feladatok egyszerűsítettek, előre jelezhető költségvetést biztosítva a készletben.

A meglévő készletekhez további eDTU-k is hozzáadhatók anélkül, hogy a készlet adatbázisai leállnának, vagy bármilyen hatás érné őket. Ugyanígy ha az eDTU-kra már nincs szükség, bármikor el is távolíthatók a meglévő készletekből. Adhat hozzá vagy adatbázisokat a készlethez kivonása vagy korlát edtu / adatbázis mérete túl nagy terhelés alatt számára más adatbázisok edtu-k lefoglalni. Ha egy adatbázis van kiszámítható alatt-okhoz erőforrásokat, akkor is vegye ki a készletbe és a szükséges erőforrások előre meghatározott egyetlen adatbázis.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hogyan állapíthatom meg a számítási feladataimhoz szükséges DTU-k számát?
Ha meglévő helyszíni vagy SQL-kiszolgálói virtuális gépeken futó számítási feladatokat kíván áthelyezni az Azure SQL Database-be, a [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével megbecsülheti a szükséges DTU-k számát. Egy meglévő Azure SQL Database-munkaterhelés használható [SQL adatbázis-lekérdezési Terheléselemző](sql-database-query-performance.md) az adatbázis erőforrás-felhasználás (dtu-k) áttekintésével mélyebb betekintést a számítási feladatok optimalizálási megértése. Használhatja a [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV megtekintéséhez az elmúlt egy óra hálózatierőforrás-fogyasztás. Másik lehetőségként a katalógusnézet [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) megjeleníti az elmúlt 14 napban, de egy alacsonyabb hűség ötperces átlagok hálózatierőforrás-fogyasztás.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Honnan tudom, hogy hasznát venném-e egy rugalmas erőforráskészletnek?
A készleteket nagy számú, speciális felhasználási mintákkal rendelkező adatbázishoz tervezték. Egy adott adatbázisnak az ebben a mintában jellemzőek, egy alacsony kihasználtságú átlagos viszonylag ritkán előforduló tüskéket rendelkező. Az SQL Database automatikusan kiértékeli az SQL Database-kiszolgálók adatbázisainak erőforrás-használati előzményeit, és felajánlja a megfelelő készletkonfigurációt az Azure Portalon. További információkért lásd: [Mikor érdemes rugalmas készletet használni?](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Mi történik, ha szeretnék elérte a maximális i?
Teljesítményszintet kalibrálni, és a legfeljebb megengedett a kiválasztott szolgáltatási szint/teljesítménye szint a adatbázis számítási feladatok futtatásához szükséges erőforrások az irányadó. Ha a terhelést van elérte-e a CPU/adat IO/Log IO korlátok egyikét, Ön továbbra is megkapják azt a erőforrások megengedett maximális szintet, de valószínűleg is fog tapasztalni megnövekedett lekérdezés késések fordulnak elő. Ezek a korlátok nem okoznak hibákat, csak a számítási feladat elvégzésének lassulását. Egyes esetekben azonban a lassulás olyan jelentős lehet, hogy a lekérések időtúllépés miatt meghiúsulnak. Ha eléri a maximális engedélyezett egyidejű felhasználói munkamenetek/kérelmek (munkavégző szál), látni fogja a explicit hibák. Lásd: [erőforrás-korlátozások az Azure SQL Database]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) nem kapcsolódik a CPU, a memória, a adat IO vagy a tranzakciós napló IO erőforrás-korlátozások információt.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Teljesítményteszt eredmények válaszoknak az összekapcsolása a valós életben adatbázis teljesítménye
Fontos tisztában lennie azzal, hogy a minden referenciaalapok csak reprezentatív és tájékoztató érhetők. A tranzakciós díjakat biztosítanak a referenciaalap alkalmazással elért nem lesz ugyanazok, mint, előfordulhat, hogy érhető el, egyéb alkalmazásokkal. A javasolt típusok futtatni egy séma tartalmazó táblák és adattípusokat számos különböző tranzakció gyűjteményét tartalmazza. A javasolt gyakorolja az ugyanazon alapvető műveleteket, amelyek közösek az OLTP-munkaterhelések, amíg nem jelent bármely adatbázis vagy alkalmazás adott osztály. A javasolt célja adjon meg egy ésszerű útmutató relatív teljesítménye, egy adatbázist, amely várható amikor felfelé vagy lefelé skálázás teljesítményszintek között. A valóságban adatbázisok különböző méretű és összetettségét, munkaterhelések különböző keverékei előforduló, és különböző módokon reagál. Például egy IO-igényes alkalmazás előfordulhat, hogy hamarabb találati IO küszöbértékeket, vagy egy processzorigényes alkalmazás előfordulhat, hogy hamarabb találati CPU-korlátok. Nem biztos, hogy bármely adott adatbázis méretezni a terheléssel végezzenek alapként azonos módon van.

A referenciaalap és a módszert az alábbi részletesebben ismerteti.

### <a name="benchmark-summary"></a>Teljesítményteszt összegzése
ASDB méri az online tranzakció-feldolgozási (OLTP) munkaterhelések leggyakrabban előforduló alapszintű adatbázis-műveletek vegyesen teljesítményét. Bár a referenciaalap úgy van kialakítva, a számítógép-használatról szem előtt, az adatbázis-séma, az adatok feltöltése a felhőalapú, és a tranzakciók készített körben jellemző az OLTP-munkaterhelések leggyakrabban használt alapvető elem lehet.

### <a name="schema"></a>Séma
A séma elég különböző és összetettsége műveletek széles körének támogatására szolgál. A javasolt fut egy adatbázist hat táblák áll. A táblák három kategóriába sorolhatók: rögzített méretű, méretezés és növekedését. Két rögzített méretű táblák; három méretezési táblák; és egy egyre bővülő tábla. Rögzített méretű sorok számának állandó kell. Méretezési azzal arányos, hogy az adatbázis teljesítményét, de nem változtatja meg a referenciaalap során számosságuk kell. A növekvő tábla mérete, például egy méretezési táblázat a kezdeti betöltés, de a cardinality változások során a referenciaalap futó sort beszúrni, és törölni.

A séma tartalmaz vegyesen adattípusok, beleértve az egész szám, numerikus, a karakter, és a dátum/idő. A séma tartalmazza az elsődleges és másodlagos kulcsok, de nem idegen kulcsokkal – Ez azt jelenti, hogy nincsenek hivatkozási integritási megkötés táblák között.

Adatok generálása program állít elő, a kezdeti adatbázis adatai. Az egész és numerikus adatokat a különböző stratégiák jön létre. Bizonyos esetekben az értékek vannak osztva véletlenszerűen tartományban. Más esetekben az értékek egy halmazát van véletlenszerűen a(z) kell biztosítják, hogy egy adott terjesztési. Szövegmező eredményezett reális kinézetű adatokat szavak súlyozott listájából jönnek létre.

Az adatbázis mérete alapján egy "Mérettényező." A méretezési tényező (KB rövidítése) számossága alapján a méretezés és táblák növekvő határozza meg. Alább részben leírt a felhasználók és Pacing, az adatbázis méretének felhasználók számát, és minden arányosan méretezni a legjobb teljesítmény.

### <a name="transactions"></a>Tranzakciók
A munkaterhelés kilenc tranzakciótípusok áll, az alábbi táblázatban látható módon. Minden tranzakció célja, hogy jelöljön ki egy meghatározott adatbázis-motor és a rendszer hardver, kontrasztos megjelenítés a más tranzakciók a rendszer jellemzőit. Ez a megközelítés megkönnyíti a különböző összetevőket az általános teljesítményt hatásának értékelése. Például a "Olvasás nehéz" tranzakció eredménye olyan jelentős lemezről az olvasási műveletek.

| Tranzakció típusa | Leírás |
| --- | --- |
| Olvassa el a Lite |VÁLASSZA KI A; a memóriában; csak olvasható |
| Olvasási közepes |VÁLASSZA KI A; legtöbbször a memóriában; csak olvasható |
| Olvasási gyakori |VÁLASSZA KI A; többnyire nincs a memóriában; csak olvasható |
| A frissítés Lite |FRISSÍTÉS; a memóriában; olvasási és írási |
| Gyakori frissítése |FRISSÍTÉS; többnyire nincs a memóriában; olvasási és írási |
| INSERT Lite |INSERT; a memóriában; olvasási és írási |
| Gyakori beszúrása |INSERT; többnyire nincs a memóriában; olvasási és írási |
| Törlés |TÖRLÉS; a memóriában, és nem memóriában; olvasási és írási |
| CPU gyakori |VÁLASSZA KI A; a memóriában; CPU-terhelés viszonylag nagy mennyiségű; csak olvasható |

### <a name="workload-mix"></a>Munkaterhelés-vegyes
Tranzakciók véletlenszerűen kiválasztott egy súlyozott terjesztési a következő általános kettőn együtt. A teljes vegyes van egy olvasási/írási arányt körülbelül 2:1.

| Tranzakció típusa | Vegyes % |
| --- | --- |
| Olvassa el a Lite |35 |
| Olvasási közepes |20 |
| Olvasási gyakori |5 |
| A frissítés Lite |20 |
| Gyakori frissítése |3 |
| INSERT Lite |3 |
| Gyakori beszúrása |2 |
| Törlés |2 |
| CPU gyakori |10 |

### <a name="users-and-pacing"></a>Felhasználók és pacing
A referenciaalap munkaterhelés célja egy eszköz, amellyel a tranzakciók közötti kapcsolatok egy egyidejű felhasználók száma szimulálásához készlete. Bár a kapcsolatok és a tranzakciók összes gép jön létre, az egyszerűség érdekében ezek a kapcsolatok lesz az "felhasználók". Bár minden felhasználó működése független a többi felhasználó, a minden felhasználó az azonos ciklus az alábbi lépéseket hajtsa végre:

1. Egy adatbázis-kapcsolat létrehozásához.
2. Ismételje meg a mindaddig, mígnem való kilépéshez jelzi:
   * Véletlenszerű (a súlyozott terjesztési) egy olyan tranzakciót választott.
   * A kijelölt tranzakció végrehajtása, és mérheti a válaszidőt.
   * Várjon, amíg pacing késést.
3. Zárja be az adatbázis-kapcsolatot.
4. Kilépés.

A pacing késleltetése (2/c. lépés) véletlenszerűen kiválasztott-e, de a terjesztési, amely rendelkezik 1.0-ás másodpercenként átlagosan. Így minden felhasználóhoz, átlagosan generálhat másodpercenként legfeljebb egy tranzakciót.

### <a name="scaling-rules"></a>Skálázási szabályok
A felhasználók száma (Mérettényező egységekben) az adatbázis mérete határozza meg. Nincs minden öt Mérettényező egységek egy felhasználó. A pacing késedelem miatt egy felhasználó hozhat létre a legfeljebb egy tranzakciót másodpercenként átlagosan.

Például egy méretezési tényezős 500 (KB = 500) adatbázis 100 felhasználót lesz, és a 100 TP-k maximális sebesség érhető el. A magasabb TP-k meghajtó arány előírja, hogy több felhasználó és egy nagyobb adatbázist.

Az alábbi táblázat az egyes szolgáltatást és teljesítményszintet ténylegesen szűk felhasználók számát jeleníti meg.

| Szolgáltatási réteg (teljesítményszint szükséges) | Felhasználók | Adatbázisméret |
| --- | --- | --- |
| Alapszintű |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2.1 GB |
| Standard (S2) |50 |7.1 GB |
| Prémium (P1) |100 |14 GB |
| Prémium (P2) |200 |28 GB |
| Prémium (P6) |800 |114 GB |

### <a name="measurement-duration"></a>Mérési időtartama
Egy érvényes teljesítményteszt futtatásához igényel a stabil állapot mérési időtartama legalább egy órát.

### <a name="metrics"></a>Mérőszámok
Az alapvető metrikákat a teljesítményteszt olyan átviteli sebesség és a válaszidőt.

* Az alapvető teljesítménymérés a teljesítményteszt esetén átviteli sebesség. Átviteli sebesség jelentésekről--időegység, minden tranzakciótípus számbavételi tranzakciókat.
* Válaszidő teljesítmény kiszámíthatóságot mérőszáma. A válasz időkorlát osztály szolgáltatást, magasabb osztályok szigorúbb válasz idő követelmény, hogy alább látható módon szolgáltatás függ.

| Szolgáltatás osztálya | Átviteli sebesség mérték | Válasz idő követelmény |
| --- | --- | --- |
| Prémium |Másodpercenkénti tranzakciók |95. százalékos érték 0,5 másodperc: |
| Standard |Percenkénti tranzakciók |90. százalékos érték 1.0-ás másodperc: |
| Alapszintű |Óránkénti tranzakciók |80 PERCENTILIS 2.0 másodperc: |

## <a name="next-steps"></a>További lépések

- VCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió), lásd: [alapjául szolgáló vásárlási modell vCore-alapú](sql-database-service-tiers-vcore.md)
- A DTU-alapú alapjául szolgáló vásárlási modell, lásd: [alapjául szolgáló vásárlási modell DTU-alapú](sql-database-service-tiers-dtu.md).