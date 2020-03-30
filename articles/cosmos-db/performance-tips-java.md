---
title: Az Azure Cosmos DB teljesítménytippjei Java-hoz
description: Az Azure Cosmos adatbázisteljesítményének javításához ügyfélkonfigurációs lehetőségek megismerése
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 3b7d221c2afc952f40da035c6e2c282b3b932aa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69616761"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Az Azure Cosmos DB és a Java teljesítményével kapcsolatos tippek

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Az Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen skálázható a garantált késéssel és átviteli kapcsolattal. Nem kell jelentős architektúra-módosításokat végrehajtania, vagy összetett kódot írnia az adatbázis azure Cosmos DB-vel való méretezéséhez. Fel- és leskálázás a könnyű, mint egyetlen API-hívás. További információ: [a tárolóátviteli-ék kiépítése](how-to-provision-container-throughput.md) vagy [az adatbázis-átviteli átbocsátás kiépítése.](how-to-provision-database-throughput.md) Mivel azonban az Azure Cosmos DB hálózati hívásokon keresztül érhető el, ügyféloldali optimalizálások érhetők el az [SQL Java SDK](documentdb-sdk-java.md)használata kori csúcsteljesítmény elérése érdekében.

Ha azt kérdezi: "Hogyan javíthatom az adatbázis teljesítményét?" fontolja meg a következő lehetőségeket:

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

