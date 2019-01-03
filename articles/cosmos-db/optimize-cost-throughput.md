---
title: Az Azure Cosmos DB átviteli költségek optimalizálása
description: Ez a cikk ismerteti az Azure Cosmos DB-ben tárolt adatokat az átviteli sebesség a költségek optimalizálása érdekében.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: f0d0442a8640a75b21e95e3ae024fd7994602b51
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807941"
---
# <a name="optimizing-throughput-cost-in-azure-cosmos-db"></a>Az Azure Cosmos DB átviteli költségek optimalizálása

Által kiosztott átviteli sebesség modellt kínál, az Azure Cosmos DB bármilyen méretben kiszámítható teljesítményt kínál. Lefoglalását, vagy időben átviteli kiépítése szüntetheti meg "zajos szomszédok" a teljesítményét. Átviteli sebességért pontos mennyisége ad meg, és az Azure Cosmos DB garantálja a konfigurált átviteli sebességgel szavatolja.

A minimális átviteli sebesség 400 RU/s kezdődhet, és akár tízezer másodpercenként, vagy még nagyobb kérések méretezheti. Minden kérelmet, az Azure Cosmos-tároló vagy adatbázisban, például egy olvasási kérést, az írási kérelmek, a lekérdezési kérés, a tárolt eljárások kiadni a kiosztott átviteli sebesség kell vonni megfelelő költségekkel rendelkezik. 400 RU/s üzembe helyezése és kiadása egy lekérdezést, amely 40 csökkenti a költségeket, ha tudják, másodpercenként 10 ilyen lekérdezések kiadására. Ezen felüli méretezhetőséget kérelmet kap sebessége korlátozott, és ismételje meg a kérelmet. Ügyfél-illesztőprogramok használja, hogy támogatja-e az automatikus újrapróbálkozási logikát.

Akkor is kioszthatja az átviteli sebességet adatbázisok vagy tárolók és minden egyes stratégia segíthet a forgatókönyvtől függően költséget takaríthat meg.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Átviteli sebesség szinten kiépítésével optimalizálása

* Ha, kioszthatja az átviteli sebességet egy adatbázist, a tárolók, például gyűjtemények/táblák/gráfok adatbázison belüli megoszthatja az átviteli sebességet a terhelés alapján. Az adatbázis szintjén számára fenntartott átviteli sebesség egyenetlenül, megosztott tárolók egy adott csoportján függően.

* Ha üzembe helyezi az átviteli sebességet egy tárolón, az átviteli sebességet garantáltan a tároló a szavatolja. Egy logikai partíciós kulcs, amely létfontosságú a terhelés elosztását több tároló összes logikai partíciót. Lásd: [particionálás](partitioning-overview.md) és [horizontális skálázást](partition-data.md) cikkekben további részletekért.

Az alábbiakban néhány kiosztott átviteli sebesség stratégia vonatkozó irányelveket:

**Érdemes a kiépítés egy Azure Cosmos DB-adatbázist (a tárolók készletét tartalmazó) átviteli sebességet, ha**:

1. Néhány tucat Azure Cosmos-tárolók rendelkezik, és meg szeretné osztani az átviteli sebesség néhányat vagy mindegyiket között. 

2. Egy egybérlős adatbázis futtatható IaaS által üzemeltetett virtuális gépek vagy a helyszínen, például az nosql-alapú vagy a relációs adatbázisok az Azure Cosmos DB-ről végez áttelepítést. Ha sok gyűjtemények/táblák/diagramok és, és nem szeretne végezze el a módosításokat az adatmodellbe. Vegye figyelembe, hogy szükség lehet a veszélyezteti, ha nem frissíti az adatmodellben egy helyszíni adatbázisból való migrálás során, az Azure Cosmos DB által kínált előnyöket nyújtja. Javasoljuk, hogy Ön mindig reaccess az adatmodellben, a teljesítmény tekintetében a legtöbbet és a költségek optimalizálása érdekében. 

