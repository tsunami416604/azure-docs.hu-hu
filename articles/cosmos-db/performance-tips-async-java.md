---
title: Az Azure Cosmos DB teljesítményével kapcsolatos tippek Async Javához |} A Microsoft Docs
description: Ismerje meg az ügyfél-konfigurációs beállításokat az Azure Cosmos DB adatbázis-teljesítmény javítása
keywords: adatbázis teljesítményének növelése
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.openlocfilehash: 233296a825653938da158fc70952c7fe7931498c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261825"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Teljesítménnyel kapcsolatos tippek Async Javához pedig az Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Az Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, teljesítmény és a késés garantált az zökkenőmentesen méretezhető. Nem rendelkezik architektúra jelentős módosításokat, vagy az adatbázis az Azure Cosmos DB méretezése összetett programkód írása. Felfelé és lefelé skálázás nem kell csak egy egyetlen API-hívás, illetve az SDK metódus hívással. Azonban mivel a Azure Cosmos DB a hálózati hívások segítségével érhető el vannak ügyféloldali optimalizálást is végezhet használata esetén a csúcsteljesítmény érhet el a [SQL aszinkron Java SDK](sql-api-sdk-async-java.md).

Így ha Ön kérő "Hogyan javíthatom adatbázis teljesítmény?" Vegye figyelembe a következő beállításokat:

## <a name="networking"></a>Hálózat
   <a id="same-region"></a>