1. **Csatlakozási mód: Közvetlen https használata**

    Az ügyfél Azure Cosmos DB-hez való csatlakozásának módja jelentősen befolyásolja a teljesítményt, főként a megfigyelt ügyféloldali késés szempontjából. Az ügyfél [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) konfigurálására egy kulcskonfigurációs beállítás áll rendelkezésre: a [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  A két rendelkezésre álló ConnectionModes a következő:

   1. [Átjáró (alapértelmezett)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [Közvetlenhttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      Az átjárómód minden SDK-platformon támogatott, és ez a konfigurált alapértelmezett beállítás.  Ha az alkalmazás fut egy vállalati hálózaton szigorú tűzfalkorlátozások, gateway a legjobb választás, mivel a szabványos HTTPS-port ot és egyetlen végpontot használ. A teljesítmény kompromisszumot, azonban az, hogy a Gateway mód egy további hálózati ugrás minden alkalommal, amikor az adatok olvasása vagy írása az Azure Cosmos DB. Emiatt a DirectHttps mód jobb teljesítményt nyújt a kevesebb hálózati ugrás miatt. 

      A Java SDK https protokollt használ átviteli protokollként. A HTTPS SSL-t használ a kezdeti hitelesítéshez és a forgalom titkosításához. A Java SDK használatakor csak a 443-as HTTPS-portnak kell nyitva lennie. 

      A ConnectionMode a DocumentClient példány létrehozása során van konfigurálva a ConnectionPolicy paraméterrel. 

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

      ![Az Azure Cosmos DB kapcsolati szabályzatának illusztrációja](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Az ügyfeleket ugyanabban az Azure-régióban helyezze el a jobb teljesítmény érdekében**

    Ha lehetséges, helyezzen el az Azure Cosmos DB-t hívó alkalmazásokat ugyanabban a régióban, mint az Azure Cosmos-adatbázist. A hozzávetőleges összehasonlítás érdekében az Azure Cosmos DB-hez való hívások ugyanabban a régióban 1–2 ms-on belül befejeződnek, de az USA nyugati és keleti partja közötti késés 50 ms >. Ez a késés valószínűleg kérésről kérelemre változhat a kérelem által megtett útvonaltól függően, ahogy az ügyfélről az Azure adatközpont határára kerül. A lehető legalacsonyabb késés érhető el annak biztosításával, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB-végpont. Az elérhető régiók listáját az [Azure Regions (Azure Regions) (Azure Regions) (Azure Regions) (Azure Regions) (Azure Regions) (Azure Regions) (Az Azure-](https://azure.microsoft.com/regions/#services)

    ![Az Azure Cosmos DB kapcsolati szabályzatának illusztrációja](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-használat
1. **A legújabb SDK telepítése**

    Az Azure Cosmos DB SDK-k folyamatosan fejlesztik, hogy a legjobb teljesítményt. Tekintse meg az [Azure Cosmos DB SDK-lapok](documentdb-sdk-java.md) a legújabb SDK meghatározásához és tekintse át a fejlesztéseket.
2. **Egyetlen Azure Cosmos DB-ügyfél használata az alkalmazás teljes élettartama alatt**

    Minden [DocumentClient-példány](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) szálbiztos, és hatékony kapcsolatkezelést és címgyorsítótárazást hajt végre, ha közvetlen módban működik. A hatékony kapcsolatkezelés és a DocumentClient jobb teljesítményének lehetővé tétele érdekében ajánlott a DocumentClient alkalmazásonkénti egyetlen példányát használni az alkalmazás teljes élettartama alatt.

   <a id="max-connection"></a>
3. **A MaxPoolSize növelése állomásonként átjáró módban**

    Az Azure Cosmos DB-kérelmek HTTPS/REST protokollon keresztül történnek az átjáró módban, és állomásnévvagy IP-cím szerint az alapértelmezett kapcsolatkorlát nak vannak kitéve. Előfordulhat, hogy a MaxPoolSize-ot magasabb értékre (200-1000) kell beállítania, hogy az ügyfélkódtár több egyidejű kapcsolatot használhasson az Azure Cosmos DB-hez. A Java SDK-ban a [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) alapértelmezett értéke 100. Az érték módosításához használja a [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) értéket.

4. **Particionált lekérdezések finomhangolása particionált gyűjtemények**

    Az Azure Cosmos DB SQL Java SDK 1.9.0-s és újabb verziója támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemény párhuzamos lekérdezését. További információ: Az SDK-kkal való munkához kapcsolódó [kódminták.](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) A párhuzamos lekérdezések célja, hogy javítsa a lekérdezés késését és átviteli képességét a soros megfelelője felett.

    (a) ***Tuning setMaxDegreeOfParallelism\: *** Párhuzamos lekérdezések párhuzamos lekérdezése több partíciópárhuzamos lekérdezésével. Az egyes particionált gyűjteményből származó adatok azonban a lekérdezéshez képest sorozatosan lekérésre kerül. Így használja [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) a partíciók számának beállításához, amely a legnagyobb eséllyel éri el a legtöbb teljesítményű lekérdezés, feltéve, hogy az összes többi rendszerfeltételek változatlanok maradnak. Ha nem ismeri a partíciók számát, a setMaxDegreeOfParallelism segítségével nagy számot állíthat be, és a rendszer a minimális (partíciók száma, felhasználói bevitel) a maximális párhuzamosság mértékét választja. 

    Fontos megjegyezni, hogy a párhuzamos lekérdezések a legjobb előnyökkel jár, ha az adatok egyenletesen oszlanak el az összes partíciót a lekérdezés tekintetében. Ha a particionált gyűjtemény particionált oly módon, hogy az összes vagy a lekérdezés által visszaadott adatok többsége koncentrálódik néhány partíciót (egy partíció a legrosszabb esetben), majd a lekérdezés teljesítményét lenne szűk keresztmetszetű az adott partíciókat.

    (b) ***Hangolás setMaxBufferedItemCount\: *** Párhuzamos lekérdezés célja, hogy előre lehívása eredmények, miközben az aktuális tétel az eredmények feldolgozása alatt az ügyfél. Az előzetes lekérés segít a lekérdezés általános késésének javításában. A setMaxBufferedItemCount korlátozza az előre lekért eredmények számát. Ha [a setMaxBufferedItemCount értéket](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) a várt számú eredményre (vagy nagyobb számra) állítja be, ez lehetővé teszi, hogy a lekérdezés maximális hasznot kapjon az előlekérésből.

    Az előolvasás ugyanúgy működik, függetlenül a MaxDegreeOfParallelism-től, és az összes partícióadataihoz egyetlen puffer van.  

5. **Visszalépés megvalósítása getRetryAfterInMilliseconds időközönként**

    A teljesítménytesztelés során növelnie kell a terhelést, amíg a kérelmek kis mértékű szabályozása meg nem szabályozva lesz. Ha szabályozott, az ügyfélalkalmazás nak vissza kell lépnie a szabályozása a kiszolgáló által megadott újrapróbálkozási időközhöz. A visszalépés tiszteletben tartása biztosítja, hogy minimális időt töltsön az újrapróbálkozások közötti várakozással. Az Újrapróbálkozási házirend támogatása a [Java SDK](documentdb-sdk-java.md)1.8.0-s és újabb verziójában található. További információ: [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Az ügyfél-munkaterhelés horizontális felskálázása**

    Ha nagy átviteli sebességű (>50 000 RU/s) sebességgel tesztel, az ügyfélalkalmazás szűk keresztmetszetté válhat a processzor vagy a hálózati kihasználtság miatt. Ha eléri ezt a pontot, továbbra is leküldéses az Azure Cosmos DB-fiók további horizontális felskálázása az ügyfélalkalmazások több kiszolgálók között.

7. **Névalapú címzés használata**

    Használjon névalapú címzést, `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`ahol a hivatkozások\_formátuma a SelfLinks (self) helyett található, amelyek formátuma `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` elkerülhető a hivatkozás létrehozásához használt összes erőforrás ResourceIds beolvasása. Is, mivel ezek az erőforrások kap újra (esetleg azonos nevű), cache ezek nem segít.

   <a id="tune-page-size"></a>
8. **A jobb teljesítmény érdekében a lekérdezések/etetők olvasása oldalméretének finomhangolása**

    Ha olvasási hírcsatorna-funkcióval (például [readDocuments)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)vagy SQL-lekérdezés kiadásakor tömegesen olvassa be a dokumentumokat, az eredmények szegmentált módon kerülnek visszaadásra, ha az eredményhalmaz túl nagy. Alapértelmezés szerint az eredmények 100 elemből vagy 1 MB-os adattömbökből adják vissza az eredményeket, attól függően, hogy melyik korlát ot érinti először.

    Az összes vonatkozó eredmény lekéréséhez szükséges hálózati átutazások számának csökkentése érdekében az [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) kérésfejléc használatával akár 1000-re is növelheti az oldalméretet. Azokban az esetekben, ahol csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az alkalmazás API-ja csak 10 eredményt ad vissza egy időben, az oldalméretet is csökkentheti 10-re az olvasások és lekérdezések során felhasznált átviteli érték csökkentése érdekében.

    Az oldalméretet a [setPageSize metódussal is beállíthatja.](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize)

## <a name="indexing-policy"></a>Indexelési szabályzat
 
1. **Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

    Az Azure Cosmos DB indexelési szabályzata lehetővé teszi, hogy meghatározza, hogy mely dokumentumelérési utakat kell belefoglalni vagy kizárni az indexelésből az indexelési útvonalak[(setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) és [setExcludedPaths)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)használatával. Az indexelési útvonalak használata jobb írási teljesítményt és alacsonyabb indextárolási adatokat kínálhat olyan esetekben, amelyekben a lekérdezési minták előre ismertek, mivel az indexelési költségek közvetlenül korrelálnak az indexelt egyedi elérési utak számával.  A következő kód például bemutatja, hogyan zárhatja ki a dokumentumok egy teljes szakaszát (más néven a dokumentumokat. részfa) a "*" helyettesítő karakter használatával történő indexelésből.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    További információ: [Azure Cosmos DB indexelési szabályzatok.](indexing-policies.md)

## <a name="throughput"></a>Teljesítmény
<a id="measure-rus"></a>

1. **Kisebb kérési egységek/másodperchasználat mérése és hangolása**

    Az Azure Cosmos DB adatbázis-műveletek széles körű készletét kínálja, beleértve az UDF-ekkel, tárolt eljárásokkal és eseményindítókkal kapcsolatos relációs és hierarchikus lekérdezéseket – az adatbázis-gyűjteményben lévő dokumentumokon működő összes. Az egyes ilyen műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardvererőforrások átgondolása és kezelése helyett a kérelemegység (RU) egyetlen mértékként is elgondolkodhat a különböző adatbázis-műveletek végrehajtásához és az alkalmazáskérelmek kiszolgálásához szükséges erőforrásokhoz.

    Átviteli átviteli van kiépítve az egyes [tárolókhoz](request-units.md) beállított kérelemegységek száma alapján. A kérelem egységfelhasználása másodpercenkénti díjként lesz kiértékelve. Alkalmazások, amelyek meghaladják a kiosztott kérelem egységdíj a tároló korlátozott, amíg a sebesség alá csökken a kiosztott szint a tároló. Ha az alkalmazás magasabb átviteli szintet igényel, növelheti az átviteli kapacitást további kérelemegységek kiépítésével. 

    A lekérdezés összetettsége hatással van arra, hogy hány kérelemegység et használ fel egy művelethez. A predikátumok száma, a predikátumok jellege, az UDF-ek száma és a forrásadatkészlet mérete mind befolyásolják a lekérdezési műveletek költségét.

    Bármely művelet (létrehozás, frissítés vagy törlés) többletterhelésének méréséhez vizsgálja meg az [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet (vagy a [Forrásválasz\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) vagy a [FeedResponse\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) megfelelő RequestCharge tulajdonságot a műveletek által felhasznált kérelemegységek számának mérésére.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    A fejlécben visszaadott kérelemdíj a kiosztott átviteli igény töredéke. Ha például 2000 RU/s kivan építve, és az előző lekérdezés 1000 1 KB-os bizonylatokat ad vissza, a művelet költsége 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést tart tiszteletben, mielőtt korlátozza a későbbi kérelmek et. További információ: [Egységek kérése](request-units.md) és a [kérelemegység-kalkulátor.](https://www.documentdb.com/capacityplanner)
   <a id="429"></a>
1. **A sebességkorlátozás/kérelem sebességének lehívása túl nagy**

    Ha egy ügyfél megpróbálja túllépni egy fiók fenntartott átviteli teljesítményét, a kiszolgálón nincs teljesítménycsökkenés, és a fenntartott szinten túl nincs átviteli kapacitás. A kiszolgáló a requestet megelőzően a RequestRateTooLarge (429-es HTTP-állapotkód) segítségével befejezi, és visszaadja az [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet, amely azt az időt jelzi , ezredmásodpercben, amíg a felhasználónak várnia kell a kérelem újbóli megkísérlése előtt.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k implicit módon elkapják ezt a választ, tiszteletben tartják a kiszolgáló által megadott újrapróbálkozási fejlécet, és újrapróbálkoznak a kéréssel. Ha csak akkor éri el fiókját, hogy több ügyfél egyidejűleg fér hozzá, a következő újrapróbálkozás sikeres lesz.

    Ha egynél több ügyfél összesítve működik következetesen meghaladja a kérelem aránya, az alapértelmezett újrapróbálkozások száma jelenleg 9 belsőleg az ügyfél nem elegendő; ebben az esetben az ügyfél egy [429-es állapotkódú DocumentClientException-et](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) dob az alkalmazásba. Az alapértelmezett újrapróbálkozások száma a [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) példány [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) parancsával módosítható. Alapértelmezés szerint a DocumentClientException állapotkód 429-es állapotkóddal 30 másodperces összesített várakozási idő után kerül vissza, ha a kérelem továbbra is a kérelemsebesség felett működik. Ez akkor is előfordul, ha az aktuális újrapróbálkozások száma kisebb, mint a maximális újrapróbálkozások száma, legyen az az alapértelmezett 9 vagy egy felhasználó által definiált érték.

    Bár az automatikus újrapróbálkozási viselkedés segít javítani a rugalmasságot és a használhatóságot a legtöbb alkalmazás számára, előfordulhat, hogy ellentétes a teljesítmény-referenciaértékek, különösen a késés mérése során.  Az ügyfél által megfigyelt késés kiugrik, ha a kísérlet eléri a kiszolgáló szabályozási, és az ügyfél SDK-t némán újrapróbálkozik. A teljesítménykísérletek során a késés kiugrásának elkerülése érdekében mérje meg az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelemdíj alatt működnek. További információ: [Request units](request-units.md).
3. **Tervezés kisebb dokumentumokhoz a nagyobb átaputlékért**

    A kérelem díj (a kérelem feldolgozási költség) egy adott művelet közvetlenül korrelál a dokumentum méretét. A nagy dokumentumokon végzett műveletek többe kerülnek, mint a kis dokumentumok műveletei.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű tervezéséről, olvassa el [a Particionálás és méretezés az Azure Cosmos DB-ben című témakört.](partition-data.md)