3. Nem tervezett kiugrások hez készletezett átviteli sebesség az adatbázis szintjén váratlan megnövekedett számítási feladat alá tartozó számítási feladatok számára szeretné. 

4. Beállítás adott átviteli sebességet különálló tárolókat, helyett az összesített átviteli sebesség lekérdezése az adatbázisban lévő tárolók több konkrét.

**Vegye figyelembe a kiépítés tároló átviteli sebességet, ha:**

1. Van néhány Azure-Cosmos-tárolókat. Mivel az Azure Cosmos DB sémafüggetlen, egy tároló elemek, amelyek heterogén sémákkal rendelkeznek, és az ügyfeleknek nem kell több tároló típusok, egyet az egyes entitásokhoz is tartalmazhat. Mindig fontolja meg, ha a csoportosítás külön tegyük fel, hogy a 10 – 20 tárolók egy egyetlen tárolóba van értelme. Minimális tárolók egy 400-ra csökkenti, az összes 10 – 20 tárolót készletezési egy lehet költséghatékonyabb. 

2. Szeretné szabályozhatja az átviteli sebességet egy adott tárolón, és a garantált átviteli sebesség lekérdezése egy adott tárolón szavatolja.

**Fontolja meg egy hibrid megoldás a fenti két stratégia részeként:**

1. Ahogy korábban említettük, az Azure Cosmos DB teszi lehetővé a keverheti a fenti két stratégia szerint, így most néhány Azure Cosmos-adatbázis, amely a az adatbázis, valamint egyes tárolók ugyanabban az adatbázisban a létesített átviteli sebesség megoszthatja tárolókra , mennyiségű kiosztott átviteli sebesség dedikált amely. 

2. A fenti stratégiák, így kapja meg a hibrid configuration mindkét adatbázis szintű kiosztott átviteli sebesség a dedikált átviteli sebességet egyes tárolók esetében is alkalmazható.

API-t, a kiválasztott függően az alábbi táblázatban látható módon telepíthet másik granularitással sebességet.

|API|A **megosztott** átviteli sebességgel konfigurálása |A **dedikált** átviteli sebességgel konfigurálása |
|----|----|----|
|SQL API|Adatbázis|Tároló|
|Az Azure Cosmos DB MongoDB API-jaival|Adatbázis|Gyűjtemény|
|Cassandra API|Kulcstér|Tábla|
|Gremlin API|Adatbázisfiók|Graph|
|Tábla API|Adatbázisfiók|Tábla|

A kiépítési átviteli sebesség szinten optimalizálhatja a költségeket a számítási feladatok jellemzői alapján. Ahogy korábban említettük, is programozott módon, ideje növelése vagy csökkentése a kiosztott átviteli sebesség vagy egyéni tároló(k) vagy együttesen illenek különböző tárolók. Rugalmasan méretezést átviteli sebességet a számítási feladatok változásainak megfelelően, által a feladatonként átviteli sebesség a konfigurált. Ha a tároló vagy egy tárolók van elosztva több régióban, majd az átviteli sebességet a konfigurálja a tároló vagy tárolók készletét garantáltan minden régióban elérhetővé.

## <a name="optimize-with-rate-limiting-your-requests"></a>A sebességhatárolt a kérelmek optimalizálása

A számítási feladatokhoz, amelyek nem a késésre érzékeny oszthatnak ki átviteli kapacitásokat kisebb, és lehetővé teszi az alkalmazás kezelni a sebességhatárolt a tényleges adatátviteli sebessége meghaladja a kiosztott átviteli sebesség. A kiszolgáló előrelátó módon RequestRateTooLarge (HTTP-állapotkódot 429-es) a kérelem befejezése, és adja vissza a `x-ms-retry-after-ms` jelző idő ezredmásodpercben, amely a felhasználónak várakoznia kell, mielőtt újra próbálkozna a kérelem fejlécében. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Újrapróbálkozási logika az SDK-k 

