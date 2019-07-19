---
title: Azure Cosmos DB teljesítményre vonatkozó tippek a Javához
description: Az ügyfél-konfigurációs beállítások megismerése Azure Cosmos DB adatbázis teljesítményének növeléséhez
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 7d6427db7090b3419fbe67535baeb4a5df6a5d65
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261315"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Teljesítménnyel kapcsolatos tippek Azure Cosmos DB és Javához

> [!div class="op_single_selector"]
> * [Aszinkron Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

A Azure Cosmos DB egy gyors és rugalmas elosztott adatbázis, amely zökkenőmentesen méretezhető a garantált késés és az átviteli sebesség tekintetében. Nem kell megváltoztatnia a jelentős architektúrát, vagy összetett kódot kell írnia az adatbázis méretezéséhez Azure Cosmos DB. A fel-és leskálázás olyan egyszerű, mint egyetlen API-hívás. További információkért lásd: [a tárolók teljesítményének kiépítése](how-to-provision-container-throughput.md) vagy [az adatbázis átviteli sebességének kiépítése](how-to-provision-database-throughput.md). Mivel azonban a Azure Cosmos DB hálózati hívásokon keresztül érhetők el, ügyféloldali optimalizálási lehetőségek érhetők el, amelyekkel elérheti a maximális teljesítményt az [SQL Java SDK](documentdb-sdk-java.md)használatakor.

Tehát ha a "Hogyan javíthatom az adatbázis teljesítményét?" című témakört kérdezi le? vegye figyelembe a következő lehetőségeket:

## <a name="networking"></a>Hálózat
<a id="direct-connection"></a>

1. **Csatlakoztatási mód: DirectHttps használata**

    Az ügyfél Azure Cosmos DBhoz való csatlakozásának módja fontos hatással van a teljesítményre, különösen a megfigyelt ügyféloldali késések tekintetében. Az ügyfél- [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) ( [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)) konfigurálásához egy kulcsfontosságú konfigurációs beállítás áll rendelkezésre.  A két elérhető ConnectionModes a következők:

   1. [Átjáró (alapértelmezett)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      Az átjáró üzemmód minden SDK-platformon támogatott, és a beállított alapértelmezett érték.  Ha az alkalmazása szigorú tűzfal-korlátozásokkal rendelkező vállalati hálózaton belül fut, az átjáró a legjobb választás, mivel a szabványos HTTPS-portot és egyetlen végpontot használ. A teljesítmény-kompromisszum azonban az, hogy az átjáró üzemmód egy további hálózati ugrást is magában foglal minden alkalommal, amikor az összes adat beolvasása vagy írása Azure Cosmos DB. Emiatt a DirectHttps mód jobb teljesítményt nyújt kevesebb hálózati ugrás miatt. 

      A Java SDK a HTTPS protokollt használja átviteli protokollként. A HTTPS SSL-t használ a kezdeti hitelesítéshez és a forgalom titkosításához. A Java SDK használatakor csak a 443-es HTTPS-portot kell megnyitni. 

      A ConnectionMode a DocumentClient-példány építése során, a ConnectionPolicy paraméterrel konfigurálható. 

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

      ![A Azure Cosmos DB-kapcsolatok házirendjének ábrája](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Rézvezetékes végezhet-ügyfelek ugyanabban az Azure-régióban a teljesítmény érdekében**

    Ha lehetséges, helyezzen minden olyan alkalmazást, amely a Azure Cosmos DBt hívja meg ugyanabban a régióban, mint a Azure Cosmos DB-adatbázist. A hozzávetőleges összehasonlításhoz az azonos régióban lévő Azure Cosmos DB a 1-2 MS-on belül fejeződik be, de az USA nyugati és keleti partja közötti késés > 50 MS. Ez a késés valószínűleg a kérelemtől függ attól függően, hogy a kérés milyen útvonalon halad át az ügyféltől az Azure Datacenter-határig. A lehető legalacsonyabb késést úgy érheti el, hogy a hívó alkalmazás ugyanabban az Azure-régióban található, mint a kiépített Azure Cosmos DB végpont. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/#services)című részben tekintheti meg.

    ![A Azure Cosmos DB-kapcsolatok házirendjének ábrája](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-használat
1. **A legújabb SDK telepítése**

    A Azure Cosmos DB SDK-kat folyamatosan fejlesztjük a legjobb teljesítmény érdekében. Tekintse meg a [Azure Cosmos db SDK](documentdb-sdk-java.md) -lapokat a legújabb SDK-val, és tekintse át a módosításokat.
2. **Egyedi Azure Cosmos DB-ügyfél használata az alkalmazás élettartama során**

    Az egyes [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) -példányok a szálon biztonságosak, és hatékony hálózatkezelést és gyorsítótárazást hajtanak végre közvetlen módban való működés közben. A hatékony DocumentClient és a jobb teljesítmény érdekében ajánlott a DocumentClient/alkalmazástartomány egyetlen példányát használni az alkalmazás élettartamára.

   <a id="max-connection"></a>
3. **MaxPoolSize növelésének engedélyezése az átjáró mód használatakor**

    Azure Cosmos DB kérések HTTPS/REST protokollon keresztül történnek az átjáró mód használatakor, és a rendszer az alapértelmezett kapcsolati korlátot adja meg egy állomásnév vagy IP-cím alapján. Előfordulhat, hogy a MaxPoolSize magasabb értékre (200-1000) kell állítania, hogy az ügyféloldali kódtár több egyidejű kapcsolatot is felhasználjon Azure Cosmos DBhoz. A Java SDK-ban a [ConnectionPolicy. getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) alapértelmezett értéke 100. Módosítsa az értéket a [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) használatával.

4. **A particionált gyűjtemények párhuzamos lekérdezésének finomhangolása**

    Azure Cosmos DB SQL Java SDK 1.9.0 és újabb verziója támogatja a párhuzamos lekérdezéseket, amelyek lehetővé teszik a particionált gyűjtemények párhuzamos lekérdezését. További információ: az SDK-k használatához kapcsolódó [kód-minták](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) . A párhuzamos lekérdezések úgy vannak kialakítva, hogy a lekérdezési késést és az adatátvitelt a soros munkatársaik

    (a) a ***setMaxDegreeOfParallelism\:***  párhuzamos lekérdezések finomhangolása több partíció párhuzamos lekérdezésével működik. Az egyedi particionált gyűjteményekből származó adatok azonban a lekérdezéssel kapcsolatos sorosan kerülnek beolvasásra. Ezért a [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) használatával állítsa be a legtöbb teljesítményű lekérdezés elérésének maximális esélyét biztosító partíciók számát, ha az összes többi rendszerfeltétel változatlan marad. Ha nem ismeri a partíciók számát, a setMaxDegreeOfParallelism használatával magas számot állíthat be, a rendszer pedig a minimális párhuzamosságot (a partíciók számát, a felhasználó által megadott bemenetet) adja meg. 

    Fontos megjegyezni, hogy a párhuzamos lekérdezések a legjobb előnyöket nyújtják, ha az adatforgalom egyenletesen oszlik el az összes partíció között a lekérdezés tekintetében. Ha a particionált gyűjtemény úgy van particionálva, hogy a lekérdezés által visszaadott összes adat többsége néhány partíción (egy partíció a legrosszabb esetben) van, akkor a lekérdezés teljesítményét a partíciók szűk keresztmetszete okozhatja.

    (b) az ***setMaxBufferedItemCount\:***  párhuzamos lekérdezés finomhangolása az eredmények előzetes beolvasására szolgál, miközben az ügyfél az aktuális eredményt dolgozza fel. Az előzetes beolvasás a lekérdezés teljes késésének javulását segíti elő. a setMaxBufferedItemCount korlátozza az előre beolvasott eredmények számát. Ha a [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) értékre állítja a visszaadott eredmények várt számát (vagy egy magasabb számot), ez lehetővé teszi a lekérdezés számára, hogy a lehető legtöbbet fogadja az előzetes lekéréstől.

    Az előzetes lekérés ugyanúgy működik, mint a Maxanalyticsunits, és egyetlen puffer van az összes partícióból származó adatokhoz.  

5. **Leállítási megvalósítása getRetryAfterInMilliseconds időközönként**

    A teljesítmény tesztelése során növelje a terhelést, amíg a kérelmek kis száma le nem kerül a szabályozásba. Ha a szabályozás meg van adva, az ügyfélalkalmazás leállítási kell lennie a kiszolgáló által megadott újrapróbálkozási időköz szabályozása érdekében. A leállítási tiszteletben tartásával biztosítható, hogy az újrapróbálkozások között minimálisan mennyi időt kell várnia. Az újrapróbálkozási szabályzat támogatása a 1.8.0-verzióban és a [Java SDK](documentdb-sdk-java.md)-ban található. További információ: [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Az ügyfél felskálázása – munkaterhelés**

    Ha nagy átviteli sebességű > (50000 RU/s) szintű tesztelést végez, akkor előfordulhat, hogy az ügyfélalkalmazás szűk keresztmetszetet okoz a PROCESSZORon vagy a hálózat kihasználtságán túl. Ha eléri ezt a pontot, továbbra is folytathatja a Azure Cosmos DB-fiók leküldését az ügyfélalkalmazások több kiszolgálón való skálázásával.

7. **Név alapú címzés használata**

    Használjon név-alapú címzést, ahol a hivatkozások formátuma `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`a SelfLinks helyett (\_saját), amelynek formátuma `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` a hivatkozás létrehozásához használt összes erőforrás ResourceId: beolvasásának elkerülése. Továbbá, mivel ezek az erőforrások újra létrejönnek (valószínűleg ugyanazzal a névvel), a gyorsítótárazás nem segít.

   <a id="tune-page-size"></a>
8. **A jobb teljesítmény érdekében a lekérdezések/olvasási hírcsatornák méretének finomhangolása**

    A dokumentumok tömeges beolvasása a hírcsatornák olvasási funkciójának használatával (például [readDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) vagy SQL-lekérdezés kiállításakor az eredményeket szegmentált módon adja vissza, ha az eredményhalmaz túl nagy. Alapértelmezés szerint a rendszer az eredményeket 100 elemből vagy 1 MB-ra adja vissza, amely a határértékek közül az első.

    Ha csökkenteni szeretné az összes vonatkozó eredmény beolvasásához szükséges hálózati kör alakú utak számát, az [x-MS-Max-Item-](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) Request fejléc használatával növelheti az oldal méretét a legfeljebb 1000 értékre. Olyan esetekben, ahol csak néhány eredményt kell megjelenítenie, például ha a felhasználói felület vagy az Application API csak 10 eredményt ad vissza, az olvasási és lekérdezési teljesítmény csökkentése érdekében csökkentheti az oldal méretét is 10 értékre.

    Az oldalméret a [setPageSize metódussal](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize)is megadható.

## <a name="indexing-policy"></a>Indexelési házirend
 
1. **Nem használt útvonalak kizárása az indexelésből a gyorsabb írás érdekében**

    Azure Cosmos DB indexelési házirendje lehetővé teszi annak meghatározását, hogy mely dokumentum-elérési utakat kell belefoglalni vagy kizárni az indexelésből az indexelési útvonalak ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) és [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)) használatával. Az indexelési útvonalak használata javíthatja az írási teljesítményt és az alacsonyabb indexű tárolást olyan helyzetekben, amikor a lekérdezési mintákat előre ismerték, mivel az indexelési költségek közvetlenül az indexelt egyedi útvonalak számával vannak összekapcsolva.  Az alábbi kód például azt mutatja be, hogyan zárható ki a dokumentumok teljes szakasza (más néven: egy részfa) az indexelésből a "*" helyettesítő karakter használatával.

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

    Az összes művelet (létrehozás, frissítés vagy törlés) mértékének méréséhez vizsgálja meg az [x-MS-Request-Charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécét (vagy a [ResourceResponse\<t >](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) vagy a [FeedResponse\<t RequestCharge tulajdonságot a következő > ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse)a műveletek által felhasznált kérelmek mennyiségének méréséhez.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Az ebben a fejlécben visszaadott kérelem díja a kiépített átviteli sebesség töredéke. Ha például 2000 RU/s van kiépítve, és ha az előző lekérdezés a 1000 1KB-dokumentumokat adja vissza, a művelet díja 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) és a [kérési egység kalkulátora](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **A kezelői sebesség korlátozása/kérelmek arányának aránya túl nagy**

    Ha egy ügyfél megpróbál meghaladni egy fiók fenntartott átviteli sebességét, nincs teljesítmény-romlás a kiszolgálón, és a lefoglalt szinten túl nem használható az átviteli kapacitás. A kiszolgáló megelőző jelleggel a kérést a RequestRateTooLarge (429-es HTTP-állapotkód), és visszaküldi az [x-MS-újrapróbálkozás-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) fejlécet, jelezve azt az időtartamot (ezredmásodpercben), ameddig a felhasználónak meg kell várnia a kérés ismételt megkísérlése előtt.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Az SDK-k minden implicit módon elkapják ezt a választ, tiszteletben tartsák a kiszolgáló által megadott újrapróbálkozás utáni újrapróbálkozást, majd próbálja megismételni a kérelmet. Ha a fiókját több ügyfél egyidejűleg nem éri el, a következő újrapróbálkozás sikeres lesz.

    Ha több ügyfél halmozottan működik, és a kérések aránya meghaladja a kérelmek arányát, akkor előfordulhat, hogy az ügyfél az újrapróbálkozások alapértelmezett száma jelenleg 9 belső értékre van állítva. Ebben az esetben az ügyfél egy 429-as állapotkódot tartalmazó [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) hoz az alkalmazáshoz. Az újrapróbálkozások alapértelmezett száma módosítható a [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) -példány [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) használatával. Alapértelmezés szerint a 429-as kódú DocumentClientException a rendszer a 30 másodperces kumulatív várakozási idő után adja vissza, ha a kérés továbbra is a kérelmek arányán felül működik. Ez akkor is előfordul, ha a jelenlegi újrapróbálkozások száma kisebb, mint az újrapróbálkozások maximális száma, legyen az alapértelmezett 9-es vagy felhasználó által definiált érték.

    Míg az automatikus újrapróbálkozási viselkedés segíti a legtöbb alkalmazás rugalmasságának és használhatóságának javítását, akkor előfordulhat, hogy a teljesítményre vonatkozó teljesítménymutatók végrehajtásakor a rendszer hasznosnak bizonyul, különösen a késés mérése során.  Az ügyfél által megfigyelt késés megszegi, ha a kísérlet megkeresi a kiszolgáló szabályozását, és az ügyfél-SDK-t csendes újrapróbálkozás okozta. A teljesítmény-kísérletek során felmerülő késések elkerülése érdekében mérje fel az egyes műveletek által visszaadott díjat, és győződjön meg arról, hogy a kérelmek a fenntartott kérelmek arányában működnek. További információt a [kérelmek egységei](request-units.md)című témakörben talál.
3. **Kisebb dokumentumok tervezése magasabb átviteli sebesség esetén**

    Egy adott műveletre vonatkozó kérelem díja (a kérelmek feldolgozási díja) közvetlenül összefügg a dokumentum méretével. A nagyméretű dokumentumokon végzett műveletek többek között a kis méretű dokumentumok műveleteinél nagyobb mértékben járnak.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az alkalmazás méretezési és nagy teljesítményű kialakításáról, tekintse meg [a particionálás és skálázás Azure Cosmos DBban](partition-data.md)című témakört.
