---
title: Átviteli költség optimalizálása az Azure Cosmos DB-ben
description: Ez a cikk bemutatja, hogyan optimalizálhatja az Azure Cosmos DB-ben tárolt adatok átviteli költségek optimalizálása.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: c80ab4acd745717e2e68ae7d9dc818594ad1ce9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501465"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben

A kiépített átviteli modell biztosításával az Azure Cosmos DB kiszámítható teljesítményt kínál bármilyen méretekben. A teljesítmény lefoglalása vagy kiépítése idő előtt kiküszöböli a "zajos szomszéd hatást" a teljesítményre. Adja meg a szükséges átviteli szintet, és az Azure Cosmos DB garantálja a konfigurált átviteli, SLA által támogatott.

Kezdheti legalább 400 RU/s átviteli fokkal, és másodpercenként több tízmillió kérelemre vagy még többre skálázható. Minden kérelem az Azure Cosmos-tároló vagy -adatbázis, például egy olvasási kérelem, írási kérelem, lekérdezési kérelem, tárolt eljárások megfelelő költség, amely le van vonva a kiosztott átviteli. Ha 400 RU/s-t létesít, és 40 több mint egy e-kódba kerülő lekérdezést ad ki, másodpercenként 10 ilyen lekérdezést adhat ki. Minden olyan kérelem, amely azon túl, hogy lesz sebesség-korlátozott, és meg kell próbálnia a kérelmet. Ha ügyfél-illesztőprogramokat használ, támogatják az automatikus újrapróbálkozási logikát.

Az átviteli sebességet adatbázisok vagy tárolók között is kioszthatja, és a különféle stratégiákkal alacsonyabbak lehetnek a költségek a forgatókönyvtől függően.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optimalizálás az átviteli hang különböző szinteken történő kiépítésével

* Ha egy adatbázis átviteli terhelését, az összes tárolók, például gyűjtemények/táblák/grafikonok az adatbázison belül megoszthatja az átviteli terhelés alapján. Az adatbázis szintjén fenntartott átviteli terhelés egyenlőtlenül van megosztva, a tárolók adott készletének munkaterhelésétől függően.

* Ha egy tárolóátviteli rendszer kiépítése, az átviteli garantált az adott tároló, az SLA által támogatott. A logikai partíciókulcs kiválasztása elengedhetetlen a terhelés egyenletes elosztásához a tároló összes logikai partíciója között. További részletek: [Particionálás](partitioning-overview.md) és [horizontális skálázáscikkek.](partition-data.md)

A kiépített átviteli stratégia eldöntéséhez az alábbiakban néhány irányelv et kell ismertette:

**Fontolja meg az átviteli érték kiépítését egy Azure Cosmos-adatbázisban (amely tárolók készletét tartalmazza), ha:**

1. Néhány tucat Azure Cosmos-tárolóval rendelkezik, és szeretné megosztani az átviteli műveleteket néhány vagy az összes között. 

2. Egybérlős adatbázisból, amelyet iaaS-üzemeltetésű virtuális gépeken vagy helyszíni futtatásra terveztek, például NoSQL vagy relációs adatbázisokat az Azure Cosmos DB-be. És ha sok gyűjtemények / táblák / grafikonok, és nem szeretné, hogy bármilyen változás az adatmodell. Vegye figyelembe, hogy előfordulhat, hogy az Azure Cosmos DB által kínált előnyök egy részét kell veszélyeztetnie, ha nem frissíti az adatmodellt, amikor egy helyszíni adatbázisból migrál. Javasoljuk, hogy mindig újra hozzáférjen az adatmodellhez, hogy a legtöbbet hozhesse ki a teljesítmény szempontjából, és optimalizálja a költségeket. 

3. Szeretné felvenni a nem tervezett kiugrások a számítási feladatok alapján az adatbázis szintjén a munkaterhelés váratlan kiugrása a készletezési átviteli teljesítmény miatt. 