1. **A teljesítmény Fájlmegosztáséval azonos régióban lévő ügyfelek elhelyezésének engedélyezése**

    Ha lehetséges, helyezze el az alkalmazásokat Azure Cosmos DB hívja meg az Azure Cosmos DB-adatbázis és ugyanabban a régióban. 1 – 2 ms belül végezze az azonos régión belüli Azure Cosmos DB-hívások hozzávetőleges összehasonlításáért, de a nyugati és keleti parti területei az Egyesült Államok a késés > 50 ms. Ez a késés kérelem kérés valószínűleg függvényében az útvonal, az ügyfél az Azure-adatközpont határt adja át a kérés által készített. A lehető legkisebb késleltetést úgy, hogy a hívó alkalmazás megtalálható az üzembe helyezett Azure Cosmos DB-végpontként az azonos Azure-régióban érhető el. Az elérhető régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/regions/#services).

    ![Az Azure Cosmos DB kapcsolati házirend ábrája](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-használata
1. **A legújabb SDK telepítése**

    Az Azure Cosmos DB SDK kat folyamatosan fejlesztik a lehető legjobb teljesítményt. Tekintse meg a [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) oldalt, hogy a legújabb SDK határozza meg, és tekintse át a fejlesztései.
2. **Az alkalmazás teljes élettartama egyedülálló Azure Cosmos DB-ügyfél használata**

    Minden egyes AsyncDocumentClient példány szálbiztos, hatékony kapcsolat kezelése és a címek gyorsítótárazása hajt végre. Ahhoz, hogy a kapcsolat hatékony kezelése és AsyncDocumentClient által jobb teljesítményt, ajánlott az alkalmazás teljes élettartama alkalmazástartományonként AsyncDocumentClient egyetlen példányát használja.

   <a id="max-connection"></a>

3. **ConnectionPolicy hangolása**

    Az Azure Cosmos DB kéri, amikor aszinkron Java SDK-val végzett HTTPS/REST, és vannak kitéve, az alapértelmezett maximális kapcsolódási készlet mérete (1000). Ez az alapértelmezett érték a legtöbb használati esetek ideális kell lennie. Azonban abban az esetben, ha sok partícióval rendelkező gyűjteménye van, beállíthatja a kapcsolatkészlet maximális mérete nagyobb számot (például 1500) setMaxPoolSize használatával.

4. **Particionált gyűjteményeknél végezzen párhuzamos lekérdezések hangolása**

    Az Azure Cosmos DB SQL aszinkron Java SDK támogatja a párhuzamos lekérdezések, amelyek lehetővé teszik egy particionált gyűjteménybe a párhuzamos lekérdezés (lásd: [az SDK-k használata](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) és a kapcsolódó [Kódminták](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) további információ). Javítja a Lekérdezések késése és az átviteli sebesség soros többszörözi a párhuzamos lekérdezések lettek kialakítva.

    (a) ***setMaxDegreeOfParallelism hangolása\:***  párhuzamos lekérdezések munkahelyi több partíció párhuzamosan lekérdezésével. Azonban az egyes particionált gyűjteményt származó adatok letöltődött tárolókonfigurációhoz garanciát a lekérdezést. A partíciók száma, amely rendelkezik a legtöbb nagy teljesítményű, a megadott lekérdezés semmilyen más feltételt rendszer elérésére maximális esélyét használata setMaxDegreeOfParallelism így ugyanazok maradnak. Ha nem ismeri a partíciók számát, setMaxDegreeOfParallelism segítségével állítsa be a magas érték, és a rendszer a minimális (partíciók, felhasználó által megadott bemeneti száma) választja, a párhuzamosság maximális foka. 

    Fontos megjegyezni, hogy párhuzamos lekérdezések a legjobb előnyöket előállításához, ha az adatok egyenletesen garanciát a lekérdezés összes partíciók között. Ha a particionált gyűjtemény particionált oly módon, hogy az összes vagy egy lekérdezés által visszaadott adatok többsége összpontosul néhány partíciók (egy partíciót a legrosszabb esetben), majd a lekérdezés teljesítményének lenne bottlenecked, ezek a partíciók szerint.

    (b) ***setMaxBufferedItemCount hangolása\:***  párhuzamos lekérdezés úgy tervezték, hogy előre olvashatók be eredményeket, amíg az eredmények az aktuális köteg feldolgozása folyamatban van az ügyfél által. A lekérdezés teljes késés fokozása lehívását segít. setMaxBufferedItemCount korlátozza az előzetesen beolvasott eredményeket. A lekérdezést úgy, hogy fogadjon a legnagyobb előny lehívását setMaxBufferedItemCount adatsorban visszaadott várt számú (vagy nagyobb) beállítás lehetővé teszi.

    Lehívását attól függetlenül, a Maxanalyticsunits ugyanúgy működik, és az összes partíciót az adatok egyetlen puffert.  

5. **Leállítási megvalósítása getRetryAfterInMilliseconds időközönként**

    Teljesítmény tesztelése során terhelés mindaddig, amíg egy kis méretű kérések másodpercenkénti száma leszabályozza növelje meg. Ha szabályozott, az ügyfélalkalmazás kell leállítási a kiszolgáló által megadott újrapróbálkozási időköz. A leállítási tiszteletben biztosítja, hogy az újrapróbálkozások közötti várakozási idő mennyisége minimális idő. 
6. **Az ügyfél-alkalmazások és szolgáltatások méretezése**

    Ha nagy átviteli sebesség szinten teszteli (> 50 000 RU/s), az ügyfélalkalmazás miatt a gép kapacitástól meg a Processzor vagy a hálózati kihasználtság a szűk keresztmetszetté válhat. Ha eléri ezt a pontot, továbbra is küldje le az Azure Cosmos DB-fiókot további horizontális felskálázása az ügyfélalkalmazásokat, több kiszolgáló között.

7. **Használja a címzési neve alapján**

    Használja, névalapú címzés, ahol hivatkozások formátuma a `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, SelfLinks helyett (\_self), amelyek rendelkeznek a formátum `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` ResourceId segítségével hozza létre a kapcsolat összes erőforrás beolvasása elkerülése érdekében. Is mivel ezek az erőforrások beolvasása újból (valószínűleg ugyanaz a neve), azokat a gyorsítótárazást nem segítségére lehetnek.

   <a id="tune-page-size"></a>
8. **Az oldal méretét a lekérdezések és olvasási hírcsatornák, a jobb teljesítmény hangolása**

    Tömeges végrehajtása dokumentumok olvasása, olvassa el a hírcsatorna-funkciókat (például readDocuments) használatával, vagy egy SQL-lekérdezést kiadásakor, az eredmény akkor szegmentált módon ha túl nagy az eredményhalmaz. Alapértelmezés szerint az eredmény akkor 100 elemet vagy 1 MB-os blokkonként, bármelyik korlát nyomja le az első.

    Kevesebb hálózati kerekíteni lelassítja az összes vonatkozó eredmények beolvasásához szükséges, növelhető a méret használatával a [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) legfeljebb 1000 kérelem fejléce. Azokban az esetekben, ahol csak néhány eredmények megjelenítéséhez szüksége például, ha a felhasználói felület vagy a kérelem API függvény csak 10 eredménye egy idő, is csökkentheti, ha az oldal méretét a 10-re az olvasást és lekérdezések felhasznált átviteli sebesség csökkentése érdekében.

    Az oldal méretét a setMaxItemCount módszerrel is megadhat.
    
9. **Használja a megfelelő Feladatütemező (kerülje lopásának megjelölése esemény hurok i/o-Netty szálak)**

    Az aszinkron Java SDK az [netty](https://netty.io/) a nem blokkoló IO. Az SDK az i/o netty esemény hurok szálak (annyi processzormagot a gépe képes) rögzített számú i/o-műveletek végrehajtásához. Az API által visszaadott rendszernek megfigyelhetőnek az eredmény egy közös IO esemény hurok netty szál bocsát ki. Ezért fontos a megosztott IO esemény hurok netty szálak blokkolja. CPU-igényes munkát végző vagy blokkolja a műveletet az i/o esemény hurok netty szálhoz holtpont miatt, vagy jelentősen csökkentheti az SDK átviteli sebességet.

    A következő kód például cpu-intenzív munkahelyi lefut a az esemény hurok i/o-netty szál:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Után ha szeretné eredmény CPU-igényes az eredmény kerülje ennek során stb esemény hurok i/o-netty szál működik. Helyette megadhat saját Scheduler az Ön saját biztosít a futó munkáját.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    A munkahelyi típusa alapján használja a megfelelő meglévő RxJava Scheduler a munkájához. Részletek [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    További információkért tekintse meg a [Github-oldalon](https://github.com/Azure/azure-cosmosdb-java) aszinkron Java SDK-hoz készült.

10. **Netty a naplózás letiltása** Netty könyvtár naplózási forgalmas, és ki legyen kapcsolva (újrainduljanak jelentkezzen be a konfigurációt nem lehet elegendő) CPU további költségek elkerülése érdekében. Ha nem hibakeresési módban, tiltsa le netty érvényesítette naplózás. Ha a log4j használatával távolítsa el a felmerülő CPU többletköltségeket ``org.apache.log4j.Category.callAppenders()`` netty hozzá a következő sort a kódbázis:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Az operációs rendszer megnyitott fájlok Erőforráskorlátot** egyes Linux rendszereket (mint például a Red Hat) rendelkezik egy felső korlátot nyílt száma fájlokat, így a kapcsolatok száma. Futtassa a következő parancsot a jelenlegi korlátozások megtekintése:

    ```bash
    ulimit -a
    ```

    A számú megnyitott fájlt (nofile) kell lennie, elég nagy ahhoz, hogy elég hellyel rendelkezzen a a konfigurált kapcsolati-példánykészlet méretét és a többi megnyitott fájlokat, az operációs rendszer. Lehetővé teszi egy nagyobb kapcsolatkészlet mérete módosíthatók.

    Nyissa meg a limits.conf fájlt:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    A következő sorok hozzáadása/módosítása:

    ```
    * - nofile 100000
    ```

12. **Natív SSL végrehajtására használható netty** Netty OpenSSL közvetlenül az SSL-megvalósítási stackhez használható jobb teljesítményt érhet el. Ennek hiányában konfigurációs netty fog térhet vissza a Java alapértelmezett SSL-megvalósítás.

    az ubuntu rendszeren:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    és adja hozzá a következő függőséget a projektet maven függőségekre:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Más platformok (Red Hat, Windows, Mac, stb.) tekintse meg ezeket az utasításokat https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexelési házirend
 
1. **A fel nem használt elérési utak kizárása a gyorsabb írások indexelése**

    Az Azure Cosmos DB indexelési házirend adja meg, melyik dokumentum elérési útjait belefoglalása vagy kizárása indexelő indexelő útvonalak (setIncludedPaths és setExcludedPaths) nyújtotta előnyöket kihasználva teszi lehetővé. Indexelő elérési utak használatát is kínál a továbbfejlesztett írási teljesítmény- és alacsonyabb index forgatókönyvekhez, ahol a lekérdezési mintáknak előzetesen ismert, mivel az indexelő költségek közvetlenül indexelt egyedi elérési utak száma közötti kapcsolatot.  Például a következő kód bemutatja, hogyan zárhat ki (más néven a dokumentumok egy teljes szakasz részfa) az indexelő használatával a "*" helyettesítő karakter.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    További információkért lásd: [indexelési szabályzatok az Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

1. **Mérheti és finomhangolási feladat alacsonyabb kérelem kérelemegység/s használat**

    Az Azure Cosmos DB adatbázis-műveletek, beleértve a relációs és hierarchikus lekérdezéseket UDF-EK, tárolt eljárásokkal és eseményindítókkal adatbázis-gyűjteményekben dokumentumok gazdag választékát kínálja. A műveletekhez kapcsolódó költségek a CPU, IO és a művelet végrehajtásához szükséges alapján változik. Szem előtt tartva és hardver-erőforrások kezelése, helyett is felfoghatók kérelemegység (RU) azon erőforrások számos adatbázis-műveletek végrehajtásához, és adott alkalmazáskérelem kiszolgálásához szükséges egyetlen érték.

    Átviteli sebesség száma alapján van kiépítve [kérelemegységek](request-units.md) állítsa be az egyes tárolók. Kérelem-egységek felhasználását másodpercenkénti minősül. Alkalmazások, amelyek túllépik a tárolóban kiosztott kérelem egység díjaival korlátozva, mindaddig, amíg az arány a tároló a kiépített szint alá csökken. Ha az alkalmazás egy magasabb adatátviteli kapacitást igényel, további kérelemegységet kiépítésével növelheti az átviteli sebességet. 

    A lekérdezés összetettségétől hatással van egy művelet felhasznált kérelemegységek számát. A predikátumok száma, a predikátumok UDF-EK számát és méretét a forrás adatkészlet összes jellege befolyásolhatják a lekérdezési műveletek költségét.

    Bármely művelet járó többletterhelést mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg a [x-ms-kérelem-díj](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejléc, ezek a műveletek által felhasznált kérelemegységek számát. Is tekintse meg a megfelelő RequestCharge tulajdonságot ResourceResponse<T> vagy FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```             

    A kérelem díja a fejléc adja vissza a kiosztott átviteli sebesség töredéke alatt. Például ha 2000 RU/s kiosztott, és ha az előző lekérdezés 1000 1KB-dokumentumokat ad vissza, a költség, a művelet 1000. Emiatt a belül egy második, a kiszolgáló figyelembe veszi előtt sebességével későbbi kérelmeket csak két ilyen kérelmeket. További információkért lásd: [Kérelemegységek](request-units.md) és a [kérelem egység Számológép](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Kezeli a sebesség korlátozása/kérelmek arányának túl nagy**

    Amikor az ügyfél meghaladja a fenntartott átviteli sebesség egy olyan fiók, nincs teljesítmény csökkenése nélkül működhet a kiszolgálón, és felül a fenntartott átviteli kapacitás használatának tilalma. A kiszolgáló előrelátó módon RequestRateTooLarge (HTTP-állapotkódot 429-es) a kérelem befejezése, és adja vissza a [x-ms-újrapróbálkozási-után – az ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) jelző idő ezredmásodpercben, amely a felhasználónak várakoznia kell, mielőtt újrapróbálkozni azoknál a fejléc a kérelmet.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k az összes implicit módon ezt a választ a tényleges, a kiszolgáló által megadott retry-after fejléccel tiszteletben, és ismételje meg a kérelmet. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozás sikeres lesz.

    Ha egynél több ügyfél felett a kérések mennyisége következetesen összesítve működő, az alapértelmezett újrapróbálkozások jelenlegi beállítása 9 belsőleg az ügyfél által esetleg nem elegendő; Ebben az esetben az ügyfél egy 429-es állapotkód DocumentClientException jelez az alkalmazásnak. Az alapértelmezett újrapróbálkozások ConnectionPolicy példányon setRetryOptions segítségével módosítható. Alapértelmezés szerint a 429-es állapotkód DocumentClientException adja vissza egy halmozódó várakozási idő pedig 30 másodperc után, ha a kérés továbbra is működőképes fent a kérések aránya. Ez akkor fordul elő, még ha az aktuális újrapróbálkozások nem éri el az újrapróbálkozások maximális számát, legyen az alapértelmezett 9 vagy egy felhasználó által definiált értéket.

    Az automatikus újrapróbálkozási viselkedés segít a rugalmasság és a legtöbb alkalmazás használhatóság javítása érdekében, miközben, előfordulhat, hogy származnak, bármikor odds esetén teljesítményteszteket, különösen akkor, amikor a késleltetés mérése.  Az ügyfél-megfigyelt késés fog hirtelen, ha eléri a kiszolgáló szabályozása a kísérletet, és az ügyfél SDK-val csendes módban próbálja újra. Teljesítmény kísérletek során késés ugrásszerűen elkerüléséhez a díjat az egyes műveletek által visszaadott mérjük, és győződjön meg arról, hogy a kérelmek működnek-e alatt a szolgáltatás számára fenntartott kérések mennyisége. További információkért lásd: [Kérelemegységek](request-units.md).
3. **A nagyobb sebesség érdekében kisebb méretű dokumentumokra tervezése**

    A kérelem díja (a kérelem feldolgozása költség) egy adott műveletnek közvetlenül visszamenőleges korrelációban állnak a dokumentum mérete. A nagy dokumentumok Operations drágább, mint a kisméretű dokumentumok műveletek.

## <a name="next-steps"></a>További lépések
A méretezés és a nagy teljesítményű alkalmazás megtervezésével kapcsolatos további tudnivalókért lásd: [particionálás és skálázás az Azure Cosmos DB](partition-data.md).