A natív SDK-k (.NET/.NET Core, Java, Node.js és Python) implicit módon ezt a választ a tényleges, a kiszolgáló által megadott retry-after fejléccel tiszteletben, és ismételje meg a kérelmet. A fiók egyidejűleg több ügyfél által elérhető, kivéve a következő újrapróbálkozás sikeres lesz.

Ha egynél több ügyfél összesítve van folyamatosan működő fent a kérések aránya az alapértelmezett újrapróbálkozások jelenleg, amely a jelenleg beállított 9 bot elegendő lehet. Ebben az esetben az ügyfél jelez egy `DocumentClientException` állapotú code 429-es az alkalmazáshoz. Az alapértelmezett újrapróbálkozások beállításával módosítható a `RetryOptions` ConnectionPolicy-példányon. Alapértelmezés szerint a 429-es állapotkód DocumentClientException adja vissza egy halmozódó várakozási idő pedig 30 másodperc után, ha a kérés továbbra is működőképes fent a kérések aránya. Ez akkor fordul elő, még ha az aktuális újrapróbálkozások nem éri el az újrapróbálkozások maximális számát, legyen az alapértelmezett 9 vagy egy felhasználó által definiált értéket. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryAtte) értéke 3, így ebben az esetben, ha egy kérés művelet meghaladja a fenntartott átviteli sebesség ahhoz a gyűjteményhez, korlátozza a sebességét a kért művelet újrapróbálkozik háromszor kivétel előtt a Kivétel történt az alkalmazáshoz.  [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) 60 értékre van állítva, tehát ebben az esetben, ha az összesített újrapróbálkozási ideig várakozik az első óta eltelt másodpercek alatt kérelem meghaladja a 60 másodperc, a kivétel történt.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 

connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 

connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Particionálási stratégia és a létesített átviteli sebesség költségek

Az Azure Cosmos DB a költségek optimalizálása érdekében fontos a jó particionálási stratégia. Győződjön meg arról, hogy nincs nem torzulása partíció, amely tármetrikák keresztül érhetők el. Győződjön meg arról, hogy van egy partíció, amely ki van téve az átviteli sebességre vonatkozó mérőszámok átviteli nem döntés. Győződjön meg arról, hogy nincs nem torzulása különleges partíciókulcsok felé. Domináns kulcsok tárolási metrikák keresztül érhetők el, de a kulcs lesz az alkalmazás-hozzáférési mintájában függ. A legcélszerűbb gondolja át a megfelelő logikai partíciókulcs. Remek partíciókulcs várhatóan a következő jellemzőkkel rendelkeznek:

* Válassza ki, a számítási feladatok egyenletes összes partíciójára és egyenletesen idővel partíciókulcsot. Más szóval a kulcsok nem tartalmazhat többsége az adatok és néhány kevésbé vagy egyáltalán nem adatokkal együtt. 

* Válassza ki a partíciós kulcs, amely lehetővé teszi, hogy hozzáférési minták egyenletesen oszlanak meg logikai partíciók között. A számítási feladatok még ésszerűen minden kulcs esetében. Más szóval a számítási feladatok többsége nem kell összpontosítania néhány konkrét kulcsokat. 

* Válasszon egy partíciókulcsot, amelyen számos különféle értékeket. 

Alapvető, hogy az adatok és a tárolót a tevékenység elosztva a logikai partíció készletét, így erőforrásait az adattárolás és átviteli sebesség a logikai partíció szét lehetnek osztva. A partíciókulcsok-kompatibilis tartalmazhatják a tulajdonságokat, amelyeket gyakran jelennek meg a lekérdezéseket szűrőként. Lekérdezések a partíciókulcs a szűrőpredikátumban fel hatékonyan továbbíthatók. Ez a particionálási stratégia, a kiosztott átviteli sebesség optimalizálásával lesz sokkal egyszerűbb. 