4. Ahelyett, hogy az egyes tárolók adott átviteli, érdekel, hogy az összesített átviteli az adatbázison belüli tárolók egy készletén keresztül.

**Fontolja meg az átviteli átbocsátást egy adott tárolón, ha:**

1. Néhány Azure Cosmos-tárolóval rendelkezik. Mivel az Azure Cosmos DB séma-független, egy tároló tartalmazhat olyan elemeket, amelyek heterogén sémákat tartalmaznak, és nem követeli meg az ügyfelektől, hogy több tárolótípust hozzanak létre, minden entitáshoz egyet. Ez mindig egy lehetőség, hogy fontolja meg, ha csoportosítása külön mondjuk 10-20 konténerek egyetlen tartályba van értelme. A konténerekre vonatkozó minimum 400 több rúg esetén az összes 10–20 tároló összevonása költséghatékonyabb lehet. 

2. Szeretné szabályozni az átviteli egy adott tárolón, és kap a garantált átviteli egy adott tároló sla által támogatott.

**Tekintsük a hibrid a fenti két stratégia:**

1. Ahogy korábban említettük, az Azure Cosmos DB lehetővé teszi a fenti két stratégia keverését és egyeztetését, így most már rendelkezhet néhány tárolóval az Azure Cosmos-adatbázisban, amelyek megoszthatják az adatbázisban kiosztott átviteli szintet, valamint az ugyanazon az adatbázison belüli egyes tárolókat , amely nek célzott mennyiségű kiosztott átviteli. 

2. A fenti stratégiákat alkalmazhatja egy hibrid konfiguráció kialakításához, ahol mindkét adatbázisszintű kiosztott átviteli mivel rendelkezik egyes tárolók dedikált átviteli mivel.

Ahogy az alábbi táblázatban látható, az API-választástól függően különböző részletességű átviteli sebességgel is kiépítheti az átviteli szintet.

|API|**A megosztott** átviteli |A **dedikált** átviteli |
|----|----|----|
|SQL API|Adatbázis|Tároló|
|MongoDB-hez készült Azure Cosmos DB API|Adatbázis|Gyűjtemény|
|Cassandra API|Kulcstér|Tábla|
|Gremlin API|Adatbázisfiók|Graph|
|Tábla API|Adatbázisfiók|Tábla|

Az átviteli képesség különböző szinteken történő kiépítésével optimalizálhatja a költségeket a számítási feladatok jellemzői alapján. Ahogy korábban említettük, programozottan és bármikor növelheti vagy csökkentheti a kiosztott átviteli-átviteli egy tároló(k) vagy együttesen egy tárolókészleten keresztül. Rugalmasskálázási átviteli terhelés, a számítási feladatok változása, csak akkor fizet az átviteli, konfigurált. Ha a tároló vagy a tárolók több régióban oszlik meg, majd a tárolón vagy tárolók készletén konfigurált átviteli hang garantáltan elérhető vé válik az összes régióban.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optimalizálás a kérések sebességkorlátozásával

A számítási feladatok, amelyek nem érzékenyek a késésre, kevesebb átviteli sebességet építhet ki, és hagyja, hogy az alkalmazás kezelje a sebességkorlátozást, ha a tényleges átviteli sebesség meghaladja a kiosztott átviteli sebességet. A kiszolgáló megelőzőjelleggel befejezi `RequestRateTooLarge` a kérést (429-es `x-ms-retry-after-ms` HTTP-állapotkód), és visszaadja a fejlécet, jelezve, hogy a felhasználónak mennyi időt kell várnia a kérelem újbóli megkísérlése előtt. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Újrapróbálkozási logika az SDK-kban 

A natív SDK-k (.NET/.NET Core, Java, Node.js és Python) implicit módon elkapják ezt a választ, tiszteletben tartják a kiszolgáló által megadott újrapróbálkozási fejlécet, és újrapróbálkoznak a kéréssel. Ha csak akkor éri el fiókját, ha több ügyfél egyidejűleg fér hozzá, a következő újrapróbálkozás sikeres lesz.

