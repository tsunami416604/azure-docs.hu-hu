---
title: Javához készült Azure Cosmos DB teljesítmény tippek |} Microsoft Docs
description: Ismerje meg az ügyfél-konfigurációs beállítások Azure Cosmos DB adatbázis teljesítményének javítása érdekében
keywords: adatbázis teljesítményének javításával
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: dfe8f426-3c98-4edc-8094-092d41f2795e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: sngun
ms.openlocfilehash: ace817bc7a703ed2aa1dcd71f7d84f91ee16cce6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Teljesítmény tippek az Azure Cosmos DB és Java
Azure Cosmos-adatbázis egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezi, garantált késéssel és átviteli sebesség. Nincs a fő architektúra módosításokat, vagy írja meg az adatbázist az Azure Cosmos DB méretezési komplex kódot. Felfelé és lefelé skálázás be lehető legkönnyebben egyetlen API-hívás vagy [SDK metódus hívása](set-throughput.md#set-throughput-java). Azonban mivel Azure Cosmos DB hálózati hívások keresztül érhető el nincsenek ügyféloldali optimalizálása, hogy használata esetén a csúcsteljesítmény eléréséhez a [SQL Java SDK](documentdb-sdk-java.md).

Ezért ha még kérése "Hogyan javítható az adatbázis teljesítménye?" Vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

1. **Csatlakozási mód: használata DirectHttps**

    Hogyan ügyfél csatlakozik-e Azure Cosmos DB rendelkezik fontos gyakorolt hatása a teljesítményre, különös tekintettel az megfigyelt ügyféloldali késés. Több kulcs konfiguráció számára, hogy az ügyfél konfigurálása [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) – a [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  A két rendelkezésre álló ConnectionModes a következők:

   1. [Átjáró (alapértelmezett)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.gateway)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.directhttps)

    Átjáró mód minden SDK-platformon támogatott, és a beállított alapértelmezett.  Ha az alkalmazás fut a vállalati hálózatról szigorú tűzfal korlátozások, átjáróra a legjobb választás, mivel a szabványos HTTPS-port és egy végpontot. A teljesítmény kompromisszumot azonban, hogy az átjáró mód érint egy további hálózati ugrások minden alkalommal, amikor adatait olvasni vagy írni az Azure Cosmos DB. Emiatt a DirectHttps mód miatt kevesebb hálózati ugrásokat jobb teljesítményt nyújt. 

    A Java SDK-t használja a HTTPS átviteli protokollként. HTTPS kezdeti hitelesítésre és a forgalom titkosításának használja az SSL. A Java SDK használata esetén csak HTTPS-port 443-as kell nyitva kell lennie. 

    A ConnectionMode úgy van beállítva, a ConnectionPolicy paraméterrel DocumentClient példányának létrehozása során. 

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

    ![Az Azure Cosmos DB kapcsolatkezelési házirendet ábrája](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **A teljesítmény azonos Azure régióban ügyfelek elhelyezésének engedélyezése**

    Ha lehetséges, helyezze el az alkalmazásokat, Azure Cosmos DB hívja az Azure Cosmos DB adatbázis ugyanabban a régióban. Hozzávetőleges összehasonlításhoz hívások Azure Cosmos DB belül ugyanabban a régióban 1-2 ms belül befejeződik, de nyugati és az Egyesült Államok, keleti part közötti késés > 50 ms. Ez a késés valószínűleg eltérőek lehetnek kérelem kérelem attól függően, hogy az útvonal kérés hajtja végre, mivel az ügyfél az Azure-adatközpontban határ adja át. A lehető legkisebb késleltetést érhető el, biztosítva, hogy a hívó alkalmazás helyen belüli és a kiépített Azure Cosmos DB végpont Azure ugyanabban a régióban. Elérhető régiók listáját lásd: [Azure-régiókat](https://azure.microsoft.com/regions/#services).

    ![Az Azure Cosmos DB kapcsolatkezelési házirendet ábrája](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK használata
1. **A legutóbbi SDK telepítése**

    Az Azure Cosmos DB SDK-k a rendszer folyamatosan javult a legjobb teljesítmény elérése érdekében. Tekintse meg a [Azure Cosmos DB SDK](documentdb-sdk-java.md) lapok használatával állapítsa meg a legutóbbi SDK, és tekintse át a fejlesztései.
2. **Az alkalmazás teljes egypéldányos Azure Cosmos DB ügyfél használata**

    Minden egyes [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) példány szálbiztos, végrehajtja a hatékony kapcsolat kezelése és a címek gyorsítótárazása, ha közvetlen módban működik. Engedélyezése hatékony kapcsolat kezelése és DocumentClient által jobb teljesítmény érdekében javasoljuk, hogy egyetlen példány futhat egy AppDomain DocumentClient használni az alkalmazás élettartamának.

   <a id="max-connection"></a>
3. **Növelje az MaxPoolSize értékénél állomásonként átjáró üzemmódban**

    Az Azure Cosmos DB kérelmek átjáró üzemmódban HTTPS/REST keresztül történik, és az alapértelmezett kapcsolati felső határ az egyes állomásnév vagy IP-cím kitéve. Szükség lehet ahhoz a MaxPoolSize értékénél (200-1000) magasabb értékre, hogy az ügyféloldali kódtár nyújthatnak Azure Cosmos DB több egyidejű kapcsolatok. A Java SDK-ban, az alapértelmezett érték [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.getmaxpoolsize) 100. Használjon [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) módosítsa az értéket.

4. **A particionált gyűjtemények párhuzamos lekérdezések hangolása**

    Azure Cosmos DB SQL Java SDK 1.9.0 verzió vagy újabb támogatási párhuzamos lekérdezések, amelyek lehetővé teszik egy particionált gyűjtemény párhuzamos lekérdezés (lásd: [az SDK-k használata](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) és a kapcsolódó [Kódminták](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) a További információ:). Párhuzamos lekérdezések úgy tervezték, hogy a soros megfelelőjére javítja a lekérdezés-késleltetés és a teljesítményt.

    (a) ***setMaxDegreeOfParallelism hangolása\:***  párhuzamos lekérdezések munkahelyi több partíció párhuzamosan lekérdezésével. Azonban az egyes particionált gyűjtemény adatait beolvassa Feladattervek tekintetében a lekérdezést. Így, [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) beállítása a partíciók elérése érdekében a legtöbb performant lekérdezést, a maximális esélyét rendelkező megadott egyéb rendszer feltételek változatlanok maradnak. Ha nem ismeri a partíciók számának, setMaxDegreeOfParallelism segítségével állítsa be a magas érték, és a rendszer a minimális (számú partíciót, felhasználó által megadott bemeneti) választja, a legnagyobb mértékben párhuzamossági. 

    Fontos megjegyezni, hogy párhuzamos lekérdezések eredményez-e a legjobb előnyöket, ha az adatok tekintetében a lekérdezés összes partíciójára egyenletesen vannak elosztva. Ha a particionált gyűjtemény particionálva van-e olyan módon, hogy néhány partíciók (egy partíciót a legrosszabb esetben) az összes vagy egy lekérdezés által visszaadott adatok többsége van koncentrált, majd a lekérdezés teljesítményét volna kell szűk keresztmetszetét adja ki ezeket a partíció.

    (b) ***setMaxBufferedItemCount hangolása\:***  párhuzamos lekérdezések arra tervezték, hogy előre fetch eredmények, amíg a jelenlegi köteg eredményeinek feldolgozása folyamatban van az ügyfél által. A lekérdezések teljes késést fokozása lehívását segít. setMaxBufferedItemCount előre lehívott eredmények számának korlátozása. Úgy, hogy [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) a várt számú eredményt adott vissza (vagy magasabb azonosítószámot), ez lehetővé teszi, hogy a lekérdezés maximális haszna nem származik, a lehívását.

    Lehívását függetlenül a MaxDegreeOfParallelism ugyanúgy működik, és minden olyan partíciónak az adatait az egyetlen puffert.  

5. **Leállítási getRetryAfterInMilliseconds időközönként megvalósítása**

    Teljesítmény tesztelés során terhelés csak a kis mértékben kérelmek get halmozódni növelje meg. Ha szabályozva, az ügyfélalkalmazás kell a késleltetési leállítási a kiszolgáló által megadott újrapróbálkozási időköz. A leállítási tiszteletben biztosítja, hogy az újrapróbálkozások közötti várakozási idő a lehető legrövidebb idő. Újrapróbálkozási házirend támogatása a rendszer része, a verzió 1.8.0 és az újabb, a [Java SDK](documentdb-sdk-java.md). További információkért lásd: [Exceeding fenntartott átviteli sebességének korlátai](request-units.md#RequestRateTooLarge) és [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).
6. **Horizontális felskálázás az ügyfél-alkalmazások és szolgáltatások**

    Ha nagy átviteli szinten teszteli (> 50 000 RU/mp), az ügyfélalkalmazás gép határértékét el a Processzor- vagy hálózati kihasználtsága miatt a szűk keresztmetszetek válhat. Ha eléri ezt a pontot, továbbra is az Azure Cosmos DB fiók további leküldéses által az ügyfélalkalmazások kiterjesztése több kiszolgáló között.

7. **Használja a név alapján címzés**

    Használja, névalapú címzés, ahol hivatkozások formátuma a `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, SelfLinks helyett (\_önkiszolgáló), amely formátumuk `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` erőforrás-azonosítók segítségével hozza létre a kapcsolat összes erőforrás beolvasása elkerülése érdekében. Is ezeket az erőforrásokat (valószínűleg azonos nevű) lekérése újból, mert a gyorsítótár nem segíthet.

   <a id="tune-page-size"></a>
8. **A jobb teljesítmény lekérdezések/olvasás hírcsatornák lapmérete hangolása**

    Tömeges végrehajtása olvasás esetén a dokumentumok olvassa el a hírcsatorna funkciók (például [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c) vagy egy SQL-lekérdezést kiadásakor eredményeinek szegmentált módon ha eredménykészlet túl nagy. Alapértelmezés szerint eredményeinek 100 elemet vagy 1 MB adattömböket, bármelyik korlát találati első.

    Adjon meg kevesebb hálózati kerekíteni összes vonatkozó eredmények beolvasásához szükséges való adatváltások számát, az oldal méretét használatával növelheti a [x-ms-maximális elem-darabszám](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) legfeljebb 1000 kérelemfejlécet. Azokban az esetekben, ahol csak néhány eredmények megtekintése céljából kell például a felhasználói felület vagy a kérelem API visszatérési értéke csak 10 egyszerre annak az eredménye, csökkentheti a 10-re csökkenteni a teljesítményt, Olvasás, mind a lekérdezések felhasznált mérete.

    A lap méret használatával is megadhat a [setPageSize metódus](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Indexelési házirend
 
1. **Nem használt elérési utak kizárása a gyorsabb írási az indexelő**

    Azure Cosmos-adatbázis indexelési házirendet lehetővé teszi, hogy mely dokumentum elérési vagy kizárja a indexelő elérési utak, ami indexelésének megadását ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) és [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). Indexelő elérési utak használatát is kínál a javított teljesítménye és alacsonyabb index tárolási forgatókönyvek, ahol a lekérdezési mintáknak előzetesen ismert, mivel az indexelő költségek közvetlenül indexelt egyedi elérési utak száma közötti kapcsolatot.  Például a következő kód bemutatja, hogyan egy teljes szakasz a dokumentumok (más néven kizárása egy részfája) indexelési használja a "*" helyettesítő karakter.

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

    A terhelést növelni az összes műveletet mérésére (létrehozása, frissítése vagy törlése), vizsgálja meg a [x-ms-kérelem-kell fizetni](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejléc (vagy a megfelelő RequestCharge tulajdonságot az [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) vagy [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response) ezeket a műveleteket által felhasznált kérelem egységek számának mérésére.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    A kérelem kell fizetni vissza ezt a fejlécet a létesített átviteli sebesség részét. Például ha 2000 RU/mp kiépített, és ha az előző lekérdezés függvény 1000 1KB-dokumentumok, a költség, a művelet 1000. Ilyen belül egy második, a kiszolgáló eleget tegyen csak két ilyen kérelmeket előtt szabályozás későbbi kérelmeket. További információkért lásd: [egységek kérelem](request-units.md) és a [kérelem egység Számológép](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Kezeli a sebesség korlátozása/kérelmek aránya túl nagy**

    Amikor az ügyfél meghaladja a fenntartott átviteli sebesség egy olyan fiók, nincs teljesítmény csökkenése nélkül működhet a kiszolgálón, és felül a fenntartott átviteli sebesség nincs használatban. A kiszolgáló megelőző jelleggel a RequestRateTooLarge (HTTP-állapotkód: 429) kérelem befejezése és térjen vissza a [x-ms-újrapróbálkozási-után-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) idő ezredmásodpercben, amely a felhasználó kell várnia, mielőtt megoldódhat jelző fejléc a kérelmet.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k minden implicit módon dolgozza fel ezt a választ, tiszteletben tartják a kiszolgáló által megadott újrapróbálkozási után fejlécet, és próbálkozzon újra a kéréssel. Kivéve, ha a fiók több ügyfélnek egyszerre használja, a következő újrapróbálkozási sikeres lesz.

    Ha egynél több ügyfél felett a kérelmek aránya következetesen összesítve működő, az alapértelmezett újrapróbálkozások száma pillanatnyilag beállított 9 belső az ügyfél nem lehetséges, hogy elegendő; Ebben az esetben az ügyfél jelez a [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) állapotú code 429 az alkalmazáshoz. Használatával módosíthatja az alapértelmezett újrapróbálkozások maximális számát [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) a a [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy) példány. Alapértelmezés szerint a DocumentClientException 429. állapotkód: esetén visszaadott 30 másodperc halmozódó várakozási idő után a kérelem folytatja a működést a kérelmek aránya fent. Ez akkor fordul elő, még ha az aktuális újrapróbálkozások maximális számát nem éri el az újrapróbálkozások maximális száma, hogy azt az alapértelmezett 9 vagy egy felhasználó által definiált értéket.

    Az automatizált újrapróbálkozásra segít a rugalmasság és a legtöbb alkalmazás használhatóság javítása érdekében, miközben, előfordulhat, hogy térjen bármikor odds teljesítménymutatókat, során, különösen akkor, ha a késleltetés mérése.  Az ügyfél-megfigyelt késés fog lefoglalását, ha a kísérlet a kiszolgáló szabályozásának találatok és következtében az ügyfél SDK használatával beavatkozás nélkül próbálja meg újra. Teljesítmény kísérletek során késleltetés igényeiben jelentkező elkerüléséhez mérjük a díj minden művelet által visszaadott, és győződjön meg arról, hogy a kérelmek működnek-e a fenntartott kérelmek aránya alatt. További információkért lásd: [egységek kérelem](request-units.md).
3. **Nagyobb átviteli sebesség eléréséhez kisebb dokumentumok kialakítása**

    A kérelem költség (a kérelem feldolgozása költség) egy adott művelet közvetlenül visszamenőleges korrelációban állnak a dokumentum méretét. Műveletek nagy dokumentumok drágább, mint műveletek kis dokumentumokhoz.

## <a name="next-steps"></a>További lépések
Az alkalmazás a méretezés és magas teljesítménnyel megtervezésével kapcsolatos további tudnivalókért lásd: [particionálás és az Azure Cosmos Adatbázisba skálázás](partition-data.md).
