---
title: Az Azure Cosmos DB teljesítménnyel kapcsolatos tippek Javához |} A Microsoft Docs
description: Ismerje meg az ügyfél-konfigurációs beállításokat az Azure Cosmos DB adatbázis-teljesítmény javítása
keywords: adatbázis teljesítményének növelése
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: sngun
ms.openlocfilehash: 0a2bd840c4e93755988cf1638a6c0bdcb6b6207d
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696380"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Az Azure Cosmos DB és a javát a teljesítménnyel kapcsolatos tippek

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Az Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, teljesítmény és a késés garantált az zökkenőmentesen méretezhető. Nem rendelkezik architektúra jelentős módosításokat, vagy az adatbázis az Azure Cosmos DB méretezése összetett programkód írása. Felfelé és lefelé skálázás nem kell csak egyetlen API-hívás vagy [SDK metódushívás](set-throughput.md#set-throughput-java). Azonban mivel a Azure Cosmos DB a hálózati hívások segítségével érhető el vannak ügyféloldali optimalizálást is végezhet használata esetén a csúcsteljesítmény érhet el a [SQL Java SDK](documentdb-sdk-java.md).

Így ha Ön kérő "Hogyan javíthatom adatbázis teljesítmény?" Vegye figyelembe a következő beállításokat:

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

1. **Kapcsolat módja: használatra DirectHttps**

    Hogyan ügyfél csatlakozik-e az Azure Cosmos DB következményeket vonhat fontos a teljesítmény, különösen a megfigyelt ügyféloldali késés szempontjából. Van egy fő konfigurációs beállítás érhető el, az ügyfél beállításával kapcsolatos [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) – a [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  A két rendelkezésre álló ConnectionModes a következők:

   1. [Átjáró (alapértelmezett)](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_mode)
   2. [DirectHttps](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._connection_mode)

    Átjáró mód az összes SDK-platformon támogatott, és a konfigurált alapértelmezett.  Ha az alkalmazás fut a vállalati hálózaton belül szigorú tűzfal korlátozásokkal, az átjáró az a legjobb választás, mivel a szabványos HTTPS-port és a egy végpontot használja. A teljesítményre vonatkozóan kompromisszumot jelent, azonban nem, hogy az átjáró mód érint egy további hálózati Ugrás minden alkalommal, amikor adatokat olvasni vagy írni az Azure Cosmos DB. Emiatt DirectHttps mód miatt kevesebb hálózati útválasztók ugrásainak jobb teljesítményt nyújt. 

    A Java SDK átviteli protokollként HTTPS PROTOKOLLT használ. HTTPS kezdeti hitelesítésre és a forgalom titkosításához használja az SSL. A Java SDK használata esetén csak HTTPS-port 443-as kell nyitva lenniük. 

    A ConnectionMode ConnectionPolicy paramétere a DocumentClient példányának a konstrukció során van konfigurálva. 

    ```Java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
    }
        
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

    ![Az Azure Cosmos DB kapcsolati házirend ábrája](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **A teljesítmény Fájlmegosztáséval azonos régióban lévő ügyfelek elhelyezésének engedélyezése**

    Ha lehetséges, helyezze el az alkalmazásokat Azure Cosmos DB hívja meg az Azure Cosmos DB-adatbázis és ugyanabban a régióban. 1 – 2 ms belül végezze az azonos régión belüli Azure Cosmos DB-hívások hozzávetőleges összehasonlításáért, de a nyugati és keleti parti területei az Egyesült Államok a késés > 50 ms. Ez a késés kérelem kérés valószínűleg függvényében az útvonal, az ügyfél az Azure-adatközpont határt adja át a kérés által készített. A lehető legkisebb késleltetést úgy, hogy a hívó alkalmazás megtalálható az üzembe helyezett Azure Cosmos DB-végpontként az azonos Azure-régióban érhető el. Az elérhető régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/regions/#services).

    ![Az Azure Cosmos DB kapcsolati házirend ábrája](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-használata
1. **A legújabb SDK telepítése**

    Az Azure Cosmos DB SDK kat folyamatosan fejlesztik a lehető legjobb teljesítményt. Tekintse meg a [Azure Cosmos DB SDK](documentdb-sdk-java.md) oldalt, hogy a legújabb SDK határozza meg, és tekintse át a fejlesztései.
2. **Az alkalmazás teljes élettartama egyedülálló Azure Cosmos DB-ügyfél használata**

    Minden egyes [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) példány szálbiztos, hatékony kapcsolat kezelése és a címek gyorsítótárazása, közvetlen üzemmódban hajt végre. Ahhoz, hogy a kapcsolat hatékony kezelése és a DocumentClient jobb teljesítményt, ajánlott az alkalmazás teljes élettartama alkalmazástartományonként DocumentClient egyetlen példányát használja.

   <a id="max-connection"></a>
3. **Növelje gazdagépenként MaxPoolSize értékénél, átjáró mód használatakor**

    Az Azure Cosmos DB kérelmek végrehajtott HTTPS/REST átjáró mód használatakor, és vannak kitéve, állomásnév vagy IP-cím alapértelmezett kapcsolathoz megadott korlátot. Szükség lehet beállítani a MaxPoolSize értékénél (200-1000) értéke, hogy az ügyféloldali kódtár képes használni az Azure Cosmos DB több egyidejű kapcsolatot. A Java SDK, az alapértelmezett érték a [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.getmaxpoolsize) 100. Használat [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) módosítsa az értéket.

4. **Particionált gyűjteményeknél végezzen párhuzamos lekérdezések hangolása**

    Az Azure Cosmos DB SQL Java SDK 1.9.0-s verzió vagy újabb támogatási párhuzamos lekérdezések, amelyek lehetővé teszik egy particionált gyűjteménybe a párhuzamos lekérdezés (lásd: [az SDK-k használata](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) és a kapcsolódó [Kódminták](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) számára További információ). Javítja a Lekérdezések késése és az átviteli sebesség soros többszörözi a párhuzamos lekérdezések lettek kialakítva.

    (a) ***setMaxDegreeOfParallelism hangolása\:***  párhuzamos lekérdezések munkahelyi több partíció párhuzamosan lekérdezésével. Azonban az egyes particionált gyűjteményt származó adatok letöltődött tárolókonfigurációhoz garanciát a lekérdezést. Ezért, a [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) , amely rendelkezik a legnagyobb esélyét jövedelmezőbb munkát tesznek lehetővé, a legtöbb nagy teljesítményű lekérdezési partíciók száma a megadott rendszer összes egyéb feltételek változatlanok maradnak. Ha nem ismeri a partíciók számát, setMaxDegreeOfParallelism segítségével állítsa be a magas érték, és a rendszer a minimális (partíciók, felhasználó által megadott bemeneti száma) választja, a párhuzamosság maximális foka. 

    Fontos megjegyezni, hogy párhuzamos lekérdezések a legjobb előnyöket előállításához, ha az adatok egyenletesen garanciát a lekérdezés összes partíciók között. Ha a particionált gyűjtemény particionált oly módon, hogy az összes vagy egy lekérdezés által visszaadott adatok többsége összpontosul néhány partíciók (egy partíciót a legrosszabb esetben), majd a lekérdezés teljesítményének lenne bottlenecked, ezek a partíciók szerint.

    (b) ***setMaxBufferedItemCount hangolása\:***  párhuzamos lekérdezés úgy tervezték, hogy előre olvashatók be eredményeket, amíg az eredmények az aktuális köteg feldolgozása folyamatban van az ügyfél által. A lekérdezés teljes késés fokozása lehívását segít. setMaxBufferedItemCount korlátozza az előzetesen beolvasott eredményeket. Beállításával [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) adatsorban visszaadott várt száma (vagy nagyobb), ez lehetővé teszi a lekérdezést úgy, hogy fogadjon a legnagyobb előny előre beolvasása.

    Lehívását attól függetlenül, a Maxanalyticsunits ugyanúgy működik, és az összes partíciót az adatok egyetlen puffert.  

5. **Leállítási megvalósítása getRetryAfterInMilliseconds időközönként**

    Teljesítmény tesztelése során terhelés mindaddig, amíg egy kis méretű kérések másodpercenkénti száma leszabályozza növelje meg. Ha szabályozott, az ügyfélalkalmazás kell leállásait, szabályozása az a kiszolgáló által megadott újrapróbálkozási időköz. A leállítási tiszteletben biztosítja, hogy az újrapróbálkozások közötti várakozási idő mennyisége minimális idő. Újrapróbálkozási házirend támogatás is biztosított, a verzió 1.8.0-as és újabb, a [Java SDK](documentdb-sdk-java.md). További információkért lásd: [több mint szolgáltatás számára fenntartott átviteli sebesség korlátok](request-units.md#RequestRateTooLarge) és [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).
6. **Az ügyfél-alkalmazások és szolgáltatások méretezése**

    Ha nagy átviteli sebesség szinten teszteli (> 50 000 RU/s), az ügyfélalkalmazás miatt a gép kapacitástól meg a Processzor vagy a hálózati kihasználtság a szűk keresztmetszetté válhat. Ha eléri ezt a pontot, továbbra is küldje le az Azure Cosmos DB-fiókot további horizontális felskálázása az ügyfélalkalmazásokat, több kiszolgáló között.

7. **Használja a címzési neve alapján**

    Használja, névalapú címzés, ahol hivatkozások formátuma a `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, SelfLinks helyett (\_self), amelyek rendelkeznek a formátum `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` ResourceId segítségével hozza létre a kapcsolat összes erőforrás beolvasása elkerülése érdekében. Is ezeket az erőforrásokat (valószínűleg egy azonos nevű) első újból, mert ezek gyorsítótárazás nem segítségére lehetnek.

   <a id="tune-page-size"></a>
8. **Az oldal méretét a lekérdezések és olvasási hírcsatornák, a jobb teljesítmény hangolása**

    Mikor végez a tömeges olvasása a dokumentumok használatával olvasási hírcsatorna-funkciókat (például [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c) vagy egy SQL-lekérdezést kiadásakor mely visszaadja az eredményeket szegmentált feldolgozza-e az eredményhalmaz túl nagy. Alapértelmezés szerint az eredmény akkor 100 elemet vagy 1 MB-os blokkonként, bármelyik korlát nyomja le az első.

    Kevesebb hálózati kerekíteni lelassítja az összes vonatkozó eredmények beolvasásához szükséges, növelhető a méret használatával a [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) legfeljebb 1000 kérelem fejléce. Azokban az esetekben, ahol csak néhány eredmények megjelenítéséhez szüksége például, ha a felhasználói felület vagy a kérelem API függvény csak 10 eredménye egy idő, is csökkentheti, ha az oldal méretét a 10-re az olvasást és lekérdezések felhasznált átviteli sebesség csökkentése érdekében.

    Az oldal méretét használatával is beállíthatja a [setPageSize metódus](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Indexelési házirend
 
1. **A fel nem használt elérési utak kizárása a gyorsabb írások indexelése**

    Indexelési házirendet az Azure Cosmos DB lehetővé teszi, hogy adja meg, mely a dokumentum elérési útjait belefoglalása vagy kizárása indexelő elérési utak kihasználva indexelő ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) és [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). Indexelő elérési utak használatát is kínál a továbbfejlesztett írási teljesítmény- és alacsonyabb index forgatókönyvekhez, ahol a lekérdezési mintáknak előzetesen ismert, mivel az indexelő költségek közvetlenül indexelt egyedi elérési utak száma közötti kapcsolatot.  Például a következő kód bemutatja, hogyan zárhat ki (más néven a dokumentumok egy teljes szakasz részfa) az indexelő használatával a "*" helyettesítő karakter.

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

    Bármely művelet járó többletterhelést mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg a [x-ms-kérelem-díj](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejléc (vagy ezzel egyenértékű RequestCharge tulajdonsága [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) vagy [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response) , ezek a műveletek által felhasznált kérelemegységek számát.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    A kérelem díja a fejléc adja vissza a kiosztott átviteli sebesség töredéke alatt. Például ha 2000 RU/s kiosztott, és ha az előző lekérdezés 1000 1KB-dokumentumokat ad vissza, a költség, a művelet 1000. Emiatt a belül egy második, a kiszolgáló figyelembe veszi előtt sebességével későbbi kérelmeket csak két ilyen kérelmeket. További információkért lásd: [Kérelemegységek](request-units.md) és a [kérelem egység Számológép](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
1. **Kezeli a sebesség korlátozása/kérelmek arányának túl nagy**

    Amikor az ügyfél meghaladja a fenntartott átviteli sebesség egy olyan fiók, nincs teljesítmény csökkenése nélkül működhet a kiszolgálón, és felül a fenntartott átviteli kapacitás használatának tilalma. A kiszolgáló előrelátó módon RequestRateTooLarge (HTTP-állapotkódot 429-es) a kérelem befejezése, és adja vissza a [x-ms-újrapróbálkozási-után – az ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) jelző idő ezredmásodpercben, amely a felhasználónak várakoznia kell, mielőtt újrapróbálkozni azoknál a fejléc a kérelmet.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k az összes implicit módon ezt a választ a tényleges, a kiszolgáló által megadott retry-after fejléccel tiszteletben, és ismételje meg a kérelmet. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozás sikeres lesz.

    Ha egynél több ügyfél felett a kérések mennyisége következetesen összesítve működő, az alapértelmezett újrapróbálkozások jelenlegi beállítása 9 belsőleg az ügyfél által esetleg nem elegendő; Ebben az esetben az ügyfél jelez egy [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) állapotú code 429-es az alkalmazáshoz. Az alapértelmezett újrapróbálkozások használatával módosítható [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) a a [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) példány. Alapértelmezés szerint a 429-es állapotkód DocumentClientException adja vissza egy halmozódó várakozási idő pedig 30 másodperc után, ha a kérés továbbra is működőképes fent a kérések aránya. Ez akkor fordul elő, még ha az aktuális újrapróbálkozások nem éri el az újrapróbálkozások maximális számát, legyen az alapértelmezett 9 vagy egy felhasználó által definiált értéket.

    Az automatikus újrapróbálkozási viselkedés segít a rugalmasság és a legtöbb alkalmazás használhatóság javítása érdekében, miközben, előfordulhat, hogy származnak, bármikor odds esetén teljesítményteszteket, különösen akkor, amikor a késleltetés mérése.  Az ügyfél-megfigyelt késés fog hirtelen, ha eléri a kiszolgáló szabályozása a kísérletet, és az ügyfél SDK-val csendes módban próbálja újra. Teljesítmény kísérletek során késés ugrásszerűen elkerüléséhez a díjat az egyes műveletek által visszaadott mérjük, és győződjön meg arról, hogy a kérelmek működnek-e alatt a szolgáltatás számára fenntartott kérések mennyisége. További információkért lásd: [Kérelemegységek](request-units.md).
3. **A nagyobb sebesség érdekében kisebb méretű dokumentumokra tervezése**

    A kérelem díja (a kérelem feldolgozása költség) egy adott műveletnek közvetlenül visszamenőleges korrelációban állnak a dokumentum mérete. A nagy dokumentumok Operations drágább, mint a kisméretű dokumentumok műveletek.

## <a name="next-steps"></a>További lépések
A méretezés és a nagy teljesítményű alkalmazás megtervezésével kapcsolatos további tudnivalókért lásd: [particionálás és skálázás az Azure Cosmos DB](partition-data.md).
