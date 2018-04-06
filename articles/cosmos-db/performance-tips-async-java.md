---
title: Aszinkron Javához készült Azure Cosmos DB teljesítmény tippek |} Microsoft Docs
description: Ismerje meg az ügyfél-konfigurációs beállítások Azure Cosmos DB adatbázis teljesítményének javítása érdekében
keywords: adatbázis teljesítményének javításával
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: dfe8f426-3c98-4edc-8094-092d41f2795e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mimig
ms.openlocfilehash: 88d9859ade8f2cd3c5f11dffa8e754e83fc8b4d4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Teljesítmény tippek az Azure Cosmos adatbázis és az aszinkron Java
Azure Cosmos-adatbázis egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezi, garantált késéssel és átviteli sebesség. Nincs a fő architektúra módosításokat, vagy írja meg az adatbázist az Azure Cosmos DB méretezési komplex kódot. Méretezés felfelé és lefelé akkor lehető legkönnyebben egy egyetlen API-hívás vagy SDK metódus hívása közben. Azonban mivel Azure Cosmos DB hálózati hívások keresztül érhető el nincsenek ügyféloldali optimalizálása, hogy használata esetén a csúcsteljesítmény eléréséhez a [SQL aszinkron Java SDK](sql-api-sdk-async-java.md).

Ezért ha még kérése "Hogyan javítható az adatbázis teljesítménye?" Vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózat
   <a id="same-region"></a>