### <a name="design-smaller-items-for-higher-throughput"></a>A nagyobb sebesség érdekében kisebb elemek tervezése 

A kérelem díja vagy egy adott művelethez a kérelem feldolgozása költsége közvetlenül visszamenőleges korrelációban állnak a cikkhez méretével. Műveletek nagy elemek fog drágább, mint a kisebb elemek műveleteket. 

## <a name="data-access-patterns"></a>Adathozzáférési mintáinak 

Mindig célszerű logikai szétválasztására az adatok logikai kategóriákba alapján, hogy milyen gyakran fér hozzá az adatokhoz. Kategorizálásához, mint a gyakran használt adatok, közepes vagy ritkán használt adatok finomhangolhatja, felhasznált tárterületen és a szükséges átviteli sebességtől. Attól függően, a hozzáférés gyakorisága helyezze el az adatokat (például táblákat, diagramok és gyűjtemények) külön tárolókba, és finomhangolása a kiosztott átviteli sebesség őket az adatok adott szegmens igényeinek megfelelően. 

Továbbá ha az Azure Cosmos DB használ, és tudja, nem fogják keresni egyes adatok értékei szerint, vagy csak ritkán érik el őket, tárolja ezeknek az attribútumoknak tömörített értékeit. Ezzel a módszerrel a tárolóhely, index lemezterület és a kiosztott átviteli sebesség mentse, és alacsonyabb költségeket eredményez.

