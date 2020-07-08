---
title: Mi a Azure Cosmos DB Analytical Store (előzetes verzió)?
description: Ismerkedjen meg Azure Cosmos DB tranzakciós (sor-alapú) és analitikai (oszlop alapú) tárolóval. A nagyméretű számítási feladatokhoz és a tranzakciós tárolóból az analitikai tárolóba történő automatikus szinkronizálás előnyei, valamint a teljesítményre gyakorolt hatás
author: SriChintala
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: a6f486f15fb5967dfb14508115e2340e4953be81
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85116026"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>Mi a Azure Cosmos DB Analytical Store (előzetes verzió)?

> [!IMPORTANT]
> Azure Cosmos DB analitikus tároló jelenleg előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Azure Cosmos DB Analytical Store egy teljesen elkülönített oszlop, amely lehetővé teszi a nagy léptékű elemzések üzemeltetését a Azure Cosmos DB működési adatain anélkül, hogy ez hatással lenne a tranzakciós munkaterhelésekre.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Kihívások az operatív adatok nagy méretű elemzésével

Egy Azure Cosmos DB tárolóban lévő többmodelles operatív adatértékek belsőleg tárolódnak egy indexelt sorokon alapuló "tranzakciós tárolóban". A sor tárolási formátuma lehetővé teszi a gyors tranzakciós olvasást és írást az ezredmásodpercekben megadott válaszidő és az operatív lekérdezések esetében. Ha az adatkészlet nagy mennyiségű, az összetett analitikus lekérdezések költségesek lehetnek az ebben a formátumban tárolt adatok kiépített átviteli sebessége alapján. A kiépített átviteli sebesség nagy mértékben felhasználható a valós idejű alkalmazások és szolgáltatások által használt tranzakciós munkaterhelések teljesítményére.

A nagy mennyiségű adatok elemzéséhez a rendszer kinyeri az operatív adatok Azure Cosmos DB tranzakciós tárolójából, és egy külön adatrétegben tárolja azokat. Az adatraktárban és az adattárban lévő adattárolók például megfelelő formátumban tárolódnak. Ezek az adatok később nagy léptékű elemzésekhez használatosak, és a számítási motor, például a Apache Spark-fürtök használatával elemezhetők. Az analitikus tárolás és a számítási rétegek az operatív adatokból való elkülönítése további késleltetést eredményez, mivel az ETL (kinyerés, átalakítás, betöltés) folyamatok ritkábban futnak a tranzakciós számítási feladatokra gyakorolt lehetséges hatás csökkentése érdekében.

Az ETL-folyamatok akkor is bonyolultak lesznek, ha az operatív információkra vonatkozó frissítéseket az újonnan betöltött működési adatmennyiség kezelésekor hasonlítják össze. 

## <a name="column-oriented-analytical-store"></a>Oszlop-orientált analitikai tár

Azure Cosmos DB analitikus tároló foglalkozik a hagyományos ETL-folyamatokkal kapcsolatos bonyolultsági és késési kihívásokkal. Azure Cosmos DB az analitikus tároló automatikusan szinkronizálhatja az operatív adatait egy külön oszlopos tárolóba. Az oszlopos tároló formátuma alkalmas a nagyméretű analitikai lekérdezések optimalizált módon történő elvégzésére, ami az ilyen lekérdezések késésének javítását eredményezi.

Az Azure szinapszis-hivatkozás használatával most már nem ETL HTAP-megoldásokat hozhat létre közvetlenül a szinapszis Analyticsből Azure Cosmos DB analitikus áruházhoz való csatolással. Lehetővé teszi a közel valós idejű, nagy léptékű elemzések futtatását az operatív adatain.

## <a name="analytical-store-details"></a>Analitikus tároló részletei

Amikor engedélyezi az analitikai tárolót egy Azure Cosmos DB tárolón, a tárolóban lévő operatív adatai alapján létrejön egy új, belső tároló. Ez az oszlop tárolja az adott tárolóhoz tartozó sor-orientált tranzakciós tárolótól függetlenül. Az operatív adataihoz tartozó lapkákat, frissítéseket és törléseket a rendszer automatikusan szinkronizálja az analitikus tárolóba. Az adatszinkronizáláshoz nincs szükség az adatmódosítási csatornára vagy ETL-ra.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>A működési adatok elemzési számítási feladatainak tárolására szolgáló oszlop

Az analitikai számítási feladatok jellemzően a kiválasztott mezők összesítéseit és szekvenciális vizsgálatait foglalják magukban. Az adatok egy oszlop – fő sorrendbe való tárolásával az analitikai tároló lehetővé teszi, hogy az egyes mezőkhöz tartozó értékek egy csoportja együtt legyen szerializálva. Ez a formátum csökkenti az adott mezőkhöz tartozó statisztikák vizsgálatához vagy számításához szükséges IOPS. Jelentősen javítja a lekérdezések válaszideje a nagy adathalmazokon végzett vizsgálatok során. 