Ha egynél több ügyfél összesítve működik következetesen meghaladja a kérelem aránya, az alapértelmezett újrapróbálkozások száma, amely jelenleg 9, nem elegendő. Ilyen esetekben az ügyfél `RequestRateTooLargeException` egy 429-es állapotkódot ad az alkalmazásnak. Az alapértelmezett újrapróbálkozások száma a `RetryOptions` ConnectionPolicy példány beállításával módosítható. Alapértelmezés szerint `RequestRateTooLargeException` a 429-es állapotkóddal rendelkező konkumulatív várakozási idő 30 másodperc után kerül vissza, ha a kérelem továbbra is a kérelemsebesség felett működik. Ez akkor is előfordul, ha az aktuális újrapróbálkozások száma kisebb, mint a maximális újrapróbálkozások száma, legyen az az alapértelmezett 9 vagy egy felhasználó által definiált érték. 

[A MaxRetryAttemptsOnThrottledRequests beállítása](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet) 3, így ebben az esetben, ha egy kérelemművelet a tároló számára fenntartott átviteli sebesség túllépésével korlátozott, a kérelemművelet háromszor újrapróbálkozik, mielőtt eldobnák a kivételt az alkalmazás alól. [A MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) értéke 60, így ebben az esetben, ha az összesítő újrapróbálkozási várakozási idő másodpercben, mivel az első kérelem meghaladja a 60 másodpercet, a kivétel megjelenik.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>A particionálási stratégia és a kiosztott átviteli sebesség költségei

A jó particionálási stratégia fontos az Azure Cosmos DB költségeinek optimalizálásához. Győződjön meg arról, hogy nincs döntés a partíciók, amelyek a tárolási metrikákon keresztül elérhetővé. Győződjön meg arról, hogy nincs döntés az átviteli egy partíció, amely elérhető az átviteli metrikák. Győződjön meg arról, hogy nincs ferde ség az adott partíciókulcsok felé. A tárolóban lévő domináns kulcsok metrikákon keresztül érhetők el, de a kulcs az alkalmazás hozzáférési mintájától függ. A legjobb, ha a megfelelő logikai partíciókulcsra gondol. Egy jó partíciókulcs nak a következő jellemzőkkel kell rendelkeznie:

* Válasszon egy partíciókulcsot, amely egyenletesen osztja el a számítási feladatokat az összes partícióközött, és egyenletesen az idő múlásával. Más szóval, nem kell néhány kulcsot az adatok többsége és néhány kulcsok kevesebb vagy nincs adat. 

* Válasszon egy partíciókulcsot, amely lehetővé teszi a hozzáférési minták egyenletes eloszlását a logikai partíciók között. A munkaterhelés ésszerűen az összes kulcsközött is. Más szóval a munkaterhelés nagy részét nem kell összpontosítani néhány konkrét kulcsokat. 

* Válasszon olyan partíciókulcsot, amely értékek széles skáláját kínálja. 

Az alapötlet az adatok és a tevékenység terjesztése a tárolóban a logikai partíciók készletében, hogy az adattároláshoz és átviteli kapacitáshoz szükséges erőforrások a logikai partíciók között legyenek elosztva. A partíciókulcsokra jelöltek tartalmazhatnak olyan tulajdonságokat, amelyek gyakran jelennek meg szűrőként a lekérdezésekben. A lekérdezések hatékonyan irányíthatók a partíciókulcs nak a szűrőpredikátumba való belefoglalásával. Egy ilyen particionálási stratégia, a kiosztott átviteli hang optimalizálása sokkal könnyebb lesz. 

### <a name="design-smaller-items-for-higher-throughput"></a>Kisebb elemek tervezése a nagyobb átmenő bb átmenő kontra 