1. **A teljesítmény azonos Azure régióban ügyfelek elhelyezésének engedélyezése**

    Ha lehetséges, helyezze el az alkalmazásokat, Azure Cosmos DB hívja az Azure Cosmos DB adatbázis ugyanabban a régióban. Hozzávetőleges összehasonlításhoz hívások Azure Cosmos DB belül ugyanabban a régióban 1-2 ms belül befejeződik, de nyugati és az Egyesült Államok, keleti part közötti késés > 50 ms. Ez a késés valószínűleg eltérőek lehetnek kérelem kérelem attól függően, hogy az útvonal kérés hajtja végre, mivel az ügyfél az Azure-adatközpontban határ adja át. A lehető legkisebb késleltetést érhető el, biztosítva, hogy a hívó alkalmazás helyen belüli és a kiépített Azure Cosmos DB végpont Azure ugyanabban a régióban. Elérhető régiók listáját lásd: [Azure-régiókat](https://azure.microsoft.com/regions/#services).

    ![Az Azure Cosmos DB kapcsolatkezelési házirendet ábrája](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK használata
1. **A legutóbbi SDK telepítése**

    Az Azure Cosmos DB SDK-k a rendszer folyamatosan javult a legjobb teljesítmény elérése érdekében. Tekintse meg a [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) lapok használatával állapítsa meg a legutóbbi SDK, és tekintse át a fejlesztései.
2. **Az alkalmazás teljes egypéldányos Azure Cosmos DB ügyfél használata**

    Minden egyes AsyncDocumentClient példány szálbiztos, hatékony kapcsolat kezelése és a címek gyorsítótárazása hajt végre. Engedélyezése hatékony kapcsolat kezelése és AsyncDocumentClient által jobb teljesítmény érdekében javasoljuk, hogy egyetlen példány futhat egy AppDomain AsyncDocumentClient használni az alkalmazás élettartamának.

   <a id="max-connection"></a>

3. **ConnectionPolicy hangolása**

    Azure Cosmos-adatbázis kéri az aszinkron Java SDK használata esetén HTTPS/REST keresztül történik, és vannak kitéve, az alapértelmezett kapcsolatkészlet maximális mérete (1000). Ez az alapértelmezett érték a legtöbb esetben használjon ideális kell lennie. Azonban ha nagyon nagy méretű gyűjtemény sok partíciókkal van, beállíthatja a kapcsolatkészlet maximális mérete nagyobb számot (például, 1500) setMaxPoolSize használatával.

4. **A particionált gyűjtemények párhuzamos lekérdezések hangolása**

    Az Azure Cosmos DB SQL aszinkron Java SDK támogatja a párhuzamos lekérdezések, amelyek lehetővé teszik egy particionált gyűjtemény párhuzamos lekérdezés (lásd: [az SDK-k használata](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) és a kapcsolódó [Kódminták](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) további információt a). Párhuzamos lekérdezések úgy tervezték, hogy a soros megfelelőjére javítja a lekérdezés-késleltetés és a teljesítményt.

    (a) ***setMaxDegreeOfParallelism hangolása\:***  párhuzamos lekérdezések munkahelyi több partíció párhuzamosan lekérdezésével. Azonban az egyes particionált gyűjtemény adatait beolvassa Feladattervek tekintetében a lekérdezést. A partíciók számát, amelynek az elérése érdekében a legtöbb performant lekérdezés, amely más rendszer feltételek megadott maximális esélyét beállításához használja setMaxDegreeOfParallelism tehát változatlanok maradnak. Ha nem ismeri a partíciók számának, setMaxDegreeOfParallelism segítségével állítsa be a magas érték, és a rendszer a minimális (számú partíciót, felhasználó által megadott bemeneti) választja, a legnagyobb mértékben párhuzamossági. 

    Fontos megjegyezni, hogy párhuzamos lekérdezések eredményez-e a legjobb előnyöket, ha az adatok tekintetében a lekérdezés összes partíciójára egyenletesen vannak elosztva. Ha a particionált gyűjtemény particionálva van-e olyan módon, hogy néhány partíciók (egy partíciót a legrosszabb esetben) az összes vagy egy lekérdezés által visszaadott adatok többsége van koncentrált, majd a lekérdezés teljesítményét volna kell szűk keresztmetszetét adja ki ezeket a partíció.

    (b) ***setMaxBufferedItemCount hangolása\:***  párhuzamos lekérdezések arra tervezték, hogy előre fetch eredmények, amíg a jelenlegi köteg eredményeinek feldolgozása folyamatban van az ügyfél által. A lekérdezések teljes késést fokozása lehívását segít. setMaxBufferedItemCount előre lehívott eredmények számának korlátozása. A várt számú eredményt adott vissza a setMaxBufferedItemCount (vagy magasabb azonosítószámot) beállítás lehetővé teszi, hogy a lekérdezés maximális haszna nem származik, a lehívását.

    Lehívását függetlenül a MaxDegreeOfParallelism ugyanúgy működik, és minden olyan partíciónak az adatait az egyetlen puffert.  

5. **Leállítási getRetryAfterInMilliseconds időközönként megvalósítása**

    Teljesítmény tesztelés során terhelés csak a kis mértékben kérelmek get halmozódni növelje meg. Ha szabályozva, az ügyfélalkalmazás kell leállítási a kiszolgáló által megadott újrapróbálkozási időköz. A leállítási tiszteletben biztosítja, hogy az újrapróbálkozások közötti várakozási idő a lehető legrövidebb idő. További információkért lásd: [Exceeding fenntartott átviteli sebességének korlátai](request-units.md#RequestRateTooLarge) és DocumentClientException.getRetryAfterInMilliseconds.
6. **Horizontális felskálázás az ügyfél-alkalmazások és szolgáltatások**

    Ha nagy átviteli szinten teszteli (> 50 000 RU/mp), az ügyfélalkalmazás gép határértékét el a Processzor- vagy hálózati kihasználtsága miatt a szűk keresztmetszetek válhat. Ha eléri ezt a pontot, továbbra is az Azure Cosmos DB fiók további leküldéses által az ügyfélalkalmazások kiterjesztése több kiszolgáló között.

7. **Használja a név alapján címzés**

    Használja, névalapú címzés, ahol hivatkozások formátuma a `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, SelfLinks helyett (\_önkiszolgáló), amely formátumuk `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` erőforrás-azonosítók segítségével hozza létre a kapcsolat összes erőforrás beolvasása elkerülése érdekében. Is mivel ezeket az erőforrásokat (valószínűleg azonos nevű) lekérése újból, gyorsítótárazás azokat nem segíthet.

   <a id="tune-page-size"></a>
8. **A jobb teljesítmény lekérdezések/olvasás hírcsatornák lapmérete hangolása**

    A tömeges végrehajtása dokumentumok funkciók (például readDocuments) hírcsatorna olvasása használatával vagy olvasási SQL-lekérdezést kiállításához, eredményeinek szegmentált módon ha eredménykészlet túl nagy. Alapértelmezés szerint eredményeinek 100 elemet vagy 1 MB adattömböket, bármelyik korlát találati első.

    Adjon meg kevesebb hálózati kerekíteni összes vonatkozó eredmények beolvasásához szükséges való adatváltások számát, az oldal méretét használatával növelheti a [x-ms-maximális elem-darabszám](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) legfeljebb 1000 kérelemfejlécet. Azokban az esetekben, ahol csak néhány eredmények megtekintése céljából kell például a felhasználói felület vagy a kérelem API visszatérési értéke csak 10 egyszerre annak az eredménye, csökkentheti a 10-re csökkenteni a teljesítményt, Olvasás, mind a lekérdezések felhasznált mérete.

    Az oldalméret, a setMaxItemCount metódussal is megadhat.
    
9. **Használja a megfelelő Feladatütemező (ne Eventloop IO Netty szálak ellophassák)**

    Az aszinkron Java SDK-t használ [netty](https://netty.io/) nem blokkoló IO számára. Az SDK-t használ IO netty eventloop szálak (annyi Processzormag használatát a számítógépen van) rögzített számú I/O műveletek végrehajtása. Az API által visszaadott megfigyelhető megfelelően kibocsát egy megosztott IO eventloop netty szálak eredménye. Ezért fontos, hogy nem blokkolja a megosztott IO eventloop netty szálak. CPU-intenzív munkát végző vagy blokkolja a műveletet a IO eventloop netty szálon holtpont miatt, vagy jelentősen csökkentheti a SDK átviteli sebességet.

    Például az alábbi kód eventloop IO netty szálban hajt végre a cpu-intenzív munkahelyi:

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

    Ha a CPU-intenzív munkahelyi kerülendő stb eventloop IO netty szálon történt meg szeretné eredmény fogadását. Helyette megadhatja a saját ütemezőt, hogy adja meg a saját szálon futó munkáját.

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

    A munkahelyi típusa alapján a munka a megfelelő meglévő RxJava Feladatütemező kell használnia. Az alábbiakban elolvashatja [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    További információkért tekintse meg a [Github-oldalon](https://github.com/Azure/azure-cosmosdb-java) aszinkron Java SDK-ban.

10. **Tiltsa le a naplózást netty meg** Netty könyvtár naplózási chatty, és ki kell kapcsolni (kikapcsolása napló a konfigurációban nem lehet elegendő) CPU további költségek elkerülése érdekében. Nincsenek hibakeresési mód, ha teljesen naplózás letiltása netty. Igen, ha a log4j segítségével távolítsa el a további CPU költségeit ``org.apache.log4j.Category.callAppenders()`` netty hozzá a következő sort a codebase:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Az operációs rendszer megnyitott fájlok erőforrás korlátját** egyes Linux rendszerek (például Redhat) rendelkezik egy felső korlátot nyílt meg fájlokat, ezért kapcsolatok teljes száma. Futtassa a következő a jelenlegi korlátozások megtekintése:

    ```bash
    ulimit -a
    ```

    A számú megnyitott fájlt (nofile) kell lennie, elég nagy ahhoz, hogy elég hely a konfigurált kapcsolatkészlet mérete és az operációs rendszer egyéb megnyitott fájlokat. Lehetővé teszi a nagyobb kapcsolatkészlet mérete módosítható.

    Nyissa meg a limits.conf fájlt:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    A következő sorokat hozzáadása vagy módosítása:

    ```
    * - nofile 100000
    ```

12. **Natív SSL-megvalósítást használ netty** Netty OpenSSL közvetlenül az SSL-megvalósítási verem használható a jobb teljesítmény érdekében. Ennek hiányában konfigurációs netty visszaáll a Java tartozó alapértelmezett SSL-megvalósításához.

    az Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    és a következő függőség felvétele a projektet maven függőségek:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

A más platformok (Redhat, Windows, Mac, stb.) tekintse meg ezeket az utasításokat https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexelési házirend
 
1. **Nem használt elérési utak kizárása a gyorsabb írási az indexelő**

    Azure Cosmos-adatbázis indexelési házirendet lehetővé teszi, hogy mely dokumentum elérési belefoglalása / kizárása indexelő indexelő elérési utakon található (setIncludedPaths setExcludedPaths) használatával történő megadását. Indexelő elérési utak használatát is kínál a javított teljesítménye és alacsonyabb index tárolási forgatókönyvek, ahol a lekérdezési mintáknak előzetesen ismert, mivel az indexelő költségek közvetlenül indexelt egyedi elérési utak száma közötti kapcsolatot.  Például a következő kód bemutatja, hogyan egy teljes szakasz a dokumentumok (más néven kizárása egy részfája) indexelési használja a "*" helyettesítő karakter.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    További információkért lásd: [Azure Cosmos DB házirendek indexelő](indexing-policies.md).

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

1. **Mérését meg és hangolható alacsonyabb kérelem egység/másodperc kihasználtsága**

    Azure Cosmos-adatbázis adatbázis-műveleteket, köztük a felhasználó által megadott függvények, tárolt eljárások és eseményindítók – összes működési egy adatbázis-gyűjteményben lévő dokumentumokon hierarchikus és relációs lekérdezések széles skáláját kínál. E műveletek költsége attól függően változik, a CPU IO és memória a művelet befejezéséhez szükséges. Továbbléphetnek és hardver-erőforrások kezelése helyett tulajdonképpen egy kérelem egységet (RU) egyetlen intézkedésként az adatbázis különböző műveleteket végezhet, és az alkalmazás kérelem kiszolgálásához szükséges erőforrásokhoz.

    Átviteli sebesség ki van építve a száma alapján [egységek kérelem](request-units.md) minden egyes tároló beállítása. Kérelem egység fogyasztás másodpercenkénti történik. Alkalmazások, amelyek mérete meghaladja a kiépített kérelmek egység aránya a tároló korlátozva, amíg a sebesség esik ahhoz a tárolóhoz kiosztott szint alatt. Ha az alkalmazás egy magasabb szintű teljesítmény, a további kérelemegység kiépítés is megnövelheti az átviteli sebesség. 

    A lekérdezés összetettsége hatással van a kérelem egységek művelet végrehajtásánál. Predikátumok a száma, a predikátum, felhasználó által megadott függvények száma és a forrás adatkészlet összes mérete befolyásolja a lekérdezési műveletek költségét.

    A terhelést növelni az összes műveletet mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg a [x-ms-kérelem-kell fizetni](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ezeket a műveleteket által felhasznált kérelem egységek számának mérésére fejléc. Is megtalálhatja a egyenértékű RequestCharge tulajdonságot az ResourceResponse<T> vagy FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```             

    A kérelem kell fizetni vissza ezt a fejlécet a létesített átviteli sebesség részét. Például ha 2000 RU/mp kiépített, és ha az előző lekérdezés függvény 1000 1KB-dokumentumok, a költség, a művelet 1000. Ilyen belül egy második, a kiszolgáló eleget tegyen csak két ilyen kérelmeket előtt szabályozás későbbi kérelmeket. További információkért lásd: [egységek kérelem](request-units.md) és a [kérelem egység Számológép](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Kezeli a sebesség korlátozása/kérelmek aránya túl nagy**

    Amikor az ügyfél meghaladja a fenntartott átviteli sebesség egy olyan fiók, nincs teljesítmény csökkenése nélkül működhet a kiszolgálón, és felül a fenntartott átviteli sebesség nincs használatban. A kiszolgáló megelőző jelleggel a RequestRateTooLarge (HTTP-állapotkód: 429) kérelem befejezése és térjen vissza a [x-ms-újrapróbálkozási-után-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) idő ezredmásodpercben, amely a felhasználó kell várnia, mielőtt megoldódhat jelző fejléc a kérelmet.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k minden implicit módon dolgozza fel ezt a választ, tiszteletben tartják a kiszolgáló által megadott újrapróbálkozási után fejlécet, és próbálkozzon újra a kéréssel. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozási sikeres lesz.

    Ha egynél több ügyfél felett a kérelmek aránya következetesen összesítve működő, az alapértelmezett újrapróbálkozások száma pillanatnyilag beállított 9 belső az ügyfél nem lehetséges, hogy elegendő; Ebben az esetben az ügyfél egy DocumentClientException állapotkóddal 429 jelez az alkalmazást. ConnectionPolicy példányon setRetryOptions használatával módosíthatja az alapértelmezett újrapróbálkozások maximális számát. Alapértelmezés szerint a DocumentClientException 429. állapotkód: esetén visszaadott 30 másodperc halmozódó várakozási idő után a kérelem folytatja a működést a kérelmek aránya fent. Ez akkor fordul elő, még ha az aktuális újrapróbálkozások maximális számát nem éri el az újrapróbálkozások maximális száma, hogy azt az alapértelmezett 9 vagy egy felhasználó által definiált értéket.

    Az automatizált újrapróbálkozásra segít a rugalmasság és a legtöbb alkalmazás használhatóság javítása érdekében, miközben, előfordulhat, hogy térjen bármikor odds teljesítménymutatókat, során, különösen akkor, ha a késleltetés mérése.  Az ügyfél-megfigyelt késés fog lefoglalását, ha a kísérlet a kiszolgáló szabályozásának találatok és következtében az ügyfél SDK használatával beavatkozás nélkül próbálja meg újra. Teljesítmény kísérletek során késleltetés igényeiben jelentkező elkerüléséhez mérjük a díj minden művelet által visszaadott, és győződjön meg arról, hogy a kérelmek működnek-e a fenntartott kérelmek aránya alatt. További információkért lásd: [egységek kérelem](request-units.md).
3. **Nagyobb átviteli sebesség eléréséhez kisebb dokumentumok kialakítása**

    A kérelem költség (a kérelem feldolgozása költség) egy adott művelet közvetlenül visszamenőleges korrelációban állnak a dokumentum méretét. Műveletek nagy dokumentumok drágább, mint műveletek kis dokumentumokhoz.

## <a name="next-steps"></a>További lépések
Az alkalmazás a méretezés és magas teljesítménnyel megtervezésével kapcsolatos további tudnivalókért lásd: [particionálás és az Azure Cosmos Adatbázisba skálázás](partition-data.md).