Ha például az operatív táblák formátuma a következő:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Példa operatív táblázatra" border="false":::

A sor tároló a fenti, soronként szerializált, soros formában megőrzött adatmennyiséget tárolja a lemezen. Ez a formátum lehetővé teszi a gyorsabb tranzakciós olvasási, írási és működési lekérdezéseket, például a "a Product1 vonatkozó információk visszaadása". Mivel azonban az adatkészlet nagy mértékben növekszik, és ha összetett analitikai lekérdezéseket szeretne futtatni az adaton, költséges lehet. Ha például a "berendezés" kategóriába tartozó termék értékesítési trendjeit szeretné lekérni különböző üzleti egységeken és hónapokon keresztül, akkor összetett lekérdezést kell futtatnia. Az adathalmaz nagyméretű vizsgálata költséges lehet a kiépített átviteli sebesség szempontjából, és hatással lehet a valós idejű alkalmazásokat és szolgáltatásokat használó tranzakciós számítási feladatok teljesítményére is.

Az analitikus tároló, amely egy oszlopos tároló, alkalmasabb az ilyen lekérdezésekhez, mert hasonló adatmezőket szerializál, és csökkenti a lemez IOPS.

Az alábbi képen a tranzakciós sor tárolója és az analitikai oszlop tárolója látható Azure Cosmos DBban:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Tranzakciós sorok tárolása és analitikai oszlopainak tárolása Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Leválasztott teljesítmény az analitikai számítási feladatokhoz

Az analitikus lekérdezések miatt nincs hatással a tranzakciós számítási feladatok teljesítményére, mivel az analitikai tároló elkülönül a tranzakciós tárolótól.  Az analitikai tárolónak nincs szüksége elkülönített kérelmekre (RUs).

### <a name="auto-sync"></a>Automatikus szinkronizálás

Az automatikus szinkronizálás a Azure Cosmos DB teljes körűen felügyelt funkcióját jelenti, ahol a lapkák, a frissítések és az operatív adatok törlését automatikusan szinkronizálja a tranzakciós tárolóból az analitikai tárolóba, közel valós időben, 5 percen belül.

Az automatikus szinkronizálási képesség az analitikai tárolóval együtt a következő alapvető előnyöket nyújtja:

#### <a name="scalability--elasticity"></a>Méretezhetőség & rugalmasság

A horizontális particionálással a Azure Cosmos DB tranzakciós tároló rugalmasan méretezheti a tárterületet és az átviteli sebességet anélkül, hogy leállás nélkül lenne. A tranzakciós tárolóban a horizontális particionálás rugalmas & rugalmasságot biztosít az automatikus szinkronizálásban, így biztosítva, hogy az adatkezelés közel valós időben történjen az analitikai tárolóba. Az adatszinkronizálás a tranzakciós adatforgalomtól függetlenül történik, függetlenül attól, hogy 1000 művelet/mp vagy 1 000 000 művelet/mp, és nem befolyásolja a tranzakciós tárolóban kiépített átviteli sebességet. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Séma frissítéseinek automatikus kezelése

Azure Cosmos DB tranzakciós tároló a séma-agnosztikus, és lehetővé teszi, hogy a tranzakciós alkalmazásait a séma vagy az indexelés kezelése nélkül lehessen megismételni. Ezzel szemben Azure Cosmos DB Analytical Store sematikus az analitikai lekérdezési teljesítmény optimalizálása érdekében. Az automatikus szinkronizálási képességgel Azure Cosmos DB felügyeli a sémát a tranzakciós tároló legújabb frissítésein.  Emellett a séma-ábrázolást is kezeli az analitikus tárolóban, amely magában foglalja a beágyazott adattípusok kezelését.

A séma fejlődése során a rendszer az új tulajdonságokat adja hozzá az idő múlásával, az analitikus tároló automatikusan egyesítő sémát jelenít meg a tranzakciós tároló összes korábbi sémája között.

Ha Azure Cosmos DB az összes operatív adattal jól definiált sémát követ az elemzéshez, akkor a séma automatikusan következtetni fog, és helyesen jelenik meg az analitikai tárolóban. Ha az elemzéshez használt, jól definiált sémát bizonyos elemek megszegik, a rendszer nem fogja tartalmazni az analitikai tárolóban. Ha az elemzési definícióhoz jól definiált séma miatt blokkolta a forgatókönyveket, küldje el a [Azure Cosmos db csapatának](mailto:cosmosdbsynapselink@microsoft.com)e-mail-címét.

Az elemzéshez jól definiált sémát a következő szempontok határozzák meg:

* Egy tulajdonságnak mindig ugyanaz a típusa több elemen belül

  * Például `{"a":123} {"a": "str"}` nem rendelkezik jól definiált sémával, mert `"a"` néha egy sztring, és néha egy szám. 
  
    Ebben az esetben az analitikai tároló regisztrálja az adattípust az adattípusként `“a”` `“a”` a tároló élettartamában lévő első előfordulási elemben. Azok az elemek, amelyekben a különböző adattípusok `“a”` nem fognak szerepelni az analitikus tárolóban.
  
    Ez az állapot nem vonatkozik a null tulajdonságokra. Például `{"a":123} {"a":null}` még mindig jól definiálva van.

* A tömbök típusának egyetlen ismétlődő típust kell tartalmaznia

  * Például `{"a": ["str",12]}` nem egy jól definiált séma, mert a tömb egész és sztring típusú kombinációt tartalmaz.

* A sémák beágyazási szintjének maximális száma 200, a maximális beágyazási mélység pedig 5

  * A legfelső szintű 201 tulajdonságokkal rendelkező elemek nem rendelkeznek jól definiált sémával.

  * A sémában ötnél több beágyazott szinttel rendelkező elemek nem rendelkeznek jól definiált sémával. Például: `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* A tulajdonságok nevei egyediek, ha a kis-és nagybetűket nem megkülönböztető módon hasonlítják össze

  * Például a következő elemek nem rendelkeznek jól definiált sémával `{"Name": "fred"} {"name": "john"}` – `"Name"` és `"name"` a kis-és nagybetűket nem megkülönböztető módon hasonlítják össze

### <a name="cost-effective-archival-of-historical-data"></a>A korábbi adatmennyiségek költséghatékony archiválása

Az adatbontás az adatok elkülönítését jelenti a különböző forgatókönyvekhez optimalizált tárolási infrastruktúra között. Ezzel javítva a végpontok közötti adatverem teljes teljesítményét és költséghatékonyságát. Az analitikai tárolóval a Azure Cosmos DB mostantól támogatja a tranzakciós tárolóból származó adatok automatikus leválasztását a különböző adatelrendezésekkel rendelkező analitikai tárolóba. A tranzakciós tárolóhoz képest a tárolási költségeket tekintve optimalizált analitikai tároló lehetővé teszi, hogy a korábbi elemzések során jóval nagyobb mennyiségű működési adat legyen megtartva.

Miután az analitikai tároló engedélyezve lett a tranzakciós munkaterhelések adatmegőrzési igényei alapján, beállíthatja, hogy a "tranzakciós tár élettartama (tranzakciós TTL)" tulajdonsága automatikusan törölje a rekordokat a tranzakciós tárolóból egy adott időszak után. Hasonlóképpen, az "analitikai tár élettartama (analitikai TTL)" lehetővé teszi az analitikus tárolóban megőrzött adatok életciklusának kezelését a tranzakciós tárolótól függetlenül. Az analitikai tár engedélyezésével és a TTL-tulajdonságok konfigurálásával zökkenőmentesen elvégezheti a két áruház adatmegőrzési időszakának meghatározását.

### <a name="global-distribution"></a>Globális terjesztés

Ha globálisan elosztott Azure Cosmos DB-fiókkal rendelkezik, a tárolóhoz tartozó analitikai tároló engedélyezése után a fiók minden régiójában elérhető lesz.  Az operatív adatai minden változása globálisan replikálódik minden régióban. Az elemzési lekérdezéseket hatékonyan futtathatja az adatai legközelebbi regionális példányán Azure Cosmos DBban.

### <a name="security"></a>Biztonság

Az analitikai tárolóval megegyező hitelesítés megegyezik egy adott adatbázis tranzakciós tárolójával. A hitelesítéshez használhat Master vagy csak olvasható kulcsokat is. A szinapszis Studióban használhatja a társított szolgáltatást, hogy megakadályozza a Azure Cosmos DB kulcsok beillesztését a Spark-jegyzetfüzetekben. A társított szolgáltatáshoz való hozzáférés mindenki számára elérhető, aki hozzáfér a munkaterülethez.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Több Azure szinapszis Analytics-futtatókörnyezet támogatása

Az analitikai tároló úgy van optimalizálva, hogy skálázhatóságot, rugalmasságot és teljesítményt nyújtson az analitikus számítási feladatokhoz, és ez nem függ a számítási futtatási időktől. A tárolási technológia önállóan felügyelhető, hogy manuális erőfeszítések nélkül optimalizálja az elemzési számítási feladatokat.

Az analitikai tárolási rendszer az analitikai számítási rendszerből való leválasztásával a Azure Cosmos DB analitikus tárolóban lévő adatok az Azure szinapszis Analytics által támogatott különböző elemzési futtatókörnyezetekkel párhuzamosan kérhetők le. Napjainkban a szinapszis Analytics a Apache Spark és az SQL Server nélküli kiszolgálót is támogatja Azure Cosmos DB analitikus tárolóval.

> [!NOTE]
> Az analitikai áruházból csak a szinapszis Analytics futtatási idején lehet olvasni. A tranzakciós tárolóba visszaírhatja az adatait a kiszolgálóként szolgáló rétegként.

## <a name="pricing"></a><a id="analytical-store-pricing"></a>Árképzési

Az analitikus tároló egy fogyasztáson alapuló díjszabási modellt követ, amelyben a következőkért kell fizetnie:

* Tárolás: az analitikus tárolóban megőrzött adatmennyiség, beleértve az analitikus TTL által meghatározott múltbeli adatmennyiséget.

* Analitikus írási műveletek: az operatív adatok frissítéseinek teljes körű kezelése a tranzakciós tárolóból az analitikai tárolóba (automatikus szinkronizálás)

* Analitikai olvasási műveletek: az analitikai tárolón végrehajtott olvasási műveletek a szinapszis Analytics Spark és az SQL Server nélküli futtatási időpontokban.

> [!NOTE]
> A Azure Cosmos DB Analytical Store ingyenesen elérhető nyilvános előzetes kiadásban, 2020. augusztus 30-ig díjmentes.

Az analitikus tároló díjszabása nem azonos a tranzakciós áruház díjszabási modelljével. Az analitikai tár nem rendelkezik kiépített RUs-koncepcióval. Tekintse meg a [Azure Cosmos db díjszabási oldalát](https://azure.microsoft.com/pricing/details/cosmos-db/), amely részletesen ismerteti az analitikus tároló díjszabási modelljét.

Ahhoz, hogy egy Azure Cosmos DB-tárolón az analitikai tárolót engedélyező magas szintű költségbecslés legyen elérhető, használhatja a [Azure Cosmos db Capacity Plannert](https://cosmos.azure.com/capacitycalculator/) , és megbecsülheti az analitikai tárolási és írási műveletek költségeit. Az analitikus olvasási műveletek költségei az elemzési számítási feladatok jellemzőitől függenek, de magas szintű becslésként az analitikai tárolóban található 1 TB-os adatok vizsgálata általában 130 000 analitikai olvasási műveletet eredményez, és $0,065-os költséget eredményez.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a>Analitikus élettartam (TTL)

Az elemzési élettartam jelzi, hogy mennyi ideig kell megőrizni az adott tároló adatait az elemzési tárban. 

A rendszer automatikusan szinkronizálja az operatív adatok beszúrását, frissítését és törlését a tranzakciós tárolóból az analitikai tárolóba, a tranzakciós TTL-konfigurációtól függetlenül. A működési adatoknak az analitikai tárolóban való megőrzését a tároló szintjén található analitikai TTL-érték szabályozhatja az alább megadott módon:

A tárolók analitikai ÉLETTARTAMa a következő `AnalyticalStoreTimeToLiveInSeconds` tulajdonsággal van beállítva:

* Ha az érték "0", hiányzik (vagy NULL értékre van állítva): az analitikai tároló le van tiltva, és nem replikálódnak adatok a tranzakciós tárolóból az analitikai tárolóba

* Ha van, és az érték "-1" értékű: az analitikai tároló megőrzi az összes korábbi adatát, függetlenül a tranzakciós tárolóban lévő adatok megőrzésének. Ez a beállítás azt jelzi, hogy az analitikai tároló a működési adatok végtelen megőrzésével rendelkezik

* Ha van, és az érték egy "n" pozitív számra van beállítva: az elemek a tranzakciós tároló utolsó módosításának időpontját követően lejárnak az "n" analitikai tárolóban. Ez a beállítás akkor lehet kihasználható, ha az analitikus tárolóban korlátozott ideig szeretné megőrizni az operatív adatait, függetlenül a tranzakciós tárolóban lévő adatok megőrzésének.

Néhány megfontolandó szempont:
*   Miután az analitikai tár engedélyezve lett egy analitikai TTL-értékkel, később más érvényes értékre is frissítheti. 
*   Míg a tranzakciós TTL beállítható a tárolóban vagy az elem szintjén, az analitikai élettartam jelenleg csak a tároló szintjén állítható be.
*   A működési adatok megőrzését az analitikus tárolóban az analitikai TTL >= tranzakciós élettartam beállításával érheti el a tároló szintjén.
*   Az analitikai tár a tranzakciós tároló tükrözésére az analitikai TTL = tranzakciós TTL beállításával végezhető el.

További információ: [az analitikai élettartam konfigurálása egy tárolón](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>További lépések

További információért lásd a következő dokumentumokat:

* [Az Azure szinapszis hivatkozása Azure Cosmos DB](synapse-link.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használatának első lépései](configure-synapse-link.md)

* [Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban](synapse-link-frequently-asked-questions.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használati esetei](synapse-link-use-cases.md)