A kérelem díj vagy a kérelem feldolgozási költsége egy adott művelet közvetlenül korrelál a cikk méretét. A nagy cikkeken végzett műveletek többe kerülnek, mint a kisebb cikkeken végzett műveletek. 

## <a name="data-access-patterns"></a>Adatelérési minták 

Mindig jó gyakorlat, hogy logikailag elkülönítse az adatokat logikai kategóriákba az adatok elérésének milyen gyakran. Ha forró, közepes vagy lehűtési adatokként kategorizálja, finomíthatja a felhasznált tárolót és a szükséges átviteli forgalmat. A hozzáférés gyakoriságától függően az adatokat külön tárolókba (például táblákba, grafikonokba és gyűjteményekbe) helyezheti el, és finomíthatja a kiosztott átviteli szintet, hogy megfeleljen az adott adatszegmens igényeinek. 

Továbbá ha az Azure Cosmos DB-t használja, és tudja, hogy nem fog bizonyos adatértékek alapján keresni, vagy ritkán fog hozzáférni, tárolja ezeknek az attribútumoknak a tömörített értékeit. Ezzel a módszerrel tárhelyet, indexterületet és kiosztott átviteli kapacitást takaríthat meg, és alacsonyabb költségeket eredményezhet.

## <a name="optimize-by-changing-indexing-policy"></a>Optimalizálás indexelési házirend módosításával 

Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli minden rekord minden tulajdonságát. Ennek célja a fejlesztés megkönnyítése és a kiváló teljesítmény biztosítása számos különböző típusú ad hoc lekérdezések. Ha több ezer tulajdonsággal rendelkező nagy rekordokkal rendelkezik, előfordulhat, hogy nem hasznos az átviteli költség kifizetése minden tulajdonság indexelése esetén, különösen akkor, ha csak 10 vagy 20 ilyen tulajdonságra kérdez. Ahogy egyre közelebb a szerzés egy fogantyút a konkrét számítási feladatok, a mi útmutatást, hogy az index házirend finomhangolása. Az Azure Cosmos DB indexelési szabályzatának részletes adatai [itt](indexing-policies.md)találhatók. 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Kiépített és felhasznált átviteli adatátatok figyelése 

Figyelheti a kiépített rúmiák teljes számát, a korlátozott díjú kérelmek számát, valamint az Azure Portalon felhasznált több felhasználói nem működő felhasználói nem működő fél szolgáltatásait. Az alábbi képen egy használati példa mérőszám látható:

![Kérésegységek figyelése az Azure Portalon](./media/optimize-cost-throughput/monitoring.png)

Riasztásokat is beállíthat annak ellenőrzésére, hogy a korlátozott sebességű kérelmek száma meghaladja-e az adott küszöbértéket. További részletekért [tekintse meg az Azure Cosmos DB-cikk figyelése](use-metrics.md) című témakört. Ezek a riasztások e-mailt küldhetnek a fiók rendszergazdáinak, vagy meghívhatnak egy egyéni HTTP Webhookot vagy egy Azure-függvényt a kiosztott átviteli kapcsolat automatikus növeléséhez. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Az átviteli mennyiség rugalmas és igény szerinti méretezése 

Mivel a kiosztott átviteli igény díja a kiosztott átviteli igény egyeztetése segíthet elkerülni a nem használt átviteli díjakat. A kiosztott átviteli átmenő bármikor felfelé vagy lefelé skálázhatja, szükség szerint. Ha az átviteli igény nagyon kiszámítható, használhatja az Azure Functions-t, és időzítőesemény-eseményindítóval [növelheti vagy csökkentheti az átviteli veszélyt az ütemezés szerint.](scale-on-schedule.md) 