## <a name="optimize-by-changing-indexing-policy"></a>Indexelési házirend módosításával optimalizálása 

Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli az összes rekord minden egyes tulajdonság. Ennek célja, hogy a fejlesztés megkönnyítése érdekében és ad hoc jellegű lekérdezésekkel sok különböző típusú kiváló teljesítmény biztosítása érdekében. Ha több ezer tulajdonságok nagy méretű rekordok, és az átviteli sebesség költség indexelő minden egyes tulajdonság nem lehet hasznos, különösen akkor, ha a 10-es vagy 20. Ezek a tulajdonságok csak lekérdezni. Növekedésével közelebb első leírót a adott számítási feladatra, ügyfeleinket, az index szabályzat finomhangolásához. Részletes információk az Azure Cosmos DB indexelési szabályzat található [Itt](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Figyelési kiépített és felhasznált átviteli sebesség 

Figyelheti a kiosztott Kérelemegységek teljes száma, sebessége korlátozott kérések száma, valamint az Azure Portalon felhasznált Kérelemegységek számát. Az alábbi képen egy példa a használati metrika:

![Az Azure Portalon kérelemegység figyelése](./media/optimize-cost-throughput/monitoring.png)

Beállíthat riasztásokat ellenőrizheti, ha a kérések sebessége korlátozott száma meghalad egy adott küszöbértéket. Lásd: [figyelése az Azure Cosmos DB](use-metrics.md) további részleteivel. Ezek a riasztások e-mailt küld a rendszergazdák vagy a hívja az egyéni HTTP-Webhook vagy az Azure-függvény automatikusan a kiosztott átviteli sebesség növelése érdekében. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Rugalmasan méretezheti az átviteli sebességet és igény szerinti 

Díjköteles az átviteli sebesség kiosztott részéért, mivel a kiosztott átviteli sebesség az igényeknek megfelelő segíthetnek elkerülni a fel nem használt átviteli sebesség díja. Méretezheti a kiosztott átviteli sebesség felfelé vagy lefelé bármikor, igény szerint.  

* A Kérelemegységek fogyasztását és sebessége korlátozott kérelmek aránya felfedheti az, hogy nem kell tartani a kiépített egész állandót a napot vagy a hét során. Forgalom éjszakánként és hétvégén a jelenhet meg. Az Azure portal vagy a Azure Cosmos DB natív SDK-k vagy a REST API használatával a kiosztott átviteli sebesség bármikor skálázhatja. Az Azure Cosmos DB REST API-val programozott módon frissíteni a tárolókat, így módosíthatja az átviteli sebesség a kódot, vagy a hét napját idejétől függően egyszerű teljesítményszintjének végpontok biztosít. A művelet állásidő nélkül történik, és általában kevesebb mint egy percet akkor lépnek érvénybe. 

* Egy horizontális területek átviteli sebesség, amikor Ön betölteni az adatokat az Azure Cosmos DB, például adatok migrálása során. Miután befejezte az áttelepítést, vertikális kiosztott átviteli sebesség kezelésére a megoldás stabil állapotot.  

* Ne feledje, a számlázás egy órás részletességgel van, ezért nem menteni fogja lemondja a kiosztott átviteli sebesség nagyobb gyakorisággal egyszerre egy óránál módosításakor.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Az új számítási feladatok szükséges átviteli meghatározása 

Annak megállapításához, a kiosztott átviteli sebesség egy új munkaterhelések esetében, használhatja az alábbi lépéseket: 

1. Hajtsa végre a capacity planner használata kezdeti, a nyers próbaverzióra, és állítsa be az Azure Cosmos-Explorer az Azure Portal segítségével. a becslést. 

2. Azt javasoljuk, hogy a tárolók létrehozásához a vártnál magasabb átviteli sebesség és a majd vertikális leskálázást, igény szerint. 

3. Azt javasoljuk, hogy ha kérések sebessége korlátozott, automatikus újrapróbálkozások kihasználhatják a natív Azure Cosmos DB SDK-k valamelyikével. Dolgozunk a platform által nem támogatott, és a Cosmos DB REST API használata, ha végrehajtja a saját újrapróbálkozási házirend használatával a `x-ms-retry-after-ms` fejléc. 

4. Győződjön meg arról, hogy az alkalmazás kódjában szabályosan támogatja az esetben, ha az összes újrapróbálkozás sikertelen. 

5. Az Azure Portalon, hogy kérjen értesítést a sebességkorlátozás riasztásokat lehet konfigurálni. Kezdje rendelkező konzervatív például 10 sebessége korlátozott kérést az elmúlt 15 perc és kapcsolót, hogy több eager szabályok, döntse el, a tényleges használat után. Alkalmanként sebességhatárok is jól működik, azok megjelenítése, hogy játssza le a beállított, és ez pontosan mit szeretne tenni. 

6. Figyelés segítségével megismerheti a forgalmi minták, így érdemes lehet dinamikusan állítsa be az átviteli sebesség kiépítése során a naponta vagy hetente kell. 

7. Ellenőrizze, hogy nem szükséges több tárolók és adatbázisok kiépítése érdekében rendszeresen felhasznált átviteli sebesség arány és a kiépített figyelésére. Egy kicsit keresztül kiosztott átviteli sebesség, akkor egy jó biztonsági ellenőrzést.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Ajánlott eljárások a kiosztott átviteli sebesség optimalizálása 

A következő lépések segítenek, hogy a megoldások hatékonyan méretezhető és költséghatékony Azure Cosmos DB használata esetén.  

1. Ha jelentősen keresztül kiosztott átviteli sebesség tárolók és adatbázisok közötti, ekkor a felülvizsgálandó RUs kiépített Vs felhasznált kérelemegység és finomhangolása a számítási feladatok.  

2. Egy fenntartott adattovábbítási kapacitással, az alkalmazás számára szükséges mennyiségű becslése módja jegyezze fel a kérés RU egységek használata után társított jellemző műveleteket futtat egy reprezentatív Azure Cosmos-tároló vagy az alkalmazása által használt adatbázis és a Ezután becsülje meg a másodpercenként végrehajtásához várhatóan műveletek száma. Mindenképpen mérni, és lekérdezéseket szokásos és a használatuk is. Megtudhatja, hogyan költségbecslést RU-lekérdezések programozás útján vagy a portál lásd: használatával [lekérdezések költségeinek optimalizálása](online-backup-and-restore.md). 

3. Műveletek és a költségek lekérése a RUs másik módja, a Log Analytics, mivel ez azt a áttekintését művelet/időtartam és a kérelem díja engedélyezésével. Az Azure Cosmos DB kérelem ingyenesen biztosít a minden művelet, így minden művelet díj tárolt visszaküldi a választ, és ezután elemzési célokra. 

4. Rugalmasan méretezheti felfelé a kiosztott átviteli sebesség és a számítási feladat igényeinek megfelelően igény szerint. 

5. Adja hozzá, és távolítsa el a költségek csökkentését és az Azure Cosmos-fiók társított régiók. 

6. Ellenőrizze, hogy az adatok és a számítási feladatok elosztását a tárolók logikai partíciók között van. Ha egyenetlen partíció terjesztési, emiatt előfordulhat, hogy nagyobb mennyiségű átviteli sebesség, mint a szükséges érték kiépítéséhez. Ha azonosítani, hogy rendelkezik-e eloszlással, javasoljuk, hogy a partíciók közt egyenletesen verziójának terjesztése a számítási feladatok vagy újraparticionálni az adatokat. 

7. Ha több tároló van, és ezek a tárolók nem szükséges SLA-k, használhatja az esetekben az adatbázis-alapú ajánlat, a tároló sebességet SLA-k nem érvényesek. Meg kell határoznia, amely az Azure Cosmos tárolókat az adatbázis-szintű átviteli sebessége a migrálni kívánt kínálnak, és utána telepítse át ezeket a módosítási hírcsatorna-alapú megoldás használatával. 

8. Fontolja meg a "Cosmos DB ingyenes szinten" (ingyenes, egyéves), a Cosmos DB kipróbálása (legfeljebb három régió) vagy a letölthető Cosmos DB emulatort használatát a fejlesztési és tesztelési célra. Ezek a beállítások használatával tesztelési-fejlesztési, jelentősen csökkentheti a költségeket.  

9. Munkaterhelés-specifikus költségek optimalizálása – például további hajthat végre, növelje a batch-méretet, a terheléselosztás olvasási több régióban, és vonja vissza adatokat, duplikálásához, ha van ilyen.

10. Az Azure Cosmos DB szolgáltatás számára fenntartott kapacitás jelentős kedvezményeket vehet igénybe kaphat akár 65 %-át három évig. Az Azure Cosmos DB tartalékkapacitást modellben egy előzetes kötelezettségvállalás szükséges idővel kérelemegységet. A kedvezmény számítógépen rétegzett úgy, hogy a további kérelemegység használja egy hosszabb időtartamon belül, a további, a kedvezménnyel lesz. Ezeket a kedvezményeket a rendszer azonnal alkalmazza. Bármely meghaladja a kiosztott értékeket használt fenntartott egységek nem fenntartott kapacitás költsége alapján lesznek kiszámlázva. Lásd: [Cosmos DB szolgáltatás számára fenntartott kapacitás](cosmos-db-reserved-capacity.md)) további részletekért. Vegye figyelembe, hogy tovább csökkentheti a kiosztott átviteli sebesség költségeket a szolgáltatás számára fenntartott kapacitás vásárlása.  

## <a name="next-steps"></a>További lépések

Ezután folytassa további tudnivalók a költségek optimalizálása az Azure Cosmos DB az alábbi cikkeket:

* Tudjon meg többet [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [az Azure Cosmos DB-elszámolások ismertetése](understand-your-bill.md)
* Tudjon meg többet [tárolási költségek optimalizálása](optimize-cost-storage.md)
* Tudjon meg többet [olvasási és írási a költségek optimalizálása](optimize-cost-reads-writes.md)
* Tudjon meg többet [lekérdezések költségeinek optimalizálása](optimize-cost-queries.md)
* Tudjon meg többet [többrégiós Azure Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md)

