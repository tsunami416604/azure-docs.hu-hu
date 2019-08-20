---
title: Azure Cosmos DB teljesítményre vonatkozó tippek aszinkron Javához
description: Az ügyfél-konfigurációs beállítások megismerése az Azure Cosmos Database teljesítményének növeléséhez
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 7a470193110fda0bb675e56e17a8f5647ebda5ab
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614969"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Teljesítménnyel kapcsolatos tippek Azure Cosmos DB és aszinkron Java-hoz

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

A Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség tekintetében. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás vagy egy SDK-metódus hívása. Mivel azonban a Azure Cosmos DB hálózati hívásokon keresztül érhetők el, ügyféloldali optimalizálási lehetőségek érhetők el, amelyekkel elérheti a maximális teljesítményt az [SQL aszinkron Java SDK](sql-api-sdk-async-java.md)használatakor.

Tehát ha a "Hogyan javíthatom az adatbázis teljesítményét?" című témakört kérdezi le? vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózat
   <a id="same-region"></a>
1. **Rézvezetékes végezhet-ügyfelek ugyanabban az Azure-régióban a teljesítmény érdekében**

    Ha lehetséges, helyezzen minden olyan alkalmazást, amely a Azure Cosmos DBt hívja meg ugyanabban a régióban, mint az Azure Cosmos Database. A hozzávetőleges összehasonlításhoz az azonos régióban lévő Azure Cosmos DB a 1-2 MS-on belül fejeződik be, de az USA nyugati és keleti partja közötti késés > 50 MS. Ez a késés valószínűleg a kérelemtől függ attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure Datacenter-határig. A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

    ![A Azure Cosmos DB-kapcsolatok házirendjének ábrája](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK-használat
1. **A legújabb SDK telepítése**

    A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. Tekintse meg a [Azure Cosmos db SDK](sql-api-sdk-async-java.md) -lapokat a legújabb SDK-val, és tekintse át a módosításokat.
2. **Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

    Minden AsyncDocumentClient-példány szál-biztonságos, és hatékonyan kezeli a kapcsolatok kezelését és a címek gyorsítótárazását. A hatékony AsyncDocumentClient és a jobb teljesítmény érdekében ajánlott a AsyncDocumentClient/alkalmazástartomány egyetlen példányát használni az alkalmazás élettartamára.

   <a id="max-connection"></a>

3. **ConnectionPolicy finomhangolása**

    Azure Cosmos DB kérések HTTPS/REST protokollon keresztül történnek az aszinkron Java SDK használatakor, és az alapértelmezett maximális kapcsolati készlet mérete (1000) alá tartoznak. Az alapértelmezett értéknek ideálisnak kell lennie a használati esetek többségében. Ha azonban sok partícióval rendelkező nagyméretű gyűjtemény van, akkor a setMaxPoolSize használatával megadhatja a maximálisan nagy számú (azaz 1500) kapacitást.

4. **A particionált gyűjtemények párhuzamos lekérdezésének finomhangolása**

    Azure Cosmos DB SQL aszinkron Java SDK támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemények párhuzamos lekérdezését. További információ: az SDK-k használatához kapcsolódó [kód-minták](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy a lekérdezési késést és az adatátvitelt a soros munkatársaik

    (a) a ***setMaxDegreeOfParallelism\:***  párhuzamos lekérdezések finomhangolása több partíció párhuzamos lekérdezésével működik. Az egyedi particionált gyűjteményekből származó adatok azonban a lekérdezéssel kapcsolatos sorosan kerülnek beolvasásra. Ezért a setMaxDegreeOfParallelism használatával állítsa be a legtöbb teljesítményű lekérdezés elérésének maximális esélyét biztosító partíciók számát, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, a setMaxDegreeOfParallelism használatával magas számot állíthat be, a rendszer pedig a minimális párhuzamosságot (a partíciók számát, a felhasználó által megadott bemenetet) adja meg.

    Fontos megjegyezni, hogy a párhuzamos lekérdezések a legjobb előnyöket nyújtják, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény úgy van particionálva, hogy a lekérdezés által visszaadott összes adat többsége néhány partíción (egy partíció a legrosszabb esetben) van, akkor a lekérdezés teljesítményét a partíciók szűk keresztmetszete okozhatja.

    (b) az ***setMaxBufferedItemCount\:***  párhuzamos lekérdezés finomhangolása az eredmények előzetes beolvasására szolgál, miközben az ügyfél az aktuális eredményt dolgozza fel. Az előzetes beolvasás a lekérdezés teljes késésének javulását segíti elő. a setMaxBufferedItemCount korlátozza az előre beolvasott eredmények számát. A setMaxBufferedItemCount beállítása a visszaadott eredmények várt számához (vagy egy magasabb szám) lehetővé teszi a lekérdezés számára, hogy a lehető legtöbbet fogadja az előzetes lekéréstől.

    Az előzetes lekérés ugyanúgy működik, mint a Maxanalyticsunits, és egyetlen puffer van az összes partícióból származó adatokhoz.

5. **Leállítási megvalósítása getRetryAfterInMilliseconds időközönként**

    A teljesítmény tesztelése során növelje a terhelést, amíg a kérelmek kis száma le nem kerül a szabályozásba. Ha szabályozva van, az ügyfélalkalmazás leállítási kell lennie a kiszolgáló által megadott újrapróbálkozási időköznek. A leállítási tiszteletben tartásával biztosítható, hogy az újrapróbálkozások között minimálisan mennyi időt kell várnia.
6. **Az ügyfél felskálázása – munkaterhelés**

    Ha nagy átviteli sebességű > (50000 RU/s) szintű tesztelést végez, akkor előfordulhat, hogy az ügyfélalkalmazás szűk keresztmetszetet okoz a PROCESSZORon vagy a hálózat kihasználtságán túl. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.

7. **Név alapú címzés használata**

    Használjon név-alapú címzést, ahol a hivatkozások formátuma `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`a SelfLinks helyett (\_saját), amelynek formátuma `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` a hivatkozás létrehozásához használt összes erőforrás ResourceId: beolvasásának elkerülése. Továbbá, mivel ezek az erőforrások újra létre lettek (valószínűleg ugyanazzal a névvel), előfordulhat, hogy a gyorsítótárazás nem segít.

   <a id="tune-page-size"></a>
8. **A jobb teljesítmény érdekében a lekérdezések/olvasási hírcsatornák méretének finomhangolása**

    A dokumentumok tömeges beolvasása a hírcsatornák olvasási funkciójának használatával (például readDocuments) vagy SQL-lekérdezés kiállításakor az eredményeket szegmentált módon adja vissza, ha az eredményhalmaz túl nagy. Alapértelmezés szerint a rendszer az eredményeket 100 elemből vagy 1 MB-ra adja vissza, amely a határértékek közül az első.

    Ha csökkenteni szeretné az összes vonatkozó eredmény beolvasásához szükséges hálózati kör alakú utak számát, az [x-MS-Max-Item-](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) Request fejléc használatával növelheti az oldal méretét a legfeljebb 1000 értékre. Olyan esetekben, ahol csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az Application API csak 10 eredményt ad vissza, az olvasási és lekérdezési teljesítmény csökkentése érdekében csökkentheti az oldal méretét is 10 értékre.

    Az oldalméret a setMaxItemCount metódussal is megadható.

9. **A megfelelő ütemező használata (az Event loop IO-szálak kilopásának elkerülése)**

    Az aszinkron Java SDK nem [](https://netty.io/) blokkoló IO-t használ. Az SDK az i/o-műveletekhez rögzített számú i/o-t használ (a gép számos CPU-magja). Az API által visszaadott megfigyelhető érték a megosztott i/o-eseményekhez tartozó többoldalas szálakat eredményezi. Ezért fontos, hogy ne tiltsa le a megosztott IO-esemény hurkos szálait. Ha CPU-igényes munkahelyi vagy letiltási műveletet hajt végre az IO-esemény hurkos szál szálán, a holtpontot okozhat, vagy jelentősen csökkentheti az SDK átviteli sebességét.

    Például az alábbi kód egy CPU-igényes munkát hajt végre az Event loop IO-szálon:

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

    Ha az eredmény megérkezik, ha a CPU-igényes munkát szeretné elvégezni az eredményen, kerülje az Event loop IO-szálon végzett műveletét. Ehelyett saját ütemező használatával biztosíthatja a saját szálát a munka futtatásához.

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

    A munkája típusától függően a megfelelő meglévő RxJava ütemező használatával kell használnia a munkát. Itt [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)olvashat.

    További információkért tekintse meg a GitHub- [oldalt](https://github.com/Azure/azure-cosmosdb-java) az ASZINKRON Java SDK-hoz.

10. **A nettó naplózás letiltása** A további CPU-költségek elkerülése érdekében a többfunkciós kódtár naplózása beszédes, és ki kell kapcsolni (a bejelentkezés a konfigurációban nem lehet elég). Ha nem hibakeresési módban van, tiltsa le a nettó naplózást. Tehát ha a log4j használatával távolítja el a további CPU ``org.apache.log4j.Category.callAppenders()`` -költségeket a (z) rendszerből, adja hozzá a következő sort a kód számára:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Operációs rendszer által megnyitott fájlok erőforrás** -korlátja Bizonyos linuxos rendszerek (például a Red Hat) felső korláttal rendelkeznek a megnyitott fájlok számától, így a kapcsolatok teljes száma. A jelenlegi korlátok megtekintéséhez futtassa a következő parancsot:

    ```bash
    ulimit -a
    ```

    A megnyitott fájlok (nem fájl) számának elég nagynak kell lennie ahhoz, hogy elegendő hely legyen a konfigurált és az operációs rendszer által megnyitott fájlok számára. Ez módosítható úgy, hogy nagyobb a kapcsolatok készletének mérete.

    Nyissa meg a Limits. conf fájlt:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    A következő sorok hozzáadása/módosítása:

    ```
    * - nofile 100000
    ```

12. **NATÍV SSL-implementáció használata a nettó** működéshez A nagyobb teljesítmény érdekében az OpenSSL az SSL implementációs veremhez közvetlenül használható. Ennek a konfigurációnak a hiányában a Java alapértelmezett SSL-implementációja fog visszaesni.

    on Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    adja hozzá a következő függőséget a projekt Maven-függőségeihez:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Más platformokon (Red Hat, Windows, Mac stb.) tekintse át ezeket az utasításokat https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexelési házirend
 
1. **Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

    Azure Cosmos DB indexelési házirendje lehetővé teszi annak meghatározását, hogy mely dokumentum-elérési utakat kell belefoglalni vagy kizárni az indexelésből az indexelési útvonalak (setIncludedPaths és setExcludedPaths) használatával. Az indexelési útvonalak használata javíthatja az írási teljesítményt és az alacsonyabb indexű tárolást olyan helyzetekben, amikor a lekérdezési mintákat előre ismerték, mivel az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával vannak összekapcsolva. Az alábbi kód például azt mutatja be, hogyan zárható ki a dokumentumok teljes szakasza (más néven: egy részfa) az indexelésből a "*" helyettesítő karakter használatával.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    További információ: [Azure Cosmos db indexelési házirendek](indexing-policies.md).

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

1. **Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

    A Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja, beleértve a UDF, tárolt eljárásokkal és triggerekkel kapcsolatos, és az adatbázis-gyűjteményen belüli összes műveletet. Az egyes műveletekhez kapcsolódó díjak a művelet végrehajtásához szükséges CPU, IO és memória alapján változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához és az alkalmazásokra vonatkozó kérések kiszolgálásához szükséges erőforrásokhoz.

    Az átviteli sebesség az egyes tárolók számára beállított [kérelmek egységeinek](request-units.md) száma alapján lett kiépítve. A kérelem egységének felhasználását a rendszer másodpercenkénti arányban értékeli. Azok az alkalmazások, amelyek túllépik a tárolók kiépített kérelmi egységének sebességét, csak a tároló számára kiépített szint alá csökkennek. Ha az alkalmazás magasabb adatátviteli kapacitást igényel, akkor a további kérelmek kiszámításával növelheti a teljesítményt.

    A lekérdezés bonyolultsága befolyásolja, hogy hány kérelem-egységet használ a művelet. A predikátumok száma, a predikátumok természete, a UDF száma és a forrásadatok készletének mérete egyaránt befolyásolja a lekérdezési műveletek költségeit.

    Bármilyen művelet (létrehozás, frissítés vagy törlés) mértékének méréséhez vizsgálja meg az [x-MS-Request-Charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejlécet a műveletek által felhasznált kérelmek mennyiségének méréséhez. Az egyenértékű RequestCharge tulajdonságot a ResourceResponse\<T > vagy a FeedResponse\<t >ban is megtekintheti.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség töredéke. Ha például 2000 RU/s van kiépítve, és ha az előző lekérdezés a 1000 1KB-dokumentumokat adja vissza, a művelet díja 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

    Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló megelőző jelleggel a kérést a RequestRateTooLarge (429-es HTTP-állapotkód), és visszaküldi az [x-MS-újrapróbálkozás-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) fejlécet, jelezve azt az időtartamot (ezredmásodpercben), ameddig a felhasználónak meg kell várnia a kérés ismételt megkísérlése előtt.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

    Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a kérelmek arányát, akkor előfordulhat, hogy az ügyfél az újrapróbálkozások alapértelmezett száma jelenleg 9 belső értékre van állítva. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó DocumentClientException hoz az alkalmazáshoz. Az újrapróbálkozások alapértelmezett száma módosítható a ConnectionPolicy-példány setRetryOptions használatával. Alapértelmezés szerint a 429-as kódú DocumentClientException a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. Ez akkor is előfordul, ha a jelenlegi újrapróbálkozások száma kisebb, mint az újrapróbálkozások maximális száma, legyen az alapértelmezett 9-es vagy felhasználó által definiált érték.

    Míg az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását, akkor előfordulhat, hogy a teljesítményre vonatkozó teljesítménymutatók végrehajtásakor a rendszer hasznosnak bizonyul, különösen a késés mérése során. Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. További információt a [kérelmek egységei](request-units.md)című témakörben talál.
3. **Kisebb dokumentumok tervezése magasabb átviteli sebesség esetén**

    Egy adott műveletre vonatkozó kérelem díja (a kérelmek feldolgozási díja) közvetlenül összefügg a dokumentum méretével. A nagyméretű dokumentumokon végzett műveletek többek között a kis méretű dokumentumok műveleteinél nagyobb mértékben járnak.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű kialakításáról, tekintse meg [a particionálás és skálázás Azure Cosmos DBban](partition-data.md)című témakört.