* A felhasználói felhasználói nem ű szolgáltatások felhasználásának és a korlátozott sebességkorlátozott kérelmek aránya azt mutatják, hogy nem kell tartani a kiépített egész állandó egész nap vagy a hét folyamán. Lehet, hogy kevesebb forgalmat éjszaka vagy a hétvégén. Az Azure Portal vagy az Azure Cosmos DB natív SDK-k vagy REST API használatával bármikor skálázhatja a kiosztott átviteli. Az Azure Cosmos DB REST API-ja végpontokat biztosít a tárolók teljesítményszintjének programozott frissítéséhez, így a nap időpontjától vagy a hét napjától függően egyszerűen módosíthatja a kód átviteli teljesítményét. A művelet leállás nélkül történik, és általában kevesebb mint egy perc alatt lép érvénybe. 

* Az egyik terület, amelyet az átviteli kell, amikor adatokat az Azure Cosmos DB-be, például az adatok áttelepítése során. Miután befejezte az áttelepítést, skálázhatja a kiosztott átviteli átbocsátásle a megoldás állandósult állapotának kezeléséhez.  

* Ne feledje, hogy a számlázás egy órás részletességű, így nem takarít meg pénzt, ha a kiosztott átviteli forgalmat gyakrabban módosítja, mint egy óra egy időben.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Az új munkaterheléshez szükséges átviteli terhelés meghatározása 

Az új számítási feladatok kiosztott átviteli igényének meghatározásához a következő lépéseket használhatja: 

1. Végezzen el egy kezdeti, durva kiértékelést a kapacitástervező vel, és módosítsa a becsléseket az Azure Cosmos Explorer segítségével az Azure Portalon. 

2. Javasoljuk, hogy hozza létre a tárolók a vártnál nagyobb átviteli, és szükség szerint leskálázás. 

3. Javasoljuk, hogy a natív Azure Cosmos DB SDK-k egyikét használja az automatikus újrapróbálkozások előnyeinek kihasználásához, amikor a kérelmek díja korlátozott lesz. Ha olyan platformon dolgozik, amely nem támogatott, és a Cosmos DB REST API-ját használja, valósítsa meg saját újrapróbálkozási szabályzatát a `x-ms-retry-after-ms` fejléc használatával. 

4. Győződjön meg arról, hogy az alkalmazáskód szabályosan támogatja az esetet, ha az összes újrapróbálkozás sikertelen. 

5. Az Azure Portalon elérhető riasztások konfigurálásával értesítéseket kaphat a sebességkorlátozásról. Az elmúlt 15 percben konzervatív korlátokkal, például 10 korlátozott díjkéréssel kezdhet, és átválthat a lelkesebb szabályokra, ha kitalálja a tényleges fogyasztást. Az alkalmi sebességkorlátok rendben vannak, azt mutatják, hogy a beállított korlátokkal játszol, és pontosan ezt szeretnéd tenni. 

6. A figyelés segítségével megismerheti a forgalmi mintát, így figyelembe veheti, hogy dinamikusan kell módosítania az átviteli átviteli kiépítést a nap vagy egy hét alatt. 

7. Rendszeresen figyelje a kiosztott és a felhasznált átviteli arányt, hogy győződjön meg arról, hogy nem létesített ki a szükséges nél több tárolót és adatbázist. Miután egy kicsit túlegy kiosztott átviteli egy jó biztonsági ellenőrzés.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Ajánlott eljárások a kiosztott átviteli hang optimalizálásához 

Az alábbi lépések segítségével rendkívül méretezhetővé és költséghatékonysá teheti megoldásait az Azure Cosmos DB használata esetén.  

1. Ha jelentősen túlépített átviteli a tárolók és adatbázisok között, tekintse át a kiépített vt-k felhasznált rendszerszámos, és finomítsa a számítási feladatokat.  

2. Az alkalmazás által igényelt fenntartott átviteli igény becsült mennyiségének becslésére az alkalmazás által igényelt fenntartott átviteli terhelés az alkalmazás által használt reprezentatív Azure Cosmos-tároló vagy-adatbázis tipikus műveletek futtatásához társított ru-díj rögzítése, és majd becsülje meg az egyes másodpercenként végrehajtandó műveletek számát. Ügyeljen arra, hogy a mérési és tartalmazza a tipikus lekérdezések és azok használatát is. A lekérdezések RU-költségeinek programozott vagy a portál használatával történő becsléséről [a lekérdezések költségének optimalizálása](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)című témakörben olvashat. 

3. Egy másik módja annak, hogy a műveletek és azok költségeit a Rendszeres egységekben az Azure Monitor naplók engedélyezésével, amely megadja a művelet/időtartam és a kérelem díjának lebontását. Az Azure Cosmos DB kérési díjat biztosít minden művelethez, így minden műveletdíj tárolható vissza a válaszból, majd elemzésre használható. 

4. Rugalmasan skálázhatja fel felé és lefelé a kiosztott átviteli, ahogy a számítási feladatok igényeinek megfelelően. 

5. Az Azure Cosmos-fiókhoz társított régiókat szükség szerint hozzáadhatja és eltávolíthatja, és szabályozhatja a költségeket. 

6. Győződjön meg arról, hogy az adatok és a számítási feladatok elosztása a tárolók logikai partíciói között. Ha egyenetlen partíció-elosztással rendelkezik, ez a szükséges értéknél nagyobb átviteli értéket eredményezhet. Ha úgy látja, hogy ferde elosztással rendelkezik, javasoljuk, hogy egyenletesen ossza el a számítási feladatokat a partíciók között, vagy particionálja újra az adatokat. 

7. Ha sok tárolóval rendelkezik, és ezek a tárolók nem igényelnek SL-eket, használhatja az adatbázis-alapú ajánlatot azokban az esetekben, ahol a tárolónkénti átviteli átviteli átviteli SlA-k nem vonatkoznak. Meg kell határoznia, hogy az Azure Cosmos-tárolók közül melyiket szeretné áttelepíteni az adatbázis szintű átviteli ajánlatra, és majd áttelepíteni őket egy módosítási hírcsatorna-alapú megoldás használatával. 

8. Fontolja meg a "Cosmos DB free tier" (ingyenes egy évig), Próbálja Cosmos DB (legfeljebb három régió) vagy letölthető Cosmos DB emulátor fejlesztési és tesztelési forgatókönyvek. Ezekkel a beállításokkal a teszt-dev, jelentősen csökkentheti a költségeket.  

9. Számítási feladatoktól függő költségoptimalizálást végezhet – például a kötegméret növelését, a több régióközötti terheléselosztási olvasásokat és adott esetben az adatok duplikálását.

10. Az Azure Cosmos DB fenntartott kapacitásával akár 65%-os kedvezményt is kaphat három éven keresztül. Az Azure Cosmos DB fenntartott kapacitásmodellje egy előzetes kötelezettségvállalás az idő múlásával szükséges kérelmek egységek. A kedvezmények többszintűek, így minél több kérelemegységet használ hosszabb ideig, annál több lesz a kedvezmény. Ezek a kedvezmények azonnal érvénybe lépnek. A kiosztott értékek felett használt fenntartott értékek felett használt fenntartott szolgáltatások díja a nem fenntartott kapacitásköltsége alapján kerül felszámításra. További részletekért lásd [a Cosmos DB fenntartott kapacitását).](cosmos-db-reserved-capacity.md) Fontolja meg a fenntartott kapacitás megvásárlását a kiosztott átviteli költségek további csökkentése érdekében.  

## <a name="next-steps"></a>További lépések

Ezután az alábbi cikkekkel többet tudhat meg a költségoptimalizálásról az Azure Cosmos DB-ben:

* További információ [az optimalizálásról fejlesztési és tesztelési célokra](optimize-dev-test.md)
* További információ [az Azure Cosmos DB-számlájának ismertetéséről](understand-your-bill.md)
* További információ a [tárolási költség optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási költségek optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések költségének optimalizálásáról](optimize-cost-queries.md)
* További információ a több régióra kiterjedő [Azure Cosmos-fiókok költségeinek optimalizálásáról](optimize-cost-regions.md)

